# 下载文件

## 前台代码

```javascript
/**
 *
 * @param { string } url 请求的路径
 * @param { string } key 请求的数据key, 默认downloadParams，post发送数据时将会以这个key发送数据给后台，get无关
 * @param { string|object } data 请求的数据, 支持string和object
 * @param { string } method 请求的方法，默认post
 * @param { function } startCallback 开始下载前的回调
 * @param { function } endCallback 请求完成结束后的回调，如果有失败可以根据返回的json提示
 * 可以选择前端的处理方式，默认为form形式选择，兼容性好一些，当浏览器不支持blob等对象时会自动选择form形式，还支持直接form形式（兼容性更好）
 * 注意form形式需要后台配合处理成功后写入cookie: downloadResult=1, 不成功则不需要处理，
 * node层可以参考publicDownload中间件
 * @param { string} downloadType 选择前端的处理方式，xhr|form
 * @param { number } timeout 超时设置
 */

export const downloadFile = function(opt) {
    const { url, key = 'downloadParams', data = {}, method = 'POST', startCallback, endCallback, downloadType = 'form', timeout = 60000 } = opt;
    if (window.XMLHttpRequest && window.Blob && window.FileReader && downloadType === 'xhr') {
        downloadFileXhr(opt);
    } else {
        startCallback();
        const iframeId = 'tempDownloadIframe' + (+new Date());
        const formId = 'tempFormId_' + (+new Date());
        clearCookie('downloadResult');
        const myEndCallback = (res) => {
            const iframEl = document.getElementById(iframeId);
            const formEl = document.getElementById(formId);
            clearCookie('downloadResult');
            iframEl.innerHTML = '';
            iframEl.parentNode.removeChild(iframEl);
            formEl.parentNode.removeChild(formEl);
            typeof endCallback === 'function' && endCallback(res);
        };

        const checkStatus = (startTime) => {
            if (((+new Date()) - startTime) > timeout) {
                myEndCallback({ errcode: '5004', description: '请求超时，请稍后再试！' });
            } else {
                let result = getCookie('downloadResult');
                if (result) {
                    if (result === '1') {
                        myEndCallback({ errcode: '0000', description: '下载成功' });
                    } else {
                        result = JSON.parse(unescape(result));
                        myEndCallback(result);
                    }
                } else {
                    setTimeout(function () {
                        checkStatus(startTime);
                    }, 1000);
                }
            }
        };
        const iframe = document.createElement('iframe');
        iframe.id = iframeId;
        iframe.name = iframeId;
        iframe.enctype = 'application/x-www-form-urlencoded';
        iframe.style.display = 'none';
        document.body.appendChild(iframe);
        const formEl = document.createElement('form');
        formEl.id = formId;
        formEl.target = iframeId;
        formEl.style.display = 'none';
        formEl.method = method;
        formEl.action = url;
        const inputEl = document.createElement('input');
        inputEl.type = 'hidden';
        inputEl.name = key;
        inputEl.value = typeof data === 'object' ? JSON.stringify(data) : data;
        formEl.appendChild(inputEl);
        document.body.appendChild(formEl);
        formEl.submit();
        checkStatus(+new Date());
    }
};

/**
 * @description 通过XMLHttpRequest方式处理下载，要求支持XMLHttpRequest、blob、FileReader
 * @param { string } url 请求的路径
 * @param { string } key 请求的数据key
 * @param { string|object } data 请求的数据, 支持string和object
 * @param { string } method 请求的方法，默认post
 * @param { function } startCallback 开始下载前的回调
 * @param { function } endCallback 请求完成结束后的回调，如果有失败可以根据返回的json提示
 */
const downloadFileXhr = function({ url, key = 'downloadParams', data = {}, method = 'POST', startCallback, endCallback, timeout = 60000 }) {
    method = method.toLocaleLowerCase();
    startCallback();

    const myEndCallback = (res) => {
        clearCookie('downloadResult');
        typeof endCallback === 'function' && endCallback(res);
    };

    const xhr = new window.XMLHttpRequest();
    if (method === 'get') {
        url += '?' + paramJson(data);
    };

    xhr.open(method, url, true);
    xhr.responseType = 'blob'; // 返回类型blob
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.timeout = timeout; // 超时时间，单位是毫秒
    xhr.onerror = () => {
        myEndCallback({ errcode: '5000', description: '服务端异常，请稍后再试' });
    };

    xhr.ontimeout = () => {
        myEndCallback({ errcode: '5004', description: '请求超时，请稍后再试' });
    };

    xhr.onload = () => {
        if (xhr.status === 200) {
            const blob = xhr.response;
            const ctype = xhr.getResponseHeader('Content-Type');
            if (ctype.indexOf('text/html') !== -1) {
                myEndCallback({ errcode: '5000', description: '服务端异常，请稍后再试' });
            } else if (ctype.indexOf('application/json') !== -1) {
                const reader = new window.FileReader();
                reader.onload = function() {
                    let content = reader.result;//内容就在这里
                    try {
                        content = JSON.parse(content);
                    } catch (error) {
                        content = { errcode: '5000', description: '服务端异常，请稍后再试' };
                        console.error(error);
                    }
                    myEndCallback(content);
                };
                reader.readAsText(blob);
            } else {
                const disposition = xhr.getResponseHeader('Content-Disposition');
                const eleLink = document.createElement('a');
                eleLink.download = decodeURIComponent(disposition.split(';')[1].split('=')[1]);
                eleLink.style.display = 'none';
                eleLink.href = URL.createObjectURL(new Blob([blob]));
                document.body.appendChild(eleLink);
                eleLink.click();
                document.body.removeChild(eleLink);
                myEndCallback({ errcode: '0000', description: '下载成功' });
            }
        } else {
            myEndCallback({ errcode: '5000', description: '请求异常，请稍后再试' });
        }
    };

    if (method === 'post') {
        const dataStr = JSON.stringify(data);
        const newData = {};
        newData[key] = dataStr;
        xhr.send(JSON.stringify(newData));
    } else {
        xhr.send();
    }
};
```

## Node层代码

> 如果是二进制流数据的话,node中间层不需要做额外的处理,直接返回流数据即可.

#### publicDownload中间件

```javascript
// eggjs中间件
module.exports = (options) => {
    // 通用转发
    return async function forwardDownload(ctx, next) {
        await next();
        let data = {};
        const method = ctx.request.method.toUpperCase();
        if (method === 'POST') {
            data = ctx.request.body;
            data = JSON.parse(data.downloadParams); // post 方式统一通过downloadParams传递一个完整的json参数
        } else {
            data = ctx.request.query;
        }

        const regFilter = new RegExp('^' + API_PRE_PATH);
        let remoteUrl = ctx.app.config.apiUrl.baseUrl + ctx.request.url.replace(regFilter, '').split('?')[0];
        const access_token = ctx.session.access_token || '';
        remoteUrl = remoteUrl + '?access_token=' + access_token;
        let result;
        if (method === 'POST') {
            console.log('method');
            result = await ctx.curl(remoteUrl, {
                data,
                method,
                contentType: 'json'
            });
        } else {
            result = await ctx.curl(remoteUrl + '&' + paramJson(data));
        }

        ctx.status = result.status;

        let resultCookies = null;
        // result.headers指的是响应头
        if (result.headers['content-type'].indexOf('application/json') !== -1) {
            resultCookies = escape(result.data.toString());
        } else {
            resultCookies = 1;
        }
        if (resultCookies === 1) {
            ctx.set(result.headers);
        } else {
            ctx.set('Content-Type', 'text/html');
        }

        ctx.cookies.set('downloadResult', resultCookies, {
            maxAge: 60 * 1000,
            httpOnly: false
        });

        ctx.body = result.data;
    };
};
```



