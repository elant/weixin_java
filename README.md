# weixin_java
weixin  
2016-01-08  
近期研究微信文件上传接口，
使用环境，Java开发，httpclient4.3.6，
---------------------------------------
部分代码如下：  
HttpEntity reqEntity = MultipartEntityBuilder.create()  
					.setMode(HttpMultipartMode.BROWSER_COMPATIBLE)  
					.addBinaryBody("media", file, ContentType.create("image/jpeg"), file.getName())  
					.setCharset(Consts.UTF_8)  
					.build();
					
出现{"errcode":41005,"errmsg":"media data missing hint: [......]"}  
错误信息，通过wireshark抓包分析后，  
异常数据包头：  
Content-Length: 22033  
Content-Type: multipart/form-data; boundary=2LQLHP0BPrf8kJsfxQwWI5lyJ3tp4eK9V1ryWo; charset=ISO-8859-1  
Content-Encoding: UTF-8  
Host: file.api.weixin.qq.com  
Connection: Keep-Alive  
User-Agent: Apache-HttpClient/4.3.1 (java 1.5)  
Accept-Encoding: gzip,deflate

--2LQLHP0BPrf8kJsfxQwWI5lyJ3tp4eK9V1ryWo  
Content-Disposition: form-data; name="media"; filename="......_20160105_175814.jpg"  
Content-Type: image/jpeg  
......  
.. ...  
--2LQLHP0BPrf8kJsfxQwWI5lyJ3tp4eK9V1ryWo--  

正常数据包头:  
Content-Length: 22041  
Content-Type: multipart/form-data; charset=ISO-8859-1  
Content-Encoding: UTF-8  
Host: file.api.weixin.qq.com  
Connection: Keep-Alive  
User-Agent: Apache-HttpClient/4.3.1 (java 1.5)  
Accept-Encoding: gzip,deflate  

-------------------------------1452219591728  
Content-Disposition: form-data; name="media"; filename="......_20160105_175814.jpg"  
Content-Type: image/jpeg  
......  
-------------------------------1452219591728--  

Google后解决，正确代码如下：  
HttpEntity reqEntity = MultipartEntityBuilder.create()  
					.setMode(HttpMultipartMode.BROWSER_COMPATIBLE)  
					.addBinaryBody("media", file, ContentType.create("image/jpeg"), file.getName())  
					.setCharset(Consts.UTF_8)  
					.build();  
			httpPost.setHeader("Content-Encoding", "UTF-8");  
			httpPost.setHeader("Content-Type", ContentType.MULTIPART_FORM_DATA.toString());  
发现问题出在"Content-Type"上：  
httpPost.setHeader("Content-Type", ContentType.MULTIPART_FORM_DATA.toString());  

