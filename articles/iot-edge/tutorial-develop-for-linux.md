---
title: Tutorial - Desenvolver módulo para dispositivos Linux utilizando o Edge Azure IoT
description: Este tutorial passa pela criação da sua máquina de desenvolvimento e recursos em nuvem para desenvolver módulos IoT Edge usando recipientes Linux para dispositivos Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 38ffd4931f318b15f1ac06fcfd742bde8e5c6838
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772959"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: Desenvolver módulos IoT Edge para dispositivos Linux

Utilize o Código do Estúdio Visual para desenvolver e implementar código para dispositivos Linux que executam o IoT Edge.

No arranque rápido, criou um dispositivo IoT Edge utilizando uma máquina virtual Linux e implementou um módulo a partir do Azure Marketplace. Este tutorial passa por desenvolver e implementar o seu próprio código para um dispositivo IoT Edge. Este artigo é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre línguas de programação específicas ou serviços Azure.

Este tutorial usa o exemplo de implantação de um  **C# módulo para um dispositivo Linux**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum para soluções IoT Edge. Mesmo que planeie utilizar uma linguagem diferente ou implementar um serviço Azure, este tutorial ainda é útil para aprender sobre as ferramentas e conceitos de desenvolvimento. Complete esta introdução ao processo de desenvolvimento e, em seguida, escolha o seu serviço de idioma ou Azure preferido para mergulhar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Configure seu computador de desenvolvimento.
> * Utilize as ferramentas IoT Edge para visual studio code para criar um novo projeto.
> * Compile seu projeto como um contêiner e armazene-o em um registro de contêiner do Azure.
> * Implante seu código em um dispositivo IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IoT Edge,* ou às vezes apenas *módulo* para abreviar, é um recipiente com código executável. Você pode implantar um ou mais módulos em um dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados a partir de sensores, limpar e analisar dados ou enviar mensagens para um hub IoT. Para obter mais informações, consulte [entender Azure IOT Edge módulos](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo de IoT Edge de destino em que o módulo será eventualmente implantado. O contêiner que você cria para armazenar o código do módulo deve corresponder ao sistema operacional (SO) do *dispositivo de destino*. Por exemplo, o cenário mais comum é alguém desenvolver um módulo num computador Windows que pretenda direcionar um dispositivo Linux em execução IoT Edge. Nesse caso, o sistema operativo do contentor seria o Linux. Ao passar por este tutorial, lembre-se da diferença entre a máquina de *desenvolvimento OS* e o *osrecipiente .*

Este tutorial tem como alvo dispositivos Linux que executam o IoT Edge. Pode utilizar o seu sistema operativo preferido desde que a sua máquina de desenvolvimento utilize os recipientes Linux. Recomendamos a utilização do Visual Studio Code para desenvolver dispositivos Linux, por isso é isso que este tutorial vai usar. Também pode utilizar o Visual Studio, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento suportados para **contentores Linux** em Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivo sinuoso** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Idiomas** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas de Azure IoT Edge para o Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Ferramentas de Azure IoT Edge para o Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>O suporte para dispositivos Arm64 Linux está disponível em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [desenvolver e depurar módulos de IOT Edge do ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Este tutorial ensina os passos de desenvolvimento do Código do Estúdio Visual. Se preferir utilizar o Visual Studio, consulte as instruções do [Use Visual Studio 2019 para desenvolver e depurar módulos para O Edge Azure IoT](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento:

* Você pode usar seu próprio computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
  * Verifique se o computador de desenvolvimento dá suporte à virtualização aninhada. Esse recurso é necessário para executar um mecanismo de contêiner, que você instala na próxima seção.
* A maioria dos sistemas operativos que podem funcionar um motor de contentores pode ser usado para desenvolver módulos IoT Edge para dispositivos Linux. Este tutorial utiliza um computador Windows, mas aponta diferenças conhecidas no MacOS ou no Linux.
* Instale [git,](https://git-scm.com/)para puxar pacotes de modelo seleções mais tarde neste tutorial.  
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

Um dispositivo Azure IoT Edge em Linux:

* Recomendamos que você não execute IoT Edge em seu computador de desenvolvimento, mas use um dispositivo separado. Essa distinção entre o computador de desenvolvimento e IoT Edge dispositivo espelha mais precisamente um cenário de implantação real e ajuda a manter os diferentes conceitos diretamente.
* Se não tiver um segundo dispositivo disponível, utilize o artigo quickstart para criar um dispositivo IoT Edge em Azure com uma [máquina virtual Linux](quickstart-linux.md).

Recursos da cloud:

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada Standard no Azure.

## <a name="install-container-engine"></a>Instalar mecanismo de contêiner

Os módulos IoT Edge são embalados como recipientes, por isso precisa de um motor de contentor na sua máquina de desenvolvimento para construí-los e geri-los. Recomendamos o Docker Desktop para o desenvolvimento devido ao seu suporte de funcionalidades e popularidade. O Docker Desktop no Windows permite-lhe alternar entre os contentores Linux e windows para que possa desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Use a documentação do Docker para instalar o em seu computador de desenvolvimento:

* [Instale o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Ao instalar o Docker desktop para Windows, você será perguntado se deseja usar contêineres do Linux ou do Windows. Esta decisão pode ser alterada a qualquer momento utilizando um interruptor fácil. Para este tutorial, usamos recipientes Linux porque os nossos módulos estão direcionados para dispositivos Linux. Para obter mais informações, consulte [alternar entre contêineres do Windows e do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instale o Docker Desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [sobre docker CE](https://docs.docker.com/install/) para informações de instalação em várias plataformas Linux.

## <a name="set-up-vs-code-and-tools"></a>Configurar código vs e ferramentas

Utilize as extensões IoT para o Visual Studio Code para desenvolver módulos IoT Edge. Essas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem que você monitore e gerencie dispositivos IoT Edge. Nesta secção, instala o Visual Studio Code e a extensão IoT e, em seguida, configura a sua conta Azure para gerir os recursos do IoT Hub a partir do Código do Estúdio Visual.

1. Instale [o Código do Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento.

2. Uma vez terminada a instalação, selecione **Ver** > **extensões**.

3. Procure **ferramentas Azure IoT**, que na verdade é uma coleção de extensões que o ajudam a interagir com dispositivos IoT Hub e IoT, bem como desenvolver módulos IoT Edge.

4. Selecione **Instalar**. Cada extensão incluída instala-se individualmente.

5. Quando as extensões estiverem prontas, abra a paleta de comando selecionando a Paleta de **Comando** ** > Vista**  .

6. Na paleta de comando, procure e selecione **Azure: Inscreva-se**. Siga os avisos para iniciar sessão na conta do Azure.

7. Na paleta de comando novamente, procure e selecione **Azure IoT Hub: Selecione IoT Hub**. Siga as instruções para selecionar a sua subscrição Azure e o seu hub IoT.

8. Abra a secção exploradora do Visual Studio Code selecionando o ícone na barra de atividade à esquerda, ou selecionando **Ver** > **Explorer**.

9. Na parte inferior da secção exploradora, expanda o menu de **dispositivos Hub Azure IoT** em colapso. Deve ver os dispositivos e dispositivos IoT Edge associados ao hub IoT que selecionou através da paleta de comando.

   ![Ver dispositivos no seu hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão Azure IoT Tools fornece modelos de projeto para todos os idiomas suportados do módulo IoT Edge no Código do Estúdio Visual. Esses modelos têm todos os arquivos e códigos de que você precisa para implantar um módulo de trabalho para testar IoT Edge ou fornecer um ponto de partida para personalizar o modelo com sua própria lógica de negócios.

Para este tutorial, C# usamos o modelo do módulo porque é o modelo mais utilizado.

### <a name="create-a-project-template"></a>Criar um modelo de projeto

Na paleta de comando visual Studio Code, procure e **selecione Azure IoT Edge: New IoT Edge Solution**. Siga as instruções e utilize os seguintes valores para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher  **C# módulo**. |
   | Indicar um nome para o módulo | Aceite o Módulo de **Amostra**padrão . |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente com base no nome que você forneceu na última etapa. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final parece \<nome de registo\>.azurecr.io/samplemodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Assim que a sua nova solução estiver na janela Do Código do Estúdio Visual, tenha um momento para se familiarizar com os ficheiros que criou:

* A pasta **.vscode** contém um ficheiro chamado **launch.json,** que é usado para depurar módulos.
* A pasta dos **módulos** contém uma pasta para cada módulo na sua solução. Neste momento, isso só deve ser **SampleModule**, ou qualquer nome que tenha dado ao módulo. A pasta SampleModule contém o código principal do programa, os metadados do módulo e vários ficheiros Docker.
* O ficheiro **.env** contém as credenciais do seu registo de contentores. Essas credenciais são compartilhadas com seu dispositivo IoT Edge para que ele tenha acesso para efetuar pull das imagens de contêiner.
* O ficheiro **implement.debug.template.json** e o ficheiro **de implementação.template.json** são modelos que o ajudam a criar um manifesto de implantação. Um *manifesto de implantação* é um arquivo que define exatamente quais módulos você deseja que sejam implantados em um dispositivo, como eles devem ser configurados e como eles podem se comunicar entre si e com a nuvem. Os ficheiros do modelo usam ponteiros para alguns valores. Quando transforma o modelo num verdadeiro manifesto de implantação, os ponteiros são substituídos por valores retirados de outros ficheiros de soluções. Localize os dois espaços reservados comuns no seu modelo de implementação:

  * Na seção credenciais do registro, o endereço é preenchido de forma automática a partir das informações fornecidas quando você criou a solução. No entanto, o nome de utilizador e a palavra-passe referem as variáveis armazenadas no ficheiro .env. Esta configuração é para a segurança, uma vez que o ficheiro .env é ignorado, mas o modelo de implementação não é.
  * Na secção SampleModule, a imagem do recipiente não é preenchida, apesar de ter fornecido o repositório de imagem quando criou a solução. Este espaço reservado aponta para o ficheiro **module.json** dentro da pasta SampleModule. Se for a esse ficheiro, verá que o campo de imagem contém o repositório, mas também um valor de etiqueta que é composto pela versão e pela plataforma do contentor. Pode iterar a versão manualmente como parte do seu ciclo de desenvolvimento e seleciona a plataforma do recipiente utilizando um comutador que introduzimos mais tarde nesta secção.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça suas credenciais de registro para o agente de IoT Edge

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O tempo de execução precisa destas credenciais para puxar as imagens do seu recipiente para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as suas credenciais de registo de contentores do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. Abra o arquivo **. env** na sua solução de módulo.
2. Adicione o nome de **utilizador** e os valores de **palavra-passe** que copiou do registo de contentores Azure.
3. Salve as alterações no arquivo. env.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, o Visual C# Studio Code pode desenvolver módulos para dispositivos Linux AMD64 e ARM32v7. Você precisa selecionar qual a arquitetura que você está alvo com cada solução, porque isso afeta a forma como o recipiente é construído e executado. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IOT Edge: definir a plataforma de destino padrão para a solução de borda**ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

   ![Selecione ícone de arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão.

### <a name="review-the-sample-code"></a>Reveja o código de exemplo

O modelo de solução que você criou inclui um código de exemplo para um módulo IoT Edge. Esse módulo de exemplo simplesmente recebe mensagens e as passa. A funcionalidade de pipeline demonstra um conceito importante em IoT Edge, que é como os módulos se comunicam entre si.

Cada módulo pode ter várias filas de *entrada* e *saída* declaradas em seu código. O Hub de IoT Edge em execução no dispositivo roteia as mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar entradas e saídas varia entre linguagens, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre roteamento entre módulos, consulte [declarar rotas](module-composition.md#declare-routes).

O código C# de exemplo que vem com o modelo de projeto usa a [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IOT para .net.

1. Abra o ficheiro **Program.cs,** que está dentro dos **módulos/SampleModule/pasta.**

2. Em program.cs, encontre o método **SetInputMessageHandlerAsync.**

3. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Examine esse método e veja como ele Inicializa uma fila de entrada chamada **entrada1**.

   ![Encontre o nome de entrada no construtor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Em seguida, localize o método **SendEventAsync** .

5. O método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) processa mensagens recebidas e configura uma fila de saída para passá-las. Examine esse método e veja que ele Inicializa uma fila de saída chamada **Saída1**.

   ![Encontre o nome de saída em SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra o arquivo **Deployment. Template. JSON** .

7. Localize a propriedade **modules** do $edgeAgent propriedades desejadas.

   Deve haver dois módulos listados aqui. O primeiro é **SimulatedTemperatureSensor**, que é incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que você pode usar para testar seus módulos. O segundo é o módulo **SampleModule** que criou como parte desta solução.

8. Na parte inferior do ficheiro, encontre as propriedades desejadas para o módulo **$edgeHub.**

   Uma das funções do módulo Hub de IoT Edge é rotear mensagens entre todos os módulos em uma implantação. Reveja os valores na propriedade das **rotas.** A primeira rota, **SampleModuleToIoTHub,** utiliza um personagem wildcard **(\*** ) para indicar quaisquer mensagens provenientes de quaisquer filas de saída no módulo SampleModule. Essas mensagens entram em *$upstream*, que é um nome reservado que indica o Hub IOT. A segunda rota, sensorToSampleModule, retira mensagens provenientes do módulo SimuladoSensor de Temperatura e encaminha-as para a fila de entrada de *entrada 1* que viu inicializada no código SampleModule.

   ![Examinar rotas em Deployment. Template. JSON](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Você examinou o código do módulo e o modelo de implantação para entender alguns dos principais conceitos de implantação. Agora, está pronto para construir a imagem do recipiente SampleModule e empurrá-la para o registo do seu contentor. Com a extensão das ferramentas IoT para o Código do Estúdio Visual, este passo também gera o manifesto de implementação com base na informação no ficheiro do modelo e na informação do módulo dos ficheiros de solução.

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça as suas credenciais de registo de contentores ao Docker para que possa empurrar a sua imagem de contentor para ser armazenada no registo.

1. Abra o terminal integrado do Código do Estúdio Visual selecionando o **Terminal** **'Ver** > .'

2. Entre no Docker com as credenciais do registro de contêiner do Azure que você salvou depois de criar o registro.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Criar e enviar por push

O Visual Studio Code tem agora acesso ao registo do seu contentor, por isso é hora de transformar o código de solução numa imagem de contentor.

1. No explorador de Código de Estúdio Visual, clique no ficheiro **deployment.template.json** e selecione **Build and Push IoT Edge Solution**.

   ![Compilar e enviar por push módulos de IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, ele executa `docker build` para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar por push o repositório de imagens para o registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas é mais rápido na próxima vez que você executar os comandos.

2. Abra o ficheiro **deployment.amd64.json** na pasta config recém-criada. O nome de ficheiro reflete a arquitetura alvo, por isso será diferente se escolher uma arquitetura diferente.

3. Note que os dois parâmetros que tinham espaços reservados estão agora preenchidos com os seus valores adequados. A secção **de credenciais** de registo tem o seu nome de utilizador e senha de registo retirados do ficheiro .env. O **SampleModule** tem o repositório de imagem completo com o nome, versão e etiqueta de arquitetura do ficheiro module.json.

4. Abra o ficheiro **módulo.json** na pasta SampleModule.

5. Altere o número de versão da imagem do módulo. (A versão, não a $schema-Version.) Por exemplo, aumente o número de versão do patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões de módulo habilitam o controle de versão e permitem que você teste alterações em um pequeno conjunto de dispositivos antes de implantar atualizações na produção. Se você não incrementar a versão do módulo antes de compilar e enviar por push, você substituirá o repositório no registro de contêiner.

6. Salve as alterações no arquivo module. JSON.

7. Clique na direita no ficheiro **deployment.template.json** novamente e, novamente, selecione **Build and Push IoT Edge Solution**.

8. Abra o ficheiro **deployment.amd64.json** novamente. Observe que um novo arquivo não foi criado quando você executou o comando criar e enviar por push novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. A imagem SampleModule aponta agora para a versão 0.0.2 do recipiente.

9. Para verificar melhor o que o comando Build e Push fez, acesse o [portal do Azure](https://portal.azure.com) e navegue até o registro de contêiner.

10. No registo do seu recipiente, selecione **Repositórios** e depois **samplemodule**. Verifique se ambas as versões da imagem foram enviadas por push para o registro.

    ![Exibir as duas versões de imagem no registro de contêiner](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Resolução de problemas

Se você encontrar erros ao compilar e enviar por push a imagem do módulo, isso geralmente terá a ver com a configuração do Docker em seu computador de desenvolvimento. Use as seguintes verificações para examinar sua configuração:

* Você executou o comando `docker login` usando as credenciais que você copiou do registro de contêiner? Essas credenciais são diferentes das que você usa para entrar no Azure.
* Seu repositório de contêineres está correto? Ele tem o nome do registro de contêiner correto e o nome correto do módulo? Abra o ficheiro **módulo.json** na pasta SampleModule para verificar. O valor do repositório deve parecer\<nome do **registo\>.azurecr.io/samplemodule**.
* Se usou um nome diferente do **SampleModule** para o seu módulo, esse nome é consistente em toda a solução?
* Seu computador está executando o mesmo tipo de contêiner que você está criando? Este tutorial é para dispositivos Linux IoT Edge, por isso o Visual Studio Code deve dizer **amd64** ou **arm32v7** na barra lateral, e Docker Desktop deve estar executando recipientes Linux.  

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Você verificou que as imagens de contêiner criadas são armazenadas no registro de contêiner, portanto, é hora de implantá-las em um dispositivo. Verifique se o dispositivo IoT Edge está em execução.

1. No explorador de Código de Estúdio Visual, expanda a secção de Dispositivos Hub Azure IoT.

2. Clique à direita no dispositivo IoT Edge que pretende implementar e, em seguida, **selecione Create Deployment para dispositivo único**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No explorador de ficheiros, navegue na pasta **de config** e, em seguida, selecione o ficheiro **deployment.amd64.json.**

   Não utilize o ficheiro deployment.template.json, que não tenha as credenciais de registo do recipiente ou valores de imagem do módulo. Se estiver a apontar para um dispositivo Arm32 Linux, o manifesto de implantação será nomeado deployment.arm32v7.json.

4. Expanda os detalhes para o seu dispositivo IoT Edge e, em seguida, expanda a lista de **Módulos** para o seu dispositivo.

5. Utilize o botão de atualização para atualizar a visão do dispositivo até ver os módulos SimuladosSensor de Temperatura e SampleModule a funcionar no seu dispositivo.

   Pode levar alguns minutos para que ambos os módulos sejam iniciados. O tempo de execução do IoT Edge precisa receber seu novo manifesto de implantação, extrair as imagens do módulo do tempo de execução do contêiner e, em seguida, iniciar cada novo módulo.

   ![Exibir módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Exibir mensagens do dispositivo

O código SampleModule recebe mensagens através da sua fila de entrada e passa-as através da sua fila de saída. O manifesto de implementação declarou rotas que passaram mensagens para SampleModule do Sensor de Temperatura Simulada e, em seguida, reencaminhou mensagens do SampleModule para o IoT Hub. As ferramentas Azure IoT para O Código do Estúdio Visual permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais.

1. No explorador de Código de Estúdio Visual, clique no dispositivo IoT Edge que pretende monitorizar e, em seguida, **selecione Start Monitoring Built-in Event Endpoint**.

2. Veja a janela de saída no Visual Studio Code para ver mensagens que chegam ao seu hub IoT.

   ![Exibir o dispositivo de entrada para mensagens na nuvem](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Exibir alterações no dispositivo

Se você quiser ver o que está acontecendo no próprio dispositivo, use os comandos nesta seção para inspecionar o tempo de execução IoT Edge e os módulos em execução no seu dispositivo.

Os comandos nesta seção são para seu dispositivo IoT Edge, não para seu computador de desenvolvimento. Se você estiver usando uma máquina virtual para seu dispositivo IoT Edge, conecte-se a ela agora. No Azure, aceda à página geral da máquina virtual e selecione **Connect** para aceder à ligação segura à concha.

* Exiba todos os módulos implantados em seu dispositivo e verifique seu status:

   ```bash
   iotedge list
   ```

   Deve ver quatro módulos: os dois módulos de tempo de execução IoT Edge, Sensor De Temperatura Simulado e SampleModule. Todos os quatro devem estar listados como em execução.

* Inspecione os logs de um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Módulos IoT Edge diferenciam maiúsculas de minúsculas.

   Os registos SimuladosSensor de Temperatura e SampleModule devem mostrar as mensagens que estão a processar. O módulo edgeAgent é responsável por iniciar os outros módulos, de modo que seus logs terão informações sobre a implementação do manifesto de implantação. Se qualquer módulo não estiver listado ou não estiver em execução, os logs do edgeAgent provavelmente terão os erros. O módulo edgeHub é responsável por comunicações entre os módulos e o Hub IoT. Se os módulos estiverem em execução, mas as mensagens não chegarem ao seu hub IoT, os logs do edgeHub provavelmente terão os erros.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurao Código de Estúdio Visual na sua máquina de desenvolvimento e implementou a partir dele o seu primeiro módulo IoT Edge. Agora que você conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que ele possa analisar os dados que passam por ele. Escolha seu idioma preferencial:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
