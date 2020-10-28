---
title: Criar imagens de contentores no Tecido de Serviço em Azure
description: Neste tutorial, saiba como criar imagens de contentor para uma aplicação do Service Fabric com vários contentores.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 31b5f870465bc1dff9d6ff7827a4efed084bcf62
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739066"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Tutorial: Criar imagens de contentor num cluster do Service Fabric do Linux

Este tutorial faz parte de uma série de tutoriais que demonstra como utilizar contentores num cluster do Service Fabric do Linux. Neste tutorial, uma aplicação de contentores múltiplos é preparada para utilização com o Service Fabric. Em tutoriais posteriores, estas imagens serão utilizadas como parte de uma aplicação do Service Fabric. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Clonar a origem de aplicação a partir do GitHub
> * Criar uma imagem de contentor a partir da origem de aplicação
> * Implementar uma instância do Azure Container Registry (ACR)
> * Marcar uma imagem de contentor para o ACR
> * Carregar os ficheiros de imagem para o ACR

Nesta série de tutoriais, ficará a saber como:

> [!div class="checklist"]
> * Criar imagens de contentor para o Service Fabric
> * [Criar e implementar uma aplicação do Service Fabric com Contentores](service-fabric-tutorial-package-containers.md)
> * [Como a ativação pós-falha e o dimensionamento são processados no Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Pré-requisitos

* Configuração de ambiente de desenvolvimento do Linux para o Service Fabric. Siga as instruções [aqui](service-fabric-get-started-linux.md) para configurar o seu ambiente do Linux.
* Este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Além disso, requer que tenha uma subscrição do Azure disponível. Para mais informações sobre uma versão de avaliação gratuita, clique [aqui](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Obter o código da aplicação

A aplicação de exemplo utilizada neste tutorial é uma aplicação de votos. A aplicação consiste num componente Web front-end e uma instância do Redis em back-end. Os componentes estão agrupados em imagens de contentores.

Utilize o git para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

A solução contém duas pastas e um ficheiro "docker-compose.yml". A pasta "azure-vote" contém o serviço front-end Python, juntamente com o Dockerfile utilizado para criar a imagem. O diretório "Voting" contém o pacote de aplicação do Service Fabric implementado para o cluster. Estes diretórios contêm os recursos necessários para este tutorial.

## <a name="create-container-images"></a>Criar imagens de contentor

No diretório **azure-vote** , execute o seguinte comando para criar a imagem para o componente Web do front-end. Este comando utiliza o Dockerfile neste diretório para criar a imagem.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Se a permissão lhe é negada, então siga [esta](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) documentação sobre como utilizar o docker sem sudo.

Este comando pode demorar algum tempo, uma vez que todas as dependências necessárias têm de ser obtidas do Docker Hub. Ao concluir, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas.

```bash
docker images
```

Repare que foram transferidas ou criadas duas imagens. A imagem *azure-vote-front* contém a aplicação. Foi derivada de uma imagem *python* do Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Implementar o Azure Container Registry

Primeiro, faça o comando **de login az** para iniciar sessão na sua conta Azure.

```azurecli
az login
```

Em seguida, utilize o comando **az account** para mudar a sua subscrição de forma a criar o registo de contentor do Azure. Tem de introduzir o ID de subscrição da sua subscrição do Azure no lugar de <subscription_id>.

```azurecli
az account set --subscription <subscription_id>
```

Ao implementar um Azure Container Registry, tem de começar por obter um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando **az group create** . Neste exemplo, é criado um grupo de recursos chamado *myResourceGroup* na região *westus* .

```azurecli
az group create --name <myResourceGroup> --location westus
```

Crie um registo do Contentor Azure com o comando **az acr create.** \<acrName>Substitua-o pelo nome do registo do contentor que pretende criar sob a sua assinatura. O nome tem de ser alfanumérico e único.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Em todo o resto deste tutorial, utilizamos "acrName" como um marcador de posição para o nome do registo de contentor que escolheu. Anote este valor.

## <a name="sign-in-to-your-container-registry"></a>Inscreva-se no registo do seu contentor

Inscreva-se na sua instância ACR antes de lhe empurrar imagens. Utilize o comando **az acr login** para concluir a operação. Forneça o nome exclusivo dado ao registo de contentor quando este foi criado.

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem "Início de sessão com êxito" depois de concluir.

## <a name="tag-container-images"></a>Marcar imagens de contentor

Cada imagem de contentor tem de ser marcada com o nome loginServer do registo. Esta etiqueta é utilizada para encaminhamento ao enviar imagens de contentor para um registo de imagem.

Para ver uma lista das imagens atuais, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Resultado:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Para obter o nome loginServer, execute o seguinte comando:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Isto dá origem a uma tabela com os seguintes resultados. Este resultado será utilizado para identificar a sua imagem **azure-vote-front** antes de enviá-la para o registo de contentor no próximo passo.

```output
Result
------------------
<acrName>.azurecr.io
```

Agora, marque a imagem *azure-vote-front* com o loginServer do seu registo de contentor. Além disso, adicione `:v1` ao final do nome da imagem. Esta etiqueta identifica a versão da imagem.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Depois de marcada, execute "docker images" para verificar a operação.

Resultado:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Enviar imagens para o registo

Envie a imagem *azure-vote-front* para o registo. 

Utilizando o seguinte exemplo, substitua o nome loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Os comandos de envio do docker demoram alguns minutos a concluir.

## <a name="list-images-in-registry"></a>Listar imagens no registo

Para devolver uma lista de imagens que foram enviadas para o seu registo de contentor do Azure, utilize o comando [az acr repository list](/cli/azure/acr/repository). Atualize o comando com o nome de instância do ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Resultado:

```output
Result
----------------
azure-vote-front
```

Ao concluir o tutorial, a imagem de contentor foi armazenada numa instância privada do Azure Container Registry. Esta imagem é implementada do ACR para um cluster do Service Fabric em tutoriais posteriores.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, uma aplicação foi retirada do GitHub e foram criadas imagens de contentores e empurradas para um registo. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Clonar a origem de aplicação a partir do GitHub
> * Criar uma imagem de contentor a partir da origem de aplicação
> * Implementar uma instância do Azure Container Registry (ACR)
> * Marcar uma imagem de contentor para o ACR
> * Carregar os ficheiros de imagem para o ACR

Avance para o próximo tutorial para saber mais sobre agrupar contentores numa aplicação do Service Fabric através do Yeoman.

> [!div class="nextstepaction"]
> [Agrupar e implementar contentores como uma aplicação do Service Fabric](service-fabric-tutorial-package-containers.md)
