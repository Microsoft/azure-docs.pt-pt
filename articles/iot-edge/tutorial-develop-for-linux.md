---
title: Desenvolver módulo para dispositivos do Linux - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial explica como configurar os recursos de máquina e na cloud de desenvolvimento para desenvolver módulos do IoT Edge através de contentores do Linux para dispositivos de Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11fa72f5853350c76b2a8d0aa4fd7b96b598b670
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303853"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: Desenvolver módulos do IoT Edge para dispositivos do Linux

Utilize o Visual Studio Code para desenvolver e implementar código a dispositivos de Linux com o IoT Edge. 

Os artigos de início rápido, criou um dispositivo IoT Edge, utilizando uma máquina virtual do Linux e implantado um módulo previamente criado no Azure Marketplace. Este tutorial explica o que é necessário para desenvolver e implementar seu próprio código para um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para todos os outros tutoriais, que irão entrar em mais detalhes sobre as linguagens de programação específicas ou serviços do Azure. 

Este tutorial utiliza o exemplo de implementação de um **módulo de C para um dispositivo Linux**. Este exemplo foi escolhido porque tem os pré-requisitos de menor, para que pode saber mais sobre as ferramentas de desenvolvimento sem se preocupar se tiver as bibliotecas corretas instaladas. Assim que compreender os conceitos de desenvolvimento, em seguida, pode escolher seu idioma preferencial ou o serviço do Azure para aprofundar os detalhes. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure a máquina de desenvolvimento.
> * Utilize as ferramentas do IoT Edge para Visual Studio Code para criar um novo projeto.
> * Compila o projeto como um contentor e o armazenamos num Azure container registry.
> * Implemente o código para um dispositivo IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Conceitos-chave

Este tutorial explica o desenvolvimento de um módulo do IoT Edge. Uma *módulo do IoT Edge*, ou, às vezes, apenas *módulo* para abreviar, é um contentor que contém o código executável. Pode implementar um ou mais módulos para um dispositivo IoT Edge. Os módulos realizam tarefas específicas, como ingerir dados de sensores, realizar análises de dados ou dados de operações de limpeza ou envio de mensagens para um hub IoT. Para obter mais informações, consulte [módulos de compreender o Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos do IoT Edge, é importante compreender a diferença entre a máquina de desenvolvimento e o destino do dispositivo IoT Edge, onde o módulo, eventualmente, será implementado. O contentor que criou para armazenar seu código do módulo tem de corresponder ao sistema operativo (SO) dos *dispositivo de destino*. Por exemplo, o cenário mais comum é alguém desenvolver um módulo num computador Windows destinar a um dispositivo de Linux com o IoT Edge de destino. Nesse caso, o sistema de operativo contentor seria Linux. À medida que avança neste tutorial, tenha em atenção a diferença entre o *SO da máquina de desenvolvimento* e o *contentor SO*.

Este tutorial destina-se os dispositivos de Linux com o IoT Edge. Pode utilizar o sistema operativo do computador de desenvolvimento preferido, desde que seu computador de desenvolvimento pode executar contentores do Linux. Recomendamos que utilize o Visual Studio Code para desenvolver para dispositivos de Linux, isso é o que irá utilizar neste tutorial. Pode utilizar o Visual Studio, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento para **contentores de Linux** no Visual Studio Code e o Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivo do Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Idiomas** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [O Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [O Azure IoT Edge Tools para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [o Azure IoT Edge Tools para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

Esse tutorial ensina os passos de desenvolvimento para o Visual Studio Code. Se usaria em vez disso, Visual Studio, consulte as instruções em [utilizar o Visual Studio 2019 para desenvolver e depurar os módulos do Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Pode utilizar o seu computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
* A maioria dos sistemas operativos que pode ser executado um mecanismo de contentor pode ser usados para desenvolver módulos do IoT Edge para dispositivos de Linux. Este tutorial utiliza um computador Windows, mas destaca as diferenças conhecidas no MacOS ou Linux. 
* Instale [Git](https://git-scm.com/), para mostrar as módulo pacotes de modelo mais tarde neste tutorial.  

Um dispositivo Azure IoT Edge no Linux:

* Recomendamos que não são executados do IoT Edge no computador de desenvolvimento, mas em vez disso, utilize um dispositivo separado. Essa distinção entre a máquina de desenvolvimento e de dispositivo IoT Edge para obter mais Espelha um cenário de implementação verdadeira com precisão e ajuda a manter os conceitos diferentes diretamente.
* Se não tiver um segundo dispositivo disponível, utilize o artigo de início rápido para criar um dispositivo IoT Edge no Azure com um [máquina virtual do Linux](quickstart-linux.md).

Recursos da cloud:

* Um gratuita ou de escalão standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) no Azure. 

## <a name="install-container-engine"></a>Instalar o motor de contentor

Módulos do IoT Edge são empacotados como contentores, pelo que necessita de um motor de contentor no seu computador de desenvolvimento para criar e gerir os contentores. Recomendamos que utilize o ambiente de trabalho do Docker para o desenvolvimento devido a seus muitos recursos e popularidade como um mecanismo de contentor. Com o ambiente de trabalho do Docker num dispositivo Windows, poderá alternar entre os contentores de Linux e contentores do Windows para que pode desenvolver facilmente módulos para diferentes tipos de dispositivos do IoT Edge. 

Utilize a documentação do Docker para instalar no computador de desenvolvimento: 

* [Instalar ambiente de trabalho do Docker para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Ao instalar o ambiente de trabalho de Docker para Windows, lhe for pedido que pretende utilizar contentores de Linux ou Windows. Esta decisão pode ser alterada em qualquer altura utilizando um comutador fácil. Neste tutorial, vamos utilizar contentores do Linux porque nossa módulos visa os dispositivos de Linux. Para obter mais informações, consulte [alternar entre contentores do Windows e Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instalar o Docker Desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [sobre o Docker CE](https://docs.docker.com/install/) para obter informações de instalação em várias plataformas de Linux.

## <a name="set-up-vs-code-and-tools"></a>Configurar VS Code e ferramentas

Utilize as extensões de IoT para o Visual Studio Code para desenvolver módulos do IoT Edge. Essas extensões fornecem modelos de projeto, automatizar a criação do manifesto de implantação e permitem-lhe monitorizar e gerir dispositivos IoT Edge. Nesta secção, instalar o Visual Studio Code e a extensão de IoT, em seguida, configure a sua conta do Azure para gerir recursos de IoT Hub a partir de dentro do Visual Studio Code. 

1. Instale [Visual Studio Code](https://code.visualstudio.com/) no computador de desenvolvimento. 

2. Depois da instalação estiver concluída, selecione **View** > **extensões**. 

3. Procure **ferramentas do Azure IoT**, que é, na verdade, uma coleção de funcionalidades que ajudam a interagir com o IoT Hub e dispositivos IoT, bem como desenvolver módulos do IoT Edge. 

4. Selecione **Instalar**. Cada extensão incluído instala individualmente. 

5. Quando terminar as extensões de instalar, abra a paleta de comandos, selecionando **View** > **paleta de comandos**. 

6. Na paleta de comandos, procure e selecione **Azure: Inicie sessão no**. Siga os avisos para iniciar sessão na conta do Azure. 

7. Na paleta de comandos novamente, procure e selecione **IoT Hub do Azure: Selecione o IoT Hub**. Siga as instruções para selecionar a sua subscrição do Azure e o IoT hub. 

7. Abra a secção de explorer do Visual Studio Code, selecionando o ícone na barra de atividade no lado esquerdo ou selecionando **View** > **Explorer**. 

8. Na parte inferior da secção do explorer, expanda o fechada **dispositivos do Azure IoT Hub** menu. Deverá ver os dispositivos e os dispositivos do IoT Edge associados com o hub IoT que selecionou através a paleta de comandos. 

   ![Ver os dispositivos no IoT hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão das ferramentas de IoT do Azure fornece modelos de projeto para o IoT Edge de suportados todos os idiomas de módulo no Visual Studio Code. Esses modelos têm todos os arquivos e código que precisa implantar um módulo de trabalho para testar o IoT Edge ou dão-lhe um ponto de partida para personalizar o modelo com sua própria lógica de negócio. 

Neste tutorial, vamos utilizar o modelo de módulo de C porque tem as menor quantidade pré-requisitos para instalar. 

### <a name="create-a-project-template"></a>Criar um modelo de projeto

Na paleta de comandos de código do Visual Studio, procure e selecione **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções e utilize os seguintes valores para criar a sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **C módulo**. |
   | Indicar um nome para o módulo | Aceite a predefinição **SampleModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/samplemodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Assim que a nova solução é carregado na janela do Visual Studio Code, dedique uns momentos para se familiarizar com os arquivos que ele criou: 

* O **.vscode** pasta contém um arquivo chamado **Launch**, que é utilizado para depuração módulos.
* O **módulos** pasta contém uma pasta para cada módulo na sua solução. No momento, que só deve estar **SampleModule**, ou qualquer nome forneceu para o módulo. A pasta de SampleModule contém o código de programa principal, os metadados do módulo e vários arquivos de Docker. 
* O **. env** ficheiro contém as credenciais para o seu registo de contentor. Estas credenciais são partilhadas com o seu dispositivo IoT Edge, para que ele tenha acesso para extrair as imagens de contentor. 
* O **deployment.debug.template.json** ficheiro e **deployment.template.json** ficheiro são modelos que ajudam a criar um manifesto de implantação. R *manifesto de implantação* é um ficheiro que define exatamente quais os módulos que pretende implementado num dispositivo, como deve ser configurados e como pode comunicar entre si e a cloud. Os arquivos de modelo utilizam ponteiros para alguns valores. Quando transforma o modelo num manifesto de implantação de verdade, os ponteiros são substituídos por valores obtidas a partir de outros ficheiros de solução. Localize os dois marcadores de posição comuns no seu modelo de implementação: 

  * Na secção de credenciais de registo, o endereço é autofilled de informações que forneceu quando criou a solução. No entanto, o nome de utilizador e palavra-passe referenciam as variáveis armazenadas no ficheiro. env. Trata-se para segurança, como o ficheiro. env é git ignorada, mas não é o modelo de implementação. 
  * Na secção SampleModule, a imagem de contentor não está preenchida, apesar de que forneceu o repositório de imagens quando criou a solução. Este marcador de posição aponta para o **Module** ficheiro dentro da pasta SampleModule. Se for para esse arquivo, verá que o campo de imagem contiver o repositório, mas também um valor de etiqueta é constituído pela versão e a plataforma do contentor. É possível iterar a versão manualmente como parte do seu ciclo de desenvolvimento e selecionar a plataforma de contentores com um comutador que apresentamos mais tarde nesta secção. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo para o agente do IoT Edge

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O tempo de execução precisa estas credenciais para extrair as imagens de contentor no dispositivo IoT Edge. 

A extensão de IoT Edge tenta obter o seu contentor as credenciais do registo do Azure e preenchê-los no ficheiro de ambiente. Verifique se as credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. Abra o **. env** ficheiro na sua solução de módulo. 
2. Adicionar a **nome de utilizador** e **palavra-passe** valores que copiou do seu registo de contentor do Azure.
3. Guarde as alterações ao ficheiro. env. 

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos de C para dispositivos AMD64 de Linux e ARM32v7 de Linux. Tem de selecionar qual arquitetura visa com cada solução, pois isso afeta como o contentor é criado e é executado. A predefinição é AMD64 de Linux. 

1. Abra a paleta de comandos e procure **Azure IoT Edge: Definir a plataforma de destino predefinido para solução de ponta**, ou selecione o ícone de atalho na barra do lado na parte inferior da janela. 

   ![Selecione o ícone de arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos a utilizar uma máquina virtual do Ubuntu como o dispositivo do IoT Edge, para que irá manter a predefinição **amd64**. 

### <a name="review-the-sample-code"></a>Reveja o código de exemplo

O modelo de solução que criou inclui código de exemplo de um módulo do IoT Edge. Este módulo de exemplo simplesmente recebe mensagens e, em seguida, passa-los no. A funcionalidade de pipeline demonstra um conceito importante no IoT Edge, o que é a forma como os módulos comunicam entre si.

Cada módulo pode ter vários *entrada* e *saída* filas declarado em seu código. O hub do IoT Edge em execução no dispositivo encaminha mensagens a partir da saída de um módulo para a entrada de um ou mais módulos. O idioma específico para declarar as entradas e saídas varia entre as linguagens, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [declarar rotas](module-composition.md#declare-routes).

1. Abra o **Main** arquivo, o que está dentro do **módulos/SampleModules/** pasta. 

2. O SDK de C do IoT Hub utiliza a função [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) inicializar filas de entrada do módulo. Pesquisar no arquivo Main XML para essa função.

3. Reveja o construtor de função SetInputMessageCallback e ver que uma fila de entrada chamado **input1** é inicializado no código. 

   ![Encontrar o nome de entrada no construtor de SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Filas de saída do módulo são inicializadas de forma semelhante. Procure o [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) função no ficheiro Main. 

5. Reveja o construtor de função SendEventToOutputAsync e ver que uma fila de saída chamado **output1** é inicializado no código. 

   ![Encontrar o nome de saída no SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra o **deployment.template.json** ficheiro.

7. Encontrar o **módulos** propriedades pretendidas de propriedade do $edgeAgent. 

   Deve haver dois módulos listados aqui. A primeira é **tempSensor**, que está incluído em todos os modelos por predefinição, para fornecer dados de temperatura simulada que pode usar para testar seus módulos. O segundo é o **SampleModule** módulo que criou como parte desta solução.

7. Na parte inferior do ficheiro, encontre as propriedades pretendidas para o **$edgeHub** módulo. 

   Uma das funções do módulo do IoT Edge hub é rotear mensagens entre todos os módulos numa implantação. Reveja os valores da **rotas** propriedade. A primeira rota **SampleModuleToIoTHub**, utiliza um caráter universal ( **\*** ) para indicar todas as mensagens provenientes de quaisquer filas de saída no módulo SampleModule. Essas mensagens aprofundar *$ a montante*, que é um nome reservado que indica o IoT Hub. A segunda rota, sensorToSampleModule, recebe mensagens recebidas do módulo tempSensor e encaminha-os para o *input1* fila de entrada que vimos inicializado no código SampleModule. 

   ![Reveja as rotas no deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Reviu o código de módulo e o modelo de implementação para compreender alguns conceitos fundamentais de implantação. Agora, está pronto para criar a imagem de contentor SampleModule e enviá-la para o seu registo de contentor. Com a extensão de ferramentas de IoT para o Visual Studio Code, este passo também gera o manifesto de implantação com base nas informações no ficheiro de modelo e as informações de módulo dos arquivos da solução. 

### <a name="sign-in-to-docker"></a>Inicie sessão no Docker

Forneça o seu contentor as credenciais do registo ao Docker, para que ele possa empurrar a imagem de contentor para ser armazenado no Registro. 

1. Abra o terminal integrado do Visual Studio Code, selecionando **View** > **Terminal**.

2. Inicie sessão no Docker com as credenciais de registo de contentor do Azure que guardou depois de criar o registo. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança recomenda a utilização do `--password-stdin`. Embora essa prática recomendada é recomendada para cenários de produção, é fora do âmbito deste tutorial. Para obter mais informações, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referência.

### <a name="build-and-push"></a>Criar e enviar por push 

Visual Studio Code tem agora acesso para o seu registo de contentor, pelo que é o tempo para transformar o código de solução numa imagem de contentor. 

1. No Explorador do Visual Studio Code, clique com botão direito a **deployment.template.json** do ficheiro e selecione **compilação e enviar por Push o IoT Edge solução**. 

   ![Criar e enviar por push módulos do IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando de compilação e emissão inicia três operações. Primeiro, ele cria uma nova pasta na solução denominada **config** que contém os ficheiros de manifesto, criados fora das informações no modelo de implementação e outras soluções de implantação completa. Em segundo lugar, ele é executado `docker build` para criar a imagem de contentor com base no dockerfile apropriado para a sua arquitetura de destino. Em seguida, ele é executado `docker push` para enviar por push o repositório de imagens para o seu registo de contentor. 

   Este processo poderá demorar vários minutos a primeira vez, mas é mais rápido na próxima vez que execute os comandos. 

2. Abra o **deployment.amd64.json** ficheiro na pasta de configuração criado recentemente. O nome de ficheiro reflete a arquitetura de destino, para que seja diferente se optou por uma arquitetura diferente.

3. Tenha em atenção que os dois parâmetros que tinha marcadores de posição agora são preenchidos com seus valores adequados. O **registryCredentials** secção tem seu nome de registo e a palavra-passe obtido a partir do ficheiro. env. O **SampleModule** tem o repositório de imagens completo com a etiqueta de nome, versão e arquitetura do arquivo Module. 

4. Abra o **Module** ficheiro na pasta SampleModule. 

5. Altere o número de versão para a imagem do módulo. (A versão, não o $schema-versão.) Por exemplo, incrementar o número de versão de patch **0.0.2** como se tivesse fizemos uma correção de pequenos no código do módulo. 

   >[!TIP]
   >Versões do módulo permitem o controle de versão e lhe permitem testar alterações num pequeno grupo de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de criar e enviar por push, em seguida, substitui o repositório no seu registo de contentor. 

6. Guarde as alterações no arquivo Module.

7. Com o botão direito a **deployment.template.json** ficheiro novamente e selecione novamente **compilação e enviar por Push o IoT Edge solução**.

8. Abra o **deployment.amd64.json** ficheiro novamente. Tenha em atenção que não foi criado um novo ficheiro quando executou o comando de compilação e emissão novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. A imagem de SampleModule agora aponta para o 0.0.2 versão do contentor. 

9. Para ainda mais, certifique-se de que o que fiz o comando de compilação e emissão, vá para o [portal do Azure](https://portal.azure.com) e navegue para o seu registo de contentor. 

10. No seu registo de contentor, selecione **repositórios** , em seguida, **samplemodule**. Certifique-se de que ambas as versões da imagem foram enviadas para o registo.

   ![Ver as duas versões de imagem no registo de contentor](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao criar e enviar a imagem do módulo, muitas vezes, tem a ver com a configuração de Docker no seu computador de desenvolvimento. Utilize as seguintes verificações para rever a configuração: 

* Deparou-se a `docker login` comando com as credenciais que copiou do seu registo de contentor? Estas credenciais são diferentes dos que utiliza para iniciar sessão no Azure. 
* O repositório do contentor está correto? Isso tem o nome do registo de contentor correto e o nome do módulo correto? Abra o **Module** ficheiro na pasta SampleModule para verificar. O valor de repositório deve ser semelhante à  **\<nome do registo\>.azurecr.io/samplemodule**. 
* Se utilizou um nome diferente **SampleModule** para seu módulo, é esse nome consistente em toda a solução?
* A máquina está a executar o mesmo tipo de contentores que está a criar? Este tutorial destina-se a dispositivos do IoT Edge do Linux, para que o Visual Studio Code deverá indicar **amd64** ou **arm32v7** na barra lateral, e o ambiente de trabalho do Docker devem estar a executar contentores do Linux. Módulos de C no Visual Studio Code não suportam contentores do Windows. 

## <a name="deploy-modules-to-device"></a>Implementar módulos no dispositivo

Verificar a que as imagens de contentor criada são armazenadas no seu registo de contentor, portanto, é hora de implantá-los a um dispositivo. Certifique-se de que o seu dispositivo IoT Edge está em execução. 

1. No Explorador do Visual Studio Code, expanda a secção de dispositivos do Azure IoT Hub. 

2. Com o botão direito do dispositivo IoT Edge que pretende implementar, em seguida, selecione **criar a implementação de único dispositivo**. 

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No Explorador de ficheiros, navegue para o **config** pasta, em seguida, selecione a **deployment.amd64.json** ficheiro. 

   Não utilize o ficheiro de deployment.template.json, que não tem os valores de imagem de módulo ou as credenciais do registo de contentor no mesmo. Se for para um dispositivo Linux ARM32, será denominado deployment.arm32v7.json o manifesto de implantação. 

4. Expandir os detalhes para o seu dispositivo IoT Edge, em seguida, expanda o **módulos** lista para o seu dispositivo. 

5. Utilize o botão de atualização para atualizar a vista do dispositivo até ver a tempSensor e módulos de SampleModule em execução no seu dispositivo. 

   Pode demorar alguns minutos para que ambos os módulos começar. O runtime do IoT Edge precisa para receber o seu novo manifesto de implantação, obter as imagens de módulo do Runtime de contentor e comece a cada novo módulo. 

   ![Módulos de vista em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens de dispositivo

O código de SampleModule recebe mensagens por meio da sua fila de entrada e transmite-los por meio da sua fila de saída. O manifesto de implantação declarado rotas que passados mensagens para SampleModule do tempSensor e, depois, reencaminhados mensagens SampleModule ao IoT Hub. As ferramentas de IoT do Azure para Visual Studio Code permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais. 

1. No Explorador do Visual Studio Code, clique no dispositivo IoT Edge que pretende monitorizar, em seguida, selecione **iniciar a monitorização de evento ponto final incorporado**. 

2. Assista a janela de saída no Visual Studio Code, para ver as mensagens que chegam ao IoT hub. 

   ![Ver dispositivo de entrada para mensagens de cloud](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se pretender ver o que acontece no seu próprio dispositivo, utilize os comandos nesta secção para inspecionar o runtime do IoT Edge e os módulos em execução no seu dispositivo. 

Os comandos nesta secção são para o seu dispositivo IoT Edge, não sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligá-la agora. No Azure, aceda à página de descrição geral da máquina virtual e selecione **Connect** para aceder a ligação de secure shell. 

* Todos os módulos implementados no seu dispositivo para ver e verificar o respetivo estado:

   ```bash
   iotedge list
   ```

   Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, tempSensor e SampleModule. Todos os quatro deverão ser listados em execução.

* Inspecione os registos para um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Módulos do IoT Edge diferenciam maiúsculas de minúsculas. 

   Os registos de SamplModule e tempSensor devem mostrar as mensagens que estão a processar. O módulo de edgeAgent é responsável por iniciar outros módulos, para que os seus registos tenham informações sobre a implementação o manifesto de implantação. Se qualquer módulo não estiver listado ou não está em execução, os registos de edgeAgent provavelmente terão os erros. O módulo de edgeHub é responsável pela comunicação entre os módulos e o IoT Hub. Se os módulos estão operacionais e em execução, mas as mensagens não estão a chegar ao seu hub IoT, os registos de edgeHub provavelmente terão os erros. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurar o Visual Studio Code no computador de desenvolvimento e implementou o seu primeiro módulo do IoT Edge do mesmo. Agora que já conhece os conceitos básicos, experimente adicionar funcionalidade a um módulo para que ele pode analisar os dados transmitidos através do mesmo. Selecione o idioma preferencial: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)