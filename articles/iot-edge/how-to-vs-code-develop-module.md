---
title: Desenvolver e depurar módulos para Azure IoT Edge | Microsoft Docs
description: Use Visual Studio Code para desenvolver, compilar e depurar um módulo para Azure IoT Edge usando C#o, Python, Node. js, Java ou C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 788d5eb049a27500a6518b7be9bb6caedd543637
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276162"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio Code para desenvolver e depurar módulos para Azure IoT Edge

Pode transformar sua lógica de negócio em módulos do Azure IoT Edge. Este artigo mostra como usar Visual Studio Code como a ferramenta principal para desenvolver e depurar módulos.

Há duas maneiras de depurar módulos gravados em C#, Node. js ou Java no Visual Studio Code: você pode anexar um processo em um contêiner de módulo ou iniciar o código do módulo no modo de depuração. Para depurar módulos escritos em Python ou C, você só pode anexar a um processo em contêineres do Linux AMD64.

Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [depuração](https://code.visualstudio.com/Docs/editor/debugging).

Este artigo fornece instruções para o desenvolvimento e a depuração de módulos em vários idiomas para várias arquiteturas. Atualmente, Visual Studio Code fornece suporte para módulos escritos em C#, C, Python, Node. js e Java. As arquiteturas de dispositivo com suporte são x64 e ARM32. Para obter mais informações sobre sistemas operacionais, linguagens e arquiteturas com suporte, consulte [suporte a linguagens e arquitetura](module-development.md#language-and-architecture-support).

>[!NOTE]
>O desenvolvimento e a depuração de suporte para dispositivos Linux ARM64 estão em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [desenvolver e depurar módulos de IOT Edge do ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar um computador ou uma máquina virtual que executa o Windows, o macOS ou o Linux como seu computador de desenvolvimento. Em computadores com Windows, você pode desenvolver módulos do Windows ou Linux. Para desenvolver módulos do Windows, use um computador Windows executando a versão 1809/Build 17763 ou mais recente. Para desenvolver módulos do Linux, use um computador Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Instale [Visual Studio Code](https://code.visualstudio.com/) primeiro e, em seguida, adicione as seguintes extensões:

- [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Extensões do Visual Studio específicas para o idioma em que você está desenvolvendo:
  - C#, incluindo Azure Functions: [ C# extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [pacote de extensão Java para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [c/C++ extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Você também precisará instalar algumas ferramentas adicionais específicas do idioma para desenvolver seu módulo:

- C#, incluindo Azure Functions: [SDK do .NET Core 2,1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) e [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar pacotes do Python (normalmente incluídos na sua instalação do Python).

- Node. js: [node. js](https://nodejs.org). Você também desejará instalar o [Yeoman](https://www.npmjs.com/package/yo) e o [gerador de módulo Azure IOT Edge node. js](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java se Development Kit 10](https://aka.ms/azure-jdks) e [Maven](https://maven.apache.org/). Você precisará [definir a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a instalação do JDK.

Para criar e implantar a imagem do módulo, você precisa do Docker para criar a imagem do módulo e um registro de contêiner para manter a imagem do módulo:

- A [Community Edition do Docker](https://docs.docker.com/install/) em seu computador de desenvolvimento.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Pode utilizar um registo do Docker local para o protótipo e fins de testes em vez de um registo de cloud.

A menos que você esteja desenvolvendo seu módulo em C, também precisará da [ferramenta de desenvolvimento do Azure IOT EdgeHub](https://pypi.org/project/iotedgehubdev/) baseada em Python para configurar seu ambiente de desenvolvimento local para depurar, executar e testar sua solução de IOT Edge. Se você ainda não tiver feito isso, instale o [Python (2.7/3.6/3.7) e o Pip](https://www.python.org/) e, em seguida, instale o **iotedgehubdev** executando esse comando em seu terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
> [!NOTE]
> Atualmente, o iotedgehubdev usa uma biblioteca Docker-py que não é compatível com o Python 3,8.
>
> Se você tiver vários Python, incluindo o Python 2,7 pré-instalado (por exemplo, no Ubuntu ou no macOS), verifique se está usando o `pip` correto ou `pip3` para instalar o **iotedgehubdev**

Para testar seu módulo em um dispositivo, você precisará de um hub IoT ativo com pelo menos um dispositivo IoT Edge. Para usar o computador como um dispositivo IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se você estiver executando IoT Edge daemon em seu computador de desenvolvimento, talvez seja necessário parar EdgeHub e EdgeAgent antes de passar para a próxima etapa.

## <a name="create-a-new-solution-template"></a>Criar um novo modelo de solução

As etapas a seguir mostram como criar um módulo IoT Edge em sua linguagem de desenvolvimento preferida (incluindo Azure Functions, escrito em C#) usando Visual Studio Code e as ferramentas de IOT do Azure. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos.

1. Selecione **View** > **paleta de comandos**.

1. Na paleta de comandos, introduza e execute o comando **do Azure IoT Edge: nova solução do IoT Edge**.

   ![Executar nova solução de IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Navegue até a pasta em que você deseja criar a nova solução e selecione **Selecionar pasta**.

1. Insira um nome para sua solução.

1. Selecione um modelo de módulo para sua linguagem de desenvolvimento preferida para ser o primeiro módulo na solução.

1. Insira um nome para o módulo. Escolha um nome que seja exclusivo no registro de contêiner.

1. Forneça o nome do repositório de imagens do módulo. Visual Studio Code preenche automaticamente o nome do módulo com **localhost: 5000/< o nome do módulo\>** . Substitua-o com as suas próprias informações de registo. Se você usar um registro do Docker local para teste, o **localhost** será bem. Se utilizar o Azure Container Registry, em seguida, utilize o servidor de início de sessão a partir das definições do seu registo. O servidor de logon é semelhante a  **_\<nome do registro\>_ . azurecr.Io**. Substitua apenas a parte **localhost: 5000** da cadeia de caracteres para que o resultado final seja semelhante a **\<*nome do registro*\>. azurecr.Io/ _\<o nome do módulo\>_** .

   ![Fornecer repositório de imagens do Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code Obtém as informações fornecidas, cria uma solução de IoT Edge e a carrega em uma nova janela.

Há quatro itens na solução:

- Uma pasta **. vscode** contém configurações de depuração.

- Uma pasta de **módulos** tem subpastas para cada módulo.  Dentro da pasta para cada módulo há um arquivo, **Module. JSON**, que controla como os módulos são criados e implantados.  Esse arquivo precisaria ser modificado para alterar o registro do contêiner de implantação do módulo do localhost para um registro remoto. Neste ponto, você tem apenas um módulo.  Mas você pode adicionar mais na paleta de comandos com o comando **Azure IOT Edge: adicionar IOT Edge módulo**.

- Um arquivo **. env** lista suas variáveis de ambiente. Se o registro de contêiner do Azure for seu registro, você terá um nome de usuário e senha do registro de contêiner do Azure nele.

  > [!NOTE]
  > O arquivo de ambiente só será criado se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões de localhost para testar e depurar localmente, você não precisará declarar variáveis de ambiente.

- Um arquivo. **Template. JSON de implantação** lista o novo módulo junto com um módulo **SimulatedTemperatureSensor** de exemplo que simula os dados que você pode usar para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md).

## <a name="add-additional-modules"></a>Adicionar módulos adicionais

Para adicionar mais módulos à sua solução, execute o comando **Azure IOT Edge: adicionar IOT Edge módulo** na paleta de comandos. Você também pode clicar com o botão direito do mouse na pasta **modules** ou no arquivo `deployment.template.json` na exibição do Visual Studio Code Explorer e, em seguida, selecionar **Adicionar IOT Edge módulo**.

## <a name="develop-your-module"></a>Desenvolver o seu módulo

O código de módulo padrão que vem com a solução está localizado no seguinte local:

- Azure FunctionsC#(): **módulos  *&lt;> seu módulo&gt;nomear* >  *&lt;seu&gt;módulo nome*. cs**
- C#: **módulos >  *&lt;nome&gt; do módulo* > Program.cs**
- Python: **os módulos > *&lt;nome do módulo&gt;* > Main.py**
- Node. js: **os módulos > *&lt;nome do módulo&gt;* > app. js**
- Java: **modules > *&lt;o nome do módulo&gt;* > src > main > java > com > edgemodulemodules > app. java**
- C: **os módulos > *&lt;nome do módulo&gt;* > Main. c**

O módulo e o ficheiro de deployment.template.json são configuradas para que possa criar a solução, enviá-la para o seu registo de contentor e implementá-la para um dispositivo para começar a testar sem tocar em nenhum código. O módulo é criado para simplesmente pegar a entrada de uma fonte (nesse caso, o módulo SimulatedTemperatureSensor que simula dados) e redirecioná-lo ao Hub IoT.

Quando você estiver pronto para personalizar o modelo com seu próprio código, use os [SDKs do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IOT, como segurança, gerenciamento de dispositivos e confiabilidade.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Depurar um módulo sem um contêiner (C#, Node. js, Java)

Se você estiver desenvolvendo no C#, Node. js ou Java, seu módulo exigirá o uso de um objeto **ModuleClient** no código do módulo padrão para que ele possa iniciar, executar e rotear mensagens. Você também usará o canal de entrada padrão **entrada1** para executar uma ação quando o módulo receber mensagens.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador de IoT Edge para a solução IoT Edge

Em seu computador de desenvolvimento, você pode iniciar um simulador de IoT Edge em vez de instalar o IoT Edge daemon de segurança para poder executar sua solução de IoT Edge.

1. No Gerenciador de dispositivos no lado esquerdo, clique com o botão direito do mouse em seu IoT Edge ID do dispositivo e, em seguida, selecione **configurar IOT Edge Simulator** para iniciar o simulador com a cadeia de conexão do dispositivo.
1. Você pode ver que o simulador de IoT Edge foi configurado com êxito lendo os detalhes de progresso no terminal integrado.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Configurar o simulador de IoT Edge para um aplicativo de módulo único

Para configurar e iniciar o simulador, execute o comando **Azure IOT Edge: iniciar IOT Edge simulador de Hub para um único módulo** na paleta de comandos Visual Studio Code. Quando solicitado, use o valor **entrada1** do código do módulo padrão (ou o valor equivalente do seu código) como o nome de entrada para seu aplicativo. O comando dispara a CLI do **iotedgehubdev** e, em seguida, inicia o simulador de IOT Edge e um contêiner de módulo do utilitário de teste. Você poderá ver as saídas abaixo no terminal integrado se o simulador tiver sido iniciado no modo de módulo único com êxito. Você também pode ver um comando `curl` para ajudar a enviar mensagens por meio do. Irá utilizá-lo mais tarde.

   ![Configurar o simulador de IoT Edge para um aplicativo de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Você pode usar a exibição do Gerenciador do Docker no Visual Studio Code para ver o status de execução do módulo.

   ![Status do módulo de simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O contêiner **edgeHubDev** é o núcleo do simulador de IOT Edge local. Ele pode ser executado em seu computador de desenvolvimento sem o daemon de segurança IoT Edge e fornece configurações de ambiente para seu aplicativo de módulo nativo ou contêineres de módulo. O contêiner de **entrada** expõe as APIs REST para ajudar a ponte de mensagens para o canal de entrada de destino em seu módulo.

### <a name="debug-module-in-launch-mode"></a>Depurar o módulo no modo de inicialização

1. Prepare seu ambiente para depuração de acordo com os requisitos de sua linguagem de desenvolvimento, defina um ponto de interrupção em seu módulo e selecione a configuração de depuração a ser usada:
   - **C#**
     - No terminal integrado Visual Studio Code, altere o diretório para a ***&lt;nome do módulo&gt;*** pasta e, em seguida, execute o comando a seguir para compilar o aplicativo .NET Core.

       ```cmd
       dotnet build
       ```

     - Abra o arquivo `Program.cs` e adicione um ponto de interrupção.

     - Navegue até a exibição de depuração Visual Studio Code selecionando **exibir > depurar**. Selecione a configuração de depuração  ***&lt;o nome do módulo&gt;* depuração local (.NET Core)** na lista suspensa.

        > [!NOTE]
        > Se o `TargetFramework` do .NET Core não estiver consistente com o caminho do programa no `launch.json`, você precisará atualizar manualmente o caminho do programa no `launch.json` para corresponder ao `TargetFramework` no arquivo. csproj para que o Visual Studio Code possa iniciar este programa com êxito.

   - **Node.js**
     - No terminal integrado Visual Studio Code, altere o diretório para a ***&lt;nome do módulo&gt;*** pasta e, em seguida, execute o seguinte comando para instalar pacotes de nó

       ```cmd
       npm install
       ```

     - Abra o arquivo `app.js` e adicione um ponto de interrupção.

     - Navegue até a exibição de depuração Visual Studio Code selecionando **exibir > depurar**. Selecione a configuração de depuração  ***&lt;o nome do módulo&gt;* depuração local (Node. js)** na lista suspensa.
   - **Java**
     - Abra o arquivo `App.java` e adicione um ponto de interrupção.

     - Navegue até a exibição de depuração Visual Studio Code selecionando **exibir > depurar**. Selecione a configuração de depuração  ***&lt;o nome do módulo&gt;* depuração local (Java)** no menu suspenso.

1. Clique em **Iniciar Depuração** ou pressione **F5** para iniciar a sessão de depuração.

1. No terminal integrado Visual Studio Code, execute o comando a seguir para enviar uma mensagem de **Olá, mundo** para o módulo. Esse é o comando mostrado nas etapas anteriores quando você configura o simulador de IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se você estiver usando o Windows, certificando-se de que o Shell de seu terminal de Visual Studio Code integrado é **git bash** ou **WSL bash**. Não é possível executar o comando `curl` de um PowerShell ou prompt de comando.
   > [!TIP]
   > Também pode utilizar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens através de, em vez de `curl`.

1. Na exibição de depuração Visual Studio Code, você verá as variáveis no painel esquerdo.

1. Para interromper a sessão de depuração, selecione o botão parar ou pressione **Shift + F5**e execute **Azure IoT Edge: parar IOT Edge simulador** na paleta de comandos para interromper o simulador e limpá-lo.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Depurar no modo de anexo com o IoT EdgeC#Simulator (, Node. js, Java Azure Functions)

Sua solução padrão contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de pipe. O sensor de temperatura simulada envia mensagens para o módulo de pipe e, em seguida, as mensagens são canalizadas para o Hub IoT. Na pasta do módulo que você criou, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **. Debug** para compilar seu módulo para teste.

Atualmente, a depuração no modo de anexo tem suporte apenas da seguinte maneira:

- C#módulos, incluindo aqueles para Azure Functions, dão suporte à depuração em contêineres do Linux AMD64
- Os módulos do node. js dão suporte à depuração em contêineres do Linux AMD64 e arm32v7 e contêineres do Windows AMD64
- Os módulos Java dão suporte à depuração em contêineres do Linux AMD64 e arm32v7

> [!TIP]
> Você pode alternar entre as opções da plataforma padrão para sua solução de IoT Edge clicando no item na barra de status do Visual Studio Code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador de IoT Edge para a solução IoT Edge

Em seu computador de desenvolvimento, você pode iniciar um simulador de IoT Edge em vez de instalar o IoT Edge daemon de segurança para poder executar sua solução de IoT Edge.

1. No Gerenciador de dispositivos no lado esquerdo, clique com o botão direito do mouse em seu IoT Edge ID do dispositivo e, em seguida, selecione **configurar IOT Edge Simulator** para iniciar o simulador com a cadeia de conexão do dispositivo.

1. Você pode ver que o simulador de IoT Edge foi configurado com êxito lendo os detalhes de progresso no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Compilar e executar o contêiner para depuração e depuração no modo de anexo

1. Abra o arquivo de módulo (`Program.cs`, `app.js`, `App.java`ou `<your module name>.cs`) e adicione um ponto de interrupção.

1. Na exibição do Visual Studio Code Explorer, clique com o botão direito do mouse no arquivo `deployment.debug.template.json` da solução e, em seguida, selecione **Compilar e executar IOT Edge solução no simulador**. Você pode assistir a todos os logs de contêiner de módulo na mesma janela. Você também pode navegar até a exibição do Docker para observar o status do contêiner.

   ![Variáveis de inspeção](media/how-to-vs-code-develop-module/view-log.png)

1. Navegue até o Visual Studio Code exibição de depuração e selecione o arquivo de configuração de depuração para seu módulo. O nome da opção de depuração deve ser semelhante a  ***&lt;nome do módulo&gt;* depuração remota**

1. Selecione **Iniciar Depuração** ou pressione **F5**. Selecione o processo ao qual anexar.

1. Na exibição Visual Studio Code depuração, você verá as variáveis no painel esquerdo.

1. Para interromper a sessão de depuração, primeiro selecione o botão parar ou pressione **Shift + F5**e, em seguida, selecione **Azure IoT Edge: parar IOT Edge simulador** na paleta de comandos.

> [!NOTE]
> O exemplo anterior mostra como depurar IoT Edge módulos em contêineres. Ele adicionou portas expostas ao contêiner do seu módulo `createOptions` configurações. Depois de concluir a depuração dos módulos, recomendamos que você remova essas portas expostas para módulos de IoT Edge prontos para produção.
>
> Para módulos escritos no C#, incluindo Azure functions, este exemplo se baseia na versão de depuração do `Dockerfile.amd64.debug`, que inclui o depurador de linha de comando do .NET Core (VSDBG) na sua imagem de contêiner ao criá-lo. Depois de depurar seus C# módulos, recomendamos que você use diretamente o DOCKERFILE sem VSDBG para módulos de IOT Edge prontos para produção.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Depurar um módulo com o tempo de execução IoT Edge

Em cada pasta de módulo, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **. Debug** para compilar seu módulo para teste.

Ao depurar módulos usando esse método, seus módulos estão em execução na parte superior do tempo de execução de IoT Edge. O dispositivo IoT Edge e seu Visual Studio Code podem estar no mesmo computador, ou mais geralmente, Visual Studio Code está no computador de desenvolvimento e o tempo de execução de IoT Edge e os módulos estão em execução em outro computador físico. Para depurar de Visual Studio Code, você deve:

- Configure seu dispositivo IoT Edge, crie seus módulos de IoT Edge com o **. Debug** Dockerfile e, em seguida, implante no dispositivo IOT Edge.
- Expor o IP e a porta do módulo para que o depurador possa ser anexado.
- Atualize o `launch.json` para que Visual Studio Code possa se conectar ao processo no contêiner no computador remoto. Esse arquivo está localizado na pasta `.vscode` no seu espaço de trabalho e é atualizado sempre que você adiciona um novo módulo que dá suporte à depuração.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Criar e implantar seu módulo no dispositivo IoT Edge

1. No Visual Studio Code, abra o arquivo `deployment.debug.template.json`, que contém a versão de depuração das imagens do módulo com os valores de `createOptions` apropriados definidos.

1. Se você estiver desenvolvendo seu módulo no Python, siga estas etapas antes de continuar:
   - Abra o arquivo `main.py` e adicione este código após a seção de importação:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adicione a seguinte linha de código ao retorno de chamada que você deseja depurar:

      ```python
      ptvsd.break_into_debugger()
      ```

     Por exemplo, se você quiser depurar a função `receive_message_listener`, você deve inserir essa linha de código, conforme mostrado abaixo:

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

1. Na paleta de comandos do Visual Studio Code:
   1. Execute o comando **Azure IOT Edge: criar e enviar por Push IOT Edge solução**.

   1. Selecione o arquivo de `deployment.debug.template.json` para sua solução.

1. Na seção **dispositivos do Hub IOT do Azure** da exibição do Visual Studio Code Explorer:
   1. Clique com o botão direito do mouse em uma IoT Edge ID do dispositivo e selecione **criar implantação para um único dispositivo**.

      > [!TIP]
      > Para confirmar que o dispositivo escolhido é um IoT Edge dispositivo, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Cada dispositivo de IoT Edge inclui esses dois módulos.

   1. Navegue até a pasta de **configuração** da solução, selecione o arquivo `deployment.debug.amd64.json` e selecione o **manifesto de implantação do Edge**.

Você verá a implantação criada com êxito com uma ID de implantação no terminal integrado.

Você pode verificar o status do contêiner executando o comando `docker ps` no terminal. Se seu Visual Studio Code e IoT Edge tempo de execução estiverem em execução no mesmo computador, você também poderá verificar o status na exibição do Docker Visual Studio Code.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Expor o IP e a porta do módulo para o depurador

Você pode ignorar esta seção se seus módulos estiverem em execução no mesmo computador que Visual Studio Code, pois você está usando localhost para anexar ao contêiner e já tem as configurações de porta corretas no arquivo **. Debug** Dockerfile, no contêiner do módulo `createOptions` configurações e `launch.json`. Se os módulos e Visual Studio Code estiverem em execução em computadores separados, siga as etapas para sua linguagem de desenvolvimento.

- **C#, incluindo Azure Functions**

  [Configure o canal ssh no computador de desenvolvimento e IOT Edge dispositivo](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) e, em seguida, edite `launch.json` arquivo a ser anexado.

- **Node.js**

  - Verifique se o módulo no computador a ser depurado está em execução e pronto para anexar os depuradores e se a porta 9229 está acessível externamente. Você pode verificar isso abrindo `http://<target-machine-IP>:9229/json` no computador do depurador. Essa URL deve mostrar informações sobre o módulo node. js a ser depurado.
  
  - Em seu computador de desenvolvimento, abra Visual Studio Code e, em seguida, edite `launch.json` para que o valor de endereço do  ***&lt;o nome do módulo&gt;* perfil de depuração remota (Node. js)** (ou  ***&lt;o nome do módulo&gt;* a depuração remota (Node. js no contêiner do Windows)** se o módulo estiver sendo executado como um contêiner do Windows

- **Java**

  - Crie um túnel SSH para o computador a ser depurado executando `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`.
  
  - No computador de desenvolvimento, abra Visual Studio Code e edite o  ***&lt;nome do módulo&gt;* perfil de depuração remota (Java)** no `launch.json` para que você possa anexá-lo ao computador de destino. Para saber mais sobre como editar `launch.json` e depurar Java com Visual Studio Code, consulte a seção sobre como [Configurar o depurador](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Verifique se a porta 5678 no computador a ser depurado está aberta e acessível.

  - No `ptvsd.enable_attach(('0.0.0.0', 5678))` de código que você inseriu anteriormente em `main.py`, altere **0.0.0.0** para o endereço IP do computador a ser depurado. Compile, envie por push e implante seu módulo IoT Edge novamente.

  - No computador de desenvolvimento, abra Visual Studio Code e, em seguida, edite `launch.json` para que o valor `host` do  ***&lt;seu nome de módulo&gt;* perfil de depuração remota (Python)** use o endereço IP do computador de destino em vez de `localhost`.

### <a name="debug-your-module"></a>Depurar seu módulo

1. Na exibição de depuração Visual Studio Code, selecione o arquivo de configuração de depuração para seu módulo. O nome da opção de depuração deve ser semelhante a  ***&lt;nome do módulo&gt;* depuração remota**

1. Abra o arquivo de módulo para sua linguagem de desenvolvimento e adicione um ponto de interrupção:

   - **Azure function (C#)** : Adicione seu ponto de interrupção ao arquivo `<your module name>.cs`.
   - **C#** : Adicione seu ponto de interrupção ao arquivo `Program.cs`.
   - **Node. js**: Adicione seu ponto de interrupção ao arquivo `app.js`.
   - **Java**: Adicione seu ponto de interrupção ao arquivo `App.java`.
   - **Python**: Adicione seu ponto de interrupção ao arquivo `main.py`no método de retorno de chamada em que você adicionou a linha de `ptvsd.break_into_debugger()`.
   - **C**: Adicione seu ponto de interrupção ao arquivo `main.c`.

1. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual anexar.

1. Na exibição de depuração Visual Studio Code, você verá as variáveis no painel esquerdo.

> [!NOTE]
> O exemplo anterior mostra como depurar IoT Edge módulos em contêineres. Ele adicionou portas expostas ao contêiner do seu módulo `createOptions` configurações. Depois de concluir a depuração dos módulos, recomendamos que você remova essas portas expostas para módulos de IoT Edge prontos para produção.

## <a name="build-and-debug-a-module-remotely"></a>Criar e depurar um módulo remotamente

Com as alterações recentes nos mecanismos Docker e Moby para dar suporte a conexões SSH e uma nova configuração nas ferramentas de IoT do Azure que habilita a injeção de configurações de ambiente na paleta de comandos Visual Studio Code e Azure IoT Edge terminais, agora você pode criar e depurar módulos em dispositivos remotos.

Consulte esta [entrada no blog do desenvolvedor IOT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) para obter mais informações e instruções passo a passo.

## <a name="next-steps"></a>Passos seguintes

Depois de criar seu módulo, saiba como [implantar Azure IOT Edge módulos do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para os seus dispositivos de IoT Edge [compreender e utilizar os SDKs do Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
