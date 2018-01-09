声明：本文仅用于学习研究，若作为商业用途一切后果自负
1. 安装x-pack
![](../img/ELK/x-pack.png)

2. 找到 ./plugins/x-pack 下的 x-pack-5.5.2.jar 包，并利用 [Luyten](https://github.com/deathmarine/Luyten/releases) 工具进行反编译
3. 主要需要反编译以下两个 class 文件
```
org/elasticsearch/license/LicenseVerifier.class
org/elasticsearch/xpack/XPackBuild.class
```
4. 将这两个文件反编译成java文件并进行修改
```
//org/elasticsearch/license/LicenseVerifier.java
// license校验的函数，直接跳过校验部分，返回 true即可
package org.elasticsearch.license;

import java.nio.*;
import java.util.*;
import java.security.*;
import org.elasticsearch.common.xcontent.*;
import org.apache.lucene.util.*;
import org.elasticsearch.common.io.*;
import java.io.*;

public class LicenseVerifier
{
    public static boolean verifyLicense(final License license, final byte[] encryptedPublicKeyData) {
        return true;
    }

    public static boolean verifyLicense(final License license) {
        return true;
    }
}

//org/elasticsearch/xpack/XPackBuild.java
// 修改hash 和date 的获取方式
package org.elasticsearch.xpack;

import org.elasticsearch.common.io.*;
import java.net.*;
import org.elasticsearch.common.*;
import java.nio.file.*;
import java.io.*;
import java.util.jar.*;

public class XPackBuild
{
    public static final XPackBuild CURRENT;
    private String shortHash;
    private String date;

    @SuppressForbidden(reason = "looks up path of xpack.jar directly")
    static Path getElasticsearchCodebase() {
        final URL url = XPackBuild.class.getProtectionDomain().getCodeSource().getLocation();
        try {
            return PathUtils.get(url.toURI());
        }
        catch (URISyntaxException bogus) {
            throw new RuntimeException(bogus);
        }
    }

    XPackBuild(final String shortHash, final String date) {
        this.shortHash = shortHash;
        this.date = date;
    }

    public String shortHash() {
        return this.shortHash;
    }

    public String date() {
        return this.date;
    }

    static {
        final Path path = getElasticsearchCodebase();
        String shortHash = null;
        String date = null;
        Label_0165: {
            if (path.toString().endsWith(".jar")) {
                try {
                    try (final JarFile jar = new JarFile(path.toString())) {  // 修改此处
                        final Manifest manifest = jar.getManifest();
                        shortHash = manifest.getMainAttributes().getValue("Change");
                        date = manifest.getMainAttributes().getValue("Build-Date");
                    }
                    break Label_0165;
                }
                catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            shortHash = "Unknown";
            date = "Unknown";
        }
        CURRENT = new XPackBuild(shortHash, date);
    }
}
```
5. 将修改后的两个java文件编译成class，注意需要带上依赖的jar包
![](../img/ELK/x-pack_1.png)
![](../img/ELK/x-pack_2.png)
6. 利用 Commander One，用这两个class去替换 x-pack-5.5.2.jar 包中的 class
7. 将新的 x-pack-5.5.2.jar 上传至服务器，替换原来的包
```
// 登录服务器，利用scp命令，从远程机器中下载文件到服务器
scp chendanjun@xxx.xx.xx.xx:/Users/chendanjun/work/elasticsearch-5.5.2/plugins/x-pack/x-pack-5.5.2.jar x-pack-5.5.2.jar

```
8. 重启es
```
ps -ef | grep elastic
kill -9 pid
./bin/elastic -d
```
9. 去官网申请一个basic license，并进行修改
```
{
    "license":{
        "uid": "7b989ff3-61ec-497b-a106-c2567ab4955d",
        "type": "platinum",  // 修改类型为platinum，我们所需要的 watcher 和 machine learning 就都能用了
        "issue_date_in_millis":1508457600000,
        "expiry_date_in_millis":1602288000000,   // 过期时间，随意修改
        "max_nodes":100,
        "issued_to":"diancan MT (mt)",
        "issuer":"Web Form",
        "signature":"AAAAAwAAAA22Z4GFoVF9hKAmfYaJTNS0+rvnKzQ0hhLanm3YKRx9Q7HUk5M7/5IAB9zKzcuy+Iopvpv",
        "start_date_in_millis":1508457600000
    }
}
```
10. 将修改后的 license 更新至服务器
```
curl -XPUT -u elastic 'http://esip:port/_xpack/license?acknowledge=true' -H "Content-Type: application/json" -d @"yourLicense.json"

```
11. 查看license
```
curl -XGET -u elastic:changeme 'http://esip:port/_xpack/license'
```
12. 再次重启 es 即可
