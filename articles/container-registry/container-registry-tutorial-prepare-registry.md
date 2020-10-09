---
title: Tutorial - Criar registo geo-replicado
description: Criar um registo de contentor do Azure, configurar a georreplicação, preparar uma imagem do Docker e implementá-la para o registo. Parte um de uma série com três partes.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 780a16d691e0d8afe62cd06f37a37fc3f6445ea6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259522"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Tutorial: Preparar um registo de contentor georreplicado do Azure

Um registo de contentor do Azure é um registo privado do Docker implementado no Azure que pode manter próximo em rede das suas implementações. Neste conjunto de três tutoriais, vai aprender a utilizar a georreplicação para implementar uma aplicação web em ASP.NET Core a executar num contentor do Linux para duas instâncias das [Aplicações Web para Contentores](../app-service/containers/index.yml). Vai ver como o Azure implementa automaticamente a imagem para cada instância de imagem Web a partir do repositório georreplicado mais próximo.

Neste tutorial, a primeira parte de uma série de três:

> [!div class="checklist"]
> * Criar um registo de contentor georreplicado do Azure
> * Clonar o código-fonte da aplicação do GitHub
> * Criar uma imagem de contentor do Docker a partir da origem da aplicação
> * Envie a imagem do contentor para o seu registo

Nos tutoriais seguintes, vai implementar o contentor do seu registo privado para uma aplicação web a executar em duas regiões do Azure. Em seguida, atualiza o código na aplicação e ambas as instâncias da Aplicação Web com um único `docker push` no seu registo.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer a instalação local da CLI do Azure (versão 2.0.31 ou posterior). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

Deve estar familiarizado com os principais conceitos do Docker, como contentores, imagens de contentores e comandos simples do Docker da CLI. Para obter noções básicas sobre os contentores, veja [Introdução ao Docker]( https://docs.docker.com/get-started/).

Para concluir este tutorial, precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para os sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

O Azure Cloud Shell não inclui os componentes do Docker necessários para concluir todos os passos deste tutorial. Portanto, recomendamos uma instalação local da CLI do Azure e do ambiente de desenvolvimento do Docker.

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Para este tutorial, você precisa de um registo de contentores Azure no nível de serviço Premium. Para criar um novo registo de contentores Azure, siga os passos nesta secção.

> [!TIP]
> Se criou previamente um registo e precisa de atualizar, consulte [níveis de alteração](container-registry-skus.md#changing-tiers). 

Inicie sessão no [portal do Azure](https://portal.azure.com).

Selecione **Criar um**registo de  >  **contentores de**recursos  >  **Azure Container**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Criar um registo de contentor no portal do Azure":::

Configure o seu novo registo com as seguintes definições. No separador **Básico:**

* **Nome do registo**: crie um nome de registo que seja globalmente exclusivo no Azure e contenha apenas 5-50 carateres alfanuméricos
* **Grupo de Recursos**: **Criar novos** > `myResourceGroup`
* **Localização**: `West US`
* **SKU**: `Premium` (necessário para a geo-replicação)

Selecione **Rever + criar** e, em seguida, **criar** para criar a instância de registo.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Criar um registo de contentor no portal do Azure":::

Durante todo o resto deste tutorial, utilizamos `<acrName>` como espaço reservado para o nome de registo **de** contentores que escolheu.

> [!TIP]
> Por os registos de contentor do Azure serem normalmente recursos de longo prazo utilizados em vários anfitriões de contentores, recomendamos que crie o seu registo no seu próprio grupo de recursos. À medida que configura registos e webhooks georreplicados, esses recursos adicionais são colocados no mesmo grupo de recursos.

## <a name="configure-geo-replication"></a>Configurar georreplicação

Agora que tem um registo Premium, pode configurar a georreplicação. A sua aplicação web, que vai configurar no próximo tutorial para que execute em duas regiões, pode então puxar as suas imagens de contentor a partir do registo mais próximo.

Navegue para o seu novo registo de contentores no portal Azure e selecione **Replicações** em **Serviços**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Criar um registo de contentor no portal do Azure":::

É mostrado um mapa que mostra hexágonos verdes que representam as regiões do Azure disponíveis para georreplicação:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Criar um registo de contentor no portal do Azure":::

Replique o seu registo para a região E.U.A. Leste ao selecionar o respetivo hexágono verde, selecione **Criar** em **Criar replicação**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Criar um registo de contentor no portal do Azure":::

Quando a replicação estiver completa, o portal reflete *Pronto* para ambas as regiões. Utilize o botão **Atualizar** para atualizar o estado da replicação; pode demorar cerca de um minuto para que as réplicas sejam criadas e sincronizadas.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Criar um registo de contentor no portal do Azure":::


## <a name="enable-admin-account"></a>Ativar conta de administração

Em tutoriais subsequentes, você implanta uma imagem de recipiente do registo diretamente para Web App para Contentores. Para ativar esta capacidade, deve também ativar a [conta de administração](container-registry-authentication.md#admin-account)do registo .

Navegue para o seu novo registo de contentores no portal Azure e selecione **as teclas de acesso** em **Definições**. Em **Utilizador administrador**, selecione **Ativar**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Criar um registo de contentor no portal do Azure":::


## <a name="container-registry-login"></a>Iniciar sessão no registo de contentor

Agora que configurou a georreplicação, crie uma imagem de contentor e envie-a para o seu registo. Primeiro tem de iniciar sessão no seu registo antes de lhe dar imagens.

Utilize o comando [az acr login](/cli/azure/acr#az-acr-login) para autenticar e colocar as credenciais em cache para o seu registo. Substitua `<acrName>` pelo nome do registo que criou anteriormente.

```azurecli
az acr login --name <acrName>
```

O comando devolver `Login Succeeded` quando concluído.

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo neste tutorial inclui uma pequena aplicação Web criada com o [ASP.NET Core][aspnet-core]. A aplicação serve uma página HTML que mostra a região da qual a imagem foi implementada pelo Azure Container Registry.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Criar um registo de contentor no portal do Azure":::

Utilize o git para transferir o exemplo para um diretório local e `cd` para o diretório:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Se não tiver o `git` instalado, poderá [transferir o arquivo ZIP][acr-helloworld-zip] diretamente do GitHub.

## <a name="update-dockerfile"></a>Atualizar o Dockerfile

O Dockerfile incluído nos exemplos mostra como é que o contentor é criado. Inicia a partir de uma imagem oficial [aspnetcore][dockerhub-aspnetcore], copia os ficheiros da aplicação para o contentor, instala as dependências, compila a saída com a imagem oficial [aspnetcore-build][dockerhub-aspnetcore-build] e, por fim, cria uma imagem aspnetcore otimizada.

O [Dockerfile][dockerfile] está localizado em `./AcrHelloworld/Dockerfile` na origem clonada.

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

A aplicação na imagem *acr-helloworld* tenta determinar a região da qual o seu contentor foi implementado ao fazer consultas DNS para obter informações sobre o servidor de início de sessão do registo. Tem de especificar o nome do domínio completamente qualificado (FQDN) do servidor de início de sessão do registo na variável de ambiente `DOCKER_REGISTRY` no Dockerfile.

Primeiro, obtenha o servidor de sessão do registo com o comando `az acr show`. Substitua `<acrName>` com o nome do registo que criou nos passos anteriores.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Resultado:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Em seguida, atualize a linha `ENV DOCKER_REGISTRY` com o FQDN do seu servidor de sessão do registo. Este exemplo reflete o nome do registo de exemplo, *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Criar imagem de contentor

Agora que atualizou o Dockerfile com o FQDN do servidor de início de sessão do registo, pode utilizar `docker build` para criar a imagem do contentor. Execute o seguinte comando para criar a imagem e marcá-la com o URL do seu registo privado, substituindo novamente `<acrName>` com o nome do seu registo:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Várias linhas de saída são apresentadas conforme a imagem do Docker é criada (mostrada aqui truncada):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Utilize `docker images` para ver a imagem compilada e etiquetada:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

Em seguida, utilize o comando `docker push` para enviar a imagem *acr-helloworld* para o registo. Substitua `<acrName>` pelo nome do seu registo.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Por ter configurado o seu registo para georreplicação, a sua imagem é automaticamente replicada para ambas as regiões *E.U.A. Oeste* e *E.U.A. Leste* com apenas este comando `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um registo de contentor georreplicado privado, criou uma imagem de contentor e então enviou essa imagem para o seu registo.

Avance para o próximo tutorial para implementar o contentor em várias Aplicações Web para Contentores, ao utilizar a georreplicação para servir as imagens localmente.

> [!div class="nextstepaction"]
> [Implementar aplicação web do Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
