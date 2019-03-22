---
title: Implementar a solução de monitorização remota localmente (por meio do IDE do Visual Studio) - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local com o Visual Studio para desenvolvimento e teste.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309984"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Implementar o monitorização remota acelerador de soluções localmente - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local para teste e desenvolvimento. Saiba como executar os microsserviços no Visual Studio. Uma implementação de microsserviços local utiliza os seguintes serviços de nuvem: IoT Hub, Cosmos DB, a análise de transmissão em fluxo do Azure e Azure Time Series Insights serviços na cloud.

Se quiser executar o acelerador de solução de monitorização remota no Docker no seu computador local, veja [implementar o monitorização remota acelerador de soluções localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços do Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração de máquina

Para concluir a implementação de local, terá das seguintes ferramentas instaladas no seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [NODE. js v8](https://nodejs.org/) -este software é um pré-requisito para a CLI de PCS que os scripts usam para criar recursos do Azure. Não utilize a v10 de node. js.

> [!NOTE]
> Visual Studio está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Execute os microsserviços

Nesta secção, vai executar os microsserviços de monitorização remota. Executar a IU da web nativamente, o serviço de simulação do dispositivo no Docker e os microsserviços no Visual Studio.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação do dispositivo

Abra uma nova janela de linha de comandos para ter certeza de que tem acesso para as variáveis de ambiente definidas pelos **start.cmd** script na secção anterior.

Execute o seguinte comando para iniciar o contentor do Docker para o serviço de simulação do dispositivo. O serviço simula os dispositivos para a solução de monitorização remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microsserviços no computador local

Os passos seguintes mostram como executar os microsserviços de monitorização remota no Visual Studio 2017:

1. Inicie o Visual Studio 2017
1. Abra o **remoto monitoring.sln** solução no **serviços** pasta na sua cópia local do repositório.
1. Na **Explorador de soluções**, clique com botão direito a solução e um simples clique **propriedades**.
1. Selecione **propriedades comuns > projeto de arranque**.
1. Selecione **vários projetos de arranque** e defina **ação** para **iniciar** para os seguintes projetos:
    * WebService (asa-manager\WebService)
    * Serviço Web (auth\WebService)
    * WebService (config\WebService)
    * Serviço Web (telemetry\WebService de dispositivo)
    * WebService (iothub-manager\WebService)
    * Serviço Web (adapter\WebService de armazenamento)
1. Clique em **OK** para guardar as suas opções.
1. Clique em **depurar > Iniciar depuração** para compilar e executar os serviços web no computador local.

Cada serviço web é aberta uma janela do browser web e de linha de comandos. No prompt de comando, verá os resultados do serviço em execução e a janela do browser permite-lhe monitorizar o estado. Não feche o prompts de comando ou páginas da web, esta ação para o serviço de web.

### <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de Stream Analytics

Siga estes passos para iniciar a tarefa de Stream Analytics:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue para o **grupo de recursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o **start.cmd** script * *.
1. Clique nas **tarefa do Stream Analytics** na lista de recursos.
1. Na tarefa de Stream Analytics **descrição geral** página, clique nas **iniciar** botão. Em seguida, clique em **iniciar** para iniciar a tarefa agora.

### <a name="run-the-web-ui"></a>Executar a IU da web

Neste passo, começa a IU da web. Abra uma nova janela de linha de comandos para ter certeza de que tem acesso para as variáveis de ambiente definidas pelos **start.cmd** script. Navegue para o **serem** copiar do repositório de pasta no seu local e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o browser apresenta a página **http:\//localhost:3000 / dashboard**. Espera-se os erros nesta página. Para ver a aplicação sem erros, conclua o passo seguinte.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor de proxy inverso para ligar a aplicação web e os microsserviços em execução no seu computador local:

* Cópia a **nginx.conf** ficheiro a partir do **webui\scripts\localhost** pasta na sua cópia local do repositório para o **nginx\conf** diretório de instalação.
* Execute **nginx**.

Para obter mais informações sobre como executar **nginx**, consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligar ao dashboard

Para aceder ao dashboard de solução de monitorização remota, navegue para o http:\//localhost:9000 no seu browser.

## <a name="clean-up"></a>Limpeza

Para evitar desnecessários custos, quando tiver concluído os testes remover os serviços em nuvem da sua subscrição do Azure. Para remover os serviços, navegue para o [portal do Azure](https://ms.portal.azure.com) e eliminar o recurso de grupo que o **start.cmd** script criado.

Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou a solução de monitorização remota, a próxima etapa é [explore os recursos do dashboard da solução](quickstart-remote-monitoring-deploy.md).
