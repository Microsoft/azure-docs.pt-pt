---
title: Implantar solução de monitoramento remoto localmente-VS IDE-Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local usando o Visual Studio para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890895"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Implantar o acelerador de solução de monitoramento remoto localmente-Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local para teste e desenvolvimento. Você aprende a executar os microserviços no Visual Studio. Uma implantação de microserviços locais usa os seguintes serviços de nuvem: Hub IoT, Cosmos DB, análise de streaming do Azure e serviços de Azure Time Series Insights na nuvem.

Se você quiser executar o acelerador de solução de monitoramento remoto no Docker em seu computador local, consulte [implantar o acelerador de solução de monitoramento remoto localmente-Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de monitoramento remoto, você precisa de uma assinatura ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisará das seguintes ferramentas instaladas em seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/) – este software é um pré-requisito para a CLI de PCs que os scripts usam para criar recursos do Azure. Não use V10 node. js.

> [!NOTE]
> O Visual Studio está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta seção, você executa os microserviços de monitoramento remoto. Você executa a interface do usuário da Web nativamente, o serviço de simulação de dispositivo no Docker e os microserviços no Visual Studio.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação de dispositivo

Abra uma nova janela de prompt de comando para ter certeza de que você tem acesso às variáveis de ambiente definidas pelo script **Start. cmd** na seção anterior.

Execute o comando a seguir para iniciar o contêiner do Docker para o serviço de simulação de dispositivo. O serviço simula dispositivos para a solução de monitoramento remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implantar todos os outros microserviços no computador local

As etapas a seguir mostram como executar os microserviços de monitoramento remoto no Visual Studio:

1. Inicie o Visual Studio.
1. Abra a solução **Remote-Monitoring. sln** na pasta **Serviços** em sua cópia local do repositório.
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na solução e clique em **Propriedades**.
1. Selecione **Propriedades comuns > projeto de inicialização**.
1. Selecione **vários projetos de inicialização** e defina a **ação** a ser **iniciada** para os seguintes projetos:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Clique em **OK** para salvar suas escolhas.
1. Clique em **depurar > iniciar a depuração** para compilar e executar os serviços Web no computador local.

Cada serviço Web abre um prompt de comando e uma janela do navegador da Web. No prompt de comando, você vê a saída do serviço em execução e a janela do navegador permite que você monitore o status. Não feche os prompts de comando ou páginas da Web, essa ação interrompe o serviço Web.

### <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho de Stream Analytics

Siga estas etapas para iniciar o trabalho de Stream Analytics:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até o **grupo de recursos** criado para sua solução. O nome do grupo de recursos é o nome escolhido para a solução quando você executou o script **Start. cmd** .
1. Clique no **Stream Analytics trabalho** na lista de recursos.
1. Na página **visão geral** do trabalho de Stream Analytics, clique no botão **Iniciar** . Em seguida, clique em **Iniciar** para iniciar o trabalho agora.

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Abra uma nova janela de prompt de comando para ter certeza de que você tem acesso às variáveis de ambiente definidas pelo script **Start. cmd** . Navegue até a pasta **WebUI** em sua cópia local do repositório e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o navegador exibirá a página **http:\//localhost: 3000/Dashboard**. Os erros nesta página são esperados. Para exibir o aplicativo sem erros, conclua a etapa a seguir.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor proxy reverso para vincular o aplicativo Web e os microserviços em execução no computador local:

* Copie o arquivo **nginx. conf** da pasta **webui\scripts\localhost** em sua cópia local do repositório para o diretório de instalação do **nginx\conf** .
* Execute **Nginx**.

Para obter mais informações sobre como executar o **Nginx**, consulte [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel da solução de monitoramento remoto, navegue até http:\//localhost: 9000 em seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando você concluir o teste, remova os serviços de nuvem da sua assinatura do Azure. Para remover os serviços, navegue até a [portal do Azure](https://ms.portal.azure.com) e exclua o grupo de recursos criado pelo script **Start. cmd** .

Você também pode excluir a cópia local do repositório de monitoramento remoto criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel da solução](quickstart-remote-monitoring-deploy.md).
