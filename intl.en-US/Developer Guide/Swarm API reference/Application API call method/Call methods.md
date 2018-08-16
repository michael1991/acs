# Call methods {#concept_qpj_k5c_xdb .concept}

The REST API of application management needs to point to the cluster access point address and interact with the cluster by using the HTTPS request of self-signed certificate.

## Obtain access point and certificate of the cluster {#section_djf_45c_xdb .section}

**Obtain in Container Service console**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/#/overview/all).
2.  Click **Clusters** in the left-side navigation pane.
3.  Click **Manage** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7305/15344166763333_en-US.png)

4.  View the cluster access point and click **Download Certificate** to download the cluster certificate.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7305/15344166763334_en-US.png)


To use API access, change the tcp in the command of the preceding figure to https.

**Obtain by programming**

Obtain the following items by using the cluster management API:

1.  The  `master_url` field value of the cluster.  For more information, see [View a cluster](intl.en-US/Developer Guide/Swarm API reference/Cluster API list/View a cluster.md#).
2.  The cluster certificate.  For more information, see  [Obtain cluster certificate](intl.en-US/Developer Guide/Swarm API reference/Cluster API list/Obtain cluster certificate.md#).

The API returned results:

```
{
    "ca": "string", ##Certificate issued by the Certification Authority, ca.pem
    "cert": "string", ##Your public key certificate, cert.pem
    "key": "string" ##Your private key certificate, key.pem
}
```

We recommend that you save the three strings in the returned results as three files, namely,  ca.pem, cert.pem, and key.pem in one directory.  For most tools or programming frameworks, the  HTTPS certificate is loaded as a file.

## Call application management API {#section_jb1_n5c_xdb .section}

Assume that your cluster name is `ClusterName`,  the preceding three certificates are saved to the ~/.docker/aliyun/ClusterName directory,  and the obtained  `master_url` address is `https://123.123.123.123:1234`.

**Application API list**

For more information, see [View application list](intl.en-US/Developer Guide/Swarm API reference/Application API list/View application list.md#).

The following example describes how to view application list interfaces \(the `context path` is `/projects/`\).

**CURL**

```
# Note: You may need to upgrade your CURL to the correct version.
curl --insecure --cert ~/.docker/aliyun/ClusterName/cert.pem --key ~/.docker/aliyun/ClusterName/key.pem https://123.123.123.123:1234/projects/
```

**PHP **

```
<? php
     $ch = curl_init();
     curl_setopt($ch, CURLOPT_URL, "https://123.123.123.123:1234/projects/");
     curl_setopt($ch, CURLOPT_SSLKEY, "~/.docker/aliyun/ClusterName/key.pem");
     curl_setopt($ch, CURLOPT_CAINFO, "~/.docker/aliyun/ClusterName/ca.pem");
     curl_setopt($ch, CURLOPT_SSLCERT, "~/.docker/aliyun/ClusterName/cert.pem");
     $result=curl_exec($ch);
     echo $result;
     curl_close($ch);
? >
```

**Python **

```
import requests
res = requests.get('https://123.123.123.123:1234/projects/', verify='~/.docker/aliyun/ClusterName/ca.pem', cert=('~/.docker/aliyun/ClusterName/cert.pem', '~/.docker/aliyun/ClusterName/key.pem'))
print res.content
```

**Java**

Add Maven dependency

```
<dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.1</version>
        </dependency>
        <dependency>
            <groupId>org.bouncycastle</groupId>
            <artifactId>bcpkix-jdk15on</artifactId>
            <version>1.52</version>
        </dependency>
```

Code sample:

```
import java.nio.file.Path;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.security.KeyFactory;
        import java.security.KeyStore;
        import java.security.PrivateKey;
        import java.security.cert.Certificate;
        import java.security.cert.CertificateFactory;
        import java.security.spec.PKCS8EncodedKeySpec;
        import javax.net.ssl.SSLContext;
        import org.bouncycastle.openssl.PEMKeyPair;
        import org.bouncycastle.openssl.PEMParser;
        import org.apache.http.client.methods.CloseableHttpResponse;
        import org.apache.http.client.methods.HttpGet;
        import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
        import org.apache.http.impl.client.CloseableHttpClient;
        import org.apache.http.impl.client.HttpClients;
        import org.apache.http.ssl.SSLContexts;
        import org.apache.http.util.EntityUtils;
        public class Test {
            public static void main(String[] argc) throws Exception {
                final char[] KEY_STORE_PASSWORD = "".toCharArray();
                //Obtain the certificate address
                Path caCertPath = Paths.get("~/.docker/aliyun/ClusterName/ca.pem");
                Path clientCertPath = Paths.get("~/.docker/aliyun/ClusterName/cert.pem");
                Path clientKeyPath = Paths.get("~/.docker/aliyun/ClusterName/key.pem");
                final CertificateFactory cf = CertificateFactory.getInstance("X. 509");
                final Certificate caCert = cf.generateCertificate(Files.newInputStream(caCertPath));
                final Certificate clientCert = cf.generateCertificate(
                        Files.newInputStream(clientCertPath));
                final PEMKeyPair clientKeyPair = (PEMKeyPair) new PEMParser(
                        Files.newBufferedReader(clientKeyPath,
                                Charset.defaultCharset()))
                        .readObject();
                final PKCS8EncodedKeySpec spec = new PKCS8EncodedKeySpec(
                        clientKeyPair.getPrivateKeyInfo().getEncoded());
                final KeyFactory kf = KeyFactory.getInstance("RSA");
                final PrivateKey clientKey = kf.generatePrivate(spec);
                //Set the trusted certificate
                final KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
                trustStore.load(null, null);
                trustStore.setEntry("ca", new KeyStore.TrustedCertificateEntry(caCert), null);
                // Set the private key
                final KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
                keyStore.load(null, null);
                keyStore.setCertificateEntry("client", clientCert);
                keyStore.setKeyEntry("key", clientKey, KEY_STORE_PASSWORD, new Certificate[]{clientCert});
                SSLContext sslContext = SSLContexts.custom()
                        .loadTrustMaterial(trustStore, null)
                        .loadKeyMaterial(keyStore, KEY_STORE_PASSWORD)
                        .build();
                SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(
                        sslContext,
                        SSLConnectionSocketFactory.getDefaultHostnameVerifier());
                //httpclient connection
                CloseableHttpClient httpclient = HttpClients.custom()
                        .setSSLSocketFactory(sslsf)
                        .build();
                try {
                    Httpget = new httpget ("https: // 123.123.123.123: 1234/projects /");
                    CloseableHttpResponse response = httpclient.execute(httpget);
                    try {
                        System.out.println("----------------------------------------");
                        String bodyAsString = EntityUtils.toString(response.getEntity());
                        System.out.println(bodyAsString);
                    } finally {
                        response.close();
                    }
                } finally {
                    httpclient.close();
                }
            }
        }
```

