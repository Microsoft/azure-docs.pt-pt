---
title: Desenvolver e depurar módulos no Visual Studio – Azure IoT Edge | Microsoft Docs
description: Use o Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 892076954535d880f9081a269215cb7e2a0a8dce
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541855"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge

Pode transformar sua lógica de negócio em módulos do Azure IoT Edge. Este artigo mostra como usar o Visual Studio 2019 como a ferramenta principal para desenvolver e depurar módulos.

As ferramentas de Edge IoT do Azure para Visual Studio fornece as seguintes vantagens:

- Crie, edite, compile, execute e depure Azure IoT Edge soluções e módulos em seu computador de desenvolvimento local.
- Implemente a sua solução do Azure IoT Edge no dispositivo Azure IoT Edge através do IoT Hub do Azure.
- Codifique seus módulos de IoT do Azure em C# C ou embora tenha todos os benefícios do desenvolvimento do Visual Studio.
- Gerir dispositivos Azure IoT Edge e os módulos com interface do Usuário.

Este artigo mostra como usar as ferramentas de Azure IoT Edge para o Visual Studio 2019 para desenvolver seus módulos de IoT Edge. Também irá aprender a implementar o seu projeto para o seu dispositivo Azure IoT Edge. Atualmente, o Visual Studio 2019 fornece suporte para módulos escritos em C C#e. As arquiteturas de dispositivo com suporte são Windows x64 e Linux x64 ou ARM32. Para obter mais informações sobre sistemas operacionais, linguagens e arquiteturas com suporte, consulte [suporte a linguagens e arquitetura](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que utilize um computador ou máquina virtual a executar o Windows como computador de desenvolvimento. Em computadores com Windows, você pode desenvolver módulos do Windows ou Linux. Para desenvolver módulos do Windows, use um computador Windows executando a versão 1809/Build 17763 ou mais recente. Para desenvolver módulos do Linux, use um computador Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Como este artigo usa o Visual Studio 2019 como a ferramenta de desenvolvimento principal, instale o Visual Studio. Certifique-se de incluir o **desenvolvimento do Azure** e o desenvolvimento de desktops com **C++** cargas de trabalho na instalação do Visual Studio 2019. Você pode [Modificar o Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) para adicionar as cargas de trabalho necessárias.

Depois que o Visual Studio 2019 estiver pronto, você também precisará das seguintes ferramentas e componentes:

- Baixe e instale as [ferramentas de Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) do Visual Studio Marketplace para criar um projeto IOT Edge no visual Studio 2019.

> [!TIP]
> Se você estiver usando o Visual Studio 2017, plrease baixar e instalar o [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para vs 2017 no Visual Studio Marketplace

- Baixe e instale o [Docker Community Edition](https://docs.docker.com/install/) em seu computador de desenvolvimento para compilar e executar suas imagens de módulo. Você precisará definir o Docker CE para ser executado no modo de contêiner do Linux ou no modo de contêiner do Windows.

- Configure seu ambiente de desenvolvimento local para depurar, executar e testar sua solução de IoT Edge instalando a [ferramenta de desenvolvimento EdgeHub do Azure IOT](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/) e, em seguida, instale o pacote **iotedgehubdev** executando o comando a seguir em seu terminal. Certifique-se de que a sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é maior que 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone o repositório e instale o Gerenciador de biblioteca do Vcpkg e, em seguida, instale o **pacote Azure-IOT-SDK-c** para Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)do Docker.

  > [!TIP]
  > Pode utilizar um registo do Docker local para o protótipo e fins de testes em vez de um registo de cloud.

- Para testar seu módulo em um dispositivo, você precisará de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para usar o computador como um dispositivo IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se você estiver executando IoT Edge daemon em seu computador de desenvolvimento, talvez seja necessário parar o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verificar a sua versão de ferramentas

1. No menu **ferramentas** , selecione **extensões e atualizações**. Expanda as **ferramentas de > instaladas** e encontre as **ferramentas de Azure IOT Edge** e o **Cloud Explorer para Visual Studio**.

1. Tenha em atenção a versão instalada. Pode comparar essa versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Se sua versão for mais antiga do que a disponível em Visual Studio Marketplace, atualize suas ferramentas no Visual Studio, conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualizar as suas ferramentas

1. Na caixa de diálogo **extensões e atualizações** , expanda **atualizações > Visual Studio Marketplace**, selecione **Azure IOT Edge ferramentas** ou **Cloud Explorer para Visual Studio** e selecione **Atualizar**.

1. Após a atualização das ferramentas é transferida, feche o Visual Studio ao acionar que as ferramentas de atualização através do instalador do VSIX.

1. No instalador, selecione **OK** para iniciar e, em seguida, **Modificar** para atualizar as ferramentas.

1. Depois que a atualização for concluída, selecione **fechar** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implementado em dispositivos do Azure IoT Edge no IoT Hub do Azure. Primeiro, você cria uma solução de Azure IoT Edge e, em seguida, gera o primeiro módulo nessa solução. Cada solução de IoT Edge pode conter mais de um módulo.

> [!TIP]
> A estrutura de projeto IoT Edge criada pelo Visual Studio não é a mesma do Visual Studio Code.

1. Na caixa de diálogo novo projeto do Visual Studio, pesquise e selecione **Azure IOT Edge** projeto e clique em **Avançar**. Na janela configuração do projeto, insira um nome para o seu projeto e especifique o local e, em seguida, selecione **criar**. O nome de projeto predefinido é **AzureIoTEdgeApp1**.

   ![Criar novo projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Na janela **Adicionar aplicativo e módulo do IOT Edge** , selecione **C#** módulo ou **módulo C** e, em seguida, especifique o nome do módulo e o repositório de imagens do módulo. O Visual Studio preenche automaticamente o nome do módulo com **localhost: 5000/< o nome\>do módulo**. Substitua-o com as suas próprias informações de registo. Se você usar um registro do Docker local para teste, o **localhost** será bem. Se utilizar o Azure Container Registry, em seguida, utilize o servidor de início de sessão a partir das definições do seu registo. O servidor de logon é semelhante   **_\<ao\>nome do registro_. azurecr.Io**. Substitua apenas a parte **localhost: 5000** da cadeia de caracteres para que o resultado final se pareça  **\<com o\> *nome do registro*. azurecr.Io/ _\<o nome\>do módulo_** . O nome do módulo padrão é **IotEdgeModule1**

   ![Adicionar aplicativo e módulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecione **OK** para criar a solução de Azure IOT Edge com um módulo que usa C# o ou o C.

Agora você tem um projeto **AzureIoTEdgeApp1. Linux. AMD64** ou um projeto **AzureIoTEdgeApp1. Windows. AMD64** e também um projeto **IotEdgeModule1** em sua solução. Cada projeto **AzureIoTEdgeApp1** tem um `deployment.template.json` arquivo, que define os módulos que você deseja compilar e implantar para sua solução de IOT Edge e também define as rotas entre os módulos. A solução padrão tem um módulo **tempSensor** e um módulo **IotEdgeModule1** . O módulo **tempSensor** gera dados simulados para o módulo **IotEdgeModule1** , enquanto o código padrão no módulo **IotEdgeModule1** canaliza diretamente as mensagens recebidas para o Hub IOT do Azure.

O projeto **IotEdgeModule1** é um aplicativo de console do .net Core 2,1 se for C# um módulo. Ele contém os arquivos do Docker necessários para o dispositivo IoT Edge em execução com um contêiner do Windows ou contêiner do Linux. O `module.json` arquivo descreve os metadados de um módulo. O código de módulo real, que usa o `Program.cs` SDK do dispositivo IOT do Azure como uma dependência, é encontrado no arquivo ou. `main.c`

## <a name="develop-your-module"></a>Desenvolver o seu módulo

O código de módulo padrão que vem com a solução está localizado em **IotEdgeModule1** > **Program.cs** ( C#para) ou **Main. c** (c). O módulo e o `deployment.template.json` arquivo são configurados para que você possa criar a solução, enviá-la por push para o registro de contêiner e implantá-la em um dispositivo para iniciar o teste sem tocar em nenhum código. O módulo foi criado para pegar a entrada de uma fonte (nesse caso, o módulo **tempSensor** que simula dados) e redirecioná-lo para o Hub IOT do Azure.

Quando você estiver pronto para personalizar o modelo de módulo com seu próprio código, use os [SDKs do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IOT, como segurança, gerenciamento de dispositivos e confiabilidade.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializar o iotedgehubdev com IoT Edge cadeia de conexão do dispositivo

1. Copie a cadeia de conexão de qualquer dispositivo IoT Edge da **cadeia de conexão primária** no Gerenciador de nuvem do Visual Studio. Certifique-se de não copiar a cadeia de conexão de um dispositivo que não seja de borda, pois o ícone de um dispositivo IoT Edge é diferente do ícone de um dispositivo que não é de borda.

   ![Copie a cadeia de ligação de dispositivo do Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Acesse **ferramentas** > **Azure IOT Edge ferramentas** > **Configurar IOT Edge Simulator**, Cole a cadeia de conexão e clique em **OK**.

   ![Abra a janela de cadeia de ligação do conjunto de borda](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Insira a cadeia de conexão da primeira etapa e, em seguida, selecione **OK**.

> [!NOTE]
> Você precisa seguir estas etapas apenas uma vez no computador de desenvolvimento, pois os resultados são aplicados automaticamente a todas as soluções de Azure IoT Edge subsequentes. Esse procedimento pode ser seguido novamente se você precisar alterar para uma cadeia de conexão diferente.

## <a name="build-and-debug-single-module"></a>Compilar e depurar um único módulo

Normalmente, você desejará testar e depurar cada módulo antes de executá-lo em uma solução inteira com vários módulos.

1. Clique com o botão direito do mouse em **IotEdgeModule1** e selecione **definir como projeto de inicialização** no menu de contexto.

   ![Definir o projeto de arranque](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pressione **F5** ou clique no botão abaixo para executar o módulo; pode levar 10&ndash;a 20 segundos na primeira vez que você fizer isso.

   ![Executar o módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Você deverá ver um aplicativo de console do .NET Core iniciar se o módulo tiver sido inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se estiver desenvolvendo C#no, defina um ponto de `PipeMessage()` interrupção na função em **Program.cs**; se estiver usando C `InputQueue1Callback()` , defina um ponto de interrupção na função em **Main. C**. Em seguida, você pode testá-lo enviando uma mensagem executando o comando a seguir em um shell do **git bash** ou **WSL bash** . (Não é possível executar `curl` o comando de um PowerShell ou prompt de comando.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depurar o módulo único](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de interrupção deve ser acionado. Você pode observar variáveis na janela **locais** do Visual Studio.

   > [!TIP]
   > Você também pode usar o [postmaster](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens em `curl`vez de.

1. Prima **Ctrl + F5** ou clique no botão Parar para interromper a depuração.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Criar e depurar a solução de IoT Edge com vários módulos

Depois de terminar de desenvolver um único módulo, talvez você queira executar e depurar uma solução inteira com vários módulos.

1. Adicione um segundo módulo à solução clicando com o botão direito do mouse em **AzureIoTEdgeApp1** e selecionando **Adicionar** > **novo IOT Edge módulo**. O nome padrão do segundo módulo é **IotEdgeModule2** e atuará como outro módulo de pipe.

1. Abra o arquivo `deployment.template.json` e você verá **IotEdgeModule2** foi adicionado na seção **módulos** . Substitua a **rotas** secção com o seguinte. Se você tiver personalizado os nomes de módulo, atualize esses nomes para que correspondam.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e selecione **definir como projeto de inicialização** no menu de contexto.

1. Crie seus pontos de interrupção e, em seguida, pressione **F5** para executar e depurar vários módulos simultaneamente. Você deve ver várias janelas do aplicativo de console do .NET Core, que cada janela representa um módulo diferente.

   ![Depurar vários módulos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Pressione **Ctrl + F5** ou selecione o botão parar para interromper a depuração.

## <a name="build-and-push-images"></a>Criar e enviar imagens

1. Certifique-se de que **AzureIoTEdgeApp1** é o projeto de inicialização. Selecione **depurar** ou **liberar** como a configuração a ser compilada para suas imagens de módulo.

    > [!NOTE]
    > Ao escolher **debug**, o Visual Studio `Dockerfile.(amd64|windows-amd64).debug` usa para criar imagens do Docker. Isto inclui o depurador de linha de comandos do .NET Core VSDBG em sua imagem de contentor ao criá-lo. Para módulos de IOT Edge prontos para produção, recomendamos que você use a configuração de **versão** , `Dockerfile.(amd64|windows-amd64)` que usa sem VSDBG.

1. Se você estiver usando um registro privado como o registro de contêiner do Azure, use o comando do Docker a seguir para entrar nele. Se você estiver usando o registro local, poderá [executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se você estiver usando um registro privado, como o registro de contêiner do Azure, precisará adicionar as informações de logon do registro às configurações de `deployment.template.json`tempo de execução encontradas no arquivo. Substitua os espaços reservados por seu nome de usuário, senha e registro do administrador do ACR real.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e selecione **criar e enviar por push a solução de borda** para compilar e enviar por push a imagem Docker para cada módulo.

   ![Criar e enviar imagens](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Implementar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com o Cloud Explorer para Visual Studio. Você já tem um manifesto de implantação preparado para seu cenário, `deployment.json` o arquivo e tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Open **Cloud Explorer** ao clicar em **vista** > **Cloud Explorer**. Verifique se você fez logon no Visual Studio 2019.

1. Na **Cloud Explorer**, expanda a sua subscrição, encontrar o seu IoT Hub do Azure e o dispositivo Azure IoT Edge que pretende implementar.

1. Clique com o botão direito do mouse no dispositivo IoT Edge para criar uma implantação para ele, você precisa selecionar o arquivo de manifesto `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`de implantação no.

   > [!NOTE]
   > Não tem de selecionar `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Clique no botão atualizar para ver os novos módulos em execução junto com o módulo **tempSensor** e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

1. Para monitorar a mensagem D2C para um dispositivo específico, selecione o dispositivo na lista e clique em **Iniciar Monitoramento de ponto de extremidade de evento interno** na janela **ação** .

1. Para interromper o monitoramento de dados, selecione o dispositivo na lista e, em seguida, selecione **parar monitoramento de ponto de extremidade de evento interno** na janela **ação** .

## <a name="next-steps"></a>Passos Seguintes

Para desenvolver módulos personalizados para seus dispositivos IoT Edge, [entenda e use SDKs do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
