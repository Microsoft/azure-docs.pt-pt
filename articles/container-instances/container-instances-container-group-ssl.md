---
title: Ativar o SSL no Azure Container Instances
description: Criar um ponto final SSL ou TLS para um grupo de contentores em execução no Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 10c015a9aee4ed8be54805f7adaae5bb4b5c422f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870391"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Ativar um ponto final SSL num grupo de contentor

Este artigo mostra como criar uma [grupo de contentores](container-instances-container-groups.md) com um contêiner de aplicativo e um contentor de sidecar com um fornecedor SSL. Ao configurar um grupo de contentores com um ponto de final separado do SSL, ativar ligações SSL para a sua aplicação sem alterar o código da aplicação.

Pode configurar um grupo de contentores que consiste em dois contêineres:
* Um contêiner de aplicativo que executa uma aplicação web simples com o Microsoft público [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) imagem. 
* Um contentor de sidecar com público [Nginx](https://hub.docker.com/_/nginx) imagem, configurada para utilizar SSL. 

Neste exemplo, o grupo de contentores apenas expõe a porta 443 para Nginx com o respetivo endereço IP público. Nginx encaminha os pedidos HTTPS para a aplicação de web complementar que internamente escuta na porta 80. Pode adaptar o exemplo para aplicações de contentor que escutar outras portas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este artigo. Se gostaria de usá-lo localmente, versão 2.0.55 ou posterior é recomendado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para configurar o Nginx como um provedor SSL, precisa de um certificado SSL. Este artigo mostra como criar e configurar um certificado SSL autoassinado. Para cenários de produção, deve obter um certificado de uma autoridade de certificação.

Para criar um certificado SSL autoassinado, utilize o [OpenSSL](https://www.openssl.org/) ferramenta disponível no Azure Cloud Shell e muitas distribuições do Linux ou utilizar uma ferramenta de cliente comparável no seu sistema operativo.

Primeiro, crie um pedido de certificado (ficheiro. CSR) num diretório de trabalho local:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Siga as instruções para adicionar as informações de identificação. Nome comum, introduza o nome de anfitrião associado ao certificado. Quando lhe for pedido para uma palavra-passe, prima Enter sem escrever, para ignorar a adição de uma palavra-passe.

Execute o seguinte comando para criar o certificado autoassinado (ficheiro. crt) no pedido de certificado. Por exemplo:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Agora, deverá ver três arquivos no diretório: o pedido de certificado (`ssl.csr`), a chave privada (`ssl.key`) e o certificado autoassinado (`ssl.crt`). Utilizar `ssl.key` e `ssl.crt` em passos posteriores.

## <a name="configure-nginx-to-use-ssl"></a>Configurar o Nginx para utilizar SSL

### <a name="create-nginx-configuration-file"></a>Criar ficheiro de configuração do Nginx

Nesta secção, vai criar um ficheiro de configuração para Nginx utilizar SSL. Comece por copiar o seguinte texto num novo arquivo chamado`nginx.conf`. No Azure Cloud Shell, pode usar o Visual Studio Code para criar o ficheiro no diretório de trabalho:

```console
code nginx.conf
```

Na `location`, certifique-se de que definir `proxy_pass` com a porta correta para a aplicação. Neste exemplo, vamos definir a porta 80 para o `aci-helloworld` contentor.

```console
# nginx Configuration File
# http://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Codificar em Base64 segredos e arquivo de configuração

Base64-codificar o ficheiro de configuração Nginx, o certificado SSL e a chave SSL. Utilize o conteúdo codificado para configurar o contentor de Nginx.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Implementar o grupo de contentores

Agora, implementar o grupo de contentor ao especificar as configurações de contentor num [ficheiro YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Criar o ficheiro YAML

Copie o YAML seguinte para um novo ficheiro designado `deploy-aci.yaml`. No Azure Cloud Shell, pode usar o Visual Studio Code para criar o ficheiro no diretório de trabalho:

```console
code deploy-aci.yaml
```

Introduza o conteúdo de com codificação base64 ficheiros onde seja indicado em `secret`. Durante a implementação, estes ficheiros são adicionados a uma [volume secreto](container-instances-volume-secret.md) no grupo de contentores. Neste exemplo, o volume secreto está montado para o contentor de Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <base64-ssl.crt>
      ssl.key: <base64-ssl.key>
      nginx.conf: <base64-nginx.conf>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Implementar o grupo de contentores

Criar um grupo de recursos com o [criar grupo az](/cli/azure/group#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementar o grupo de contentores com o [criar contentor de az](/cli/azure/container#az-container-create) comando, passando o ficheiro YAML como um argumento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Estado de implementação de exibição

Para ver o estado da implementação, utilize o seguinte procedimento [show de contentor az](/cli/azure/container#az-container-show) comando:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Para uma implantação bem-sucedida, o resultado é semelhante ao seguinte:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Certifique-se a ligação SSL

Para ver a aplicação em execução, navegue para o respetivo endereço IP no seu browser. Por exemplo, o endereço IP mostrado neste exemplo é `52.157.22.76`. Tem de utilizar `https://<IP-ADDRESS>` para ver a aplicação em execução, devido à configuração de servidor Nginx. Tenta estabelecer ligação com `http://<IP-ADDRESS>` falhar.

![Captura de ecrã do browser a mostrar a aplicação em execução numa instância do contentor do Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Como este exemplo utiliza um certificado autoassinado e não um a partir de uma autoridade de certificação, o browser apresenta um aviso de segurança ao ligar ao site através de HTTPS. Este comportamento é esperado.
>

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou como configurar um contentor de Nginx para ativar as ligações de SSL para uma aplicação web em execução no grupo de contentores. Pode adaptar este exemplo para as aplicações que a escutar nas portas sem ser a porta 80. Também pode atualizar o ficheiro de configuração Nginx para redirecionar automaticamente ligações de servidor na porta 80 (HTTP) para utilizar HTTPS.

Embora este artigo utiliza o Nginx num sidecar, pode utilizar outro fornecedor SSL como [Caddy](https://caddyserver.com/).

Outra abordagem para habilitar o SSL num grupo de contentores é implementar o grupo num [rede virtual do Azure](container-instances-vnet.md) com um [gateway de aplicação do Azure](../application-gateway/overview.md). O gateway pode ser definido como um ponto final SSL. Veja um exemplo [modelo de implementação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) pode ser adaptados para ativar a terminação de SSL no gateway.
