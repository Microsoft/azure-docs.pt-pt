---
title: 'Tutorial: desenvolver módulo para dispositivos Linux-Azure IoT Edge'
description: Este tutorial explica como configurar seu computador de desenvolvimento e recursos de nuvem para desenvolver IoT Edge módulos usando contêineres do Linux para dispositivos Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 01ca118348b3a084c97182338bf656da83d52cb4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114053"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: desenvolver módulos IoT Edge para dispositivos Linux

Use Visual Studio Code para desenvolver e implantar o código em dispositivos Linux que executam o IoT Edge. 

Nos artigos de início rápido, você criou um dispositivo IoT Edge usando uma máquina virtual Linux e implantou um módulo pré-compilado do Azure Marketplace. Este tutorial percorre o que é necessário para desenvolver e implantar seu próprio código em um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para todos os outros tutoriais, que entrarão em mais detalhes sobre linguagens de programação específicas ou serviços do Azure. 

Este tutorial usa o exemplo de implantação de um  **C# módulo em um dispositivo Linux**. Este exemplo foi escolhido porque é o cenário de desenvolvedor mais comum para soluções de IoT Edge. Mesmo se você planeja usar um idioma diferente ou implantar um serviço do Azure, este tutorial ainda é útil para aprender sobre as ferramentas e os conceitos de desenvolvimento. Depois de concluir essa introdução ao processo de desenvolvimento, você pode escolher o idioma preferencial ou o serviço do Azure para se aprofundar nos detalhes. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu computador de desenvolvimento.
> * Use as ferramentas de IoT Edge para Visual Studio Code para criar um novo projeto.
> * Compile seu projeto como um contêiner e armazene-o em um registro de contêiner do Azure.
> * Implante seu código em um dispositivo IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IOT Edge*ou, às vezes, apenas *módulo* para curto, é um contêiner que contém código executável. Você pode implantar um ou mais módulos em um dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados de sensores, executar operações de análise de dados ou limpeza de dados ou enviar mensagens para um hub IoT. Para obter mais informações, consulte [entender Azure IOT Edge módulos](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo de IoT Edge de destino em que o módulo será eventualmente implantado. O contêiner que você cria para armazenar o código do módulo deve corresponder ao sistema operacional (SO) do *dispositivo de destino*. Por exemplo, o cenário mais comum é alguém desenvolvendo um módulo em um computador Windows que pretender direcionar um dispositivo Linux em execução IoT Edge. Nesse caso, o sistema operacional do contêiner seria o Linux. Ao percorrer este tutorial, tenha em mente a diferença entre o *sistema operacional do computador de desenvolvimento* e o *sistema operacional do contêiner*.

Este tutorial destina-se a dispositivos Linux que executam o IoT Edge. Você pode usar seu sistema operacional preferido, desde que seu computador de desenvolvimento possa executar contêineres do Linux. É recomendável usar Visual Studio Code para desenvolver para dispositivos Linux, portanto, é isso que este tutorial usará. Você também pode usar o Visual Studio, embora haja diferenças no suporte entre as duas ferramentas.

A tabela a seguir lista os cenários de desenvolvimento com suporte para **contêineres do Linux** no Visual Studio Code e no Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivo Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Idiomas** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas de Azure IoT Edge para o Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Ferramentas de Azure IoT Edge para o Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>O suporte para dispositivos Linux ARM64 está disponível em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [desenvolver e depurar módulos de IOT Edge do ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Este tutorial ensina as etapas de desenvolvimento para Visual Studio Code. Se você preferir usar o Visual Studio, consulte as instruções em [usar o visual studio 2019 para desenvolver e depurar módulos para Azure IOT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento:

* Você pode usar seu próprio computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
  * Verifique se o computador de desenvolvimento dá suporte à virtualização aninhada. Esse recurso é necessário para executar um mecanismo de contêiner, que você instala na próxima seção.
* A maioria dos sistemas operacionais que podem executar um mecanismo de contêiner pode ser usada para desenvolver módulos IoT Edge para dispositivos Linux. Este tutorial usa um computador Windows, mas indica diferenças conhecidas no MacOS ou Linux. 
* Instale o [git](https://git-scm.com/)para extrair pacotes de modelo de módulo posteriormente neste tutorial.  
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

Um dispositivo Azure IoT Edge no Linux:

* Recomendamos que você não execute IoT Edge em seu computador de desenvolvimento, mas use um dispositivo separado. Essa distinção entre o computador de desenvolvimento e IoT Edge dispositivo espelha mais precisamente um cenário de implantação real e ajuda a manter os diferentes conceitos diretamente.
* Se você não tiver um segundo dispositivo disponível, use o artigo de início rápido para criar um dispositivo IoT Edge no Azure com uma [máquina virtual do Linux](quickstart-linux.md).

Recursos da cloud:

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada Standard no Azure. 

## <a name="install-container-engine"></a>Instalar mecanismo de contêiner

IoT Edge módulos são empacotados como contêineres, portanto, você precisa de um mecanismo de contêiner em seu computador de desenvolvimento para criar e gerenciar os contêineres. É recomendável usar o Docker desktop para desenvolvimento devido a seus muitos recursos e popularidade como um mecanismo de contêiner. Com o Docker desktop em um dispositivo Windows, você pode alternar entre contêineres do Linux e contêineres do Windows para que seja possível desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge. 

Use a documentação do Docker para instalar o em seu computador de desenvolvimento: 

* [Instalar o Docker desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Ao instalar o Docker desktop para Windows, você será perguntado se deseja usar contêineres do Linux ou do Windows. Essa decisão pode ser alterada a qualquer momento usando um comutador fácil. Para este tutorial, usamos contêineres do Linux porque nossos módulos estão visando dispositivos Linux. Para obter mais informações, consulte [alternar entre contêineres do Windows e do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instalar o Docker desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [sobre o Docker CE](https://docs.docker.com/install/) para obter informações de instalação em várias plataformas Linux.

## <a name="set-up-vs-code-and-tools"></a>Configurar VS Code e ferramentas

Use as extensões de IoT para Visual Studio Code para desenvolver módulos IoT Edge. Essas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem que você monitore e gerencie dispositivos IoT Edge. Nesta seção, você instala Visual Studio Code e a extensão de IoT e, em seguida, configura sua conta do Azure para gerenciar os recursos do Hub IoT no Visual Studio Code. 

1. Instale o [Visual Studio Code](https://code.visualstudio.com/) no computador de desenvolvimento. 

2. Quando a instalação for concluída, selecione **Exibir** **extensões**de > . 

3. Pesquise por **ferramentas de IOT do Azure**, que é, na verdade, uma coleção de extensões que o ajudarão a interagir com dispositivos IOT e Hub IOT, bem como para desenvolver módulos de IOT Edge. 

4. Selecione **Instalar**. Cada extensão incluída é instalada individualmente. 

5. Quando as extensões terminarem de instalar, abra a paleta de comandos selecionando **exibir** > **paleta de comandos**. 

6. Na paleta de comandos, procure e selecione **Azure: entrar**. Siga os avisos para iniciar sessão na conta do Azure. 

7. Na paleta de comandos novamente, procure e selecione **Hub IOT do Azure: selecione Hub IOT**. Siga os prompts para selecionar sua assinatura do Azure e o Hub IoT. 

7. Abra a seção Gerenciador de Visual Studio Code selecionando o ícone na barra de atividade à esquerda ou selecionando **exibir** > **Explorer**. 

8. Na parte inferior da seção do Explorer, expanda o menu de **dispositivos do Hub IOT do Azure** recolhido. Você deve ver os dispositivos e dispositivos IoT Edge associados ao Hub IoT que você selecionou por meio da paleta de comandos. 

   ![Exibir dispositivos em seu hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão de ferramentas de IoT do Azure fornece modelos de projeto para todas as linguagens de módulo IoT Edge com suporte no Visual Studio Code. Esses modelos têm todos os arquivos e códigos de que você precisa para implantar um módulo de trabalho para testar IoT Edge ou fornecer um ponto de partida para personalizar o modelo com sua própria lógica de negócios. 

Para este tutorial, usamos o modelo C# de módulo porque ele é o modelo usado com mais frequência. 

### <a name="create-a-project-template"></a>Criar um modelo de projeto

Na paleta de comandos do Visual Studio Code, procure e selecione **Azure IOT Edge: nova solução de IOT Edge**. Siga os prompts e use os seguintes valores para criar sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher  **C# módulo**. |
   | Indicar um nome para o módulo | Aceite o **SampleModule**padrão. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente com base no nome que você forneceu na última etapa. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final é semelhante a \<nome do registro\>. azurecr.io/samplemodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Depois que a nova solução for carregada na janela de Visual Studio Code, Reserve um tempo para se familiarizar com os arquivos que criou: 

* A pasta **. vscode** contém um arquivo chamado **Launch. JSON**, que é usado para depurar módulos.
* A pasta **modules** contém uma pasta para cada módulo em sua solução. No momento, isso só deve ser **SampleModule**ou qualquer nome que você tenha fornecido ao módulo. A pasta SampleModule contém o código do programa principal, os metadados do módulo e vários arquivos do Docker. 
* O arquivo **. env** contém as credenciais para o registro de contêiner. Essas credenciais são compartilhadas com seu dispositivo IoT Edge para que ele tenha acesso para efetuar pull das imagens de contêiner. 
* O arquivo **Deployment. Debug. Template. JSON** e o arquivo **Deployment. Template. JSON** são modelos que ajudam a criar um manifesto de implantação. Um *manifesto de implantação* é um arquivo que define exatamente quais módulos você deseja que sejam implantados em um dispositivo, como eles devem ser configurados e como eles podem se comunicar entre si e com a nuvem. Os arquivos de modelo usam ponteiros para alguns valores. Quando você transforma o modelo em um manifesto de implantação real, os ponteiros são substituídos por valores extraídos de outros arquivos de solução. Localize os dois espaços reservados comuns em seu modelo de implantação: 

  * Na seção credenciais do registro, o endereço é preenchido de forma automática a partir das informações fornecidas quando você criou a solução. No entanto, o nome de usuário e a senha referenciam as variáveis armazenadas no arquivo. env. Isso é para segurança, pois o arquivo. env é git ignorado, mas o modelo de implantação não é. 
  * Na seção SampleModule, a imagem de contêiner não é preenchida, mesmo que você tenha fornecido o repositório de imagens quando criou a solução. Esse espaço reservado aponta para o arquivo **Module. JSON** dentro da pasta SampleModule. Se você for para esse arquivo, verá que o campo de imagem contém o repositório, mas também um valor de marca que é composto da versão e da plataforma do contêiner. Você pode iterar a versão manualmente como parte do ciclo de desenvolvimento e selecionar a plataforma do contêiner usando um interruptor que apresentamos mais adiante nesta seção. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça suas credenciais de registro para o agente de IoT Edge

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull das imagens de contêiner para o dispositivo IoT Edge. 

A extensão IoT Edge tenta efetuar pull de suas credenciais de registro de contêiner do Azure e preenchê-las no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. Abra o arquivo **. env** na sua solução de módulo. 
2. Adicione os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve as alterações no arquivo. env. 

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver C# módulos para dispositivos Linux AMD64 e ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque isso afeta como o contêiner é compilado e executado. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IOT Edge: definir a plataforma de destino padrão para a solução de borda**ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

   ![Selecione o ícone de arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

### <a name="review-the-sample-code"></a>Reveja o código de exemplo

O modelo de solução que você criou inclui um código de exemplo para um módulo IoT Edge. Esse módulo de exemplo simplesmente recebe mensagens e as passa. A funcionalidade de pipeline demonstra um conceito importante em IoT Edge, que é como os módulos se comunicam entre si.

Cada módulo pode ter várias filas de *entrada* e *saída* declaradas em seu código. O Hub de IoT Edge em execução no dispositivo roteia as mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar entradas e saídas varia entre linguagens, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre roteamento entre módulos, consulte [declarar rotas](module-composition.md#declare-routes).

O código C# de exemplo que vem com o modelo de projeto usa a [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IOT para .net. 

1. Abra o arquivo **Program.cs** , que está dentro da pasta **modules/SampleModule/** . 

2. No program.cs, localize o método **SetInputMessageHandlerAsync** .

2. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Examine esse método e veja como ele Inicializa uma fila de entrada chamada **entrada1**. 

   ![Localizar o nome de entrada no Construtor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. Em seguida, localize o método **SendEventAsync** .

4. O método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) processa mensagens recebidas e configura uma fila de saída para passá-las. Examine esse método e veja que ele Inicializa uma fila de saída chamada **Saída1**. 

   ![Localize o nome de saída em SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra o arquivo **Deployment. Template. JSON** .

7. Localize a propriedade **modules** do $edgeAgent propriedades desejadas. 

   Deve haver dois módulos listados aqui. O primeiro é **SimulatedTemperatureSensor**, que é incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que você pode usar para testar seus módulos. O segundo é o módulo **SampleModule** que você criou como parte dessa solução.

7. Na parte inferior do arquivo, localize as propriedades desejadas para o módulo **$edgeHub** . 

   Uma das funções do módulo Hub de IoT Edge é rotear mensagens entre todos os módulos em uma implantação. Examine os valores na propriedade **Routes** . A primeira rota, **SampleModuleToIoTHub**, usa um caractere curinga ( **\*** ) para indicar todas as mensagens provenientes de quaisquer filas de saída no módulo SampleModule. Essas mensagens entram em *$upstream*, que é um nome reservado que indica o Hub IOT. A segunda rota, sensorToSampleModule, pega as mensagens provenientes do módulo SimulatedTemperatureSensor e as encaminha para a fila de entrada *entrada1* que você viu inicializado no código SampleModule. 

   ![Examinar rotas em Deployment. Template. JSON](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Você examinou o código do módulo e o modelo de implantação para entender alguns dos principais conceitos de implantação. Agora, você está pronto para criar a imagem de contêiner SampleModule e enviá-la por push para o registro de contêiner. Com a extensão de ferramentas de IoT para Visual Studio Code, essa etapa também gera o manifesto de implantação com base nas informações no arquivo de modelo e nas informações do módulo dos arquivos de solução. 

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça suas credenciais de registro de contêiner para o Docker para que possa enviar por push sua imagem de contêiner para ser armazenada no registro. 

1. Abra o terminal integrado Visual Studio Code selecionando **exibir** > **terminal**.

2. Entre no Docker com as credenciais do registro de contêiner do Azure que você salvou depois de criar o registro. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Criar e enviar por push 

Agora Visual Studio Code tem acesso ao registro de contêiner, portanto, é hora de transformar o código da solução em uma imagem de contêiner. 

1. No Visual Studio Code Explorer, clique com o botão direito do mouse no arquivo **Deployment. Template. JSON** e selecione **criar e enviar por push IOT Edge solução**. 

   ![Compilar e enviar por push módulos de IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, ele executa `docker build` para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar por push o repositório de imagens para o registro de contêiner. 

   Esse processo pode levar vários minutos na primeira vez, mas é mais rápido na próxima vez que você executar os comandos. 

2. Abra o arquivo **Deployment. AMD64. JSON** na pasta de configuração recém-criada. O nome de arquivo reflete a arquitetura de destino, portanto, será diferente se você escolher uma arquitetura diferente.

3. Observe que os dois parâmetros que tinham espaços reservados agora são preenchidos com seus valores adequados. A seção **registryCredentials** tem seu nome de usuário e senha de registro extraídos do arquivo. env. O **SampleModule** tem o repositório de imagens completo com o nome, a versão e a marca de arquitetura do arquivo module. JSON. 

4. Abra o arquivo **Module. JSON** na pasta SampleModule 

5. Altere o número de versão da imagem do módulo. (A versão, não a $schema-Version.) Por exemplo, aumente o número de versão do patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo. 

   >[!TIP]
   >As versões de módulo habilitam o controle de versão e permitem que você teste alterações em um pequeno conjunto de dispositivos antes de implantar atualizações na produção. Se você não incrementar a versão do módulo antes de compilar e enviar por push, você substituirá o repositório no registro de contêiner. 

6. Salve as alterações no arquivo module. JSON.

7. Clique com o botão direito do mouse no arquivo **Deployment. Template. JSON** novamente e selecione **criar e enviar por push IOT Edge solução**.

8. Abra o arquivo **Deployment. AMD64. JSON** novamente. Observe que um novo arquivo não foi criado quando você executou o comando criar e enviar por push novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. A imagem SampleModule agora aponta para a versão 0.0.2 do contêiner. 

9. Para verificar melhor o que o comando Build e Push fez, acesse o [portal do Azure](https://portal.azure.com) e navegue até o registro de contêiner. 

10. No registro de contêiner, selecione **repositórios** e, em seguida, **SampleModule**. Verifique se ambas as versões da imagem foram enviadas por push para o registro.

    ![Exibir as duas versões de imagem no registro de contêiner](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Resolução de problemas

Se você encontrar erros ao compilar e enviar por push a imagem do módulo, isso geralmente terá a ver com a configuração do Docker em seu computador de desenvolvimento. Use as seguintes verificações para examinar sua configuração: 

* Você executou o comando `docker login` usando as credenciais que você copiou do registro de contêiner? Essas credenciais são diferentes das que você usa para entrar no Azure. 
* Seu repositório de contêineres está correto? Ele tem o nome do registro de contêiner correto e o nome correto do módulo? Abra o arquivo **Module. JSON** na pasta SampleModule para verificar. O valor do repositório deve ser semelhante a **\<nome do registro\>. azurecr.Io/SampleModule**. 
* Se você usou um nome diferente do **SampleModule** para seu módulo, esse nome é consistente em toda a solução?
* Seu computador está executando o mesmo tipo de contêiner que você está criando? Este tutorial é para dispositivos Linux IoT Edge, portanto, Visual Studio Code deve dizer **AMD64** ou **arm32v7** na barra lateral, e o Docker desktop deve estar executando contêineres do Linux.  

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Você verificou que as imagens de contêiner criadas são armazenadas no registro de contêiner, portanto, é hora de implantá-las em um dispositivo. Verifique se o dispositivo IoT Edge está em execução. 

1. No Visual Studio Code Explorer, expanda a seção dispositivos do Hub IoT do Azure. 

2. Clique com o botão direito do mouse no dispositivo IoT Edge no qual você deseja implantar e selecione **criar implantação para um único dispositivo**. 

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No explorador de arquivos, navegue até a pasta **config** e selecione o arquivo **Deployment. AMD64. JSON** . 

   Não use o arquivo implantation. Template. JSON, que não tem as credenciais de registro de contêiner ou os valores de imagem do módulo nele. Se você estiver direcionando a um dispositivo Linux ARM32, o manifesto de implantação será nomeado Deployment. arm32v7. JSON. 

4. Expanda os detalhes de seu dispositivo IoT Edge e, em seguida, expanda a lista de **módulos** para seu dispositivo. 

5. Use o botão atualizar para atualizar a exibição do dispositivo até ver os módulos SimulatedTemperatureSensor e SampleModule em execução no seu dispositivo. 

   Pode levar alguns minutos para que ambos os módulos sejam iniciados. O tempo de execução do IoT Edge precisa receber seu novo manifesto de implantação, extrair as imagens do módulo do tempo de execução do contêiner e, em seguida, iniciar cada novo módulo. 

   ![Exibir módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Exibir mensagens do dispositivo

O código SampleModule recebe mensagens por meio de sua fila de entrada e as passa por meio de sua fila de saída. O manifesto de implantação declarava rotas que passaram mensagens para SampleModule de SimulatedTemperatureSensor e, em seguida, mensagens encaminhadas do SampleModule para o Hub IoT. As ferramentas de IoT do Azure para Visual Studio Code permitem que você veja mensagens à medida que elas chegam no Hub IoT de seus dispositivos individuais. 

1. No Visual Studio Code Explorer, clique com o botão direito do mouse no dispositivo IoT Edge que você deseja monitorar e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**. 

2. Assista à janela de saída em Visual Studio Code para ver as mensagens que chegam ao seu hub IoT. 

   ![Exibir o dispositivo de entrada para mensagens na nuvem](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Exibir alterações no dispositivo

Se você quiser ver o que está acontecendo no próprio dispositivo, use os comandos nesta seção para inspecionar o tempo de execução IoT Edge e os módulos em execução no seu dispositivo. 

Os comandos nesta seção são para seu dispositivo IoT Edge, não para seu computador de desenvolvimento. Se você estiver usando uma máquina virtual para seu dispositivo IoT Edge, conecte-se a ela agora. No Azure, acesse a página Visão geral da máquina virtual e selecione **conectar** para acessar a conexão do Secure Shell. 

* Exiba todos os módulos implantados em seu dispositivo e verifique seu status:

   ```bash
   iotedge list
   ```

   Você deve ver quatro módulos: os dois módulos de tempo de execução IoT Edge, SimulatedTemperatureSensor e SampleModule. Todos os quatro devem estar listados como em execução.

* Inspecione os logs de um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Módulos IoT Edge diferenciam maiúsculas de minúsculas. 

   Os logs de SimulatedTemperatureSensor e SampleModule devem mostrar as mensagens que estão processando. O módulo edgeAgent é responsável por iniciar os outros módulos, de modo que seus logs terão informações sobre a implementação do manifesto de implantação. Se qualquer módulo não estiver listado ou não estiver em execução, os logs do edgeAgent provavelmente terão os erros. O módulo edgeHub é responsável por comunicações entre os módulos e o Hub IoT. Se os módulos estiverem em execução, mas as mensagens não chegarem ao seu hub IoT, os logs do edgeHub provavelmente terão os erros. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura Visual Studio Code em seu computador de desenvolvimento e implantou seu primeiro módulo IoT Edge a partir dele. Agora que você conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que ele possa analisar os dados que passam por ele. Escolha seu idioma preferencial: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)