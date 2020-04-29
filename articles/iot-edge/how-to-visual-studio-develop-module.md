---
title: Desenvolver e depurar módulos em Estúdio Visual - Azure IoT Edge
description: Utilize o Estúdio Visual com ferramentas Azure IoT para desenvolver um módulo C ou C# IoT Edge e empurrá-lo do seu IoT Hub para um dispositivo IoT, configurado por um manifesto de implantação.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384862"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge

Pode transformar a sua lógica de negócio em módulos para O Edge Azure IoT. Este artigo mostra-lhe como usar o Visual Studio 2019 como a principal ferramenta para desenvolver e depurar módulos.

As Ferramentas de Borda Azure IoT para Estúdio Visual proporcionam os seguintes benefícios:

- Crie, edite, construa, corra e debug Azure IoT Edge soluções e módulos no seu computador de desenvolvimento local.
- Implemente a sua solução Azure IoT Edge para o dispositivo Azure IoT Edge via Azure IoT Hub.
- Codifique os seus módulos Azure IoT em C ou C# ao mesmo tempo que tem todos os benefícios do desenvolvimento do Estúdio Visual.
- Gerencie dispositivos e módulos Azure IoT Edge com UI.

Este artigo mostra-lhe como usar as Ferramentas De Borda Azure IoT para O Estúdio Visual 2019 para desenvolver os seus módulos IoT Edge. Também aprende como implementar o seu projeto no seu dispositivo Azure IoT Edge. Atualmente, o Visual Studio 2019 oferece suporte para módulos escritos em C e C#. As arquiteturas de dispositivos suportados são Windows X64 e Linux X64 ou ARM32. Para obter mais informações sobre sistemas operativos, línguas e arquiteturas suportados, consulte o [suporte à linguagem e arquitetura.](module-development.md#language-and-architecture-support)
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que utiliza um computador ou uma máquina virtual que executa o Windows como a sua máquina de desenvolvimento. Nos computadores Windows, pode desenvolver módulos Windows ou Linux. Para desenvolver módulos Windows, utilize um computador Windows que executa a versão 1809/build 17763 ou mais recente. Para desenvolver módulos Linux, utilize um computador Windows que cumpra os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Porque este artigo usa o Visual Studio 2019 como a principal ferramenta de desenvolvimento, instalar o Visual Studio. Certifique-se de que inclui o desenvolvimento do **Azure** e desenvolvimento desktop com cargas de trabalho **C++** na sua instalação Visual Studio 2019. Pode modificar o [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) para adicionar as cargas de trabalho necessárias.

Depois do Estúdio Visual 2019 estar pronto, também precisa das seguintes ferramentas e componentes:

- Descarregue e [instale ferramentas De borda Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) do mercado do Estúdio Visual para criar um projeto IoT Edge no Visual Studio 2019.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017, por favor, baixe e [instale ferramentas de borda Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do mercado do Visual Studio

- Descarregue e instale a [Docker Community Edition](https://docs.docker.com/install/) na sua máquina de desenvolvimento para construir e executar as imagens do seu módulo. Terá de definir o Docker CE para funcionar no modo de contentor Linux ou no modo de contentor windows.

- Instale o seu ambiente de desenvolvimento local para depurar, executar e testar a sua solução IoT Edge instalando a [Ferramenta Dev EdgeHub Azure IoT](https://pypi.org/project/iotedgehubdev/). Instale [python (2.7/3.6+) e Pip](https://www.python.org/) e, em seguida, instale a embalagem **iotedgehubdev** executando o seguinte comando no seu terminal. Certifique-se de que a versão Azure IoT EdgeHub Dev Tool é superior a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone o repositório e instale o gestor da biblioteca vcpkg e, em seguida, instale o **pacote azure-iot-sdk-c** para windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registo de contentores azure](https://docs.microsoft.com/azure/container-registry/) ou [Centro de Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Você pode usar um registo local do Docker para fins de protótipo e teste em vez de um registo na nuvem.

- Para testar o seu módulo num dispositivo, vai precisar de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para utilizar o computador como um dispositivo IoT Edge, siga os passos no arranque rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver a executar o daemon IoT Edge na sua máquina de desenvolvimento, poderá ter de parar o EdgeHub e o EdgeAgent antes de começar a ser desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verifique a versão das suas ferramentas

1. A partir do menu **Extensões,** selecione **Gerir extensões**. Expanda **ferramentas de > instaladas** e pode encontrar ferramentas de **borda Azure IoT para estúdio visual** e Cloud Explorer para estúdio **visual.**

1. Note a versão instalada. Pode comparar esta versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer,](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019) [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

1. Se a sua versão for mais antiga do que a disponível no Visual Studio Marketplace, atualize as suas ferramentas no Visual Studio, como mostra a seguinte secção.

### <a name="update-your-tools"></a>Atualize as suas ferramentas

1. Na janela **'Gerir extensões',** expandir **atualizações > Visual Studio Marketplace,** selecionar Ferramentas de **Borda Azure IoT** ou **Cloud Explorer para Estúdio Visual** e selecionar **Atualização**.

1. Depois de ser descarregada a atualização das ferramentas, feche o Visual Studio para acionar a atualização das ferramentas utilizando o instalador VSIX.

1. No instalador, selecione **OK** para iniciar e, em seguida, **Modificar** para atualizar as ferramentas.

1. Depois da atualização estar completa, selecione **Close** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto Azure IoT Edge

O modelo de projeto Azure IoT Edge no Estúdio Visual cria um projeto que pode ser implementado para dispositivos Azure IoT Edge em Azure IoT Hub. Primeiro cria-se uma solução Azure IoT Edge e depois gera-se o primeiro módulo nessa solução. Cada solução IoT Edge pode conter mais do que um módulo.

> [!TIP]
> A estrutura do projeto IoT Edge criada pelo Visual Studio não é a mesma que no Visual Studio Code.

1. No Estúdio Visual novo diálogo de projeto, procure e selecione projeto **Azure IoT Edge** e clique **em Next**. Na janela de configuração do projeto, introduza um nome para o seu projeto e especifique a localização e, em seguida, selecione **Criar**. O nome do projeto padrão é **AzureIoTEdgeApp1**.

   ![Criar Novo Projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Na janela **Add IoT Edge Application and Module,** selecione **c# Módulo** ou **Módulo C** e, em seguida, especifique o seu nome de módulo e o repositório de imagem do módulo. O Visual Studio autopovoa o nome do módulo com o **anfitrião local:5000/<o seu nome\>** de módulo . Substitua-o por informações do seu próprio registo. Se usar estivador local para testar, então o **hospedeiro local** está bem. Se utilizar o Registo de Contentores Azure, utilize o servidor de login a partir das definições do registo. O servidor de login parece ** _ \<\>_ o nome do registo .azurecr.io**. Substitua a parte do **hospedeiro local:5000** da cadeia de modo a que o resultado final se pareça ** \<com o *nome*\>do registo .azurecr.io/_\<o seu nome\>_** de módulo . O nome do módulo padrão é **IotEdgeModule1**

   ![Adicionar Aplicação e Módulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecione **OK** para criar a solução Azure IoT Edge com um módulo que utiliza C# ou C.

Agora tem um projeto **AzureIoTEdgeApp1.Linux.Amd64** ou um projeto **AzureIoTEdgeApp1.Windows.Amd64** e também um projeto **IotEdgeModule1** na sua solução. Cada projeto **AzureIoTEdgeApp1** tem um `deployment.template.json` ficheiro, que define os módulos que pretende construir e implementar para a sua solução IoT Edge, e também define as rotas entre módulos. A solução padrão tem um módulo **SimulatedTemperatureSensor** e um módulo **IotEdgeModule1.** O módulo **SimuladoTemperatureSensor** gera dados simulados para o módulo **IotEdgeModule1,** enquanto o código predefinido no módulo **IotEdgeModule1** os tubos receberam diretamente mensagens para o Hub Azure IoT.

Para ver como funciona o sensor de temperatura simulado, veja o [código fonte SimuladoTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

O projeto **IotEdgeModule1** é uma aplicação de consola .NET Core 2.1 se for um módulo C#. Contém ficheiros Docker necessários para o seu dispositivo IoT Edge em funcionamento com um recipiente Windows ou um recipiente Linux. O `module.json` ficheiro descreve os metadados de um módulo. O código real do módulo, que toma o Dispositivo Azure IoT `Program.cs` `main.c` Como dependência, encontra-se no ficheiro ou ficheiro.

## <a name="develop-your-module"></a>Desenvolva o seu módulo

O código de módulo predefinido que vem com a solução está localizado em **IotEdgeModule1** > **Program.cs** (para C#) ou **main.c** (C). O módulo `deployment.template.json` e o ficheiro são configurados para que possa construir a solução, empurrá-la para o registo do seu recipiente e implantá-la num dispositivo para começar a testar sem tocar em nenhum código. O módulo foi construído para retirar a entrada de uma fonte (neste caso, o módulo **SimuladoSensor** de Temperatura que simula dados) e canaliza-o para o Hub Azure IoT.

Quando estiver pronto para personalizar o modelo de módulo com o seu próprio código, utilize os [SDKs Hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) para construir módulos que respondam às necessidades-chave para soluções IoT, como segurança, gestão de dispositivos e fiabilidade.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicialize iotedgehubdev com cadeia de ligação do dispositivo IoT Edge

1. Copie a cadeia de ligação de qualquer dispositivo IoT Edge da **Primary Connection String** no Visual Studio Cloud Explorer. Certifique-se de não copiar a cadeia de ligação de um dispositivo não-Edge, uma vez que o ícone de um dispositivo IoT Edge é diferente do ícone de um dispositivo não-Edge.

   ![Fio de ligação do dispositivo de borda de cópia](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. No menu **Tools,** selecione **Azure IoT Edge Tools** > **Configuração IoT Edge Simulator,** cola a corda de ligação e clica **OK**.

   ![Janela de corda de ligação de borda de conjunto aberto](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Introduza a cadeia de ligação a partir do primeiro passo e, em seguida, selecione **OK**.

> [!NOTE]
> Só precisa de seguir estes passos uma vez no seu computador de desenvolvimento, uma vez que os resultados são automaticamente aplicados a todas as soluções subsequentes do Azure IoT Edge. Este procedimento pode ser seguido novamente se precisar de mudar para uma cadeia de ligação diferente.

## <a name="build-and-debug-single-module"></a>Construir e depurar módulo único

Normalmente, você vai querer testar e depurar cada módulo antes de executá-lo dentro de toda uma solução com vários módulos.

1. No **Solution Explorer,** clique à direita **IotEdgeModule1** e selecione **set como StartUp Project** a partir do menu de contexto.

   ![Definir projeto de arranque](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Prima **F5** ou clique no botão abaixo para executar o módulo; Pode levar 10&ndash;20 segundos na primeira vez que o fizer.

   ![Módulo de execução](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Deverá ver uma aplicação de consola .NET Core começar se o módulo tiver sido inicializado com sucesso.

   ![Execução de módulos](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se desenvolver em C#, detete `PipeMessage()` um ponto de rutura na função em **Program.cs;** se utilizar C, desloque um ponto de rutura na `InputQueue1Callback()` função **principal.c**. Em seguida, pode testá-la enviando uma mensagem executando o seguinte comando numa concha **Git Bash** ou **WSL Bash.** (Não é `curl` possível executar o comando a partir de um powerShell ou de um pedido de comando.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Módulo Único de depuração](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de rutura deve ser acionado. Pode ver variáveis na janela Visual Studio **Locals.**

   > [!TIP]
   > Também pode utilizar o [PostMan](https://www.getpostman.com/) ou outras `curl`ferramentas API para enviar mensagens em vez de .

1. Prima **Ctrl + F5** ou clique no botão stop para parar de depurar.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Construir e depurar solução IoT Edge com vários módulos

Depois de terminar de desenvolver um único módulo, talvez queira correr e depurar uma solução inteira com vários módulos.

1. No **Solution Explorer,** adicione um segundo módulo à solução clicando à direita **AzureIoTEdgeApp1** e selecione **Adicionar** > **novo Módulo de Borda IoT**. O nome padrão do segundo módulo é **IotEdgeModule2** e funcionará como outro módulo de tubo.

1. Abra o `deployment.template.json` ficheiro e verá que o **IotEdgeModule2** foi adicionado na secção de **módulos.** Substitua a secção de **rotas** com a seguinte. Se personalizar os nomes dos seus módulos, certifique-se de atualizar estes nomes para corresponder.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique no **AzureIoTEdgeApp1** e selecione **set como StartUp Project** a partir do menu de contexto.

1. Crie os seus pontos de rutura e, em seguida, pressione **F5** para executar e depurar vários módulos simultaneamente. Deve ver várias janelas de aplicações de consola .NET Core, que cada janela representa um módulo diferente.

   ![Depurar módulos múltiplos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Prima **Ctrl + F5** ou selecione o botão de paragem para parar de depurar.

## <a name="build-and-push-images"></a>Construir e empurrar imagens

1. Certifique-se de que **o AzureIoTEdgeApp1** é o projeto de arranque. Selecione **debug** ou **Desbloqueio** como configuração para construir para as imagens do seu módulo.

    > [!NOTE]
    > Ao escolher **o Debug,** o Visual Studio usa `Dockerfile.(amd64|windows-amd64).debug` para construir imagens do Docker. Isto inclui o desordeiro vsDBG da linha de comando .NET Core na sua imagem de contentor enquanto o constrói. Para módulos IoT Edge prontos para a produção, recomendamos que utilize a configuração **de Lançamento,** que utiliza `Dockerfile.(amd64|windows-amd64)` sem VSDBG.

1. Se estiver a utilizar um registo privado como o Registo de Contentores Azure (ACR), utilize o seguinte comando do Docker para iniciar sessão.  Pode obter o nome de utilizador e palavra-passe na página de chaves de **acesso** do seu registo no portal Azure. Se estiver a usar o registo local, pode [executar um registo local.](https://docs.docker.com/registry/deploying/#run-a-local-registry)

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se estiver a utilizar um registo privado como o Registo de Contentores Azure, tem de adicionar `deployment.template.json`as informações de login do registo às definições de tempo de execução encontradas no ficheiro . Substitua os espaços reservados pelo seu nome de utilizador, senha e nome de registo anteria a nível atual.

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

1. No **Solution Explorer,** clique no **AzureIoTEdgeApp1** e selecione **Módulos de Borda Build e Push IoT** para construir e empurrar a imagem do Docker para cada módulo.

## <a name="deploy-the-solution"></a>Implementar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos utilizando o Cloud Explorer para o Estúdio Visual. Já tem um manifesto de implementação `deployment.json` preparado para o seu cenário, o ficheiro e tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Abra o **Cloud Explorer** clicando em **Ver** > **Cloud Explorer**. Certifique-se de ter entrado no Visual Studio 2019.

1. No **Cloud Explorer,** expanda a sua subscrição, encontre o seu Hub Azure IoT e o dispositivo Azure IoT Edge que pretende implementar.

1. Clique à direita no dispositivo IoT Edge para criar uma implementação para o mesmo. Navegue para o manifesto de implementação configurado para a sua plataforma `deployment.arm32v7.json`localizada na pasta **config** na sua solução Visual Studio, como .

1. Clique no botão de atualização para ver os novos módulos a funcionar juntamente com o módulo **SimuladoTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

1. Para monitorizar a mensagem D2C para um dispositivo específico IoT-Edge, selecione-a no seu hub IoT no **Cloud Explorer** e, em seguida, clique em **Iniciar monitorização do ponto final** de evento incorporado na janela **action.**

1. Para parar de monitorizar os dados, selecione **Stop Monitoring Built-in Event Endpoint** na janela **Action.**

## <a name="next-steps"></a>Passos seguintes

Para desenvolver módulos personalizados para os seus dispositivos IoT Edge, [compreenda e utilize SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
