---
title: Implantar a solução de monitoramento remoto localmente-Visual Studio Code-Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local usando Visual Studio Code para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890962"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Implantar o acelerador de solução de monitoramento remoto Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local para teste e desenvolvimento. Você aprende a executar os microserviços no Visual Studio Code. Uma implantação de microserviços locais usa os seguintes serviços de nuvem: Hub IoT, Cosmos DB, análise de streaming do Azure e Azure Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de monitoramento remoto, você precisa de uma assinatura ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisará das seguintes ferramentas instaladas em seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão de C# vs Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node. js V8](https://nodejs.org/) – este software é um pré-requisito para a CLI de PCs que os scripts usam para criar recursos do Azure. Não usar V10 node. js

> [!NOTE]
> Visual Studio Code está disponível para Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta seção, você executa os microserviços de monitoramento remoto. Você executa a interface do usuário da Web nativamente, o serviço de simulação de dispositivo no Docker e os microserviços no Visual Studio Code.

### <a name="build-the-code"></a>Compilar o código

Navegue até Azure-IOT-PCs-Remote-Monitoring-dotnet\services no prompt de comando e execute os comandos a seguir para compilar o código.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implantar todos os outros microserviços no computador local

As etapas a seguir mostram como executar os microserviços de monitoramento remoto no Visual Studio Code:

1. Abra o Visual Studio Code.
1. Em VS Code, abra a pasta **Azure-IOT-PCs-Remote-Monitoring-dotnet** .
1. Crie uma nova pasta chamada **. vscode** na pasta **Azure-IOT-PCs-Remote-Monitoring-dotnet** .
1. Copie os arquivos **Launch. JSON** e **Tasks. JSON** de services\scripts\local\launch\idesettings\vscode para a pasta **. vscode** que você acabou de criar.
1. Abra o **painel de depuração** no vs Code e execute a configuração **executar todos os microserviços** . Essa configuração executa o microserviço de simulação de dispositivo no Docker e executa os outros microserviços no depurador.

A saída da execução **executar todos os microsserviços** no console de depuração é semelhante ao seguinte:

[![implantar-locais-microserviços](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Navegue até a pasta **Azure-IOT-PCs-Remote-Monitoring-dotnet\webui** em sua cópia local e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o navegador exibirá a página **http:\//localhost: 3000/Dashboard**. Os erros nesta página são esperados. Para exibir o aplicativo sem erros, conclua a etapa a seguir.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor proxy reverso para vincular o aplicativo Web e os microserviços em execução no computador local:

* Copie o arquivo **nginx. conf** da pasta **webui\scripts\localhost** para o diretório de instalação do **nginx\conf** .
* Execute **Nginx**.

Para obter mais informações sobre como executar o **Nginx**, consulte [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel da solução de monitoramento remoto, navegue até http:\//localhost: 9000 em seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando você concluir o teste, remova os serviços de nuvem da sua assinatura do Azure. Para remover os serviços, navegue até a [portal do Azure](https://ms.portal.azure.com) e exclua o grupo de recursos criado pelo script **Start. cmd** .

Você também pode excluir a cópia local do repositório de monitoramento remoto criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel da solução](quickstart-remote-monitoring-deploy.md).
