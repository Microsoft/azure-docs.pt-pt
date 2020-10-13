---
title: Implementar a solução de Monitorização Remota localmente - IntelliJ IDE - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como implantar o acelerador de solução de Monitorização Remota para a sua máquina local, utilizando o IntelliJ para testes e desenvolvimento.
author: v-krghan
manager: dominicbetts
ms.custom: devx-track-java
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: f7554843db247ade1cddff78902430a5d84debe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319172"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implementar o acelerador de solução de monitorização remota localmente - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implantar o acelerador de solução de Monitorização Remota na sua máquina local para testes e desenvolvimento. Vais aprender a gerir os microserviços em IntelliJ. Uma implementação local de microserviços utilizará os seguintes serviços em nuvem: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

Se pretender executar o acelerador de solução de monitorização remota no Docker na sua máquina local, consulte [implementar localmente o acelerador de solução de monitorização remota - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implantação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Rio Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Edição Comunitária intellij](https://www.jetbrains.com/idea/download/)
* [Plugin IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plugin IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plugin do Executor IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 é um pré-requisito para o PCS CLI que os scripts usam para criar recursos Azure. Não use Node.js v10.

> [!NOTE]
> IntelliJ IDE está disponível para Windows e Mac.

## <a name="download-the-source-code"></a>Descarregue o código fonte

Os repositórios de código fonte de monitorização remota incluem o código fonte e os ficheiros de configuração do Docker que precisa para executar as imagens do Docker dos microserviços.

Para clonar e criar uma versão local do repositório, use o ambiente da linha de comando para ir a uma pasta adequada na sua máquina local. Em seguida, executar um dos seguintes conjuntos de comandos para clonar o repositório java:

* Para descarregar a versão mais recente das implementações do microserviço Java, execute o seguinte comando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Para recuperar os últimos submodulos, executar os seguintes comandos:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Estes comandos descarregam o código fonte para todos os microserviços, além dos scripts que utiliza para executar os microserviços localmente. Não precisas do código fonte para gerir os microserviços no Docker. Mas o código fonte é útil se mais tarde planeia modificar o acelerador de solução e testar as suas alterações localmente.

## <a name="deploy-the-azure-services"></a>Implementar os serviços Azure

Embora este artigo mostre como executar os microserviços localmente, eles dependem dos serviços Azure que funcionam na nuvem. Utilize o seguinte script para implantar os serviços Azure. Os exemplos do script assumem que está a usar o repositório Java numa máquina Windows. Se estiver a trabalhar noutro ambiente, ajuste os caminhos, as extensões de ficheiros e os separadores do caminho adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos Azure

Se ainda não criou os recursos Azure necessários, siga estes passos:

1. No seu ambiente de linha de comando, vá à pasta **\services\scripts\local\launch** na sua cópia clonada do repositório.

1. Executar os seguintes comandos para instalar a ferramenta CLI **dos computadores** e iniciar súm na sua conta Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Executar o script **start.cmd.** O script solicita-lhe as seguintes informações:

   * Um nome de solução.
   * A subscrição do Azure que deve utilizar.
   * A localização do centro de dados Azure para usar.

   O script cria um grupo de recursos em Azure que tem o seu nome de solução. Este grupo de recursos contém os recursos Azure que o acelerador de solução utiliza. Pode eliminar este grupo de recursos depois de já não precisar dos recursos correspondentes.

   O script também adiciona um conjunto de variáveis ambientais à sua máquina local. Cada nome variável tem o **PCS**prefixo. Estas variáveis ambientais fornecem detalhes que permitem que a Monitorização Remota leia os seus valores de configuração a partir de um recurso Azure Key Vault.

   > [!TIP]
   > Quando o script termina, guarda as variáveis ambientais para um ficheiro chamado ** \<your home folder\> \\ .pcs \\ \<solution name\> .env**. Pode usá-las para futuras implementações de aceleradores de soluções. Note que quaisquer variáveis ambientais definidas na sua máquina local sobrepõem os valores nos ** \\ scripts de serviços \\ ficheiro local \\ .env** quando executar **o docker-compose**.

1. Feche o ambiente da linha de comando.

### <a name="use-existing-azure-resources"></a>Utilizar os recursos Azure existentes

Se já criou os recursos Azure necessários, desconfie das variáveis ambientais correspondentes na sua máquina local:
* **PCS_KEYVAULT_NAME**: O nome do recurso Key Vault.
* **PCS_AAD_APPID**: O ID da aplicação Azure Ative (Azure AD).
* **PCS_AAD_APPSECRET**: O segredo da aplicação AD Azure.

Os valores de configuração serão lidos a partir deste recurso Key Vault. Estas variáveis ambientais podem ser guardadas no ficheiro ** \<your home folder\> \\ \\ \<solution name\> .pcs .env** a partir da implementação. Note que as variáveis ambientais definidas na sua máquina local sobrepõem os valores nos ** \\ scripts de serviços \\ locais \\ .env** file quando você executar **docker-compor**.

Algumas das configurações necessárias pelo microserviço são armazenadas num caso de Key Vault que foi criado na implementação inicial. As variáveis correspondentes no cofre-chave devem ser modificadas conforme necessário.

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, execute os microserviços de monitorização remota. Corres:

* A web UI de forma nativa.
* Os serviços Azure IoT Device Simulation, Auth e Azure Stream Analytics Manager em Docker.
* Os microserviços em IntelliJ.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de Simulação de Dispositivos

Abra uma nova janela de pedido de comando. Verifique se tem acesso às variáveis ambientais definidas pelo script **start.cmd** na secção anterior.

Executar o seguinte comando para abrir o recipiente Docker para o serviço de Simulação de Dispositivo. O serviço simula dispositivos para a solução de Monitorização Remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Executar o serviço Auth

Abra uma nova janela de pedido de comando e, em seguida, executar o seguinte comando para abrir o recipiente Docker para o serviço Auth. Ao utilizar este serviço, pode gerir os utilizadores autorizados a aceder a soluções Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Executar o serviço Stream Analytics Manager

Abra uma nova janela de solicitação de comando e, em seguida, executar o seguinte comando para abrir o recipiente Docker para o serviço Stream Analytics Manager. Com este serviço, você pode gerir trabalhos stream Analytics. Tal gestão inclui a definição de configuração de trabalho e início, paragem e monitorização do estado do trabalho.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Implemente todos os outros microserviços na sua máquina local

Os passos seguintes mostram-lhe como executar os microserviços de monitorização remota no IntelliJ.

#### <a name="import-a-project"></a>Importar um projeto

1. Abra o IntelliJ IDE.
1. Selecione **Import Project**.
1. Escolha **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Criar configurações de execução

1. Selecione **Run**  >  **Configurações de edição de execução**.
1. Selecione Adicionar nova tarefa sbt de **configuração**  >  **sbt task**.
1. Insira **o Nome**e, em seguida, **introduza Tarefas** como **executada**.
1. Selecione o **Diretório de Trabalho** com base no serviço que pretende executar.
1. **Selecione Apply**  >  **OK** para guardar as suas escolhas.
1. Criar configurações de execução para os seguintes serviços web:
    * WebService (serviços\config)
    * WebService (serviços\device-telemetria)
    * WebService (services\iothub-manager)
    * WebService (serviços\storage-adapter)

Como exemplo, a imagem a seguir mostra como adicionar uma configuração para um serviço:

[![Screenshot da janela configurações IntelliJ IDE Run/Debug, mostrando a opção de armazenamentoAdapter realçada na lista de tarefas sbt no painel esquerdo e entradas nas caixas de parâmetros Name, Tasks, Working e VM no painel direito.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Criar uma configuração composta

1. Para executar todos os serviços em conjunto, selecione Adicionar novo Composto **de Configuração**  >  **Compound**.
1. Introduzir **Nome**e, em seguida, **selecionar adicionar tarefas sbt**.
1. **Selecione Apply**  >  **OK** para guardar as suas escolhas.

Como exemplo, a imagem a seguir mostra como adicionar todas as tarefas sbt a uma única configuração:

[![Screenshot da janela configurações IntelliJ IDE Run/Debug, mostrando a opção AllServices realçada na lista Composta no painel esquerdo e a opção 'deviceTelemetry' sbt realçada no painel direito.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecione **Executar** para construir e executar os serviços web na máquina local.

Cada serviço web abre uma janela de solicitação de comando e janela do navegador web. Na origem do comando, vê-se a saída do serviço de execução. A janela do navegador permite monitorizar o estado. Não feche as janelas ou páginas web do Comando, uma vez que estas ações param o serviço web.

Para aceder ao estado dos serviços, aceda aos seguintes URLs:

* IoT-Hub Manager: `http://localhost:9002/v1/status`
* Telemetria do dispositivo: `http://localhost:9004/v1/status`
* config: `http://localhost:9005/v1/status`
* adaptador de armazenamento: `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho stream analytics

Siga estes passos para iniciar o trabalho stream Analytics:

1. Aceda ao [portal do Azure](https://portal.azure.com).
1. Vá ao **grupo de Recursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Selecione o **trabalho stream Analytics** na lista de recursos.
1. Na página **de visão geral** do trabalho stream Analytics, selecione o botão **Iniciar** e, em seguida, selecione **Iniciar** para iniciar o trabalho.

### <a name="run-the-web-ui"></a>Executar a uI web

Neste passo, começa-se a uI web. Abra uma nova janela de pedido de comando. Verifique se tem acesso às variáveis ambientais definidas pelo script **start.cmd.** Vá à pasta **webui** na sua cópia local do repositório e, em seguida, execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o comando **inicial** estiver concluído, o seu navegador exibe a página no endereço `http://localhost:3000/dashboard` . Os erros nesta página são esperados. Para ver a aplicação sem erros, complete os seguintes passos.

### <a name="configure-and-run-nginx"></a>Configure e executar Nginx

Crie um servidor de procuração inversa que ligue a aplicação web aos microserviços em execução na sua máquina local:

1. Copie o ficheiro **nginx.conf** a partir da pasta **webui\scripts\local na** sua cópia local do repositório para o diretório de instalação **nginx\conf.**
1. Corre nginx.

Para obter mais informações sobre a execução do Nginx, consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao painel de solução de monitorização remota, aceda no `http://localhost:9000` seu browser.

## <a name="clean-up"></a>Limpeza

Para evitar cargas desnecessárias, remova os serviços na nuvem da sua subscrição Azure depois de terminar os seus testes. Para remover os serviços, vá ao [portal Azure](https://ms.portal.azure.com)e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de monitorização remota que foi criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de instrumentos de solução](quickstart-remote-monitoring-deploy.md).
