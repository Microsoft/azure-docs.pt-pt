---
title: 'Início rápido: Criar um aplicativo Java no Azure Service Fabric'
description: Neste início rápido, vai criar uma aplicação em Java para o Azure, utilizando o exemplo de aplicação de serviços fiáveis do Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703287"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Início rápido:  Implantar um aplicativo Java no Azure Service Fabric no Linux

Este guia de início rápido mostra como implantar seu primeiro aplicativo Java no Azure Service Fabric usando o IDE do eclipse em um computador de desenvolvedor do Linux. Quando tiver terminado, terá uma aplicação de votações com um front-end da Web Java que guarda os resultados das votações num serviço de back-end com estado no cluster.

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores.

![Exemplo de votação do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

Neste início rápido, vai aprender a:

* Utilize o Eclipse como uma ferramenta para as suas aplicações de Java do Service Fabric
* Implementar a aplicação no seu cluster local
* Aumentar horizontalmente a aplicação em vários nós

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. [Instalar o SDK do Service Fabric e Interface de Linha de Comandos (CLI) do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instale o Git](https://git-scm.com/)
3. [Instalar o Eclipse](https://www.eclipse.org/downloads/)
4. [Configure o Ambiente de Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), garantindo que segue os passos opcionais para instalar o plug-in do Eclipse

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

1. Inicie o seu cluster local com o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    O arranque do cluster local demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceda ao Service Fabric Explorer em **http://localhost:19080** . Os cinco nós em bom estado indicam que o cluster local está a funcionar.

    ![O Service Fabric Explorer do Azure mostra nós íntegros](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Abra o Eclipse.
3. Selecione **arquivo** > **importar** **gradle projeto gradle existente** e siga o assistente. >  > 
4. Selecione **diretório** e escolha o `Voting` diretório na `service-fabric-java-quickstart` pasta clonada do github. Selecione **Concluir**.

    ![Importar o projeto gradle para o eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Tem agora o projeto `Voting` no Explorador do Pacote para o Eclipse.
6. Clique com o botão direito do mouse no projeto e selecione **publicar aplicativo** na lista suspensa **Service Fabric** . Escolha **PublishProfiles/local. JSON** como o perfil de destino e selecione **publicar**.

    ![JSON local de publicação Service Fabric do Azure](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Abra seu navegador da Web favorito e acesse o aplicativo `http://localhost:8080`acessando.

    ![Host local do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Agora, pode adicionar um conjunto de opções de voto e começar a recolher votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem que seja preciso uma base de dados separada.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster

Os serviços podem ser facilmente dimensionados num cluster para se prepararem para alterações à carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Existem várias formas de dimensionar os seus serviços, por exemplo, pode utilizar scripts ou comandos da CLI do Service Fabric (sfctl). Nos passos seguintes, é utilizado o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acedido num browser, navegando para a porta (19080) de gestão HTTP dos clusters; por exemplo `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `https://localhost:19080`.
2. Selecione as reticências ( **...** ) ao lado do nó **Fabric:/votação/VotingWeb** em TreeView e selecione **dimensionar serviço**.

    ![Dimensionar um serviço no Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e selecione **dimensionar serviço**.
4. Selecione o nó **Fabric:/votação/VotingWeb** na exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço dimensionado no Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Agora, pode ver que o serviço tem duas instâncias e na vista de árvore vê em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicou os recursos disponíveis para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para que o mesmo seja executado de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância do serviço é executada no cluster.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Utilize o Eclipse como uma ferramenta para as suas aplicações de Java do Service Fabric
* Implementar aplicações Java no seu cluster local
* Aumentar horizontalmente a aplicação em vários nós

Para saber mais sobre como trabalhar com aplicações Java no Service Fabric, avance para o tutorial para aplicações Java.

> [!div class="nextstepaction"]
> [Implementar uma aplicação Java](./service-fabric-tutorial-create-java-app.md)
