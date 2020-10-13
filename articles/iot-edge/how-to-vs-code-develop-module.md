---
title: Desenvolver e depurar módulos para Azure IoT Edge Microsoft Docs
description: Use o Código do Estúdio Visual para desenvolver, construir e depurar um módulo para Azure IoT Edge usando C#, Python, Node.js, Java ou C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 2fcb389736df8bedb2602919e986f7d65e8f3024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296914"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Código do Estúdio Visual para desenvolver e depurar módulos para Azure IoT Edge

Pode transformar a sua lógica de negócio em módulos para Azure IoT Edge. Este artigo mostra-lhe como usar o Código do Estúdio Visual como a principal ferramenta para desenvolver e depurar módulos.

Existem duas formas de depurar módulos escritos em C#, Node.js ou Java no Código do Estúdio Visual: Pode anexar um processo num recipiente de módulos ou lançar o código do módulo no modo de depuração. Para depurar módulos escritos em Python ou C, só pode anexar-se a um processo em recipientes Linux amd64.

Se não está familiarizado com as capacidades de depuração do Código do Estúdio Visual, leia sobre [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Este artigo fornece instruções para desenvolver e depurar módulos em várias línguas para múltiplas arquiteturas. Atualmente, o Visual Studio Code fornece suporte para módulos escritos em C#, C, Python, Node.js e Java. As arquiteturas de dispositivos suportados são X64 e ARM32. Para obter mais informações sobre sistemas operativos apoiados, línguas e arquiteturas, consulte [suporte de linguagem e arquitetura.](module-development.md#language-and-architecture-support)

>[!NOTE]
>O suporte de desenvolvimento e depuragem para dispositivos Linux ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [os módulos Develop and debug ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar um computador ou uma máquina virtual que executa o Windows, macOS ou Linux como máquina de desenvolvimento. Nos computadores Windows pode desenvolver módulos Windows ou Linux. Para desenvolver módulos Windows, utilize um computador Windows que executa a versão 1809/build 17763 ou mais recente. Para desenvolver módulos Linux, utilize um computador Windows que satisfaça os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Instale primeiro o [Código do Estúdio Visual](https://code.visualstudio.com/) e, em seguida, adicione as seguintes extensões:

- [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Extensão do estivador](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Extensão(s) visual Studio específica para o idioma em que está a desenvolver:
  - C#, incluindo funções Azure: [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Extensão python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Pacote de extensão de Java para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Também terá de instalar algumas ferramentas adicionais específicas da linguagem para desenvolver o seu módulo:

- C#, incluindo funções Azure: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) e [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar pacotes Python (normalmente incluídos com a sua instalação Python).

- Node.js: [Node.js. ](https://nodejs.org) Também vai querer instalar o [Yeoman](https://www.npmjs.com/package/yo) e o [Gerador de Módulos Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [Maven](https://maven.apache.org/). Terá de [definir a `JAVA_HOME` variável ambiental](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação JDK.

Para construir e implantar a imagem do módulo, precisa do Docker para construir a imagem do módulo e um registo de contentores para manter a imagem do módulo:

- [Edição Comunitária do Docker](https://docs.docker.com/install/) na sua máquina de desenvolvimento.

- [Registo de contentores Azure](https://docs.microsoft.com/azure/container-registry/) ou [Centro de Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Você pode usar um registro estivador local para fins de protótipo e teste em vez de um registro em nuvem.

A menos que esteja a desenvolver o seu módulo em C, também precisa da [Ferramenta Azure IoT EdgeHub Dev,](https://pypi.org/project/iotedgehubdev/) baseada em Python, para configurar o seu ambiente de desenvolvimento local para depurar, executar e testar a sua solução IoT Edge. Se ainda não o fez, instale [Python (2.7/3.6/3.7) e Pip](https://www.python.org/) e instale o **iotedgehubdev** executando este comando no seu terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Atualmente, iotedgehubdev usa uma biblioteca de estivadores que não é compatível com Python 3.8.
>
> Se tiver várias Python incluindo python pré-instalado 2.7 (por exemplo, em Ubuntu ou macOS), certifique-se de que está a usar o correto `pip` ou para instalar o `pip3` **iotedgehubdev**

Para testar o seu módulo num dispositivo, precisará de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para utilizar o seu computador como dispositivo IoT Edge, siga os passos no arranque rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver a executar o daemon IoT Edge na sua máquina de desenvolvimento, poderá ter de parar o EdgeHub e o EdgeAgent antes de passar ao próximo passo.

## <a name="create-a-new-solution-template"></a>Criar um novo modelo de solução

Os passos a seguir mostram-lhe como criar um módulo IoT Edge na sua linguagem de desenvolvimento preferida (incluindo Funções Azure, escrita em C#) usando o Código do Estúdio Visual e as Ferramentas IoT Azure. Começa-se por criar uma solução e, em seguida, gera o primeiro módulo nessa solução. Cada solução pode conter vários módulos.

1. Selecione **Ver**  >  **Paleta de Comando**.

1. Na paleta de comando, insira e execute o comando **Azure IoT Edge: Nova Solução de Borda IoT**.

   ![Executar nova solução IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Navegue na pasta onde pretende criar a nova solução e, em seguida, **selecione Selecione a pasta**.

1. Insira um nome para a sua solução.

1. Selecione um modelo de módulo para que o seu idioma de desenvolvimento preferido seja o primeiro módulo na solução.

1. Insira um nome para o seu módulo. Escolha um nome único dentro do seu registo de contentores.

1. Forneça o nome do repositório de imagem do módulo. Visual Studio Code autopopula o nome do módulo com **local local:5000/<o nome \> do seu módulo**. Substitua-o pela sua própria informação de registo. Se usares um registo local do Docker para testes, então **o local** está bem. Se utilizar o Registo do Contentor Azure, utilize o servidor de login a partir das definições do seu registo. O servidor de login parece ** _\<registry name\>_ .azurecr.io**. Substitua apenas a parte **local: 5000** da corda para que o resultado final pareça ** \<*registry name*\> .azurecr.io/ _\<your module name\>_ **.

   ![Fornecer repositório de imagens do Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code pega na informação que forneceu, cria uma solução IoT Edge e, em seguida, carrega-as numa nova janela.

Existem quatro itens dentro da solução:

- Uma pasta **.vscode** contém configurações de depurador.

- Uma pasta **de módulos** tem sub-dobradores para cada módulo.  Dentro da pasta para cada módulo existe um ficheiro, **module.jsligado,** que controla a forma como os módulos são construídos e implantados.  Este ficheiro teria de ser modificado para alterar o registo do contentor de implantação do módulo de local local para um registo remoto. Neste momento, só tens um módulo.  Mas pode adicionar mais na paleta de comando com o comando **Azure IoT Edge: Add IoT Edge Module**.

- Um ficheiro **.env** lista as variáveis ambientais. Se o Registo do Contentor Azure for o seu registo, terá nele um nome de utilizador e uma palavra-passe do Registo do Contentor Azure.

  > [!NOTE]
  > O ficheiro ambiente só é criado se fornecer um repositório de imagem para o módulo. Se aceitou os predefinidos locais para testar e depurar localmente, então não precisa de declarar variáveis ambientais.

- Uma **deployment.template.jsno** ficheiro lista o seu novo módulo juntamente com uma amostra **Simulada DesteperatureSensor** que simula dados que pode usar para testes. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [Saiba como utilizar manifestos de implantação para implantar módulos e estabelecer rotas.](module-composition.md)

Para ver como funciona o módulo de temperatura simulado, consulte o [código fonte SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Adicionar módulos adicionais

Para adicionar módulos adicionais à sua solução, executar o comando **Azure IoT Edge: Adicionar módulo de borda IoT** a partir da paleta de comando. Também pode clicar à direita na pasta de **módulos** ou `deployment.template.json` no ficheiro na vista Visual Studio Code Explorer e, em seguida, selecionar Adicionar Módulo de Borda **IoT**.

## <a name="develop-your-module"></a>Desenvolva o seu módulo

O código do módulo predefinido que acompanha a solução está localizado no seguinte local:

- Função Azure (C#): **módulos > * &lt; nome do módulo o &gt; *nome  >  * &lt; &gt; do módulo*.cs**
- C#: **módulos > * &lt; o &gt; nome do módulo* > Program.cs**
- Python: **módulos > * &lt; o nome &gt; do seu módulo* > main.py**
- Node.js: **módulos > * &lt; o nome &gt; do módulo* > app.js**
- Java: **os módulos > * &lt; o &gt; nome* do módulo > src > principal > java > com > edgemodulemodules > App.java**
- C: **módulos > * &lt; nome &gt; do módulo* > main.c**

O módulo e a deployment.template.jsem ficheiro são configurados para que possa construir a solução, empurrá-la para o registo do seu contentor e implantá-la num dispositivo para iniciar os testes sem tocar em nenhum código. O módulo é construído para simplesmente tirar a entrada de uma fonte (neste caso, o módulo SimuladoTemperatureSensor que simula dados) e canalizar para o IoT Hub.

Quando estiver pronto para personalizar o modelo com o seu próprio código, utilize os [SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) para construir módulos que respondam às principais necessidades de soluções IoT, tais como segurança, gestão de dispositivos e fiabilidade.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Depurar um módulo sem recipiente (C#, Node.js, Java)

Se estiver a desenvolver em C#, Node.js ou Java, o seu módulo requer a utilização de um objeto **MóduloClient** no código do módulo predefinido para que possa iniciar, executar e encaminhar mensagens. Também utilizará o canal de entrada padrão **1** para tomar medidas quando o módulo receber mensagens.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar simulador IoT Edge para solução IoT Edge

Na sua máquina de desenvolvimento, pode iniciar um simulador IoT Edge em vez de instalar o daemon de segurança IoT Edge para que possa executar a sua solução IoT Edge.

1. No explorador do dispositivo no lado esquerdo, clique no iD do dispositivo IoT Edge e, em seguida, selecione **Setup IoT Edge Simulator** para iniciar o simulador com a cadeia de ligação do dispositivo.
1. Pode ver que o IoT Edge Simulator foi configurado com sucesso lendo o detalhe de progresso no terminal integrado.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Configurar simulador IoT Edge para uma aplicação de módulo único

Para configurar e iniciar o simulador, executar o comando **Azure IoT Edge: Iniciar O Simulador de Hub IoT Edge para módulo único** a partir da paleta de comando do Código do Estúdio Visual. Quando solicitado, utilize o **valor de entrada1** a partir do código do módulo predefinido (ou o valor equivalente do seu código) como o nome de entrada para a sua aplicação. O comando aciona o **Iotedgehubdev** CLI e, em seguida, inicia o simulador IoT Edge e um recipiente de módulo de utilidade de teste. Pode ver as saídas abaixo no terminal integrado se o simulador tiver sido iniciado no modo módulo único com sucesso. Também pode ver um `curl` comando para ajudar a enviar a mensagem. Irá utilizá-lo mais tarde.

   ![Configurar simulador IoT Edge para uma aplicação de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Pode utilizar a vista do Docker Explorer no Código do Estúdio Visual para ver o estado de funcionamento do módulo.

   ![Estado do módulo do simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O recipiente **edgeHubDev** é o núcleo do simulador IoT Edge local. Pode funcionar na sua máquina de desenvolvimento sem o daemon de segurança IoT Edge e fornece configurações ambientais para a sua app de módulos nativo ou recipientes de módulos. O recipiente **de entrada** expõe AS APIs do REST para ajudar a fazer a ponte de mensagens para o canal de entrada do alvo no seu módulo.

### <a name="debug-module-in-launch-mode"></a>Módulo Debug no modo de lançamento

1. Prepare o seu ambiente para depurar de acordo com os requisitos do seu idioma de desenvolvimento, desaponte um ponto de rutura no seu módulo e selecione a configuração de depuração para usar:
   - **C#**
     - No terminal integrado do Código do Estúdio Visual, altere o diretório para a pasta *** &lt; do nome &gt; *** do módulo e, em seguida, execute o seguinte comando para construir a aplicação .NET Core.

       ```cmd
       dotnet build
       ```

     - Abra o ficheiro `Program.cs` e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a configuração de depurg ** * &lt; o &gt; nome* do módulo Local Debug (.NET Core)** a partir do dropdown.

        > [!NOTE]
        > Se o seu Núcleo .NET `TargetFramework` não for consistente com o seu percurso de `launch.json` programa, terá de atualizar manualmente o caminho do programa `launch.json` para combinar com o ficheiro `TargetFramework` .csproj para que o Código do Estúdio Visual possa lançar este programa com sucesso.

   - **Node.js**
     - No terminal integrado do Código do Estúdio Visual, altere o diretório para a pasta *** &lt; do nome &gt; *** do módulo e, em seguida, execute o seguinte comando para instalar pacotes de nó

       ```cmd
       npm install
       ```

     - Abra o ficheiro `app.js` e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a configuração de depurg ** * &lt; o nome &gt; * do módulo Local Debug (Node.js)** a partir do dropdown.
   - **Java**
     - Abra o ficheiro `App.java` e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a configuração de depurg ** * &lt; o nome &gt; * do módulo Local Debug (Java)** a partir do dropdown.

1. Clique **em Iniciar Debugging** ou prima **F5** para iniciar a sessão de depuragem.

1. No terminal integrado do Código do Estúdio Visual, executar o seguinte comando para enviar uma mensagem **Hello World** para o seu módulo. Este é o comando mostrado em passos anteriores quando configurar o simulador IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se estiver a utilizar o Windows, certifique-se de que a casca do seu terminal integrado visual Studio Code é **Git Bash** ou **WSL Bash**. Não é possível executar o comando a `curl` partir de uma powerShell ou de um pedido de comando.
   > [!TIP]
   > Também pode utilizar [o PostMan](https://www.getpostman.com/) ou outras ferramentas API para enviar mensagens em vez de `curl` .

1. Na vista Visual Studio Code Debug, você verá as variáveis no painel esquerdo.

1. Para parar a sua sessão de depuração, selecione o botão Stop ou prima **Shift + F5**, e depois executar **Azure IoT Edge: Stop IoT Edge Simulator** na paleta de comando para parar o simulador e limpar.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debug em modo de anexação com IoT Edge Simulator (C#, Node.js, Java, Funções Azure)

A sua solução predefinida contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de tubo. O sensor de temperatura simulado envia mensagens para o módulo do tubo e, em seguida, as mensagens são canalizadas para o Hub IoT. Na pasta do módulo que criou, existem vários ficheiros Docker para diferentes tipos de recipientes. Utilize qualquer um dos ficheiros que terminam com a extensão **.debug** para construir o seu módulo para testes.

Atualmente, a depuragem no modo de anexação é suportada apenas da seguinte forma:

- Os módulos C#, incluindo os das Funções Azure, suportam a depuração em recipientes Linux amd64
- Node.js módulos suportam depurações em recipientes Linux amd64 e arm32v7, e recipientes amd64 do Windows
- Os módulos Java suportam a depuragem em recipientes Linux amd64 e arm32v7

> [!TIP]
> Pode alternar entre as opções para a plataforma padrão para a sua solução IoT Edge clicando no item na barra de estado do Código do Estúdio Visual.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar simulador IoT Edge para solução IoT Edge

Na sua máquina de desenvolvimento, pode iniciar um simulador IoT Edge em vez de instalar o daemon de segurança IoT Edge para que possa executar a sua solução IoT Edge.

1. No explorador do dispositivo no lado esquerdo, clique no iD do dispositivo IoT Edge e, em seguida, selecione **Setup IoT Edge Simulator** para iniciar o simulador com a cadeia de ligação do dispositivo.

1. Pode ver que o IoT Edge Simulator foi configurado com sucesso lendo o detalhe de progresso no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Construa e executar recipiente para depurar e depurar em modo de anexação

1. Abra o seu ficheiro de módulos `Program.cs` `app.js` (, , `App.java` ou ) e adicione um ponto de `<your module name>.cs` rutura.

1. Na vista Visual Studio Code Explorer, clique com o botão direito para a `deployment.debug.template.json` sua solução e, em seguida, selecione **a solução Build and Run IoT Edge no Simulador**. Pode ver todos os registos dos contentores do módulo na mesma janela. Também pode navegar para a vista do Docker para ver o estado do contentor.

   ![Variáveis de relógio](media/how-to-vs-code-develop-module/view-log.png)

1. Navegue para a vista Visual Studio Code Debug e selecione o ficheiro de configuração de depurg para o seu módulo. O nome da opção de depurar deve ser semelhante ao ** * &lt; nome &gt; * do seu módulo Remote Debug**

1. Selecione **Iniciar Depuragem** ou prima **F5**. Selecione o processo a que se anexar.

1. Na vista Visual Studio Code Debug, você verá as variáveis no painel esquerdo.

1. Para parar a sessão de depuração, selecione primeiro o botão Stop ou prima **Shift + F5**e, em seguida, selecione **Azure IoT Edge: Stop IoT Edge Simulator** a partir da paleta de comando.

> [!NOTE]
> O exemplo anterior mostra como depurar módulos IoT Edge em recipientes. Adicionou portas expostas às definições do contentor do `createOptions` módulo. Depois de terminar de depurar os seus módulos, recomendamos que remova estas portas expostas para módulos IoT Edge prontos para a produção.
>
> Para módulos escritos em C#, incluindo Funções Azure, este exemplo baseia-se na versão de depurador de `Dockerfile.amd64.debug` , que inclui o debugger de linha de comando .NET Core (VSDBG) na imagem do seu recipiente enquanto o constrói. Depois de depurar os seus módulos C#, recomendamos que utilize diretamente o Dockerfile sem VSDBG para módulos IoT Edge prontos para a produção.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Depurar um módulo com o tempo de execução IoT Edge

Em cada pasta de módulos, existem vários ficheiros Docker para diferentes tipos de recipientes. Utilize qualquer um dos ficheiros que terminam com a extensão **.debug** para construir o seu módulo para testes.

Ao depurar módulos utilizando este método, os seus módulos estão a funcionar em cima do tempo de funcionamento do IoT Edge. O dispositivo IoT Edge e o seu Código de Estúdio Visual podem estar na mesma máquina, ou mais tipicamente, o Código do Estúdio Visual está na máquina de desenvolvimento e o tempo de funcionamento e módulos IoT Edge estão a funcionar noutra máquina física. Para depurar do Código do Estúdio Visual, deve:

- Configurar o seu dispositivo IoT Edge, construir o seu(s) módulo IoT Edge com o **.debug** Dockerfile e, em seguida, implementar para o dispositivo IoT Edge.
- Exponha o IP e a porta do módulo de modo a que o depurador possa ser ligado.
- Atualize o `launch.json` código do estúdio visual para que o Código do Estúdio Visual possa ser ligado ao processo no recipiente na máquina remota. Este ficheiro está localizado na pasta do `.vscode` seu espaço de trabalho e atualiza cada vez que adiciona um novo módulo que suporta a depuragem.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Construa e implemente o seu módulo no dispositivo IoT Edge

1. No Código do Estúdio Visual, abra o `deployment.debug.template.json` ficheiro, que contém a versão depurg das imagens do módulo com os `createOptions` valores adequados definidos.

1. Se estiver a desenvolver o seu módulo em Python, siga estes passos antes de prosseguir:
   - Abra o ficheiro `main.py` e adicione este código após a secção de importação:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adicione a seguinte linha de código única à chamada que pretende depurar:

      ```python
      ptvsd.break_into_debugger()
      ```

     Por exemplo, se quiser depurar a `receive_message_listener` função, inserirá essa linha de código como mostrado abaixo:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Na paleta de comando visual Studio Code:
   1. Executar o comando **Azure IoT Edge: Construir e empurrar a solução IoT Edge**.

   1. Selecione o `deployment.debug.template.json` ficheiro para a sua solução.

1. Na secção **Azure IoT Hub Devices** da vista Visual Studio Code Explorer:
   1. Clique com o botão direito num ID do dispositivo IoT Edge e, em seguida, selecione **Criar Implementação para um único dispositivo**.

      > [!TIP]
      > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Cada dispositivo IoT Edge inclui estes dois módulos.

   1. Navegue na pasta **config** da sua solução, selecione o `deployment.debug.amd64.json` ficheiro e, em seguida, selecione **Select Edge Deployment Manifest**.

Verá a implementação criada com sucesso com um ID de implementação no terminal integrado.

Pode verificar o estado do seu contentor executando o `docker ps` comando no terminal. Se o seu código de estúdio visual e o tempo de execução IoT Edge estiverem a funcionar na mesma máquina, também pode verificar o estado na vista Visual Studio Code Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Expor o IP e a porta do módulo para o depurador

Pode saltar esta secção se os seus módulos estiverem a funcionar na mesma máquina que o Código do Estúdio Visual, uma vez que está a utilizar a localidade local para se ligar ao recipiente e já tem as definições de porta corretas no **.debug** Dockerfile, configurações do contentor do módulo `createOptions` e `launch.json` ficheiro. Se os seus módulos e Código de Estúdio Visual estiverem a funcionar em máquinas separadas, siga os passos para o seu idioma de desenvolvimento.

- **C#, incluindo funções Azure**

  [Configure o canal SSH na sua máquina de desenvolvimento e dispositivo IoT Edge](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) e, em seguida, edite `launch.json` o ficheiro para anexar.

- **Node.js**

  - Certifique-se de que o módulo da máquina a depurar está em funcionamento e pronto para os depurados fixarem e que a porta 9229 está acessível externamente. Pode verificar isto abrindo `http://<target-machine-IP>:9229/json` na máquina de depurar. Este URL deve mostrar informações sobre o módulo Node.js a ser depurado.
  
  - Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual e edite `launch.json` de modo a que o valor de endereço do ** * &lt; &gt; * perfil de Depurador Remoto (Node.js) (ou** ** * &lt; o &gt; nome do módulo* Remote Debug (Node.js no Windows Container)** se o módulo estiver a funcionar como um recipiente Windows) seja o IP da máquina que está a ser depurada.

- **Java**

  - Construa um túnel SSH para a máquina a ser depurada em funcionamento `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual e edite o perfil de ** * &lt; nome &gt; * do módulo Remote Debug (Java)** `launch.json` para que possa ser ligado à máquina-alvo. Para saber mais sobre edição `launch.json` e depurar Java com Código de Estúdio Visual, consulte a secção sobre [a configuração do depurante](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Certifique-se de que a porta 5678 da máquina a depurar está aberta e acessível.

  - No código `ptvsd.enable_attach(('0.0.0.0', 5678))` que inseriu `main.py` anteriormente, altere **0.0.0.0** para o endereço IP da máquina a depurar. Construa, empurre e volte a colocar o seu módulo IoT Edge.

  - Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual e, em seguida, edite `launch.json` de modo a que o valor do perfil de `host` **Debug Remoto (Python) do * &lt; seu módulo &gt; * ** utilize o endereço IP da máquina-alvo em vez de `localhost` .

### <a name="debug-your-module"></a>Depurar o seu módulo

1. Na vista Visual Studio Code Debug, selecione o ficheiro de configuração de depurg para o seu módulo. O nome da opção de depurar deve ser semelhante ao ** * &lt; nome &gt; * do seu módulo Remote Debug**

1. Abra o ficheiro do módulo para o seu idioma de desenvolvimento e adicione um ponto de rutura:

   - **Função Azure (C#)**: Adicione o seu ponto de rutura ao ficheiro `<your module name>.cs` .
   - **C.**: Adicione o seu ponto de rutura ao ficheiro `Program.cs` .
   - **Node.js**: Adicione o seu ponto de rutura ao ficheiro `app.js` .
   - **Java**: Adicione o seu ponto de rutura ao ficheiro `App.java` .
   - **Python**: Adicione o seu ponto de rutura ao ficheiro `main.py` no método de chamada onde adicionou a `ptvsd.break_into_debugger()` linha.
   - **C:** Adicione o seu ponto de rutura ao ficheiro `main.c` .

1. Selecione **Iniciar Debugging** ou selecione **F5**. Selecione o processo a que se anexar.

1. Na vista Visual Studio Code Debug, você verá as variáveis no painel esquerdo.

> [!NOTE]
> O exemplo anterior mostra como depurar módulos IoT Edge em recipientes. Adicionou portas expostas às definições do contentor do `createOptions` módulo. Depois de terminar de depurar os seus módulos, recomendamos que remova estas portas expostas para módulos IoT Edge prontos para a produção.

## <a name="build-and-debug-a-module-remotely"></a>Construa e depure um módulo remotamente

Com as recentes alterações nos motores Docker e Moby para suportar ligações SSH, e uma nova definição em Azure IoT Tools que permite a injeção de configurações ambientais na paleta de comando visual Studio Code e nos terminais Azure IoT Edge, pode agora construir e depurar módulos em dispositivos remotos.

Consulte esta [entrada no blog IoT Developer](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) para obter mais informações e instruções passo a passo.

## <a name="next-steps"></a>Passos seguintes

Depois de ter construído o seu módulo, aprenda a [implantar módulos Azure IoT Edge a partir do Código do Estúdio Visual](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para os seus dispositivos IoT Edge, [compreenda e utilize SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
