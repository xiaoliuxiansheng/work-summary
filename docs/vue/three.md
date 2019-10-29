## 如何动态添加页面title 跟 favicon.ico
```javascript
 schoolName(newName) {
                document.title = newName
            },
            h5Host(newUrl){
                this.common.goH5(newUrl)
            },
            logo(newLogo){
                var link = document.querySelector("link[rel*='icon']") || document.createElement('link');
                link.type = 'image/x-icon';
                link.rel = 'shortcut icon';
                link.href = newLogo;
                document.getElementsByTagName('head')[0].appendChild(link)
            }
 ```