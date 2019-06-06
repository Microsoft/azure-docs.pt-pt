---
title: Desenvolver e depurar os módulos no Visual Studio – Azure IoT Edge | Documentos da Microsoft
description: Utilizar o Visual Studio 2019 para desenvolver e depurar os módulos do Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/27/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4014827366afc492d73757a0ac5e1acb64262c51
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474775"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>Utilizar o Visual Studio 2019 para desenvolver e depurar os módulos do Azure IoT Edge (pré-visualização)

Pode transformar sua lógica de negócio em módulos do Azure IoT Edge. Este artigo mostra-lhe como utilizar o Visual Studio 2019 como a principal ferramenta para desenvolver e depurar módulos.

As ferramentas de Edge IoT do Azure para Visual Studio fornece as seguintes vantagens:

- Criar, editar, compilar, executar e depurar as soluções do Azure IoT Edge e os módulos no seu computador de desenvolvimento local.
- Implemente a sua solução do Azure IoT Edge no dispositivo Azure IoT Edge através do IoT Hub do Azure.
- Os módulos do IoT do Azure em C de código ou C# enquanto tem todos os benefícios de desenvolvimento do Visual Studio.
- Gerir dispositivos Azure IoT Edge e os módulos com interface do Usuário.

Este artigo mostra-lhe como utilizar as ferramentas de Edge IoT do Azure para Visual Studio 2019 para desenvolver seus módulos do IoT Edge. Também irá aprender a implementar o seu projeto para o seu dispositivo Azure IoT Edge.

> [!TIP]
> A estrutura do projeto de IoT Edge criada pelo Visual Studio não é o mesmo, como no Visual Studio Code.
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que utilize um computador ou máquina virtual a executar o Windows como computador de desenvolvimento. Dispositivo IoT Edge pode ser outro dispositivo físico.

Uma vez que este artigo utiliza o Visual Studio 2019 como a ferramenta de desenvolvimento principal, instale o Visual Studio. Certifique-se de que inclui a **desenvolvimento do Azure** e **desenvolvimento com C++**  cargas de trabalho na sua instalação do Visual Studio 2019. Pode [2019 para modificar Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) para adicionar as cargas de trabalho.

Depois do Visual Studio de 2019 estiver pronto, também terá das ferramentas e os componentes seguintes:

- Transfira e instale [ferramentas do Azure IoT Edge (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) partir do mercado do Visual Studio para criar um projeto de IoT Edge no Visual Studio 2019.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017, plrease transfira e instale [ferramentas do Azure IoT Edge (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do Visual Studio marketplace

- Transfira e instale [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento para compilar e executar as imagens de módulo. Terá de definir o Docker CE para executar no modo de contentor do Linux ou modo de contentor do Windows.

- Configurar o seu ambiente de desenvolvimento local para depuração, executar e testar a sua solução de IoT Edge, instalando as [ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale [Python (2.7/3.6) e o Pip](https://www.python.org/) e, em seguida, instale o **iotedgehubdev** pacote executando o seguinte comando no seu terminal. Certifique-se de que a sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é maior que 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clonar o repositório e instalar o Gestor de biblioteca Vcpkg e, em seguida, instale o **azure-iot-sdk-c pacote** para Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Pode utilizar um registo do Docker local para o protótipo e fins de testes em vez de um registo de cloud.

- Para testar seu módulo num dispositivo, terá um hub IoT Active Directory com, pelo menos, um dispositivo do IoT Edge. Para utilizar o seu computador como um dispositivo IoT Edge, siga os passos no guia de introdução para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver a executar o daemon de IoT Edge no computador de desenvolvimento, precisará parar EdgeHub e EdgeAgent antes de começar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verificar a sua versão de ferramentas

1. Partir do **ferramentas** menu, selecione **extensões e atualizações**. Expanda **instalada > ferramentas** e pode encontrar **ferramentas do Azure IoT Edge** e **Cloud Explorer para Visual Studio**.

1. Tenha em atenção a versão instalada. Pode comparar essa versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Se tiver uma versão mais antiga do que está disponível no Visual Studio Marketplace, Atualize as suas ferramentas no Visual Studio, conforme mostrado na secção seguinte.

### <a name="update-your-tools"></a>Atualizar as suas ferramentas

1. Na **extensões e atualizações** caixa de diálogo, expanda **atualizações > Visual Studio Marketplace**, selecione **ferramentas do Azure IoT Edge** ou **Cloud Explorer para o elemento Visual Studio** e selecione **atualização**.

1. Após a atualização das ferramentas é transferida, feche o Visual Studio ao acionar que as ferramentas de atualização através do instalador do VSIX.

1. No instalador do, selecione **OK** para iniciar e, em seguida **modificar** para atualizar as ferramentas.

1. Após a atualização estiver concluída, selecione **fechar** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implementado em dispositivos do Azure IoT Edge no IoT Hub do Azure. Em primeiro lugar cria uma solução de Azure IoT Edge e, em seguida, gerar primeiro módulo nessa solução. Cada solução de IoT Edge pode conter mais de um módulo.

1. No Visual Studio de diálogo new project, procure e selecione **do Azure IoT Edge** do projeto e clique em **próxima**. Na janela de configuração do projeto, introduza um nome para o seu projeto e especifique a localização e, em seguida, selecione **criar**. O nome de projeto predefinido é **AzureIoTEdgeApp1**.

   ![Criar novo projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Na **Adicionar aplicação do IoT Edge e o módulo** janela, selecione  **C# módulo** ou **módulo C** e, em seguida, especifique o nome do módulo e o repositório de imagens do módulo. Visual Studio autopopulates o módulo nome com **localhost:5000 / < nome do seu módulo\>** . Substitua-o com as suas próprias informações de registo. Se usar um registo do Docker local para fins de teste, em seguida, **localhost** tudo bem. Se utilizar o Azure Container Registry, em seguida, utilize o servidor de início de sessão a partir das definições do seu registo. O servidor de início de sessão se parece com * **\<nome do registo\>*. azurecr.io**. Apenas substituir a **localhost:5000** parte da cadeia de caracteres para que o resultado final se parece com * *\<* nome do registo *\>.azurecr.io/* \<seu nome de módulo\>***. O nome de módulo predefinido é **IoTEdgeModule1**

   ![Adicionar aplicação e do módulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecione **OK** para criar a solução do Azure IoT Edge com um módulo que utiliza a C# ou C.

Agora tem um **AzureIoTEdgeApp1.Linux.Amd64** projeto ou uma **AzureIoTEdgeApp1.Windows.Amd64** projeto e também uma **IoTEdgeModule1** projeto da sua solução. Cada **AzureIoTEdgeApp1** projeto tem um `deployment.template.json` arquivo, que define os módulos que pretende criar e implementar para a sua solução de IoT Edge e também define as rotas entre módulos. A solução padrão tem um **tempSensor** módulo e uma **IoTEdgeModule1** módulo. O **tempSensor** módulo gera dados simulados para o **IoTEdgeModule1** módulo, enquanto o código de predefinição no **IoTEdgeModule1** módulo diretamente os pipes recebidos mensagens do IoT Hub do Azure.

O **IoTEdgeModule1** projeto é uma aplicação de consola .NET Core 2.1, se for um C# módulo. Ela contém ficheiros Docker necessários, que precisa para o seu dispositivo IoT Edge em execução com um contentor do Windows ou o contentor do Linux. O `module.json` ficheiro descreve os metadados de um módulo. O código de módulo real, que usa o SDK de dispositivo do IoT do Azure como uma dependência, se encontra no `Program.cs` ou `main.c` ficheiro.

## <a name="develop-your-module"></a>Desenvolver o seu módulo

O código de módulo de predefinição que vem com a solução está localizado em **IoTEdgeModule1** > **Program.cs** (para C#) ou **Main** (C). O módulo e o `deployment.template.json` ficheiro estão configurados para que possa criar a solução, enviá-la para o seu registo de contentor e implementá-la para um dispositivo para começar a testar sem tocar em nenhum código. O módulo foi criado para dar entrada de uma origem (neste caso, o **tempSensor** módulo que simula dados) e encaminhá-la para o IoT Hub do Azure.

Quando estiver pronto para personalizar o modelo de módulo com o seu próprio código, utilize o [SDKs do Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos esse endereço tem da chave para soluções de IoT, como segurança, gestão de dispositivos e confiabilidade.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializar iotedgehubdev pela cadeia de ligação do dispositivo IoT Edge

1. Copie a cadeia de ligação de qualquer dispositivo IoT Edge partir **cadeia de ligação primária** no Explorador de nuvem do Visual Studio. Não se esqueça de não copiar a cadeia de ligação de um dispositivo de não-Edge, como o ícone de um dispositivo IoT Edge é diferente do ícone de um dispositivo de não-Edge.

   ![Copie a cadeia de ligação de dispositivo do Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Aceda a **ferramentas** > **ferramentas do Azure IoT Edge** > **configuração IoT Edge simulador**, cole a cadeia de ligação e clique em **OK**.

   ![Abra a janela de cadeia de ligação do conjunto de borda](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Introduza a cadeia de ligação da primeira etapa e, em seguida, selecione **OK**.

> [!NOTE]
> Tem de seguir estes passos apenas uma vez no computador de desenvolvimento, como os resultados são automaticamente aplicadas a todas as soluções do Azure IoT Edge subsequentes. Este procedimento pode ser seguido novamente se tiver de alterar para uma cadeia de ligação diferente.

## <a name="build-and-debug-single-module"></a>Criar e depurar o módulo único

Normalmente, vai querer testar e depurar cada módulo antes de executá-lo dentro de uma solução inteira com vários módulos.

1. Com o botão direito **IoTEdgeModule1** e selecione **Set as StartUp Project** no menu de contexto.

   ![Definir o projeto de arranque](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Prima **F5** ou clique no botão abaixo para executar o módulo; poderá demorar 10&ndash;20 segundos na primeira vez que fazer isso.

   ![Executar o módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Deverá ver uma aplicação de consola .NET Core, iniciar se o módulo foi inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se desenvolver no C#, defina um ponto de interrupção `PipeMessage()` funcionar **Program.cs**; se com C, definir um ponto de interrupção no `InputQueue1Callback()` funcionar **Main**. Em seguida, pode testá-lo, enviando uma mensagem ao executar o seguinte comando numa **Git Bash** ou **WSL Bash** shell. (Não é possível executar o `curl` comando a partir de um PowerShell ou a linha de comandos.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depurar o módulo único](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de interrupção deve ser acionado. Pode ver as variáveis no Visual Studio **locais** janela.

   > [!TIP]
   > Também pode utilizar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens em vez de `curl`.

1. Prima **Ctrl + F5** ou clique no botão Parar para interromper a depuração.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Criar e depurar a solução de IoT Edge com vários módulos

Depois de terminar a desenvolver um módulo único, pode querer executar e depurar uma solução inteira com vários módulos.

1. Adicione um módulo de segundo para a solução clicando com o **AzureIoTEdgeApp1** e selecionando **Add** > **novo módulo do IoT Edge**. É o nome predefinido do módulo do segundo **IoTEdgeModule2** e atuará como outro módulo do pipe.

1. Abra o ficheiro `deployment.template.json` e verá **IoTEdgeModule2** foi adicionado no **módulos** secção. Substitua a **rotas** secção com o seguinte. Se tiver personalizado seus nomes de módulos, certifique-se de que atualiza esses nomes para corresponder.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Com o botão direito **AzureIoTEdgeApp1** e selecione **Set as StartUp Project** no menu de contexto.

1. Criar os seus pontos de interrupção e, em seguida, prima **F5** para executar e depurar vários módulos simultaneamente. Verá várias janelas de aplicação .NET Core de consola que cada janela que representa um módulo diferentes.

   ![Depurar vários módulos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Prima **Ctrl + F5** ou selecione o botão Parar para parar a depuração.

## <a name="build-and-push-images"></a>Criar e enviar imagens

1. Certifique-se **AzureIoTEdgeApp1** é o projeto de arranque. Selecione **depurar** ou **versão** como a configuração para criar aplicativos para as imagens de módulo.

    > [!NOTE]
    > Ao escolher **depurar**, o Visual Studio utiliza `Dockerfile.(amd64|windows-amd64).debug` para criar imagens do Docker. Isto inclui o depurador de linha de comandos do .NET Core VSDBG em sua imagem de contentor ao criá-lo. Para os módulos do IoT Edge prontos para produção, recomendamos que utilize o **lançamento** configuração, que utiliza `Dockerfile.(amd64|windows-amd64)` sem VSDBG.

1. Se estiver a utilizar um registo privado, como o Azure Container Registry, utilize o seguinte comando do Docker para iniciar sessão na mesma. Se estiver a utilizar o registo local, pode [executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se estiver a utilizar um registo privado, como o Azure Container Registry, tem de adicionar as suas informações de início de sessão do registo para as definições de tempo de execução presentes no ficheiro de `deployment.template.json`. Substitua os marcadores de posição pelo seu real ACR nome de utilizador, palavra-passe e registro do nome de administrador.

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

1. Com o botão direito **AzureIoTEdgeApp1** e selecione **compilação e solução de ponta de Push** para criar e enviar a imagem do Docker para cada módulo.

   ![Criar e enviar imagens](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Implementar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com o Cloud Explorer para Visual Studio. Já tem um manifesto de implantação preparado para o seu cenário, o `deployment.json` de ficheiros e tudo o que precisa fazer é selecionar um dispositivo para receber a implementação.

1. Open **Cloud Explorer** ao clicar em **vista** > **Cloud Explorer**. Certifique-se de que iniciou a sessão para o Visual Studio 2019.

1. Na **Cloud Explorer**, expanda a sua subscrição, encontrar o seu IoT Hub do Azure e o dispositivo Azure IoT Edge que pretende implementar.

1. Com o botão direito no dispositivo IoT Edge para criar uma implementação para o mesmo, tem de selecionar o ficheiro de manifesto de implantação sob o `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Não tem de selecionar `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Clique no botão Atualizar para ver os novos módulos em execução juntamente com o **TempSensor** módulo e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

1. Para monitorizar a mensagem D2C, de um dispositivo específico, selecione o dispositivo na lista e, em seguida, clique em **iniciar a monitorização de evento ponto final incorporado** no **ação** janela.

1. Para parar a monitorização de dados, selecione o dispositivo na lista e, em seguida, selecione **parar a monitorização de evento ponto final incorporado** no **ação** janela.

## <a name="next-steps"></a>Passos Seguintes

Para desenvolver módulos personalizados para os seus dispositivos de IoT Edge [compreender e utilizar os SDKs do Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
