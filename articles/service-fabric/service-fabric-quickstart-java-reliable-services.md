---
title: Criar uma aplicação Java no Service Fabric no Azure | Microsoft Docs
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
ms.custom: mvc, devcenter
ms.openlocfilehash: ddd56b8479678b288424dd896baadea6a41a2aef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008135"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>Início rápido: Implementar uma aplicação de reliable services do Java no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores.

Este guia de introdução mostra como implementar a sua primeira aplicação de Java no Service Fabric com o ODE do Eclipse numa máquina de programador do Linux. Quando tiver terminado, terá uma aplicação de votações com um front-end da Web Java que guarda os resultados das votações num serviço de back-end com estado no cluster.

![Captura de Ecrã da Aplicação](./media/service-fabric-quickstart-java/votingapp.png)

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
    O arranque do cluster local demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceda ao Service Fabric Explorer em **http://localhost:19080**. Os cinco nós em bom estado indicam que o cluster local está a funcionar.

    ![Cluster local em bom estado de funcionamento](./media/service-fabric-quickstart-java/localclusterup.png)

2. Abra o Eclipse.
3. Clique em Ficheiro -> Importar -> Gradle -> Projeto do Gradle existente e siga o assistente.
4. Clique em diretório e escolha o `Voting` diretório da `service-fabric-java-quickstart` pasta clonou do GitHub. Clique em Concluir.

    ![Caixa de Diálogo Importar Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Tem agora o projeto `Voting` no Explorador do Pacote para o Eclipse.
6. Clique com o botão direito do rato no projeto e selecione **Publicar Aplicação...** sob o menu pendente **Service Fabric**. Escolha **PublishProfiles/Local.json** como o Perfil de Destino e clique em Publicar.

    ![Caixa de Diálogo Publicar](./media/service-fabric-quickstart-java/localjson.png)

7. Abra o browser favorito e aceda a aplicação `http://localhost:8080`.

    ![Front-end da aplicação Local](./media/service-fabric-quickstart-java/runninglocally.png)

Agora, pode adicionar um conjunto de opções de voto e começar a recolher votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem que seja preciso uma base de dados separada.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster

Os serviços podem ser facilmente dimensionados num cluster para se prepararem para alterações à carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Existem várias formas de dimensionar os seus serviços, por exemplo, pode utilizar scripts ou comandos da CLI do Service Fabric (sfctl). Nos passos seguintes, é utilizado o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acedido num browser, navegando para a porta (19080) de gestão HTTP dos clusters; por exemplo `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `https://localhost:19080`.
2. Clique nas reticências (três pontos) junto ao nó **fabric:/Voting/VotingWeb**, na vista de árvore, e escolha**Dimensionar Serviço**.

    ![Dimensionar Serviço no Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/VotingWeb**, na vista de árvore, e expanda o nó de partição (representado por uma GUID).

    ![Dimensionar Serviço no Service Fabric Explorer Completo](./media/service-fabric-quickstart-java/servicescaled.png)

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
