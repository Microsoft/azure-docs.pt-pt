---
title: Criar uma imagem personalizada e para a aplicação Web para contentores - serviço de aplicações do Azure | Documentos da Microsoft
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
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545607"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Tutorial: Criar uma imagem personalizada e executar no serviço de aplicações a partir de um registo privado

[Serviço de aplicações](app-service-linux-intro.md) fornece imagens do Docker incorporadas no Linux com suporte para versões específicas, como PHP 7.0 e node. js 4.5. Serviço de aplicações utiliza a tecnologia de contentor do Docker para alojar imagens incorporadas e imagens personalizadas como plataforma como um serviço. Neste tutorial, saiba como criar uma imagem personalizada e executá-lo no serviço de aplicações. Este padrão é útil se as imagens incorporadas não incluírem a linguagem que quer ou se a aplicação exigir uma configuração específica que não é fornecida nas mesmas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar uma imagem personalizada para o registo de contentor privado
> * Execute a imagem personalizada no serviço de aplicações
> * Configurar as variáveis de ambiente
> * Atualizar e Reimplementar a imagem
> * Aceder aos registos de diagnósticos
> * Ligar ao contentor com SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

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

Criar a imagem do Docker com o `docker build` comando.

```bash
docker build --tag mydockerimage .
```

Execute o contentor do Docker para verificar se a compilação funciona. Emita o comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) e transmita o nome e a etiqueta da imagem para o mesmo. Certifique-se de que utiliza o argumento `-p` para especificar a porta.

```bash
docker run -p 8000:8000 mydockerimage
```

Navegue para `http://localhost:8000` para confirmar que a aplicação Web e o contentor estão a funcionar corretamente.

![Testar a aplicação Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Para criar uma aplicação que utiliza a imagem que acabou de criar, executar comandos da CLI do Azure que criar um grupo de recursos, envia a imagem e, em seguida, cria a aplicação de web de plano de serviço de aplicações para executá-lo.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

No Cloud Shell, utilize o comando [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) para criar um Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Inicie sessão no Azure Container Registry

Para enviar uma imagem para o registo, terá de autenticar com o registo privado. No Cloud Shell, utilize o [ `az acr show` ](/cli/azure/acr?view=azure-cli-latest#az-acr-show) comando para obter as credenciais do registo que criou.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

A saída revela duas palavras-passe, juntamente com o nome de utilizador.

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

Na janela de terminal do local, inicie sessão para o Azure Container Registry com o `docker login` de comando, conforme mostrado no exemplo a seguir. Substitua  *\<azure---nome do container registry >* e  *\<registry-username >* com valores para o seu registo. Quando lhe for pedido, escreva um das palavras-passe do passo anterior.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Confirme que o início de sessão for concluída com êxito.

### <a name="push-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

Etiqueta a imagem local para o Azure Container Registry. Por exemplo:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Utilize o comando `docker push` para enviar a imagem. Identifique a imagem com o nome do registo, seguido do nome e da etiqueta da mesma.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

No Cloud Shell, certifique-se de que o push é efetuada com êxito.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Obterá o resultado seguinte.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Criar plano do App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Criar aplicação Web

No Cloud Shell, crie uma [aplicação Web](app-service-linux-intro.md) no plano do Serviço de Aplicações `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Substitua  _\<nome da aplicação >_ com um nome de aplicação exclusivo, e  _\<azure---nome do container registry >_ com o nome do registo.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Configurar as credenciais do registo na aplicação web

Para o serviço de aplicações extrair a imagem privada, ele precisa de informações sobre o seu registo e a imagem. No Cloud Shell, fornecê-los com o [ `az webapp config container set` ](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) comando. Substitua  *\<nome da aplicação >*,  *\<azure---nome do container registry >*,  _\<registry-username >_ e  _\<palavra-passe >_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Quando utilizar um registo que não seja o Docker Hub `--docker-registry-server-url` têm de ser formatados como `https://` seguido do nome de domínio completamente qualificado do Registro.
>

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

A maioria das imagens do Docker utilizam variáveis de ambiente personalizadas, por exemplo, uma porta diferente da 80. Informar o serviço de aplicações sobre a porta que sua imagem utiliza utilizando o `WEBSITES_PORT` definição de aplicação. A página do GitHub para o [exemplo de Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` como _8000_.

Para configurar as definições da aplicação, utilize o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As definições da aplicação são sensíveis a maiúsculas e minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testar a aplicação Web

Navegue para a aplicação Web (`http://<app-name>.azurewebsites.net`) para confirmar que está a funcionar.

> [!NOTE]
> Na primeira vez que aceder à aplicação, pode demorar algum tempo porque o serviço de aplicações tem de extrair a imagem inteira. Se o navegador exceder o tempo limite, apenas Atualize a página.

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

Depois de modificar o ficheiro do Python e de o guardar, tem de recriar e enviar a imagem do Docker nova. Em seguida, reinicie a aplicação Web, para que as alterações entrem em vigor. Utilize os mesmos comandos que utilizou anteriormente neste tutorial. Pode consultar [criar a imagem a partir do dockerfile](#build-the-image-from-the-docker-file) e [imagem Push para o Azure Container Registry](#push-image-to-azure-container-registry). Siga as instruções em [Testar a aplicação Web](#test-the-web-app) para testar a aplicação Web.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Ativar ligações SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para ativar a ligação de SSH para o seu contentor, a sua imagem personalizada deve ser configurada para o mesmo. Vamos dar uma olhada no repositório de exemplo que já tem as configurações necessárias.

* Na [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), o código seguinte instala o servidor SSH e também define as credenciais de início de sessão.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Esta configuração não permite ligações externas ao contentor. O SSH só está disponível através do Site Kudu/SCM. O site Kudu/SCM é autenticado com a sua conta do Azure.

* O [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) cópias a [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository) para o */etc/ssh/* diretório.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* O [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) expõe a porta 2222 no contentor. Esta é uma porta interna a que só podem aceder os contentores dentro da rede de bridge de uma rede virtual privada. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* O [script de entrada](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) inicia o servidor SSH.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

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

Parabéns! Configurou um contentor personalizado do Linux no serviço de aplicações.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

O que aprendeu:

> [!div class="checklist"]
> * Implementar uma imagem personalizada para o registo de contentor privado
> * Execute a imagem personalizada no serviço de aplicações
> * Configurar as variáveis de ambiente
> * Atualizar e Reimplementar a imagem
> * Aceder aos registos de diagnósticos
> * Ligar ao contentor com SSH

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado à sua aplicação](../app-service-web-tutorial-custom-domain.md)

Em alternativa, consulte outros recursos:

> [!div class="nextstepaction"]
> [Configurar o contentor personalizado](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicação do WordPress com vários contentores](tutorial-multi-container-app.md)
