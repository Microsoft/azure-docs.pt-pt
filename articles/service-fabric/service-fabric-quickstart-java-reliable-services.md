---
title: 'Quickstart: Criar uma aplicação Java no Azure Service Fabric'
description: Neste início rápido, vai criar uma aplicação em Java para o Azure, utilizando o exemplo de aplicação de serviços fiáveis do Service Fabric.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 199fe459ec8fd58d05b4946dda4291c46a4881c3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530904"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Quickstart: Implementar uma aplicação Java para o Azure Service Fabric no Linux

Neste arranque rápido, você implementa uma aplicação Java para O Tecido de Serviço Azure usando o Eclipse IDE em uma máquina de desenvolvedor linux. Quando tiver terminado, terá uma aplicação de votações com um front-end da Web Java que guarda os resultados das votações num serviço de back-end com estado no cluster.

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores.

## <a name="prerequisites"></a>Pré-requisitos

- [Ambiente java](./service-fabric-get-started-linux.md#set-up-java-development) e [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) e [Eclipse plug-in para tecido de serviço](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Interface de linha de tecido de serviço SDK e comando (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

1. Inicie o seu cluster local com o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    O arranque do cluster local demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceda ao Service Fabric Explorer em `http://localhost:19080`. Os cinco nós em bom estado indicam que o cluster local está a funcionar.

    ![Azure Service Fabric Explorer mostra nómadas saudáveis](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Abra o Eclipse.
3. Selecione **File**  >  **Import**  >  **Gradle**  >  **Existente Gradle Project** e siga o assistente.
4. Selecione o **Diretório** e escolha o **diretório** de voto da pasta de **arranque rápido de tecido de serviço-java** que clonou do GitHub. Selecione **Concluir**.

    ![Projeto Import Gradle em Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Tem agora o projeto `Voting` no Explorador do Pacote para o Eclipse.
6. Clique com o botão direito no projeto e **selecione Publicar Aplicação** no âmbito do dropdown do Tecido de **Serviço.** Escolha **PublishProfiles/Local.jscomo** perfil-alvo e **selecione Publicar**.

    ![Azure Service Fabric publica JSON local](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Abra o browser favorito e aceda à aplicação em `http://localhost:8080`.

    ![Anfitrião local do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Agora, pode adicionar um conjunto de opções de voto e começar a recolher votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem que seja preciso uma base de dados separada.

![Amostra de votação do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster

Os serviços podem ser facilmente dimensionados num cluster para se prepararem para alterações à carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Há muitas formas de escalonar os seus serviços. Por exemplo, pode utilizar scripts ou comandos a partir do Service Fabric CLI `sfctl` (). Nos passos seguintes, é utilizado o Service Fabric Explorer.

O Service Fabric Explorer funciona em todos os clusters de Tecidos de Serviço e pode ser acedido a partir de um browser navegando para a porta de gestão HTTP do cluster (19080). Por exemplo, `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Open Service Fabric Explorer no seu cluster. Por exemplo, `https://localhost:19080`.
2. Selecione a elipse **(...**) ao lado do **nó de tecido:/Nó de Votação/VotaçãoWeb** na visão de árvore e selecione **o Serviço de Escala**.

    ![Dimensione um serviço em Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e selecione **o Serviço de Escala**.
4. Selecione o **nó de tecido:/Votação/Votação O nóweb** na vista para a árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço escalado em Tecido de Serviço Azure](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Agora, pode ver que o serviço tem duas instâncias e na vista de árvore vê em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicou os recursos disponíveis para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para que o mesmo seja executado de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância do serviço é executada no cluster.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Utilize o Eclipse como uma ferramenta para as suas aplicações de Java do Service Fabric
* Implementar aplicações Java no seu cluster local
* Escalar horizontalmente a aplicação em vários nós

Para saber mais sobre como trabalhar com aplicações Java no Service Fabric, avance para o tutorial para aplicações Java.

> [!div class="nextstepaction"]
> [Implementar uma aplicação Java](./service-fabric-tutorial-create-java-app.md)
