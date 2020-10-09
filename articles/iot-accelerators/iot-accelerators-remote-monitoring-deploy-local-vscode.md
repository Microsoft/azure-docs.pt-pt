---
title: Implementar solução de monitorização remota localmente - Código de Estúdio Visual - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como implantar o acelerador de solução de monitorização remota para a sua máquina local utilizando o Código do Estúdio Visual para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890962"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Implementar o acelerador de solução de monitorização remota localmente - Código do Estúdio Visual

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implantar o acelerador de solução de Monitorização Remota na sua máquina local para testes e desenvolvimento. Aprende-se a executar os microserviços no Código do Estúdio Visual. Uma implementação local de microserviços utiliza os seguintes serviços em nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implantação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Rio Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão C# do Código VS](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o PCS CLI que os scripts usam para criar recursos Azure. Não use Node.js v10

> [!NOTE]
> Visual Studio Code está disponível para Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, execute os microserviços de monitorização remota. Você executou a uI web de forma nativa, o serviço de Simulação de Dispositivos em Docker, e os microserviços em Visual Studio Code.

### <a name="build-the-code"></a>Compilar o código

Navegue para serviços azure-iot-pcs-remote-monitoring-dotnet\na pressão de comando e executar os seguintes comandos para construir o código.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microserviços na máquina local

Os seguintes passos mostram-lhe como executar os microserviços de monitorização remota no Código do Estúdio Visual:

1. Abra o Visual Studio Code.
1. Em Código VS, abra a pasta **azure-iot-pcs-remote-monitoring-dotnet.**
1. Crie uma nova pasta chamada **.vscode** na pasta **azure-iot-pcs-remote-monitoring-dotnet.**
1. Copie os ficheiros **launch.js** e **tasks.jsligados** a partir de serviços\scripts\local\launch\idesettings\vscode para a pasta **.vscode** que acaba de criar.
1. Abra o **painel Debug** no Código VS e execute a configuração **executar todos os microserviços.** Esta configuração executa o microserviço de simulação do dispositivo em Docker e executa os outros microserviços no depurar.

A saída de **executar Run Todos os microsoervices** na Consola Debug parece o seguinte:

[![Implantação-Microserviços Locais](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Executar a uI web

Neste passo, começa-se a uI web. Navegue para a pasta **azure-iot-pcs-remote-monitoring-dotnet\webui** na sua cópia local e executar os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início estiver concluído, o seu navegador exibe a página **http: \/ /localhost:3000/dashboard**. Os erros nesta página são esperados. Para ver a aplicação sem erros, complete o passo seguinte.

### <a name="configure-and-run-nginx"></a>Configure e executar NGINX

Crie um servidor de procuração inversa para ligar a aplicação web e microserviços em execução na sua máquina local:

* Copie o ficheiro **nginx.conf** da pasta **webui\scripts\localhost** para o diretório de instalação **nginx\conf.**
* Executar **nginx**.

Para obter mais informações sobre a execução **do nginx,** consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao painel de solução de monitorização remota, navegue para http: \/ /localhost:9000 no seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes remova os serviços de cloud da sua subscrição Azure. Para remover os serviços, navegue no [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de instrumentos de solução](quickstart-remote-monitoring-deploy.md).
