---
title: Implementar solução de monitorização remota local - VS IDE - Azure [ Microsoft Docs
description: Este guia de como fazer mostra como implementar o acelerador de solução de monitorização remota para a sua máquina local utilizando o Visual Studio para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890895"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Implementar o acelerador de soluções de monitorização remota localmente - Estúdio Visual

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota na sua máquina local para testes e desenvolvimento. Aprende-se a gerir os microserviços no Estúdio Visual. Uma implantação local de microserviços utiliza os seguintes serviços na nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Serviços Azure Time Series Insights na nuvem.

Se quiser executar o acelerador de solução de monitorização remota em Docker na sua máquina local, consulte [a instalação local do acelerador](iot-accelerators-remote-monitoring-deploy-local-docker.md)de soluções de monitorização remota - Docker .

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de soluções de monitorização remota, necessita de uma subscrição azure ativa.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implementação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Estivador](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o CLI PCS que os scripts usam para criar recursos Azure. Não use node.js v10.

> [!NOTE]
> O Visual Studio está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, executa os microserviços de Monitorização Remota. Você executa a Web UI de forma nativa, o serviço de simulação de dispositivos em Docker, e os microserviços no Estúdio Visual.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação do dispositivo

Abra uma nova janela de solicitação de comando para se certificar de que tem acesso às variáveis ambientais definidas pelo script **start.cmd** na secção anterior.

Execute o seguinte comando para lançar o recipiente Docker para o serviço de simulação do dispositivo. O serviço simula dispositivos para a solução de monitorização remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microserviços na máquina local

Os seguintes passos mostram-lhe como executar os microserviços de Monitorização Remota em Estúdio Visual:

1. Inicie o Visual Studio.
1. Abra a solução de **monitorização remota.sLN** na pasta de **serviços** na sua cópia local do repositório.
1. No **Solution Explorer,** clique à direita na solução e no clique **em Propriedades**.
1. Selecione **Propriedades Comuns > Projeto**de Arranque .
1. Selecione **vários projetos** de startups e detete **to** **Start** para os seguintes projetos:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (dispositivo-telemetria\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (adaptador de armazenamento\WebService)
1. Clique **em OK** para salvar as suas escolhas.
1. Clique em **Debug > Começar Debugging** para construir e executar os serviços web na máquina local.

Cada serviço web abre uma janela de comando e navegador web. No pedido de comando, você vê a saída do serviço de funcionamento, e a janela do navegador permite-lhe monitorizar o estado. Não feche os pedidos de comando ou páginas web, esta ação para o serviço web.

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho de Stream Analytics

Siga estes passos para iniciar o trabalho de Stream Analytics:

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue para o **grupo Derecursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Clique no **trabalho do Stream Analytics** na lista de recursos.
1. Na página de **visão geral** do trabalho do Stream Analytics, clique no botão **Iniciar.** Em seguida, clique **em Começar** a trabalhar agora.

### <a name="run-the-web-ui"></a>Executar a Web UI

Neste passo, inicia-se a UI web. Abra uma nova janela de solicitação de comando para se certificar de que tem acesso às variáveis ambientais definidas pelo script **start.cmd.** Navegue para a pasta **webui** na sua cópia local do repositório e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início estiver concluído, o seu navegador exibe a página **\/http: /localhost:3000/dashboard**. Os erros nesta página são esperados. Para visualizar a aplicação sem erros, complete o seguinte passo.

### <a name="configure-and-run-nginx"></a>Configure e executar NGINX

Configurar um servidor proxy invertido para ligar a aplicação web e os microserviços em execução na sua máquina local:

* Copie o ficheiro **nginx.conf** da pasta **webui\scripts\localhost** na sua cópia local do repositório para o diretório de instalação **nginx\conf.**
* Executar **nginx**.

Para mais informações sobre a execução de **nginx,** consulte [o nginx para windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao dashboard de solução\/de monitorização remota, navegue para http: /localhost:9000 no seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes, remova os serviços de nuvem da sua subscrição Azure. Para remover os serviços, navegue para o [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de Monitorização Remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de soluções](quickstart-remote-monitoring-deploy.md).
