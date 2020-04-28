---
title: Implementar solução de monitorização remota localmente - Código de Estúdio Visual - Azure [ Microsoft Docs
description: Este guia de como fazer mostra como implementar o acelerador de solução de monitorização remota para a sua máquina local utilizando o Código de Estúdio Visual para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890962"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Implementar o acelerador de soluções de monitorização remota localmente - Código de Estúdio Visual

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota na sua máquina local para testes e desenvolvimento. Aprende-se a gerir os microserviços no Código do Estúdio Visual. Uma implantação local de microserviços utiliza os seguintes serviços na nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de soluções de monitorização remota, necessita de uma subscrição azure ativa.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implementação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Estivador](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão C# do Código VS](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o CLI PCS que os scripts usam para criar recursos Azure. Não use Node.js v10

> [!NOTE]
> Visual Studio Code está disponível para Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, executa os microserviços de Monitorização Remota. Você executa a Web UI de forma nativa, o serviço de simulação de dispositivos em Docker, e os microserviços em Visual Studio Code.

### <a name="build-the-code"></a>Compilar o código

Navegue para azure-iot-pcs-remote-monitoring-dotnet\services no pedido de comando e executar os seguintes comandos para construir o código.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microserviços na máquina local

Os seguintes passos mostram-lhe como executar os microserviços de Monitorização Remota em Código de Estúdio Visual:

1. Abra o Visual Studio Code.
1. No Código VS, abra a pasta **azure-iot-pcs-remote-monitoring-dotnet.**
1. Crie uma nova pasta chamada **.vscode** na pasta **azure-iot-pcs-remote-monitoring-dotnet.**
1. Copie os **ficheiros launch.json** e **tasks.json** from services\scripts\local\launch\idesettingss\vscode para a pasta **.vscode** que acabade criar.
1. Abra o **painel Debug** em Código VS e execute a configuração **executar todos os microserviços.** Esta configuração executa o microserviço de simulação do dispositivo em Docker e executa os outros microserviços no debugger.

A saída de **executar Executar Todas as microsoervices** na Consola Debug parece ser a seguinte:

[![Implantação-Microserviços Locais](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Executar a Web UI

Neste passo, inicia-se a UI web. Navegue para a pasta **azure-iot-pcs-remote-monitoring-dotnet\webui** na sua cópia local e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início estiver concluído, o seu navegador exibe a página **\/http: /localhost:3000/dashboard**. Os erros nesta página são esperados. Para visualizar a aplicação sem erros, complete o seguinte passo.

### <a name="configure-and-run-nginx"></a>Configure e executar NGINX

Configurar um servidor proxy invertido para ligar a aplicação web e os microserviços em execução na sua máquina local:

* Copie o ficheiro **nginx.conf** da pasta **webui\scripts\localhost** para o diretório de instalação **nginx\conf.**
* Executar **nginx**.

Para mais informações sobre a execução de **nginx,** consulte [o nginx para windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao dashboard de solução\/de monitorização remota, navegue para http: /localhost:9000 no seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes, remova os serviços de nuvem da sua subscrição Azure. Para remover os serviços, navegue para o [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de Monitorização Remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de soluções](quickstart-remote-monitoring-deploy.md).
