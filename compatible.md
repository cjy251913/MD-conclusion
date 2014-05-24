

兼容代码总结
==================
*   [兼容代码](#compatible)
    *   [EventUtil](#eventUtil)
*   [DOM](#dom)
    *   [动态添加脚本](#dynamicScript)
    *   [动态添加样式表](#dynamicCss)
*   [工具代码](#tools)
    *   [序列化表单](#serilizeForm)
    *   [确定网络是否连通](#onlineOrOffline)
* * *


<h2 id="compatible">兼容</h2>
<h3 id="eventUtil">EvnetUtil</h3>
    EventUtil = {
            addHandler:function (ele,type,handle){
                if(window.addEventListener) {
                    ele.addEventListener(type,handle,false);
                }
                else {
                    window.attachEvent("on"+type,handle);
                }
            },
            getEvent:function(event) {
                return event||window.event;
            },
            getTarget:function(event){
                return event.target||event.srcElement;
            },
            preventDefault:function(event){
                if(event.preventDefault) {
                    event.preventDefault();
                }
                else {
                    event.returnValue = false;
                }
            },
            stopPropagation:function(event) {
                if(event.stopPropagation) {
                    event.stopPropagation();
                }
                else{
                    event.cancelBubble = true;
                }
            }
        }

<h2 id="dom">DOM</h2>
<h3 id="dynamicScript">动态添加脚本</h3>

    //url法
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    document.body.appendChild(script);

    //添加具体内容法
    var script = document.createElement("script");
    script.type = "text/javascript";
    var code = "alert("hello");"
    try{
        script.appendChild(document.createTextNode(code));  //w3c
    }catch(ex){
        script.text = code;  //IE
    }
    document.body.appendChild(script);

<h3 id="dynamicCss">动态添加样式表</h3>

    //url法
    var link = document.createElement("link");
    link.type = "text/css";
    link.rel = "stylesheet";
    link.href = url;
    document.getElementsByTagName("head")[0].appendChild(link);
    
    //添加具体内容法
    var style = document.createElement("style");
    style.type = "text/css";
    try {
        style.appendChild(document.createTextNode("body{color:red;}"));  //w3c
    }catch(ex){
        style.stylesheet.cssText = "body{color:red;}";   //IE
    }
    document.getElementsByTagName("head")[0].appendChild(style);



<h2 id="tools">工具函数</h2>
<h3 id="serializeForm">序列化表单</h3>
     function serialize(form){        
        var parts = [],
            field = null,
            i,
            len,
            j,
            optLen,
            option,
            optValue;
        
        for (i=0, len=form.elements.length; i < len; i++){
            field = form.elements[i];
        
            switch(field.type){
                case "select-one":
                case "select-multiple":
                
                    if (field.name.length){
                        for (j=0, optLen = field.options.length; j < optLen; j++){
                            option = field.options[j];
                            if (option.selected){
                                optValue = "";
                                if (option.hasAttribute){
                                    optValue = (option.hasAttribute("value") ? option.value : option.text);  //DOM
                                } else {
                                    optValue = (option.attributes["value"].specified ? option.value : option.text);   //IE
                                }
                                parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(optValue));
                            }
                        }
                    }
                    break;
                    
                case undefined:     //fieldset
                case "file":        //file input
                case "submit":      //submit button
                case "reset":       //reset button
                case "button":      //custom button
                    break;
                    
                case "radio":       //radio button
                case "checkbox":    //checkbox
                    if (!field.checked){
                        break;
                    }
                    /* falls through */
                                
                default:
                    //don't include form fields without names
                    if (field.name.length){
                        parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(field.value));
                    }
            }
        }        
        return parts.join("&");
    }

<h3 id="onlineOrOffline">确定网络是否连通</h3>
     EventUtil.addHandler(window, "online", function(){
            document.getElementById("status").innerHTML = "Online";
        });
        EventUtil.addHandler(window, "offline", function(){
            document.getElementById("status").innerHTML = "Offline";
        });

