---
title: Implementar a solução de monitorização remota localmente (por meio de IntelliJ IDE) - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local com o IntelliJ para teste e desenvolvimento.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: ccdb3e16ea60cf85ae28e533e3b2d9f473cc90c8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316393"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implementar o monitorização remota acelerador de soluções localmente - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local para teste e desenvolvimento. Saiba como executar os microsserviços no IntelliJ. Uma implementação de microsserviços local utiliza os seguintes serviços de nuvem: IoT Hub, Cosmos DB, a análise de transmissão em fluxo do Azure e Azure Time Series Insights serviços na cloud.

Se quiser executar o acelerador de solução de monitorização remota no Docker no seu computador local, veja [implementar o monitorização remota acelerador de soluções localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços do Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração de máquina

Para concluir a implementação de local, terá das seguintes ferramentas instaladas no seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Edição de Comunidade do IntelliJ](https://www.jetbrains.com/idea/download/)
* [Scala de plug-in IntelliJ](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Executor SBT de plug-in do IntelliJ](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [NODE. js v8](https://nodejs.org/) -este software é um pré-requisito para a CLI de PCS que os scripts usam para criar recursos do Azure. Não utilize a v10 de node. js.

> [!NOTE]
> IntelliJ IDE está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Execute os microsserviços

Nesta secção, vai executar os microsserviços de monitorização remota. Executar a IU da web nativamente, a simulação de dispositivo, serviço de autenticação e o Gestor do ASA no Docker e os microsserviços no IntelliJ.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação do dispositivo

Abra uma nova janela de linha de comandos para ter certeza de que tem acesso para as variáveis de ambiente definidas pelos **start.cmd** script na secção anterior.

Execute o seguinte comando para iniciar o contentor do Docker para o serviço de simulação do dispositivo. O serviço simula os dispositivos para a solução de monitorização remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Executar o serviço de autenticação

Abra uma nova janela de linha de comandos e execute o seguinte comando para iniciar o contentor do Docker para o serviço de autenticação. Permite que o serviço para gerir os utilizadores autorizados a aceder a soluções de IoT do Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Executar o serviço de Gestor do ASA

Abra uma nova janela de linha de comandos e execute o seguinte comando para iniciar o contentor do Docker para o serviço de Gestor do ASA. O serviço permite a gestão de tarefas do Azure Stream Analytics (ASA), incluindo a configuração e início, paragem e respetivos Estados de monitorização.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementar todos os outros microsserviços no computador local

Os passos seguintes mostram como executar os microsserviços de monitorização remota no IntelliJ:

#### <a name="import-project"></a>Importar projeto

1. Iniciar IntelliJ IDE
1. Selecione **importar projeto** e escolha **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Criar as configurações de execução

1. Selecione **executar > Editar configurações**
1. Selecione **Adicionar nova configuração > sbt tarefas** 
1. Introduza **Name** e introduza **tarefas** como executar 
1. Selecione o **diretório de trabalho** com base no serviço que pretende executar
1. Clique em **aplicar > Ok** para guardar as suas opções.
1. Crie as configurações de execução para os seguintes serviços:
    * WebService (services\config)
    * Serviço Web (services\device telemetria)
    * WebService (services\iothub-manager)
    * Serviço Web (placa de services\storage)

Como exemplo a imagem seguinte, mostra a configuração para um serviço a adicionar:

[![Add-Configuration](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Criar configuração composta

1. Para executar todos os serviços, em conjunto selecione **Adicionar nova configuração > composta**
1. Introduza o **Name** e **adicionar sbt tarefas**
1. Clique em **aplicar > Ok** para guardar as suas opções.

Por exemplo a imagem seguinte, mostra a adição de todas as tarefas de sbt à configuração única:


[![Add-All-Services](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Clique em **executar** para compilar e executar os serviços web no computador local.

Cada serviço web é aberta uma janela do browser web e de linha de comandos. No prompt de comando, verá os resultados do serviço em execução e a janela do browser permite-lhe monitorizar o estado. Não feche o prompts de comando ou páginas da web, esta ação para o serviço de web.


Para acessar o estado dos serviços, pode navegar para os seguintes URLs:
* Gestor do IoT Hub [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria do dispositivo  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adaptador de armazenamento [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


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
