---
title: Desenvolver e depurar módulos em Estúdio Visual - Azure IoT Edge
description: Utilize o Visual Studio com Ferramentas IoT Azure para desenvolver um módulo IoT Edge C ou C# e empurre-o do seu Hub IoT para um dispositivo IoT, conforme configurado por um manifesto de implantação.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 6e5b5c021eb6a83de9ecfb31757855065b70c290
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103196946"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Pode transformar a sua lógica de negócio em módulos para Azure IoT Edge. Este artigo mostra-lhe como usar o Visual Studio 2019 como a principal ferramenta para desenvolver e depurar módulos.

As ferramentas Azure IoT Edge Tools para Visual Studio proporcionam os seguintes benefícios:

- Crie, edite, construa, corra e depure soluções e módulos Azure IoT Edge no seu computador de desenvolvimento local.
- Implemente a sua solução Azure IoT Edge para o dispositivo Azure IoT Edge via Azure IoT Hub.
- Comente os seus módulos Azure IoT em C ou C# enquanto tem todos os benefícios do desenvolvimento do Visual Studio.
- Gerir dispositivos e módulos Azure IoT Edge com UI.

Este artigo mostra-lhe como usar as Ferramentas de Borda Azure IoT para o Visual Studio 2019 para desenvolver os seus módulos IoT Edge. Também aprende a implementar o seu projeto no seu dispositivo Azure IoT Edge. Atualmente, o Visual Studio 2019 fornece suporte para módulos escritos em C e C#. As arquiteturas suportadas do dispositivo são o Windows X64 e o Linux X64 ou o ARM32. Para obter mais informações sobre sistemas operativos apoiados, línguas e arquiteturas, consulte [suporte de linguagem e arquitetura.](module-development.md#language-and-architecture-support)
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que utilize um computador ou máquina virtual que executa o Windows como máquina de desenvolvimento. Nos computadores Windows, pode desenvolver módulos Windows ou Linux. Para desenvolver módulos Windows, utilize um computador Windows que executa a versão 1809/build 17763 ou mais recente. Para desenvolver módulos Linux, utilize um computador Windows que satisfaça os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Como este artigo utiliza o Visual Studio 2019 como a principal ferramenta de desenvolvimento, instale o Visual Studio. Certifique-se de que inclui o desenvolvimento do **Azure** e o desenvolvimento do Desktop com cargas de trabalho **C++** na sua instalação Visual Studio 2019. Pode [modificar o Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) para adicionar as cargas de trabalho necessárias.

Depois de o seu Visual Studio 2019 estar pronto, também precisa das seguintes ferramentas e componentes:

- Descarregue e instale [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) do mercado do Visual Studio para criar um projeto IoT Edge no Visual Studio 2019.

> [!TIP]
> Se estiver a utilizar o Visual Studio 2017, por favor descarregue e instale [ferramentas de borda Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 a partir do mercado do Estúdio Visual

- Descarregue e instale [a Docker Community Edition](https://docs.docker.com/install/) na sua máquina de desenvolvimento para construir e executar as imagens do módulo. Terá de configurar o Docker CE para funcionar no modo de contentor Linux ou no modo de contentor do Windows.

- Confifiquei o seu ambiente de desenvolvimento local para depurar, executar e testar a sua solução IoT Edge instalando a [Ferramenta Azure IoT EdgeHub Dev](https://pypi.org/project/iotedgehubdev/). Instale [python (2.7/3.6+) e Pip](https://www.python.org/) e, em seguida, instale o pacote **iotedgehubdev** executando o seguinte comando no seu terminal. Certifique-se de que a sua versão Azure IoT EdgeHub Dev Tool é superior a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone o repositório e instale o gestor da biblioteca Vcpkg e, em seguida, instale o **pacote azure-iot-sdk-c** para windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registo de Contentores Azure](../container-registry/index.yml) ou [Centro de Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Você pode usar um registro estivador local para fins de protótipo e teste em vez de um registro em nuvem.

- Para testar o seu módulo num dispositivo, precisará de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para utilizar o seu computador como dispositivo IoT Edge, siga os passos no arranque rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver a executar o daemon IoT Edge na sua máquina de desenvolvimento, poderá ter de parar o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verifique a versão das suas ferramentas

1. A partir do menu **Extensões,** **selecione 'Gerir extensões'.** Expanda **ferramentas de > instaladas** e pode encontrar **ferramentas de borda Azure IoT para estúdio visual** e **Cloud Explorer para estúdio visual.**

1. Note a versão instalada. Pode comparar esta versão com a versão mais recente no Visual Studio Marketplace[(Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

1. Se a sua versão for mais antiga do que a disponível no Visual Studio Marketplace, atualize as suas ferramentas no Visual Studio, como mostrado na secção seguinte.

### <a name="update-your-tools"></a>Atualize as suas ferramentas

1. Na janela **'Gerir extensões',** expandir **atualizações > Visual Studio Marketplace**, selecione **Azure IoT Edge Tools** ou Cloud Explorer para Visual **Studio** e selecione **Update**.

1. Depois de descarregar a atualização das ferramentas, feche o Visual Studio para ativar a atualização de ferramentas utilizando o instalador VSIX.

1. No instalador, selecione **OK** para iniciar e, em seguida, **modifique** para atualizar as ferramentas.

1. Depois de concluída a atualização, selecione **Close** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto Azure IoT Edge

O modelo de projeto Azure IoT Edge no Visual Studio cria um projeto que pode ser implementado para dispositivos Azure IoT Edge em Azure IoT Hub. Primeiro cria-se uma solução Azure IoT Edge e depois gera o primeiro módulo nessa solução. Cada solução IoT Edge pode conter mais de um módulo.

> [!TIP]
> A estrutura do projeto IoT Edge criada pelo Visual Studio não é a mesma que no Visual Studio Code.

1. No Visual Studio, novo diálogo de projeto, procure e selecione o projeto **Azure IoT Edge** e clique em **Next**. Na janela de configuração do projeto, insira um nome para o seu projeto e especifique a localização e, em seguida, selecione **Criar**. O nome do projeto padrão é **AzureIoTEdgeApp1**.

   ![Criar Novo Projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Na janela **add IoT Edge Application and Module,** selecione **módulo C#** ou **Módulo C** e, em seguida, especifique o seu nome do módulo e repositório de imagem do módulo. O Visual Studio autopovoa o nome do módulo com **local:5000/<o nome \> do módulo**. Substitua-o pela sua própria informação de registo. Se usares um registo local do Docker para testes, então **o local** está bem. Se utilizar o Registo do Contentor Azure, utilize o servidor de login a partir das definições do seu registo. O servidor de login parece **_\<registry name\>_ .azurecr.io**. Substitua apenas a parte **local: 5000** da corda para que o resultado final pareça **\<*registry name*\> .azurecr.io/ _\<your module name\>_**. O nome do módulo padrão é **IotEdgeModule1**

   ![Adicionar aplicação e módulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecione **OK** para criar a solução Azure IoT Edge com um módulo que utiliza C# ou C.

Agora tem um projeto **AzureIoTEdgeApp1.Linux.Amd64** ou um projeto **AzureIoTEdgeApp1.Windows.Amd64,** e também um projeto **IotEdgeModule1** na sua solução. Cada projeto **AzureIoTEdgeApp1** tem um `deployment.template.json` ficheiro, que define os módulos que pretende construir e implementar para a sua solução IoT Edge, e também define as rotas entre os módulos. A solução predefinida tem um módulo **SimulaedTemperatureSensor** e um módulo **IotEdgeModule1.** O módulo **SimuladoSteperatureSensor** gera dados simulados para o módulo **IotEdgeModule1,** enquanto o código padrão no módulo **IotEdgeModule1** diretamente tubos recebeu mensagens para Azure IoT Hub.

Para ver como funciona o sensor de temperatura simulado, consulte o [código fonte SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

O projeto **IotEdgeModule1** é uma aplicação de consola .NET Core 2.1 se for um módulo C#. Contém ficheiros Docker necessários para o seu dispositivo IoT Edge em funcionamento com um recipiente Windows ou linux. O `module.json` ficheiro descreve os metadados de um módulo. O código do módulo real, que toma O Azure IoT Device SDK como uma dependência, encontra-se no `Program.cs` `main.c` ou ficheiro.

## <a name="develop-your-module"></a>Desenvolva o seu módulo

O código do módulo predefinido que acompanha a solução está localizado no Programa **IotEdgeModule1.cs**  >   (para C#) ou **principal.c** (C). O módulo e o `deployment.template.json` ficheiro são configurados para que possa construir a solução, empurrá-la para o registo do seu contentor e implantá-la num dispositivo para iniciar os testes sem tocar em nenhum código. O módulo foi construído para obter a entrada de uma fonte (neste caso, o módulo **SimulaçãoTemperatureSensor** que simula dados) e canalizar para o Azure IoT Hub.

Quando estiver pronto para personalizar o modelo do módulo com o seu próprio código, utilize os [SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) para construir módulos que respondam às principais necessidades de soluções IoT, tais como segurança, gestão de dispositivos e fiabilidade.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicialize iotedgehubdev com a cadeia de ligação do dispositivo IoT Edge

1. Copie a cadeia de ligação de qualquer dispositivo IoT Edge da Cadeia de **Ligação Primária** no Explorador de Nuvem de Estúdio Visual. Certifique-se de que não copia a cadeia de ligação de um dispositivo não-Edge, uma vez que o ícone de um dispositivo IoT Edge é diferente do ícone de um dispositivo não-Edge.

   ![Cadeia de ligação do dispositivo de borda de cópia](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. No menu **Ferramentas,** selecione **Azure IoT Edge Tools**  >  **Setup IoT Edge Simulator**, cole a cadeia de ligação e clique EM **OK**.

   ![Abrir janela de cadeia de ligação de borda de definida](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Introduza a cadeia de ligação a partir do primeiro passo e, em seguida, selecione **OK**.

> [!NOTE]
> Só precisa de seguir estes passos uma vez no seu computador de desenvolvimento, uma vez que os resultados são automaticamente aplicados a todas as soluções Azure IoT Edge subsequentes. Este procedimento pode ser novamente seguido se precisar de alterar para uma cadeia de ligação diferente.

## <a name="build-and-debug-single-module"></a>Construir e depurar módulo único

Normalmente, você vai querer testar e depurar cada módulo antes de executá-lo dentro de uma solução inteira com vários módulos.

1. No **Solution Explorer,** clique à direita **IotEdgeModule1** e selecione **set as StartUp Project** a partir do menu de contexto.

   ![Definir Projeto de Arranque](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Prima **F5** ou clique no botão abaixo para executar o módulo; pode levar 10 &ndash; 20 segundos na primeira vez que o fizer.

   ![Módulo de execução](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Deverá ver o arranque de uma aplicação de consola .NET Core se o módulo tiver sido inicializado com sucesso.

   ![Módulo correndo](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se desenvolver em C#, desaponte um ponto de rutura `PipeMessage()` na função no **Programa.cs**; se utilizar C, desaponte um ponto de rutura `InputQueue1Callback()` na função em **geral.c**. Em seguida, pode testá-la enviando uma mensagem executando o seguinte comando numa casca **de Git Bash** ou **WSL Bash.** (Não é possível executar o comando a `curl` partir de um powerShell ou de um pedido de comando.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Módulo Único Debug](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de rutura deve ser acionado. Pode ver variáveis na janela Visual Studio **Locals.**

   > [!TIP]
   > Também pode utilizar [o PostMan](https://www.getpostman.com/) ou outras ferramentas API para enviar mensagens em vez de `curl` .

1. Prima **Ctrl + F5** ou clique no botão stop para parar de depurar.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Construa e depure a solução IoT Edge com vários módulos

Depois de ter terminado de desenvolver um único módulo, talvez queira executar e depurar uma solução inteira com vários módulos.

1. No **Solution Explorer**, adicione um segundo módulo à solução clicando à direita no **AzureIoTEdgeApp1** e selecionando **Adicionar** Novo Módulo de  >  **Borda IoT**. O nome predefinido do segundo módulo é **IotEdgeModule2** e funcionará como outro módulo de tubo.

1. Abra o ficheiro `deployment.template.json` e verá **que iotEdgeModule2** foi adicionado na secção de **módulos.** Substitua a secção **de rotas** pelo seguinte. Se tiver personalizado os nomes do módulo, certifique-se de atualizar estes nomes para corresponder.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique com o botão direito **AzureIoTEdgeApp1** e selecione **set as StartUp Project** a partir do menu de contexto.

1. Crie os seus pontos de rutura e, em seguida, pressione **F5** para executar e depurar vários módulos simultaneamente. Deve ver várias janelas de aplicações de consola .NET Core, que representam cada janela representando um módulo diferente.

   ![Debug Múltiplos Módulos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Prima **Ctrl + F5** ou selecione o botão stop para parar de depurar.

## <a name="build-and-push-images"></a>Construir e impulsionar imagens

1. Certifique-se de que **o AzureIoTEdgeApp1** é o projeto de arranque. Selecione **Debug** ou **Release** como a configuração para construir para as imagens do seu módulo.

    > [!NOTE]
    > Ao escolher o **Debug,** o Visual Studio utiliza `Dockerfile.(amd64|windows-amd64).debug` para construir imagens docker. Isto inclui o depurar linha de comando VSDBG .NET Core na sua imagem de contentor enquanto o constrói. Para módulos IoT Edge prontos para a produção, recomendamos que utilize a configuração **de Desbloqueio,** que utiliza `Dockerfile.(amd64|windows-amd64)` sem VSDBG.

1. Se estiver a utilizar um registo privado como o Registo de Contentores Azure (ACR), utilize o seguinte comando Docker para iniciar seduca.  Pode obter o nome de utilizador e a palavra-passe na página de **teclas de acesso** do seu registo no portal Azure. Se estiver a usar o registo local, pode [executar um registo local.](https://docs.docker.com/registry/deploying/#run-a-local-registry)

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se estiver a utilizar um registo privado como o Registo do Contentor Azure, tem de adicionar as informações de login do seu registo às definições de tempo de execução encontradas no ficheiro `deployment.template.json` . Substitua os espaços reservados pelo seu nome de utilizador, palavra-passe e nome de registo ACR.

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

1. No **Solution Explorer,** clique à direita **AzureIoTEdgeApp1** e selecione **Módulos de Borda De Construção e Pressão para** construir e empurrar a imagem do Docker para cada módulo.

## <a name="deploy-the-solution"></a>Implementar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos utilizando o Cloud Explorer para o Estúdio Visual. Já tem um manifesto de implantação preparado para o seu cenário, o ficheiro e tudo o `deployment.json` que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Open **Cloud Explorer** clicando **view** Cloud  >  **Explorer**. Certifique-se de que iniciou sessão no Visual Studio 2019.

1. No **Cloud Explorer,** expanda a sua subscrição, encontre o seu Azure IoT Hub e o dispositivo Azure IoT Edge que pretende implementar.

1. Clique com o botão direito no dispositivo IoT Edge para criar uma implementação para o mesmo. Navegue para o manifesto de implantação configurado para a sua plataforma localizada na pasta **config** na sua solução Visual Studio, tal como `deployment.arm32v7.json` .

1. Clique no botão de atualização para ver os novos módulos em execução juntamente com o módulo **SimuladoTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

1. Para monitorizar a mensagem D2C para um dispositivo de IoT-Edge específico, selecione-o no seu hub IoT no **Cloud Explorer** e, em seguida, clique em **Iniciar monitorização do ponto final de evento incorporado** na janela **Action.**

1. Para parar de monitorizar os dados, selecione **Stop Monitoring Built-in Event Endpoint** na janela **Action.**

## <a name="next-steps"></a>Passos seguintes

Para desenvolver módulos personalizados para os seus dispositivos IoT Edge, [compreenda e utilize SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).