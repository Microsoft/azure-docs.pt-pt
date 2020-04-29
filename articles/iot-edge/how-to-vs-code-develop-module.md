---
title: Desenvolver e depurar módulos para Azure IoT Edge [ Microsoft Docs
description: Use o Visual Studio Code para desenvolver, construir e depurar um módulo para Azure IoT Edge usando C#, Python, Node.js, Java ou C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944305"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Código do Estúdio Visual para desenvolver e depurar módulos para Azure IoT Edge

Pode transformar a sua lógica de negócio em módulos para O Edge Azure IoT. Este artigo mostra-lhe como usar o Código do Estúdio Visual como a ferramenta principal para desenvolver e depurar módulos.

Existem duas formas de depurar módulos escritos em C#, Node.js ou Java no Código do Estúdio Visual: Pode anexar um processo num recipiente de módulos ou lançar o código do módulo no modo de depuração. Para depurar módulos escritos em Python ou C, só pode ser anexado a um processo em recipientes Linux amd64.

Se não está familiarizado com as capacidades de depuração do Visual Studio Code, leia sobre [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Este artigo fornece instruções para desenvolver e depurar módulos em várias línguas para várias arquiteturas. Atualmente, o Visual Studio Code fornece suporte para módulos escritos em C#, C, Python, Node.js e Java. As arquiteturas de dispositivos suportados são X64 e ARM32. Para obter mais informações sobre sistemas operativos, línguas e arquiteturas suportados, consulte o [suporte à linguagem e arquitetura.](module-development.md#language-and-architecture-support)

>[!NOTE]
>O suporte para desenvolver e depurar dispositivos Arm64 está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para mais informações, consulte [Desenvolver e depurar módulos ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar um computador ou uma máquina virtual que executa windows, macOS ou Linux como a sua máquina de desenvolvimento. Nos computadores Windows pode desenvolver módulos Windows ou Linux. Para desenvolver módulos Windows, utilize um computador Windows que executa a versão 1809/build 17763 ou mais recente. Para desenvolver módulos Linux, utilize um computador Windows que cumpra os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Instale primeiro o [Código do Estúdio Visual](https://code.visualstudio.com/) e adicione as seguintes extensões:

- [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Extensão do Estivador](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Extensão(s) do Estúdio Visual específica da linguagem em que está a desenvolver:
  - C#, incluindo funções Azure: [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Extensão python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Pacote de extensão java para código](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) de estúdio visual
  - C: [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Também terá de instalar algumas ferramentas adicionais específicas para desenvolver o seu módulo:

- C#, incluindo funções Azure: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) e [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar pacotes Python (normalmente incluídos com a sua instalação Python).

- Node.js: [Node.js](https://nodejs.org). Também vai querer instalar o [Yeoman](https://www.npmjs.com/package/yo) e o Gerador de Módulos De Nó [de Borda Azure IoT.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [Maven](https://maven.apache.org/). Terá de [definir a `JAVA_HOME` variável ambiental](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação JDK.

Para construir e implementar a sua imagem de módulo, precisa do Docker para construir a imagem do módulo e um registo de contentores para manter a imagem do módulo:

- [Docker Community Edition](https://docs.docker.com/install/) na sua máquina de desenvolvimento.

- [Registo de contentores azure](https://docs.microsoft.com/azure/container-registry/) ou [centro de Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Você pode usar um registo local do Docker para fins de protótipo e teste em vez de um registo na nuvem.

A menos que esteja a desenvolver o seu módulo em C, também precisa da [ferramenta Azure IoT EdgeHub Dev](https://pypi.org/project/iotedgehubdev/) baseada em Python para configurar o seu ambiente de desenvolvimento local para depurar, executar e testar a sua solução IoT Edge. Se ainda não o fez, instale [python (2.7/3.6/3.7) e Pip e,](https://www.python.org/) em seguida, **instale iotedgehubdev** executando este comando no seu terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Atualmente, iotedgehubdev usa uma biblioteca docker-py que não é compatível com Python 3.8.
>
> Se tiver vários Python incluindo python 2.7 pré-instalado (por exemplo, em Ubuntu `pip` `pip3` ou macOS), certifique-se de que está a usar o correto ou a instalar **iotedgehubdev**

Para testar o seu módulo num dispositivo, vai precisar de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para utilizar o computador como um dispositivo IoT Edge, siga os passos no arranque rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver a executar o daemon IoT Edge na sua máquina de desenvolvimento, poderá ter de parar o EdgeHub e o EdgeAgent antes de passar para o próximo passo.

## <a name="create-a-new-solution-template"></a>Criar um novo modelo de solução

Os seguintes passos mostram-lhe como criar um módulo IoT Edge na sua linguagem de desenvolvimento preferida (incluindo funções Azure, escrita em C#) usando o Código do Estúdio Visual e as Ferramentas Azure IoT. Começa-se por criar uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos.

1. Selecione **Ver** > Paleta de**Comando**.

1. Na paleta de comando, introduza e corra o comando **Azure IoT Edge: Nova Solução de Borda IoT**.

   ![Executar nova solução de borda iot](./media/how-to-develop-csharp-module/new-solution.png)

1. Navegue na pasta onde pretende criar a nova solução e, em seguida, **selecione selecione a pasta .**

1. Insira um nome para a sua solução.

1. Selecione um modelo de módulo para o seu idioma de desenvolvimento preferido para ser o primeiro módulo na solução.

1. Introduza um nome para o seu módulo. Escolha um nome único dentro do seu registo de contentores.

1. Forneça o nome do repositório de imagem do módulo. Visual Studio Code autopovoa o nome do módulo com **localhost:5000/<o seu nome\>** de módulo . Substitua-o por informações do seu próprio registo. Se usar estivador local para testar, então o **hospedeiro local** está bem. Se utilizar o Registo de Contentores Azure, utilize o servidor de login a partir das definições do registo. O servidor de login parece ** _ \<\>_ o nome do registo .azurecr.io**. Substitua a parte do **hospedeiro local:5000** da cadeia de modo a que o resultado final se pareça ** \<com o *nome*\>do registo .azurecr.io/_\<o seu nome\>_** de módulo .

   ![Fornecer repositório de imagens do Docker](./media/how-to-develop-csharp-module/repository.png)

O Visual Studio Code pega na informação que forneceu, cria uma solução IoT Edge e, em seguida, carrega-a numa nova janela.

Existem quatro itens dentro da solução:

- Uma pasta **.vscode** contém configurações de depuração.

- Uma pasta de **módulos** tem subpastas para cada módulo.  Dentro da pasta para cada módulo existe um ficheiro, **module.json,** que controla a forma como os módulos são construídos e implantados.  Este ficheiro teria de ser modificado para alterar o registo do contentor de implantação do módulo do local para um registo remoto. Neste momento, só tem um módulo.  Mas pode adicionar mais na paleta de comando com o comando **Azure IoT Edge: Adicione módulo de borda IoT**.

- Um ficheiro **.env** lista as variáveis ambientais. Se o Registo de Contentores Azure for o seu registo, terá nele um nome de utilizador e uma senha do Registo de Contentores Azure.

  > [!NOTE]
  > O ficheiro ambiente só é criado se fornecer um repositório de imagem para o módulo. Se aceitou os incumprimentos do hospedeiro local para testar e depurar localmente, então não precisa de declarar variáveis ambientais.

- Um ficheiro **deployment.template.json** lista o seu novo módulo juntamente com um módulo **simulado** Desensor de temperatura que simula os dados que pode utilizar para o teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte Saiba como usar manifestos de [implantação para implantar módulos e estabelecer rotas.](module-composition.md)

Para ver como funciona o módulo de temperatura simulado, veja o [código fonte SimuladoTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Adicione módulos adicionais

Para adicionar módulos adicionais à sua solução, execute o comando **Azure IoT Edge: Adicione o Módulo De Borda IoT** da paleta de comando. Também pode clicar na pasta dos `deployment.template.json` **módulos** ou no ficheiro na vista Visual Studio Code Explorer e, em seguida, selecionar Adicionar Módulo De Borda **IoT**.

## <a name="develop-your-module"></a>Desenvolva o seu módulo

O código de módulo predefinido que vem com a solução está localizado no seguinte local:

- Função Azure (C#): **módulos > * &lt;o seu&gt;nome*de módulo > *&lt;&gt;.cs***
- C#: **os módulos > * &lt;o seu nome&gt; * de módulo > Program.cs**
- Python: **os módulos > * &lt;o seu nome&gt; * de módulo > main.py**
- Node.js: **módulos > * &lt;&gt; o seu nome de módulo* > app.js**
- Java: **os módulos > * &lt;o nome&gt; do módulo* > sRC > principal > java > com módulos de borda > > App.java**
- C: **os módulos > * &lt;o&gt; nome do módulo* > main.c**

O módulo e o ficheiro deployment.template.json são configurados para que possa construir a solução, empurrá-la para o seu registo de contentores e implantá-la para um dispositivo para começar a testar sem tocar em nenhum código. O módulo foi construído para simplesmente retirar a entrada de uma fonte (neste caso, o módulo SimuladoSensor de Temperatura que simula dados) e encaná-lo para o Hub IoT.

Quando estiver pronto para personalizar o modelo com o seu próprio código, utilize os [SDKs Do Hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) para construir módulos que respondam às necessidades-chave para soluções IoT, como segurança, gestão de dispositivos e fiabilidade.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Depurar um módulo sem recipiente (C#, Node.js, Java)

Se estiver a desenvolver-se em C#, Node.js ou Java, o seu módulo requer a utilização de um objeto **ModuleClient** no código de módulo predefinido para que possa iniciar, executar e encaminhar mensagens. Também utilizará a entrada do canal de entrada **predefinido1** para tomar medidas quando o módulo receber mensagens.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar simulador IoT Edge para a solução IoT Edge

Na sua máquina de desenvolvimento, pode iniciar um simulador IoT Edge em vez de instalar o daemon de segurança IoT Edge para que possa executar a sua solução IoT Edge.

1. No explorador do dispositivo no lado esquerdo, clique à direita no ID do dispositivo IoT Edge e, em seguida, selecione Configurar o Simulador de **Borda IoT** para iniciar o simulador com a cadeia de ligação do dispositivo.
1. Pode ver que o IoT Edge Simulator foi criado com sucesso lendo os detalhes de progresso no terminal integrado.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Configurar simulador IoT Edge para aplicação de módulo único

Para configurar e iniciar o simulador, execute o comando **Azure IoT Edge: Start IoT Edge Hub Simulator para Módulo Único** a partir da paleta de comando visual Studio Code. Quando solicitado, utilize a entrada de **valor1** do código do módulo predefinido (ou o valor equivalente do seu código) como nome de entrada para a sua aplicação. O comando aciona o **iotedgehubdev** CLI e, em seguida, inicia o simulador IoT Edge e um recipiente de módulo de teste. Pode ver as saídas abaixo no terminal integrado se o simulador tiver sido iniciado no modo de módulo único com sucesso. Também pode ver `curl` um comando para ajudar a enviar mensagem. Irá utilizá-lo mais tarde.

   ![Configurar simulador IoT Edge para aplicação de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Pode utilizar a vista Docker Explorer no Visual Studio Code para ver o estado de funcionamento do módulo.

   ![Estado do módulo simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O recipiente **edgeHubDev** é o núcleo do simulador local IoT Edge. Pode funcionar na sua máquina de desenvolvimento sem o daemon de segurança IoT Edge e fornece configurações ambientais para a sua aplicação de módulos nativoou contentores de módulos. O recipiente de **entrada** expõe ASAP REST para ajudar a ligar mensagens para o canal de entrada do alvo no seu módulo.

### <a name="debug-module-in-launch-mode"></a>Módulo Debug no modo de lançamento

1. Prepare o seu ambiente para depurar de acordo com os requisitos da sua linguagem de desenvolvimento, detetete um ponto de rutura no seu módulo e selecione a configuração de depuração para usar:
   - **C #**
     - No terminal integrado do Código do Estúdio Visual, altere o diretório para a pasta de *** &lt;nome&gt; do módulo*** e, em seguida, execute o seguinte comando para construir a aplicação .NET Core.

       ```cmd
       dotnet build
       ```

     - Abra o `Program.cs` ficheiro e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a ** * &lt;&gt; * ** configuração do depurado do seu nome de módulo Local Debug (.NET Core) a partir do dropdown.

        > [!NOTE]
        > Se o seu `TargetFramework` Núcleo .NET não `launch.json`for consistente com o seu percurso de `launch.json` programa, `TargetFramework` terá de atualizar manualmente o caminho do programa para combinar com o ficheiro .csproj para que o Código do Estúdio Visual possa lançar com sucesso este programa.

   - **Node.js**
     - No terminal integrado do Código do Estúdio Visual, altere o diretório para a pasta de *** &lt;nome&gt; do módulo*** e, em seguida, execute o seguinte comando para instalar pacotes de Nó

       ```cmd
       npm install
       ```

     - Abra o `app.js` ficheiro e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a configuração de depuração ** * &lt;do seu nome&gt; de módulo* Local Debug (Node.js)** a partir do dropdown.
   - **Java**
     - Abra o `App.java` ficheiro e adicione um ponto de rutura.

     - Navegue para a vista Visual Studio Code Debug selecionando **Ver > Debug**. Selecione a ** * &lt;&gt; * ** configuração de depuração do seu nome de módulo Local Debug (Java) a partir do dropdown.

1. **Clique em Iniciar Depuração** ou prima **F5** para iniciar a sessão de depuração.

1. No terminal integrado do Código do Estúdio Visual, execute o seguinte comando para enviar uma mensagem **Hello World** para o seu módulo. Este é o comando mostrado em passos anteriores quando configura o simulador IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se estiver a utilizar o Windows, certifique-se de que a casca do seu terminal integrado visual Studio Code é **Git Bash** ou **WSL Bash**. Não é `curl` possível executar o comando a partir de um powerShell ou de um pedido de comando.
   > [!TIP]
   > Também pode utilizar o [PostMan](https://www.getpostman.com/) ou outras ferramentas API para enviar mensagens em vez de `curl`.

1. Na vista Visual Studio Code Debug, verá as variáveis no painel esquerdo.

1. Para parar a sua sessão de depuração, selecione o botão Stop ou prima **Shift + F5**, e, em seguida, execute **o Azure IoT Edge Simulator: Pare o Simulador de Borda IoT** na paleta de comando para parar o simulador e limpar.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Depuração no modo de fixação com IoT Edge Simulator (C#, Node.js, Java, Funções Azure)

A sua solução padrão contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de tubo. O sensor de temperatura simulado envia mensagens para o módulo do tubo e, em seguida, as mensagens são canalizadas para o Hub IoT. Na pasta do módulo que criou, existem vários ficheiros Docker para diferentes tipos de contentores. Utilize qualquer um dos ficheiros que terminam com a extensão **.debug** para construir o seu módulo para testes.

Atualmente, a depuração no modo anexo é suportada apenas da seguinte forma:

- Módulos C#, incluindo os das Funções Azure, suporte a depuração em recipientes Linux amd64
- Os módulos Node.js suportam a depuração em recipientes Linux amd64 e arm32v7, e recipientes Windows amd64
- Os módulos Java suportam a depuração em recipientes Linux amd64 e arm32v7

> [!TIP]
> Pode trocar entre opções para a plataforma padrão para a sua solução IoT Edge clicando no item na barra de status Code do Estúdio Visual.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar simulador IoT Edge para a solução IoT Edge

Na sua máquina de desenvolvimento, pode iniciar um simulador IoT Edge em vez de instalar o daemon de segurança IoT Edge para que possa executar a sua solução IoT Edge.

1. No explorador do dispositivo no lado esquerdo, clique à direita no ID do dispositivo IoT Edge e, em seguida, selecione Configurar o Simulador de **Borda IoT** para iniciar o simulador com a cadeia de ligação do dispositivo.

1. Pode ver que o IoT Edge Simulator foi criado com sucesso lendo os detalhes de progresso no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Construir e executar recipiente para depurar e depurar no modo de anexação

1. Abra o seu`Program.cs` `app.js`ficheiro `App.java`de `<your module name>.cs`módulos ( , ou ) e adicione um ponto de rutura.

1. Na vista Visual Studio Code Explorer, `deployment.debug.template.json` clique no ficheiro para obter a sua solução e, em seguida, selecione **build and Run IoT Edge solução em Simulator**. Pode observar todos os troncos do recipiente do módulo na mesma janela. Também pode navegar até à vista do Docker para ver o estado do contentor.

   ![Ver Variáveis](media/how-to-vs-code-develop-module/view-log.png)

1. Navegue para a vista Visual Studio Code Debug e selecione o ficheiro de configuração de depuração para o seu módulo. O nome da opção de depuração deve ser semelhante ao ** * &lt;seu nome&gt; de módulo* Remote Debug**

1. Selecione **Iniciar Depuração** ou prima **F5**. Selecione o processo a que se ligar.

1. Na vista Visual Studio Code Debug, você verá as variáveis no painel esquerdo.

1. Para parar a sessão de depuração, primeiro selecione o botão Stop ou prima **Shift + F5**, e, em seguida, selecione **Azure IoT Edge: Pare** o Simulador de Borda IoT da paleta de comando.

> [!NOTE]
> O exemplo anterior mostra como depurar módulos IoT Edge em recipientes. Adicionou portas expostas às definições do recipiente `createOptions` do seu módulo. Depois de terminar de depurar os seus módulos, recomendamos que remova estas portas expostas para módulos IoT Edge prontos para produção.
>
> Para os módulos escritos em C#, incluindo funções Azure, `Dockerfile.amd64.debug`este exemplo baseia-se na versão dedepura de , que inclui o debugger de linha de comando .NET Core (VSDBG) na sua imagem de contentor enquanto o constrói. Depois de depurar os seus módulos C#, recomendamos que utilize diretamente o Dockerfile sem VSDBG para módulos IoT Edge prontos para produção.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Depurar um módulo com o tempo de funcionação do IoT Edge

Em cada pasta de módulos, existem vários ficheiros Docker para diferentes tipos de recipientes. Utilize qualquer um dos ficheiros que terminam com a extensão **.debug** para construir o seu módulo para testes.

Ao depurar módulos utilizando este método, os seus módulos estão a funcionar em cima do tempo de funcionamento do IoT Edge. O dispositivo IoT Edge e o seu Código de Estúdio Visual podem estar na mesma máquina, ou, mais tipicamente, o Visual Studio Code está na máquina de desenvolvimento e o tempo de funcionamento e módulos IoT Edge estão a funcionar noutra máquina física. Para depurar o Código do Estúdio Visual, deve:

- Instale o seu dispositivo IoT Edge, construa o seu módulo IoT Edge com o **.debug** Dockerfile e, em seguida, desloque-se para o dispositivo IoT Edge.
- Exponha o IP e a porta do módulo para que o desbugger possa ser ligado.
- Atualize `launch.json` o Código do Estúdio Visual para que o Código do Estúdio Visual possa anexar-se ao processo no recipiente na máquina remota. Este ficheiro encontra-se localizado na pasta no `.vscode` seu espaço de trabalho e atualiza cada vez que adiciona um novo módulo que suporta a depuração.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Construa e implante o seu módulo para o dispositivo IoT Edge

1. No Visual Studio Code, abra o `deployment.debug.template.json` ficheiro, que contém a `createOptions` versão depurada das imagens do seu módulo com os valores adequados definidos.

1. Se estiver a desenvolver o seu módulo em Python, siga estes passos antes de prosseguir:
   - Abra o `main.py` ficheiro e adicione este código após a secção de importação:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adicione a seguinte linha de código única à chamada que pretende depurar:

      ```python
      ptvsd.break_into_debugger()
      ```

     Por exemplo, se quiser depurar a `receive_message_listener` função, inseriria essa linha de código como mostrado abaixo:

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
   1. Executar o comando **Azure IoT Edge: Construir e Empurrar a solução Edge IoT**.

   1. Selecione o `deployment.debug.template.json` ficheiro para a sua solução.

1. Na secção **dispositivos Azure IoT Hub** da vista Visual Studio Code Explorer:
   1. Clique num ID do dispositivo IoT Edge e, em seguida, **selecione Create Deployment for Single Device**.

      > [!TIP]
      > Para confirmar que o dispositivo que escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos IoT Edge incluem estes dois módulos.

   1. Navegue na pasta **de config** `deployment.debug.amd64.json` da sua solução, selecione o ficheiro e, em seguida, selecione **Select Edge Deployment Manifest**.

Verá a implantação criada com sucesso com um ID de implantação no terminal integrado.

Pode verificar o estado do `docker ps` seu contentor executando o comando no terminal. Se o seu Código de Estúdio Visual e o tempo de funcionamento do IoT Edge estiverem a funcionar na mesma máquina, também pode verificar o estado na vista Visual Studio Code Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Expor o IP e a porta do módulo para o debugger

Pode saltar esta secção se os seus módulos estiverem a funcionar na mesma máquina que o Código do Estúdio Visual, já que está a `createOptions` utilizar `launch.json` o hospedeiro local para se ligar ao recipiente e já tem as definições corretas da porta no **.debug** Dockerfile, as definições do recipiente do módulo e o ficheiro. Se os seus módulos e código de estúdio visual estiverem a funcionar em máquinas separadas, siga os passos para a sua linguagem de desenvolvimento.

- **C#, incluindo funções azure**

  [Configure o canal SSH na sua máquina](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) de desenvolvimento `launch.json` e no dispositivo IoT Edge e, em seguida, edite o ficheiro para anexar.

- **Node.js**

  - Certifique-se de que o módulo da máquina a ser depurado está a funcionar e pronto para os desordeiros se fixarem e que a porta 9229 está acessível externamente. Pode verificar isto `http://<target-machine-IP>:9229/json` abrindo a máquina de desbugger. Este URL deve mostrar informações sobre o módulo Node.js a ser depurado.
  
  - Na sua máquina de desenvolvimento, abra `launch.json` o Código do Estúdio Visual e, em seguida, edite de modo a que o valor de endereço do ** * &lt;seu&gt; nome* de módulo Remote Debug (Node.js)** (ou ** * &lt;o seu nome&gt; de módulo* Remote Debug (Node.js no Windows Container)** se o módulo estiver a funcionar como recipiente do Windows) seja o IP da máquina a ser depurada.

- **Java**

  - Construa um túnel SSH para a `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`máquina para ser depurado correndo .
  
  - Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual `launch.json` e edite o ** * &lt;&gt; * ** perfil do seu módulo Remote Debug (Java) para que possa ligar à máquina-alvo. Para saber mais `launch.json` sobre a edição e depuração de Java com o Visual Studio Code, consulte a secção sobre [configurar o debugger](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Certifique-se de que a porta 5678 da máquina a depurada está aberta e acessível.

  - No código `ptvsd.enable_attach(('0.0.0.0', 5678))` que inseriu `main.py`anteriormente, mude **0.0.0.0** para o endereço IP da máquina a ser depurado. Construa, empurre e implante o módulo IoT Edge novamente.

  - Na sua máquina de desenvolvimento, abra `launch.json` o Código `host` do Estúdio Visual e, em seguida, edite de modo que `localhost`o valor do ** * &lt;seu&gt; nome* de módulo Remote Debug (Python)** utilize o endereço IP da máquina-alvo em vez de .

### <a name="debug-your-module"></a>Depurar o seu módulo

1. Na vista Visual Studio Code Debug, selecione o ficheiro de configuração de depuração para o seu módulo. O nome da opção de depuração deve ser semelhante ao ** * &lt;seu nome&gt; de módulo* Remote Debug**

1. Abra o ficheiro do módulo para a sua linguagem de desenvolvimento e adicione um ponto de rutura:

   - **Função Azure (C#)**: Adicione `<your module name>.cs`o seu ponto de rutura ao ficheiro .
   - **C#**: Adicione o seu `Program.cs`ponto de rutura ao ficheiro .
   - **Node.js**: Adicione o seu `app.js`ponto de rutura ao ficheiro .
   - **Java**: Adicione o seu `App.java`ponto de rutura ao ficheiro .
   - **Python**: Adicione o seu `main.py`ponto de rutura ao `ptvsd.break_into_debugger()` ficheiro no método de chamada onde adicionou a linha.
   - **C:** Adicione o seu `main.c`ponto de rutura ao ficheiro .

1. Selecione **Iniciar depuração** ou selecione **F5**. Selecione o processo a que se ligar.

1. Na vista Visual Studio Code Debug, verá as variáveis no painel esquerdo.

> [!NOTE]
> O exemplo anterior mostra como depurar módulos IoT Edge em recipientes. Adicionou portas expostas às definições do recipiente `createOptions` do seu módulo. Depois de terminar de depurar os seus módulos, recomendamos que remova estas portas expostas para módulos IoT Edge prontos para produção.

## <a name="build-and-debug-a-module-remotely"></a>Construir e depurar um módulo remotamente

Com as recentes alterações nos motores Docker e Moby para suportar as ligações SSH, e uma nova configuração em Ferramentas Azure IoT que permite a injeção de configurações ambientais na paleta de comando do Código do Estúdio Visual e nos terminais Azure IoT Edge, pode agora construir e depurar módulos em dispositivos remotos.

Consulte esta entrada no [blog IoT Developer](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) para obter mais informações e instruções passo a passo.

## <a name="next-steps"></a>Passos seguintes

Depois de ter construído o seu módulo, aprenda a [implementar módulos Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para os seus dispositivos IoT Edge, [compreenda e utilize SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
