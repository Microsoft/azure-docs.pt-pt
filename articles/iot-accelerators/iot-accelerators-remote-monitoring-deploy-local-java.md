---
title: Implementar solução de monitorização remota local - IntelliJ IDE - Azure [ Microsoft Docs
description: Este guia de como fazer mostra como implementar o acelerador de solução de monitorização remota para a sua máquina local, utilizando o IntelliJ para testes e desenvolvimento.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888818"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implementar o acelerador de solução de monitorização remota localmente - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota na sua máquina local para testes e desenvolvimento. Vaiaprender a gerir os microserviços em IntelliJ. Uma implantação local de microserviços utilizará os seguintes serviços na nuvem: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

Se quiser executar o acelerador de solução de monitorização remota em Docker na sua máquina local, consulte [a instalação local do acelerador](iot-accelerators-remote-monitoring-deploy-local-docker.md)de soluções de monitorização remota - Docker .

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de soluções de monitorização remota, necessita de uma subscrição azure ativa.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implementação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Estivador](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Edição Comunitária IntelliJ](https://www.jetbrains.com/idea/download/)
* [Plugin IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plugin IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plugin de Executor IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Nó.js v8](https://nodejs.org/)

Node.js v8 é um pré-requisito para o CLI PCS que os scripts usam para criar recursos Azure. Não use node.js v10.

> [!NOTE]
> O IntelliJ IDE está disponível para Windows e Mac.

## <a name="download-the-source-code"></a>Descarregue o código fonte

Os repositórios de código de código de monitorização remota incluem o código fonte e os ficheiros de configuração do Docker que você precisa para executar as imagens do Docker de microserviços.

Para clonar e criar uma versão local do repositório, use o ambiente da linha de comando para ir a uma pasta adequada na sua máquina local. Em seguida, executar um dos seguintes conjuntos de comandos para clonar o repositório Java:

* Para descarregar a versão mais recente das implementações do microserviço Java, execute o seguinte comando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Para recuperar os submódulos mais recentes, executar os seguintes comandos:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Estes comandos descarregam o código fonte para todos os microserviços, além dos scripts que utiliza para executar os microserviços localmente. Não precisas do código fonte para gerir os microserviços no Docker. Mas o código fonte é útil se mais tarde planeia modificar o acelerador de solução e testar as suas alterações localmente.

## <a name="deploy-the-azure-services"></a>Implementar os serviços Azure

Embora este artigo mostre como gerir os microserviços localmente, eles dependem dos serviços azure que funcionam na nuvem. Utilize o seguinte script para implantar os serviços Azure. Os exemplos do guião assumem que está a usar o repositório Java numa máquina windows. Se estiver a trabalhar noutro ambiente, ajuste os caminhos, as extensões de ficheiros e os separadores de caminhos adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos Azure

Se ainda não criou os recursos azure necessários, siga estes passos:

1. No seu ambiente de linha de comando, vá à pasta **\services\scripts\local\launch** na sua cópia clonada do repositório.

1. Execute os seguintes comandos para instalar a ferramenta **CLI do PCS** e iniciar sessão na sua conta Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Execute o script **start.cmd.** O script solicita-lhe as seguintes informações:

   * Um nome de solução.
   * A subscrição do Azure que deve utilizar.
   * A localização do datacenter Azure a utilizar.

   O script cria um grupo de recursos em Azure que tem o seu nome de solução. Este grupo de recursos contém os recursos Azure que o acelerador de solução utiliza. Pode eliminar este grupo de recursos depois de já não precisar dos recursos correspondentes.

   O script também adiciona um conjunto de variáveis ambientais à sua máquina local. Cada nome variável tem o **PCS**prefixo . Estas variáveis ambientais fornecem detalhes que permitem que a Monitorização Remota leia os seus valores de configuração a partir de um recurso Azure Key Vault.

   > [!TIP]
   > Quando o script termina, guarda as variáveis ambientais para um ficheiro chamado ** \<"your home folder\>\\".pcs\\\<solution name\>.env**. Pode usá-los para futuras implementações de aceleradores de soluções. Note que quaisquer variáveis ambientais definidas na sua máquina local sobrepõem os valores nos **scripts de\\serviços\\ficheiro .env local\\** quando executa **docker-compose**.

1. Feche o ambiente da linha de comando.

### <a name="use-existing-azure-resources"></a>Utilizar os recursos azure existentes

Se já criou os recursos Azure necessários, detete as variáveis ambientais correspondentes na sua máquina local:
* **PCS_KEYVAULT_NAME:** O nome do recurso Key Vault.
* **PCS_AAD_APPID**: Id de aplicação Azure Ative Directory (Azure AD).
* **PCS_AAD_APPSECRET**: O segredo da aplicação da AD Azure.

Os valores de configuração serão lidos a partir deste recurso Key Vault. Estas variáveis ambientais podem ser guardadas na ** \<pasta\>\\inicial .pcs\\\<nome de\>solução .env** da implementação. Note que as variáveis ambientais definidas na sua máquina local sobrepõem os valores nos **scripts de\\serviços\\ficheiro .env local\\** quando executa **docker-compor**.

Parte da configuração necessária pelo microserviço é armazenada num caso de Key Vault que foi criado na implementação inicial. As variáveis correspondentes no cofre-chave devem ser modificadas conforme necessário.

## <a name="run-the-microservices"></a>Executar os microserviços

Nesta secção, executa os microserviços de Monitorização Remota. Corre:

* A teia ui nativa.
* Os serviços de Simulação de Dispositivos Azure IoT, Auth e Azure Stream Analytics Manager em Docker.
* Os microserviços em IntelliJ.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação de dispositivo

Abra uma nova janela de comando. Verifique se tem acesso às variáveis ambientais definidas pelo script **start.cmd** na secção anterior.

Executar o seguinte comando para abrir o recipiente Docker para o serviço de simulação de dispositivos. O serviço simula dispositivos para a solução de Monitorização Remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Executar o serviço Auth

Abra uma nova janela De Comando Pronta e, em seguida, executar o seguinte comando para abrir o recipiente Docker para o serviço Auth. Ao utilizar este serviço, pode gerir os utilizadores autorizados a aceder às soluções Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Executar o serviço Stream Analytics Manager

Abra uma nova janela Command Prompt e, em seguida, executar o seguinte comando para abrir o recipiente Docker para o serviço Stream Analytics Manager. Com este serviço, você pode gerir trabalhos stream analytics. Essa gestão inclui a definição de configuração de emprego e o início, paragem e monitorização do estado do trabalho.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Implante todos os outros microserviços na sua máquina local

Os seguintes passos mostram-lhe como executar os microserviços de Monitorização Remota em IntelliJ.

#### <a name="import-a-project"></a>Importar um projeto

1. Abra o IntelliJ IDE.
1. Selecione **Projeto de Importação**.
1. Escolha **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Criar configurações de execução

1. Selecione **Configurações** > de edição de**execução**.
1. **Selecione Adicionar nova configuração** > **tarefa sbt**.
1. Introduza **o Nome**e, em seguida, **introduza tarefas** como **executada**.
1. Selecione o **Diretório** de Trabalho com base no serviço que pretende executar.
1. Selecione **Aplicar** > **OK** para salvar as suas escolhas.
1. Criar configurações de execução para os seguintes serviços web:
    * WebService (serviços\config)
    * WebService (serviços\dispositivo-telemetria)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Como exemplo, a seguinte imagem mostra como adicionar uma configuração para um serviço:

[![Screenshot da janela IntelliJ IDE Configurações de execução/depuração, mostrando a opção de armazenamentoAdapter destacada na lista de tarefas sbt no painel esquerdo e entradas no nome, tarefas, diretório de trabalho e caixas de parâmetros VM no painel direito.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Criar uma configuração composta

1. Para executar todos os serviços em conjunto, selecione Adicionar novo**composto**de **configuração** > .
1. Introduza **o Nome**e, em seguida, selecione **adicionar tarefas sbt**.
1. Selecione **Aplicar** > **OK** para salvar as suas escolhas.

Como exemplo, a imagem que se segue mostra como adicionar todas as tarefas sbt a uma única configuração:

[![Screenshot da janela IntelliJ IDE Run/Debug Configurations, mostrando a opção AllServices realçada na lista Composto na vidraça esquerda e a opção sbt Task 'deviceTelemettry' realçada na vidraça direita.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecione **Executar** para construir e executar os serviços web na máquina local.

Cada serviço web abre uma janela Command Prompt e janela do navegador web. No pedido de comando, vê-se a saída do serviço de funcionamento. A janela do navegador permite-lhe monitorizar o estado. Não feche as janelas ou páginas web do Comando Prompt, pois estas ações param o serviço web.

Para aceder ao estado dos serviços, aceda aos seguintes URLs:

* IoT-Hub Manager:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria do dispositivo:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adaptador de armazenamento:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho de Stream Analytics

Siga estes passos para iniciar o trabalho de Stream Analytics:

1. Vá ao [portal Azure.](https://portal.azure.com)
1. Vá ao **grupo Derecursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Selecione o **trabalho stream analytics** na lista de recursos.
1. Na página de **visão geral** do trabalho do Stream Analytics, selecione o botão **Iniciar** e, em seguida, selecione **Iniciar** o trabalho.

### <a name="run-the-web-ui"></a>Executar a Web UI

Neste passo, inicia-se a UI web. Abra uma nova janela de comando. Verifique se tem acesso às variáveis ambientais definidas pelo script **start.cmd.** Vá à pasta **webui** na sua cópia local do repositório e, em seguida, execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o comando de **arranque** estiver concluído, o [http://localhost:3000/dashboard](http://localhost:3000/dashboard)seu navegador exibe a página no endereço . Os erros nesta página são esperados. Para visualizar a aplicação sem erros, complete os seguintes passos.

### <a name="configure-and-run-nginx"></a>Configure e executar Nginx

Configurar um servidor proxy invertido que ligue a aplicação web aos microserviços em execução na sua máquina local:

1. Copie o ficheiro **nginx.conf** da pasta **webui\scripts\localhost** na sua cópia local do repositório para o diretório de instalação **nginx\conf.**
1. Executar Nginx.

Para mais informações sobre a execução de Nginx, consulte [o nginx para windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao dashboard de solução de monitorização remota, aceda ao http://localhost:9000 seu navegador.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, remova os serviços de nuvem da sua subscrição Azure depois de terminar os seus testes. Para remover os serviços, vá ao [portal Azure](https://ms.portal.azure.com)e elimine o grupo de recursos que o script **start.cmd** criou.

Também pode eliminar a cópia local do repositório de Monitorização Remota que foi criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de soluções](quickstart-remote-monitoring-deploy.md).
