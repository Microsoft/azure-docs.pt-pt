---
title: Implementar a solução de monitorização remota localmente (Visual Studio Code) - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como implementar o acelerador de solução de monitorização remota em seu computador local com o Visual Studio Code para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 93935aa36c53e1d5de1bba215826d0754ee83b8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902846"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Implementar o monitorização remota acelerador de soluções localmente - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local para teste e desenvolvimento. Saiba como executar os microsserviços no Visual Studio Code. Uma implementação de microsserviços local utiliza os seguintes serviços de nuvem: IoT Hub, o Cosmos DB, análise de transmissão em fluxo do Azure e Azure Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços do Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração de máquina

Para concluir a implementação de local, terá das seguintes ferramentas instaladas no seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code C# extensão](https://code.visualstudio.com/docs/languages/csharp)
* [NODE. js v8](https://nodejs.org/) -este software é um pré-requisito para a CLI de PCS que os scripts usam para criar recursos do Azure. Não utilize a v10 de node. js

> [!NOTE]
> Código do Visual Studio está disponível para Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Execute os microsserviços

Nesta secção, vai executar os microsserviços de monitorização remota. Executar a IU da web nativamente, o serviço de simulação do dispositivo no Docker e os microsserviços no Visual Studio Code.

### <a name="build-the-code"></a>Compilar o código

Navegue para azure-iot-pcs-remote-monitoring-dotnet\services no prompt de comando e execute os seguintes comandos para criar o código.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microsserviços no computador local

Os passos seguintes mostram como executar os microsserviços de monitorização remota no Visual Studio 2017:

1. Abra o Visual Studio Code.
1. Abra o **azure-iot-pcs-remote-monitoring-dotnet** módulo a partir da sua cópia local no VS Code.
1. Copie os ficheiros **Launch** e **tasks.json** de scripts\local\launch\idesettings\vscode\. Crie uma nova pasta **azure-iot-pcs-remote-monitoring-dotnet\.vscode** e cole os arquivos que lá.
1. Abra o painel de depuração no VS Code e execute o **executar todos os microsserviços** configuração. Esta configuração é executada o simulação de dispositivo dos microsserviços no Docker e executa os outros microsserviços no depurador.

Por exemplo, a saída para **Auth** serviço no **consola depurar** tem a seguinte aparência:

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Executar a IU da web

Neste passo, começa a IU da web. Navegue para **azure-iot-pcs-remote-monitoring-dotnet\webui** pasta no seu local copiar e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o browser apresenta a página **http://localhost:3000/dashboard**. Espera-se os erros nesta página. Para ver a aplicação sem erros, conclua o passo seguinte.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor de proxy inverso para ligar a aplicação web e os microsserviços em execução no seu computador local:

* Cópia a **nginx.conf** ficheiro a partir do **webui\scripts\localhost** pasta para o **nginx\conf** diretório de instalação.
* Execute **nginx**.

Para obter mais informações sobre como executar **nginx**, consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligar ao dashboard

Para aceder ao dashboard de solução de monitorização remota, navegue até [ http://localhost:9000 ](http://localhost:9000) no seu browser.

## <a name="clean-up"></a>Limpeza

Para evitar desnecessários custos, quando tiver concluído os testes remover os serviços em nuvem da sua subscrição do Azure. Para remover os serviços, navegue para o [portal do Azure](https://ms.portal.azure.com) e eliminar o recurso de grupo que o **start.cmd** script criado.

Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou a solução de monitorização remota, a próxima etapa é [explore os recursos do dashboard da solução](quickstart-remote-monitoring-deploy.md).
