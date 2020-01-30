---
title: Ativar o SSL num grupo de contentores
description: Crie um ponto final SSL ou TLS para um grupo de contentores que funciona em instâncias de contentores Azure
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 541d53a9a9530f7ac80227dbae598b3da2691301
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773060"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Ativar um ponto final SSL num grupo de contentores

Este artigo mostra como criar um grupo de [contentores](container-instances-container-groups.md) com um recipiente de aplicação e um contentor sidecar que gere um fornecedor SSL. Ao configurar um grupo de contentores com um ponto final SSL separado, ativa as ligações SSL para a sua aplicação sem alterar o seu código de aplicação.

Criou um grupo de contentores de exemplo composto por dois contentores:
* Um recipiente de aplicação que executa uma simples aplicação web usando a imagem pública da Microsoft [aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Um recipiente sidecar que executa a imagem pública [de Nginx,](https://hub.docker.com/_/nginx) configurado para usar SSL. 

Neste exemplo, o grupo de contentores apenas expõe a porta 443 para nginx com o seu endereço IP público. Nginx rotas HTTPS solicita para a aplicação web companheira, que escuta internamente na porta 80. Pode adaptar o exemplo para aplicações de contentores que ouvem noutras portas. Consulte os [próximos passos](#next-steps) para outras abordagens para permitir o SSL num grupo de contentores.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir este artigo. Se quiser usá-lo localmente, recomenda-se a versão 2.0.55 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para configurar a Nginx como fornecedor ssl, precisa de um certificado SSL. Este artigo mostra como criar e criar um certificado SSL auto-assinado. Para cenários de produção, deverá obter um certificado de uma autoridade de certificados.

Para criar um certificado SSL auto-assinado, utilize a ferramenta [OpenSSL](https://www.openssl.org/) disponível na Azure Cloud Shell e muitas distribuições linux, ou utilize uma ferramenta cliente comparável no seu sistema operativo.

Primeiro crie um pedido de certificado (ficheiro.csr) num diretório de trabalho local:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Siga as instruções para adicionar as informações de identificação. Para Nome Comum, introduza o nome de anfitrião associado ao certificado. Quando solicitado para obter uma senha, prima Enter sem escrever, para não adicionar uma palavra-passe.

Executar o seguinte comando para criar o certificado auto-assinado (ficheiro.crt) a partir do pedido de certificado. Por exemplo:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Deverá agora ver três ficheiros no diretório: o pedido de certificado (`ssl.csr`), a chave privada (`ssl.key`) e o certificado auto-assinado (`ssl.crt`). Usa-se `ssl.key` e `ssl.crt` em passos posteriores.

## <a name="configure-nginx-to-use-ssl"></a>Configure nginx para usar SSL

### <a name="create-nginx-configuration-file"></a>Criar ficheiro de configuração Nginx

Nesta secção, cria-se um ficheiro de configuração para a Nginx utilizar o SSL. Comece por copiar o seguinte texto num novo ficheiro chamado`nginx.conf`. Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```console
code nginx.conf
```

Em `location`, certifique-se de que `proxy_pass` com a porta correta para a aplicação. Neste exemplo, definimos a porta 80 para o contentor `aci-helloworld`.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Segredos de código Base64 e ficheiro de configuração

Base64-codificar o ficheiro de configuração Nginx, o certificado SSL e a tecla SSL. Na secção seguinte, introduza o conteúdo codificado num ficheiro YAML utilizado para implantar o grupo de contentores.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Implementar grupo de contentores

Agora, desloque o grupo do contentor especificando as configurações do recipiente num [ficheiro YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Criar ficheiro YAML

Copie o seguinte YAML num novo ficheiro chamado `deploy-aci.yaml`. Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```console
code deploy-aci.yaml
```

Introduza o conteúdo dos ficheiros codificados base64 sempre que indicado em `secret`. Por exemplo, `cat` cada um dos ficheiros codificados base64 para ver o seu conteúdo. Durante a implantação, estes ficheiros são adicionados a um [volume secreto](container-instances-volume-secret.md) no grupo de contentores. Neste exemplo, o volume secreto é montado no recipiente Nginx.

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
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
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

Criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Desloque o grupo de contentores com o [recipiente az criar](/cli/azure/container#az-container-create) comando, passando o ficheiro YAML como argumento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Ver estado de implantação

Para ver o estado da implantação, utilize o seguinte comando de demonstração de [contentores az:](/cli/azure/container#az-container-show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Para uma implementação bem sucedida, a saída é semelhante à seguinte:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verificar a ligação SSL

Para visualizar a aplicação em execução, navegue para o seu endereço IP no seu navegador. Por exemplo, o endereço IP apresentado neste exemplo é `52.157.22.76`. Deve utilizar `https://<IP-ADDRESS>` para ver a aplicação de execução, devido à configuração do servidor Nginx. As tentativas de ligação com `http://<IP-ADDRESS>` falham.

![Captura de ecrã do browser a mostrar a aplicação em execução numa instância do contentor do Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Uma vez que este exemplo utiliza um certificado auto-assinado e não um de uma autoridade de certificados, o navegador apresenta um aviso de segurança ao ligar-se ao site em HTTPS. Este comportamento é esperado.
>

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como configurar um contentor Nginx para permitir as ligações SSL a uma aplicação web que funciona no grupo de contentores. Pode adaptar este exemplo para apps que ouvem em portas que não a porta 80. Também pode atualizar o ficheiro de configuração Nginx para redirecionar automaticamente as ligações do servidor na porta 80 (HTTP) para utilizar https.

Enquanto este artigo utiliza Nginx no sidecar, você pode usar outro fornecedor SSL como [caddy](https://caddyserver.com/).

Se implantar o seu grupo de contentores numa [rede virtual Azure,](container-instances-vnet.md)pode considerar outras opções para ativar um ponto final SSL para uma instância de contentor de backend, incluindo:

* [Proxies de Funções Azure](../azure-functions/functions-proxies.md)
* [Gestão de API do Azure](../api-management/api-management-key-concepts.md)
* [Gateway de Aplicação do Azure](../application-gateway/overview.md)

Para utilizar um gateway de aplicação, consulte um modelo de [implementação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)da amostra .
