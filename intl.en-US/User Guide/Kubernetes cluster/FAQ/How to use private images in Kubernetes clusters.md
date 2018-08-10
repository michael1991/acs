# How to use private images in Kubernetes clusters {#concept_yvc_fxb_wdb .concept}

```
kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
```

Where:

-   **regsecret**: Specifies the secret key name and the name is customizable.
-   **—docker-server**: Specifies the Docker repository address.
-   **—docker-username**: Specifies the user name of the Docker repository.
-   **—docker-password**: Specifies the logon password of the Docker repository.
-   **—docker-email**: Specifies the email address \(optional\).

Add secret key parameters in the YML file.

```
containers:
    - name: foo
      image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
imagePullSecrets:
    - name: regsecret
```

Where:

-   `imagePullSecrets` declares that a secret key must be specified when you pull the image.
-   `regsecret` must be the same as the preceding secret key name.
-   The Docker repository name in `image` must be the same as that in `--docker-server`.

For more information, see the official documentation [Use private repository](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry).

