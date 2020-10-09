---
title: Implementar soluções de monitorização remota localmente - VS IDE - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como implantar o acelerador de solução de monitorização remota para a sua máquina local utilizando o Visual Studio para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890895"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Implementar o acelerador de solução de monitorização remota localmente - Estúdio Visual

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implantar o acelerador de solução de Monitorização Remota na sua máquina local para testes e desenvolvimento. Aprende-se a gerir os microserviços no Visual Studio. Uma implementação local de microserviços utiliza os seguintes serviços em nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights na nuvem.

Se pretender executar o acelerador de solução de monitorização remota no Docker na sua máquina local, consulte [implementar localmente o acelerador de solução de monitorização remota - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implantação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Rio Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o PCS CLI que os scripts usam para criar recursos Azure. Não use Node.js v10.

> [!NOTE]
> O Visual Studio está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, execute os microserviços de monitorização remota. Você dirige a web UI de forma nativa, o serviço de Simulação de Dispositivos em Docker, e os microserviços em Visual Studio.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação do dispositivo

Abra uma nova janela de pedido de comando para ter a certeza de que tem acesso às variáveis ambientais definidas pelo script **start.cmd** na secção anterior.

Executar o seguinte comando para lançar o recipiente Docker para o serviço de simulação do dispositivo. O serviço simula dispositivos para a solução de monitorização remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microserviços na máquina local

Os seguintes passos mostram-lhe como executar os microserviços de monitorização remota no Estúdio Visual:

1. Lançamento Visual Studio.
1. Abra a solução **de monitorização remota.sln** na pasta **de serviços** na sua cópia local do repositório.
1. No **Solution Explorer,** clique com o botão direito na solução e no click **Properties**.
1. Selecione **Propriedades Comuns > Projeto de Arranque**.
1. Selecione **vários projetos de startups** e deslote **a Ação** para **iniciar** os seguintes projetos:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (dispositivo-telemetria\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (adaptador de armazenamento\WebService)
1. Clique **em OK** para guardar as suas escolhas.
1. Clique **em Debug > Comece a depurar** para construir e executar os serviços web na máquina local.

Cada serviço web abre uma janela de pedido de comando e web browser. Na solicitação de comando, vê a saída do serviço de execução e a janela do navegador permite monitorizar o estado. Não feche as instruções de comando ou páginas web, esta ação para o serviço web.

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho stream analytics

Siga estes passos para iniciar o trabalho stream Analytics:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Navegue para o **grupo de Recursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Clique no **trabalho stream Analytics** na lista de recursos.
1. Na página **de visão geral** do trabalho stream Analytics, clique no botão **Iniciar.** Em seguida, clique **em Começar** a trabalhar agora.

### <a name="run-the-web-ui"></a>Executar a uI web

Neste passo, começa-se a uI web. Abra uma nova janela de pedido de comando para ter a certeza de que tem acesso às variáveis ambientais definidas pelo script **start.cmd.** Navegue para a pasta **webui** na sua cópia local do repositório e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início estiver concluído, o seu navegador exibe a página **http: \/ /localhost:3000/dashboard**. Os erros nesta página são esperados. Para ver a aplicação sem erros, complete o passo seguinte.

### <a name="configure-and-run-nginx"></a>Configure e executar NGINX

Crie um servidor de procuração inversa para ligar a aplicação web e microserviços em execução na sua máquina local:

* Copie o ficheiro **nginx.conf** a partir da pasta **webui\scripts\local na** sua cópia local do repositório para o diretório de instalação **nginx\conf.**
* Executar **nginx**.

Para obter mais informações sobre a execução **do nginx,** consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao painel de solução de monitorização remota, navegue para http: \/ /localhost:9000 no seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes remova os serviços de cloud da sua subscrição Azure. Para remover os serviços, navegue no [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de instrumentos de solução](quickstart-remote-monitoring-deploy.md).
