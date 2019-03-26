---
title: Utilizar uma imagem personalizada do Docker para a aplicação Web para contentores - serviço de aplicações do Azure | Documentos da Microsoft
description: Como utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores.
keywords: serviço de aplicações do azure, aplicação web, linux, docker, contentor
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 98e690ab73b9a51126f4eae9ac5eff410e211957
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435847"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores

As [Aplicações Web para Contentores](app-service-linux-intro.md) disponibilizam imagens do Docker incorporadas no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. As Aplicações Web para Contentores utilizam a tecnologia de contentores do Docker para alojar imagens incorporadas e personalizadas como plataforma como serviço. Neste tutorial, vai aprender a criar uma imagem personalizada do Docker e implementá-la nas Aplicações Web para Contentores. Este padrão é útil se as imagens incorporadas não incluírem a linguagem que quer ou se a aplicação exigir uma configuração específica que não é fornecida nas mesmas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar uma imagem personalizada do Docker no Azure
> * Configurar variáveis de ambiente para executar o contentor
> * Atualizar a imagem do Docker e reimplementá-la
> * Ligar ao contentor com SSH
> * Implementar uma imagem privada do Docker no Azure

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* Uma [conta do Docker Hub](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela de terminal, execute o comando seguinte para clonar o repositório de aplicação de exemplo no seu computador local e altere o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Compilar a imagem a partir do ficheiro Docker

No repositório Git, veja _Dockerfile_. Este ficheiro descreve o ambiente do Python que é necessário para executar a sua aplicação. Além disso, a imagem configura um servidor [SSH](https://www.ssh.com/ssh/protocol/) para comunicação segura entre o contentor e o anfitrião.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Para compilar a imagem do Docker, execute o comando `docker build` e indique um nome, _mydockerimage_ e uma etiqueta, _v1.0.0_. Substitua _\<docker-id>_ pelo ID da sua conta do Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz um resultado semelhante ao seguinte:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Execute o contentor do Docker para verificar se a compilação funciona. Emita o comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) e transmita o nome e a etiqueta da imagem para o mesmo. Certifique-se de que utiliza o argumento `-p` para especificar a porta.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Navegue para `http://localhost:2222` para confirmar que a aplicação Web e o contentor estão a funcionar corretamente.

![Testar a aplicação Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Também pode ligar ao contentor de aplicações diretamente a partir do seu computador de desenvolvimento local através de SSH, SFTP ou do Visual Studio Code (para aplicações Node.js de depuração em direto). Para obter mais informações, veja [Depuração remota e SSH no Serviço de Aplicações no Linux](https://aka.ms/linux-debug).
>

## <a name="push-the-docker-image-to-docker-hub"></a>Enviar a imagem do Docker para o Docker Hub

Um registo de contentor é uma aplicação que aloja imagens e disponibiliza serviços de imagens e contentores. Para partilhar a sua imagem, tem de enviá-la para um registo. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Quer enviar para um Registo Privado do Docker? Veja as instruções opcionais para [Utilizar uma imagem do Docker a partir de qualquer registo privado](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

O Docker Hub é um registo de contentores para imagens do Docker que lhe permite alojar os seus próprios repositórios, sejam públicos ou privados. Para enviar uma imagem personalizada do Docker para o Docker Hub público, utilize o comando [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) e indique um nome e uma etiqueta completos da imagem. O nome e a etiqueta completos da imagem são semelhantes ao exemplo seguinte:

```
<docker-id>/image-name:tag
```

Antes de pode enviar uma imagem, tem de iniciar sessão no Docker Hub com o comando [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). Substitua _\<docker-id>_ pelo nome da sua conta e introduza a palavra-passe na consola, quando lhe for pedido.

```bash
docker login --username <docker-id>
```

A mensagem "início de sessão bem-sucedido" confirma que tem sessão iniciada. Quando tiver sessão iniciada, pode enviar a imagem para o Docker Hub com o comando [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Veja o resultado do comando para confirmar se o envio foi bem-sucedido.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Para criar uma aplicação que utiliza a imagem que enviou, execute comandos CLI do Azure que criar um grupo, em seguida, um plano do serviço e, finalmente, a aplicação web propriamente dita. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Criar uma aplicação Web

No Cloud Shell, crie uma [aplicação Web](app-service-linux-intro.md) no plano do Serviço de Aplicações `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça de substituir _<appname>_ por um nome de aplicação exclusivo e  _\<docker-ID >_ pelo seu ID do Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

A maioria das imagens do Docker tem variáveis de ambiente que têm de ser configuradas. Se estiver a utilizar uma imagem do Docker existente criada por outra pessoa, essa poderá utilizar uma porta que não a 80. Utilize a definição da aplicação `WEBSITES_PORT` para dizer ao Azure qual é a porta que a sua imagem utiliza. A página do GitHub para o [exemplo de Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` como _8000_.

Para configurar as definições da aplicação, utilize o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As definições da aplicação são sensíveis a maiúsculas e minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Está a implementar um Registo Privado do Docker? Veja as instruções opcionais para [Utilizar uma imagem do Docker a partir de qualquer registo privado](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testar a aplicação Web

Navegue para a aplicação Web (`http://<app_name>.azurewebsites.net`) para confirmar que está a funcionar. 

![Testar a configuração da porta da aplicação Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Alterar e reimplementar a aplicação Web

No seu repositório Git local, abra app/templates/app/index.html. Localize o primeiro elemento HTML e altere-o para

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Depois de modificar o ficheiro do Python e de o guardar, tem de recriar e enviar a imagem do Docker nova. Em seguida, reinicie a aplicação Web, para que as alterações entrem em vigor. Utilize os mesmos comandos que utilizou anteriormente neste tutorial. Pode ver [Criar a imagem a partir do ficheiro do Docker](#build-the-image-from-the-docker-file) e [Enviar a imagem do Docker para Docker Hub](#push-the-docker-image-to-docker-hub). Siga as instruções em [Testar a aplicação Web](#test-the-web-app) para testar a aplicação Web.

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Ligar à Aplicação Web para Contentores com SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para que as imagens personalizadas do Docker suportem SSH, tem de criá-las num Dockerfile. O SSH é ativado no próprio ficheiro do Docker. As instruções do SSH já foram adicionadas ao dockerfile de exemplo, pelo que pode seguir estas instruções com a sua imagem personalizada:

* Uma instrução [RUN](https://docs.docker.com/engine/reference/builder/#run) que chama `apt-get` e, em seguida, define a palavra-passe da conta de raiz como `"Docker!"`.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Esta configuração não permite ligações externas ao contentor. O SSH só está disponível através do Site Kudu/SCM. O site Kudu/SCM é autenticado com as credenciais de publicação.

* Uma instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) que diz ao motor do Docker para copiar o ficheiro [sshd_config](https://man.openbsd.org/sshd_config) para o diretório */etc/ssh/*. O seu ficheiro de configuração deve basear-se [neste ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O ficheiro *sshd_config* tem de incluir os itens seguintes: 
    > * `Ciphers` tem de incluir, pelo menos, um item na lista `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` tem de incluir, pelo menos, um item na lista `hmac-sha1,hmac-sha1-96`.

* Uma instrução [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) que expõe a porta 2222 no contentor. Embora a palavra-passe de raiz seja conhecida, não é possível aceder à porta 2222 a partir da Internet. Esta é uma porta interna a que só podem aceder os contentores dentro da rede de bridge de uma rede virtual privada. Depois disso, os comandos copiam os detalhes de configuração do SSH e iniciam o serviço `ssh`.

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Certifique-se de que [inicia o serviço ssh](https://github.com/Azure-App-Service/node/blob/master/8.9/startup/init_container.sh#L18) com um script de shell no diretório /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Abrir a ligação SSH ao contentor

A Aplicação Web para Contentores não permite ligações externas ao contentor. O SSH só está disponível através do site Kudu, que é acessível em `https://<app_name>.scm.azurewebsites.net`.

Para ligar, navegue para `https://<app_name>.scm.azurewebsites.net/webssh/host` e inicie sessão com a sua conta do Azure.

Em seguida, é redirecionado para uma página que apresenta uma consola interativa. 

Pode ser útil verificar se determinadas aplicações estão em execução no contentor. Para inspecionar o contentor e verificar os processos em execução, emita o comando `top` na linha de comandos.

```bash
top
```

O comando `top` expõe todos os processos em execução num contentor.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Parabéns! Configurou uma imagem personalizada do Docker para uma Aplicação Web para Contentores.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Utilizar uma imagem privada do Docker Hub (opcional)

Em [Criar uma aplicação Web](#create-a-web-app), especificou uma imagem no Docker Hub no comando `az webapp create`. Isto é suficiente para imagens públicas. Para utilizar uma imagem privada, tem de configurar o ID e a palavra-passe da sua conta do Docker na aplicação Web do Azure.

No Cloud Shell, siga o comando `az webapp create` com [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Substitua *\<app_name>*, bem como _\<docker-id>_ e _\<password>_ pelo ID e a palavra-passe do Docker.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

O comando revela um resultado semelhante à cadeia JSON seguinte, que mostra que a alteração à configuração foi bem-sucedida:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Utilizar uma imagem do Docker de qualquer registo privado (opcional)

Nesta secção, vai aprender a utilizar uma imagem do Docker a partir de um registo privado na Aplicação Web para Contentores, sendo que o Azure Container Registry vai ser utilizado como exemplo. Os passos para utilizar outros registos privados são semelhantes. 

O Azure Container Registry é um serviço gerido do Docker que o Azure oferece para alojar imagens privadas. As implementações podem ser de qualquer tipo, incluindo [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) e Aplicações Web para Contentores. 

### <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

No Cloud Shell, utilize o comando [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) para criar um Azure Container Registry. Transmita o nome, o grupo de recursos e `Basic` para o SKU. Os SKUs disponíveis são `Classic`, `Basic`, `Standard` e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Criar um contentor produz o resultado seguinte:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Iniciar sessão no Azure Container Registry

Para enviar uma imagem para o registo, tem de indicar credenciais para que o registo aceita o envio. Pode utilizar o comando [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) no Cloud Shell para obter essas credenciais. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

O comando revela duas palavras-passe que podem ser utilizadas com o nome de utilizador.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Na janela do terminal local, inicie sessão no Azure Container Registry com o comando `docker login`. É necessário o nome do servidor para iniciar sessão. Utilize o formato `{azure-container-registry-name>.azurecr.io`. Escreva a palavra-passe na consola da linha de comandos.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Confirme que o início de sessão foi bem-sucedido. 

### <a name="push-an-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

> [!NOTE]
> Se estiver a utilizar a sua própria imagem, identifique-a da seguinte forma:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Utilize o comando `docker push` para enviar a imagem. Identifique a imagem com o nome do registo, seguido do nome e da etiqueta da mesma.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Liste os repositórios do ACR para confirmar que o envio adicionou um contentor ao registo de contentores. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Listar as imagens no registo confirma que `mydockerimage` está no registo.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configurar a Aplicação Web para utilizar a imagem do Azure Container Registry (ou de qualquer registo privado)

Pode configurar a Aplicação Web para Contentores de modo a que execute um contentor armazenado no Azure Container Registry. Utilizar o Azure Container Registry é igual a utilizar qualquer registo privado. Assim, se tiver de utilizar o seu próprio registo privado, os passos para concluir esta tarefa são semelhantes.

No Cloud Shell, execute [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) para apresentar o nome de utilizador e a palavra-passe do Azure Container Registry. Copie o nome de utilizador e uma das palavras-passe de forma a poder utilizá-los para configurar a aplicação Web no próximo passo.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

No Cloud Shell, execute o comando [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) para atribuir a imagem personalizada do Docker à aplicação Web. Substitua *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ e _\<password>_. No Azure Container Registry, *\<docker-registry-server-url>* está no formato `https://<azure-container-registry-name>.azurecr.io`. Se estiver a utilizar um registo que não o Docker Hub, o nome da imagem tem de começar com o nome de domínio completamente qualificado (FQDN) do seu registo. No Azure Container Registry, será parecido com `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` é necessário em *\<docker-registry-server-url>*.
>
> [!NOTE]
> Ao utilizar o registo que não seja o dockerhub, `docker-custom-image-name` tem de incluir o nome de domínio completamente qualificado (FQDN) do seu registo.  
> No Azure Container Registry, será parecido com `<azure-container-registry>.azurecr.io/mydockerimage`.

O comando revela um resultado semelhante à cadeia JSON seguinte, que mostra que a alteração à configuração foi bem-sucedida:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Build a Docker Python and PostgreSQL web app in Azure](tutorial-python-postgresql-app.md) (Criar uma aplicação Web Docker Python com PostgreSQL no Azure)