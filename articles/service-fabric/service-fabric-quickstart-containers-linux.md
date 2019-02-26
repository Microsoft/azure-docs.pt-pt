---
title: Criar uma aplicação de contentor do Linux no Service Fabric no Azure | Microsoft Docs
description: Neste início rápido, irá criar uma imagem do Docker com a sua aplicação, enviá-la para um registo de contentor e, em seguida, implementar o contentor para um cluster do Service Fabric.
services: service-fabric
documentationcenter: linux
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/30/2019
ms.author: aljo,suhuruli
ms.custom: mvc
ms.openlocfilehash: 559b51b70c2bcb18d74d77fddaf928e0356435df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804558"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Início rápido: Implementar contentores do Linux no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis.

Este início rápido mostra como implementar contentores do Linux num cluster do Service Fabric no Azure. Quando tiver terminado, terá uma aplicação de voto constituída por um front-end da Web Python e um back-end Redis em execução num cluster do Service Fabric. Também irá aprender a efetuar a ativação pós-falha de uma aplicação e a dimensionar uma aplicação no seu cluster.

![Página Web da aplicação de Voto][quickstartpic]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de iniciar se não tiver uma subscrição.

2. Instalar a [CLI do Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. Instalar o [SDK do Service Fabric e CLI](service-fabric-get-started-linux.md#installation-methods)

4. Instalar [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Obter o pacote de aplicação

Para implementar contentores no Service Fabric, precisa de um conjunto de ficheiros de manifesto (a definição da aplicação), que descrevem os contentores individuais e a aplicação.

Num console, utilize o git para clonar uma cópia da definição da aplicação; em seguida, altere os diretórios para o `Voting` diretório no seu clone.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Para implementar a aplicação no Azure, precisa de um cluster do Service Fabric para executar a aplicação. Os seguintes comandos criam um cluster de cinco nós no Azure.  Os comandos também criar um certificado autoassinado, adiciona-o para um cofre de chaves e transfere o certificado localmente. O novo certificado é utilizado para proteger o cluster quando implementa e é utilizado para autenticar clientes.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> O serviço de front-end da Web está configurado para escutar tráfego de entrada na porta 80. Por predefinição, a porta 80 está aberta no seu cluster de VMs e o Balanceador de carga do Azure.
>

## <a name="configure-your-environment"></a>Configurar o ambiente

O Service Fabric fornece várias ferramentas que pode utilizar para gerir um cluster e as respetivas aplicações:

- O Service Fabric Explorer, uma ferramenta baseada no browser.
- A CLI (Interface de Linha de Comandos) do Service Fabric, que é executada na CLI do Azure. 
- Comandos do PowerShell.

Neste início rápido, vai utilizar a CLI do Service Fabric e o Service Fabric Explorer (uma ferramenta baseada na web). Para utilizar o Service Fabric Explorer, terá de importar o ficheiro PFX de certificado para o navegador. Por predefinição, o ficheiro PFX não tem nenhuma palavra-passe.

Mozilla Firefox é o browser predefinido do Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu browser e, em seguida, clique em **Opções**. Na página **Preferências**, utilize a caixa de pesquisa para procurar "certificados". Clique em **Ver Certificados**, selecione o separador **Os Seus Certificados**, clique em **Importar** e siga as instruções para importar o certificado.

   ![Instalar o certificado no Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Implementar a aplicação do Service Fabric

1. Ligar ao cluster do Service Fabric no Azure com a CLI. O ponto final é o ponto final de gestão do seu cluster. Criou o ficheiro PEM na secção anterior. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Utilize o script de instalação para copiar a definição da aplicação de Voto para o cluster, registar o tipo de aplicação e criar uma instância da mesma.  O ficheiro de certificado PEM deve estar localizado no mesmo diretório como o *install.sh* ficheiro.

    ```bash
    ./install.sh
    ```

3. Abra um browser e navegue para o ponto final do Service Fabric Explorer para o seu cluster. O ponto final tem o seguinte formato:  **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**; por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Expanda o nó **Aplicações** para ver que existe agora uma entrada para o tipo de aplicação de Voto e a instância que criou.

    ![Service Fabric Explorer][sfx]

5. Para ligar ao contentor em execução, abra um browser e navegue para o URL do seu cluster; por exemplo, `http://containertestcluster.eastus.cloudapp.azure.com:80`. Deverá ver a aplicação de Voto no browser.

    ![Página Web da aplicação de Voto][quickstartpic]

> [!NOTE]
> Também pode implementar aplicações do Service Fabric com o Docker Compose. Por exemplo, o comando seguinte pode ser utilizado para implementar e instalar a aplicação no cluster com o Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Efetuar a ativação pós-falha de um contentor num cluster

O Service Fabric garante que as instâncias de contentor são movidas automaticamente para outros nós do cluster, se ocorrer uma falha. Também pode drenar manualmente um nó para contentores e, em seguida, mover harmoniosamente para outros nós do cluster. O Service Fabric fornece várias formas de dimensionar os seus serviços. Nos passos seguintes, vai utilizar o Service Fabric Explorer.

Para efetuar a ativação pós-falha do contentor de front-end, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster; por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Clique no nó **fabric:/Voting/azurevotefront** na vista de árvore e expanda o nó de partição (representado por um GUID). Repare no nome do nó na vista de árvore, que mostra os nós nos quais o contentor está atualmente a ser executado; por exemplo, `_nodetype_1`.
3. Expanda o nó **Nós** na vista de árvore. Clique nas reticências (...) junto ao nó que está a executar o contentor.
4. Escolha **Reiniciar** para reiniciar esse nó e confirme a ação de reinício. O reinício provoca a ativação pós-falha do contentor para outro nó no cluster.

    ![Vista de nó no Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster

Os serviços do Service Fabric podem ser facilmente dimensionados num cluster para se prepararem para a carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster.

Para dimensionar o serviço de front-end da Web, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster; por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) junto ao nó **fabric:/Voting/azurevotefront** na vista de árvore e escolha **Dimensionar Serviço**.

    ![Início do serviço de dimensionamento do Service Fabric Explorer][containersquickstartscale]

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/azurevotefront** na vista de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de dimensionamento do Service Fabric Explorer concluído][containersquickstartscaledone]

    Agora, pode ver que o serviço tem duas instâncias. Na vista de árvore, pode ver em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicou os recursos disponíveis para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para que o mesmo seja executado de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância do serviço é executada no cluster.

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o script de desinstalação (uninstall.sh) fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação. Este script demora algum tempo a limpar a instância, pelo que não deve executar o script de instalação imediatamente após este script. Pode utilizar o Service Fabric Explorer para determinar quando a instância foi removida e o tipo de aplicação cujo registo foi anulado.

```bash
./uninstall.sh
```

A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Inicie sessão no Azure e selecione o ID da subscrição com a qual pretende remover o cluster. Pode encontrar o seu ID de subscrição ao iniciar sessão no portal do Azure. Eliminar o grupo de recursos e todos os recursos de cluster utilizando o [comando de eliminação do grupo az](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Se tiver terminado de trabalhar com o cluster, pode remover o certificado do arquivo de certificados. Por exemplo:
- No Windows: Utilize o [snap-in MMC de certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Certifique-se de que seleciona **A minha conta de utilizador** ao adicionar o snap-in. Navegue para `Certificates - Current User\Personal\Certificates` e remova o certificado.
- No Mac: Utilize a aplicação Keychain.
- On Ubuntu: Siga os passos que utilizou para ver os certificados e remova o certificado.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma aplicação de contentor do Linux num cluster do Service Fabric do Azure, efetuou a ativação pós-falha na aplicação e dimensionou a aplicação no cluster. Para saber mais sobre como trabalhar com contentores do Linux no Service Fabric, avance para o tutorial para aplicações de contentor do Linux.

> [!div class="nextstepaction"]
> [Criar uma aplicação de contentor do Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
