---
title: Criar uma aplicação de recipiente Linux no Tecido de Serviço em Azure
description: Neste início rápido, irá criar uma imagem do Docker com a sua aplicação, enviá-la para um registo de contentor e, em seguida, implementar o contentor para um cluster do Service Fabric.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f58b8feda0e88d8a5e7cddaabbc650b0f0ab3973
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788167"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Início rápido: Implementar contentores do Linux no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis.

Este quickstart mostra como colocar os recipientes Linux num cluster de tecido de serviço em Azure. Quando tiver terminado, terá uma aplicação de voto constituída por um front-end da Web Python e um back-end Redis em execução num cluster do Service Fabric. Também irá aprender a efetuar a ativação pós-falha de uma aplicação e a dimensionar uma aplicação no seu cluster.

![Página Web da aplicação de Voto][quickstartpic]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. Crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar se não tiver uma subscrição.

2. Instale o [Azure CLI](/cli/azure/install-azure-cli-apt)

3. Instalar o [Service Fabric SDK e o CLI](service-fabric-get-started-linux.md#installation-methods)

4. Instalar [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Obter o pacote de aplicação

Para implementar contentores no Service Fabric, precisa de um conjunto de ficheiros de manifesto (a definição da aplicação), que descrevem os contentores individuais e a aplicação.

Numa consola, utilize git para clonar uma cópia da definição de aplicação; em seguida, mude os diretórios para o `Voting` diretório no seu clone.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Para implementar a aplicação no Azure, precisa de um cluster do Service Fabric para executar a aplicação. Os seguintes comandos criam um aglomerado de cinco nós em Azure.  Os comandos também criam um certificado auto-assinado, adiciona-o a um cofre chave e descarrega o certificado localmente. O novo certificado é utilizado para garantir o cluster quando este é implantado e é utilizado para autenticar clientes.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

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
> O serviço de front-end da Web está configurado para escutar tráfego de entrada na porta 80. Por predefinição, a porta 80 está aberta nos seus VMs de cluster e no balançador de carga Azure.
>

## <a name="configure-your-environment"></a>Configurar o ambiente

O Service Fabric fornece várias ferramentas que pode utilizar para gerir um cluster e as respetivas aplicações:

- O Service Fabric Explorer, uma ferramenta baseada no browser.
- A CLI (Interface de Linha de Comandos) do Service Fabric, que é executada na CLI do Azure. 
- Comandos do PowerShell.

Neste arranque rápido, utilize o Service Fabric CLI e o Service Fabric Explorer (uma ferramenta baseada na web). Para utilizar o Service Fabric Explorer, é necessário importar o certificado ficheiro PFX para o navegador. Por predefinição, o ficheiro PFX não tem senha.

Mozilla Firefox é o navegador padrão em Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu browser e, em seguida, clique em **Opções**. Na página **Preferências**, utilize a caixa de pesquisa para procurar "certificados". Clique em **Ver Certificados**, selecione o separador **Os Seus Certificados**, clique em **Importar** e siga as instruções para importar o certificado.

   ![Instalar o certificado no Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Implementar a aplicação do Service Fabric

1. Ligue-se ao cluster de tecido de serviço em Azure utilizando o CLI. O ponto final é o ponto final de gestão do seu cluster. Criou o ficheiro PEM na secção anterior. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Utilize o script de instalação para copiar a definição da aplicação de Voto para o cluster, registar o tipo de aplicação e criar uma instância da mesma.  O ficheiro de certificado PEM deve estar localizado no mesmo diretório que o *ficheiro install.sh.*

    ```bash
    ./install.sh
    ```

3. Abra um browser e navegue para o ponto final do Service Fabric Explorer para o seu cluster. O ponto final tem o seguinte formato:  **https:// \<my-azure-service-fabric-cluster-url> :19080/Explorer;** por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer` . </br>

4. Expanda o nó **de Aplicações** para ver se existe agora uma inscrição para o tipo de candidatura de Voto e o caso que criou.

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

## <a name="clean-up-resources"></a>Limpar os recursos

Utilize o script de desinstalação (uninstall.sh) fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação. Este script demora algum tempo a limpar a instância, pelo que não deve executar o script de instalação imediatamente após este script. Pode utilizar o Service Fabric Explorer para determinar quando a instância foi removida e o tipo de aplicação cujo registo foi anulado.

```bash
./uninstall.sh
```

A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Faça o súmis no Azure e selecione o ID de subscrição com o qual pretende remover o cluster. Pode encontrar o ID da subscrição ao iniciar sessão no portal do Azure. Elimine o grupo de recursos e todos os recursos de cluster utilizando o [comando de eliminação](/cli/azure/group)do grupo az .

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Se tiver terminado de trabalhar com o cluster, pode remover o certificado do arquivo de certificados. Por exemplo:
- No Windows: utilize o [Snap-in da MMC de certificados](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Certifique-se de que seleciona **A minha conta de utilizador** ao adicionar o snap-in. Navegue para `Certificates - Current User\Personal\Certificates` e remova o certificado.
- No Mac: utilize a aplicação Keychain.
- No Ubuntu: siga os passos utilizados para ver os certificados e remova o certificado.

## <a name="next-steps"></a>Próximos passos

Neste início rápido, implementou uma aplicação de contentor do Linux num cluster do Service Fabric do Azure, efetuou a ativação pós-falha na aplicação e dimensionou a aplicação no cluster. Para saber mais sobre como trabalhar com contentores do Linux no Service Fabric, avance para o tutorial para aplicações de contentor do Linux.

> [!div class="nextstepaction"]
> [Criar uma aplicação de contentor do Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
