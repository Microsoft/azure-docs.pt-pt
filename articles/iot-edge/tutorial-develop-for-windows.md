---
title: Desenvolver módulo para dispositivos Windows - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial explica como configurar os recursos de máquina e na cloud de desenvolvimento para desenvolver módulos do IoT Edge com contentores do Windows para dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 94a287cd996bd18b757620254540f8dc0df499e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051813"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Tutorial: Desenvolver módulos do IoT Edge para dispositivos Windows

Utilize o Visual Studio para desenvolver e implementar código para dispositivos Windows que executam o IoT Edge.

Guia de introdução, criou um dispositivo IoT Edge, utilizando uma máquina virtual do Windows ou implementou um módulo previamente criado no Azure Marketplace. Este tutorial explica o que é necessário para desenvolver e implementar seu próprio código para um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para todos os outros tutoriais, que irão entrar em mais detalhes sobre as linguagens de programação específicas ou serviços do Azure. 

Este tutorial utiliza o exemplo de implementação de um  **C# módulo para um dispositivo de Windows**. Este exemplo foi escolhido como é o cenário mais comum de desenvolvimento. Se estiver interessado em desenvolver num idioma diferente, ou se pretender implementar os serviços do Azure como módulos, neste tutorial ainda será útil para saber mais sobre as ferramentas de desenvolvimento. Assim que compreender os conceitos de desenvolvimento, em seguida, pode escolher seu idioma preferencial ou o serviço do Azure para aprofundar os detalhes. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure a máquina de desenvolvimento.
> * Utilize as ferramentas do IoT Edge para Visual Studio para criar um novo projeto.
> * Compila o projeto como um contentor e o armazenamos num Azure container registry.
> * Implemente o código para um dispositivo IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Conceitos-chave

Este tutorial explica o desenvolvimento de um módulo do IoT Edge. Uma *módulo do IoT Edge*, ou, às vezes, apenas *módulo* para abreviar, é um contentor que contém o código executável. Pode implementar um ou mais módulos para um dispositivo IoT Edge. Os módulos realizam tarefas específicas, como ingerir dados de sensores, realizar análises de dados ou dados de operações de limpeza ou envio de mensagens para um hub IoT. Para obter mais informações, consulte [módulos de compreender o Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos do IoT Edge, é importante compreender a diferença entre a máquina de desenvolvimento e o destino do dispositivo IoT Edge, onde o módulo, eventualmente, será implementado. O contentor que criou para armazenar seu código do módulo tem de corresponder ao sistema operativo (SO) dos *dispositivo de destino*. Para o desenvolvimento de contentores do Windows, esse conceito é mais simples porque os contentores do Windows são executados apenas em sistemas de operativos do Windows. Mas, por exemplo, poderia, utilizar o seu computador de desenvolvimento do Windows para criar módulos para dispositivos do IoT Edge do Linux. Nesse cenário, terá de certificar-se de que sua máquina de desenvolvimento foi contentores de Linux em execução. À medida que avança neste tutorial, tenha em atenção a diferença entre *SO da máquina de desenvolvimento* e o *contentor SO*.

Este tutorial destina-se dispositivos Windows que executam o IoT Edge. Dispositivos IoT Edge da Windows utilizam contentores do Windows. Recomendamos que utilize o Visual Studio para desenvolver para dispositivos Windows, isso é o que irá utilizar neste tutorial. Pode utilizar Visual Studio Code, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento para **contentores Windows** no Visual Studio Code e o Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics |   |
| **Idiomas** | C#(não depuração suportados) | C <br> C# |
| **Mais informações** | [O Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [O Azure IoT Edge Tools para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[O Azure IoT Edge Tools para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Windows 10 com o 1809 update ou mais recente.
* Pode utilizar o seu computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
* Instale o [Git](https://git-scm.com/). 

Um dispositivo Azure IoT Edge no Windows:

* Recomendamos que não são executados do IoT Edge no computador de desenvolvimento, mas em vez disso, utilize um dispositivo separado. Essa distinção entre a máquina de desenvolvimento e de dispositivo IoT Edge para obter mais Espelha um cenário de implementação verdadeira com precisão e ajuda a manter os conceitos diferentes diretamente.
* Se não tiver um segundo dispositivo disponível, utilize o artigo de início rápido para criar um dispositivo IoT Edge no Azure com um [máquina virtual de Windows](quickstart.md).

Recursos da cloud:

* Um gratuita ou de escalão standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) no Azure. 

## <a name="install-container-engine"></a>Instalar o motor de contentor

Módulos do IoT Edge são empacotados como contentores, pelo que necessita de um motor de contentor no seu computador de desenvolvimento para criar e gerir os contentores. Recomendamos que utilize o ambiente de trabalho do Docker para o desenvolvimento devido a seus muitos recursos e popularidade como um mecanismo de contentor. Com o ambiente de trabalho do Docker num computador Windows, poderá alternar entre os contentores de Linux e contentores do Windows para que pode desenvolver facilmente módulos para diferentes tipos de dispositivos do IoT Edge. 

Utilize a documentação do Docker para instalar no computador de desenvolvimento: 

* [Instalar ambiente de trabalho do Docker para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Ao instalar o ambiente de trabalho de Docker para Windows, lhe for pedido que pretende utilizar contentores de Linux ou Windows. Neste tutorial, utilize **contentores Windows**. Para obter mais informações, consulte [alternar entre contentores do Windows e Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Configurar o Visual Studio e ferramentas

As extensões de IoT para o Visual Studio ajudá-lo a desenvolver módulos do IoT Edge. Essas extensões fornecem modelos de projeto, automatizar a criação do manifesto de implantação e permitem-lhe monitorizar e gerir dispositivos IoT Edge. Nesta secção, instalar o Visual Studio e a extensão de IoT Edge, em seguida, configure a sua conta do Azure para gerir recursos de IoT Hub a partir de dentro do Visual Studio. 

Esse tutorial ensina os passos de desenvolvimento para o Visual Studio 2019. Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), os passos são muito semelhantes. Se usaria em vez disso, Visual Studio Code, consulte as instruções em [utilizar o Visual Studio Code para desenvolver e depurar os módulos do Azure IoT Edge](how-to-vs-code-develop-module.md). 

1. Prepare o Visual Studio 2019 no computador de desenvolvimento. 

   * Se ainda não tiver o Visual Studio no seu computador de desenvolvimento [2019 para instalar o Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio) com as seguintes cargas de trabalho: 

      * Desenvolvimento do Azure
      * Desenvolvimento comC++
      * Desenvolvimento de várias plataformas .NET core

   * Se já tiver o Visual Studio 2019 no computador de desenvolvimento, siga os passos em [modificar o Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para adicionar as cargas de trabalho.

2. Transferir e instalar o [ferramentas do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) extensão para o Visual Studio 2019. 

   Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), transfira e instale o [ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Quando as instalações do estiverem concluídas, abra o Visual Studio 2019 e selecione **continuar sem código**.

4. Selecione **View** > **Cloud Explorer**. 

5. Selecione o ícone do perfil no Gerenciador de nuvem e inicie sessão na sua conta do Azure se não tiver iniciado sessão. 

6. Depois de iniciar sessão, as suas subscrições do Azure estão listadas. Expanda a subscrição que tem o seu hub IoT. 

7. Com a sua subscrição, expanda **os Hubs IoT** , em seguida, o hub IoT. Deverá ver uma lista dos seus dispositivos IoT e pode utilizar este explorer para geri-los. 

   ![Aceder aos recursos do IoT Hub no Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão das ferramentas do Azure IoT Edge fornece modelos de projeto para o IoT Edge de suportados todos os idiomas de módulo no Visual Studio. Esses modelos têm todos os arquivos e código que precisa implantar um módulo de trabalho para testar o IoT Edge ou dão-lhe um ponto de partida para personalizar o modelo com sua própria lógica de negócio. 

1. Selecione **arquivo** > **nova** > **Project...**

2. Na janela novo projeto, procure **IoT Edge** e escolha o **Azure IoT Edge (Windows amd64)** projeto. Clique em **Seguinte**. 

   ![Criar um novo projeto do Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Na configurar a nova janela de projeto, mudar o nome do projeto e a solução para algo descritivo semelhantes **CSharpTutorialApp**. Clique em **criar** para criar o projeto.

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)
 

4. Na aplicação do IoT Edge e janela do módulo, configure o seu projeto com os seguintes valores: 

   | Campo | Value |
   | ----- | ----- |
   | Selecione um modelo | Selecione  **C# módulo**. | 
   | Nome do projeto de módulo | Aceite a predefinição **IoTEdgeModule1**. | 
   | Repositório de imagens do docker | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida com aceder a partir do valor de nome de projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/iotedgemodule1. |

   ![Configurar o seu projeto para o dispositivo de destino, o tipo de módulo e o registo de contentor](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecione **Sim** para aplicar as alterações. 

Assim que o novo projeto é carregado na janela do Visual Studio, dedique uns momentos para se familiarizar com os arquivos que ele criou: 

* Chamada de um projeto de IoT Edge **CSharpTutorialApp**.
    * O **módulos** pasta contém ponteiros para os módulos incluídos no projeto. Neste caso, deve ser apenas IoTEdgeModule1. 
    * O **deployment.template.json** ficheiro é um modelo para o ajudar a criar um manifesto de implantação. R *manifesto de implantação* é um ficheiro que define exatamente quais os módulos que pretende implementado num dispositivo, como deve ser configurados e como pode comunicar entre si e a cloud. 
* Chamada de um projeto de módulo do IoT Edge **IoTEdgeModule1**.
    * O **program.cs** ficheiro contém a predefinição C# código de módulo que vem com o modelo de projeto. O módulo de predefinição pega a entrada de uma origem e os passa para o IoT Hub. 
    * O **Module** detalhes de espera do arquivo sobre o módulo, incluindo o repositório de imagens completas, versão de imagem e qual Dockerfile para utilizar para cada plataforma de suportada.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo para o agente do IoT Edge

O runtime do IoT Edge tem as credenciais de registo para extrair as imagens de contentor no dispositivo IoT Edge. Adicione estas credenciais para o modelo de implementação. 

1. Abra o **deployment.template.json** ficheiro.

2. Encontrar o **registryCredentials** propriedades pretendidas de propriedade no $edgeAgent. 

3. Atualize a propriedade com as suas credenciais, seguindo este formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Guarde o ficheiro de deployment.template.json. 

### <a name="review-the-sample-code"></a>Reveja o código de exemplo

O modelo de solução que criou inclui código de exemplo de um módulo do IoT Edge. Este módulo de exemplo simplesmente recebe mensagens e, em seguida, passa-los no. A funcionalidade de pipeline demonstra um conceito importante no IoT Edge, o que é a forma como os módulos comunicam entre si.

Cada módulo pode ter vários *entrada* e *saída* filas declarado em seu código. O hub do IoT Edge em execução no dispositivo encaminha mensagens a partir da saída de um módulo para a entrada de um ou mais módulos. O idioma específico para declarar as entradas e saídas varia entre as linguagens, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [declarar rotas](module-composition.md#declare-routes).

O exemplo C# utiliza o código que vem com o modelo de projeto a [classe de ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IoT para .NET. 

1. Na **program.cs** do ficheiro, encontre o **SetInputMessageHandlerAsync** método.

2. O [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) método configura uma fila de entrada para receber mensagens de entrada. Reveja este método e veja como ele inicializa uma fila de entrada chamada **input1**. 

   ![Encontrar o nome de entrada no construtor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Em seguida, localize a **SendEventAsync** método.

4. O [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) método processa as mensagens recebidas e configura uma fila de saída para os transmitirá. Rever este método e ver o que inicializa uma fila de saída chamada **output1**. 

   ![Encontrar o nome de saída no construtor SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Abra o **deployment.template.json** ficheiro.

6. Encontrar o **módulos** propriedades pretendidas de propriedade do $edgeAgent. 

   Deve haver dois módulos listados aqui. A primeira é **tempSensor**, que está incluído em todos os modelos por predefinição, para fornecer dados de temperatura simulada que pode usar para testar seus módulos. O segundo é o **IotEdgeModule1** módulo que criou como parte deste projeto.

   Esta propriedade de módulos declara quais módulos devem ser incluídos na implementação para o seu dispositivo ou dispositivos. 

7. Encontrar o **rotas** propriedades pretendidas de propriedade do $edgeHub. 

   Uma das funções se o módulo do IoT Edge hub é rotear mensagens entre todos os módulos numa implantação. Reveja os valores na propriedade rotas. A primeira rota **IotEdgeModule1ToIoTHub**, utiliza um caráter universal ( **\*** ) para incluir qualquer mensagem recebida a partir de qualquer fila de saída no módulo IoTEdgeModule1. Essas mensagens aprofundar *$ a montante*, que é um nome reservado que indica o IoT Hub. A segunda rota **sensorToIotEdgeModule1**, recebe mensagens recebidas do módulo tempSensor e encaminha-os para o *input1* fila de entrada do módulo IotEdgeModule1. 

   ![Reveja as rotas no deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Reviu o código de módulo e o modelo de implementação para compreender alguns conceitos fundamentais de implantação. Agora, está pronto para criar a imagem de contentor IotEdgeModule1 e enviá-la para o seu registo de contentor. Com a extensão de ferramentas de IoT para o Visual Studio, este passo também gera o manifesto de implantação com base nas informações no ficheiro de modelo e as informações de módulo dos arquivos da solução. 

### <a name="sign-in-to-docker"></a>Inicie sessão no Docker

Fornece seu contentor as credenciais do registo de Docker no seu computador de desenvolvimento, para que ele possa empurrar a imagem de contentor para ser armazenado no Registro. 

1. Abra o PowerShell ou a um prompt de comando.

2. Inicie sessão no Docker com as credenciais de registo de contentor do Azure que guardou depois de criar o registo. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança recomenda a utilização do `--password-stdin`. Embora essa prática recomendada é recomendada para cenários de produção, é fora do âmbito deste tutorial. Para obter mais informações, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referência.

### <a name="build-and-push"></a>Criar e enviar por push

Seu computador de desenvolvimento tem agora acesso ao seu registo de contentor e seus dispositivos IoT Edge também compartilharão. Chegou a hora para transformar o código de projeto numa imagem de contentor. 

1. Com o botão direito a **CSharpTutorialApp** pasta do projeto e selecione **compilação e de Push módulos do IoT Edge**. 

   ![Criar e enviar por push módulos do IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   O comando de compilação e emissão inicia três operações. Primeiro, ele cria uma nova pasta na solução denominada **config** que contém os ficheiros de manifesto, criados fora das informações no modelo de implementação e outras soluções de implantação completa. Em segundo lugar, ele é executado `docker build` para criar a imagem de contentor com base no dockerfile apropriado para a sua arquitetura de destino. Em seguida, ele é executado `docker push` para enviar por push o repositório de imagens para o seu registo de contentor. 

   Este processo poderá demorar vários minutos a primeira vez, mas é mais rápido na próxima vez que execute os comandos. 

2. Abra o **deployment.windows amd64.json** ficheiro na pasta config criado recentemente. (A pasta de configuração não pode aparecer no Explorador de soluções no Visual Studio. Se for esse o caso, selecione o **Mostrar todos os ficheiros** ícone na barra de tarefas do Explorador de soluções.)

3. Encontrar o **imagem** parâmetro da seção IotEdgeModule1. Tenha em atenção que a imagem contém o repositório de imagens completo com a etiqueta de nome, versão e arquitetura do arquivo Module.

4. Abra o **Module** ficheiro na pasta IotEdgeModule1. 

5. Altere o número de versão para a imagem do módulo. (A versão, não o $schema-versão.) Por exemplo, incrementar o número de versão de patch **0.0.2** como se tivesse fizemos uma correção de pequenos no código do módulo. 

   >[!TIP]
   >Versões do módulo permitem o controle de versão e lhe permitem testar alterações num pequeno grupo de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de criar e enviar por push, em seguida, substitui o repositório no seu registo de contentor. 

6. Guarde as alterações no arquivo Module.

7. Com o botão direito a **CSharpTutorialApp** novamente a pasta de projeto e selecione **módulos de compilação e de Push IoT Edge** novamente. 

8. Abra o **deployment.windows amd64.json** ficheiro novamente. Tenha em atenção que não foi criado um novo ficheiro quando executou o comando de compilação e emissão novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. A imagem de IotEdgeModule1 agora aponta para o 0.0.2 versão do contentor. Esta alteração no manifesto de implantação é como informa o dispositivo de IoT Edge que existe uma nova versão de um módulo para extrair. 

9. Para ainda mais, certifique-se de que o que fiz o comando de compilação e emissão, vá para o [portal do Azure](https://portal.azure.com) e navegue para o seu registo de contentor. 

10. No seu registo de contentor, selecione **repositórios** , em seguida, **iotedgemodule1**. Certifique-se de que ambas as versões da imagem foram enviadas para o registo.

    ![Ver as duas versões de imagem no registo de contentor](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao criar e enviar a imagem do módulo, muitas vezes, tem a ver com a configuração de Docker no seu computador de desenvolvimento. Utilize as seguintes verificações para rever a configuração: 

* Deparou-se a `docker login` comando com as credenciais que copiou do seu registo de contentor? Estas credenciais são diferentes dos que utiliza para iniciar sessão no Azure. 
* O repositório do contentor está correto? Isso tem o nome do registo de contentor correto e o nome do módulo correto? Abra o **Module** ficheiro na pasta IotEdgeModule1 para verificar. O valor de repositório deve ser semelhante à  **\<nome do registo\>.azurecr.io/iotedgemodule1**. 
* Se utilizou um nome diferente **IotEdgeModule1** para seu módulo, é esse nome consistente em toda a solução?
* A máquina está a executar o mesmo tipo de contentores que está a criar? Este tutorial destina-se a dispositivos Windows IoT Edge, para que os ficheiros do Visual Studio devem ter o **windows-amd64** extensão e o ambiente de trabalho do Docker que devem estar a executar contentores do Windows. 

## <a name="deploy-modules-to-device"></a>Implementar módulos no dispositivo

Verificar a que as imagens de contentor criada são armazenadas no seu registo de contentor, portanto, é hora de implantá-los a um dispositivo. Certifique-se de que o seu dispositivo IoT Edge está em execução. 

1. Abra o Cloud Explorer no Visual Studio e expandir os detalhes do seu hub IoT. 

2. Selecione o nome do dispositivo que pretende implementar. Na **ações** lista, selecione **criar implementação**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-windows/create-deployment.png)


3. No Explorador de ficheiros, navegue para a pasta de configuração do seu projeto e selecione o **deployment.windows amd64.json** ficheiro. Este ficheiro, muitas vezes, está localizado em `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Não utilize o ficheiro de deployment.template.json, que não tem os valores de imagem completa do módulo nela. 

4. Expanda os detalhes para o seu dispositivo IoT Edge no Cloud Explorer para ver os módulos no seu dispositivo.

5. Utilize o **atualizar** botão para atualizar o estado do dispositivo para ver que o tempSensor e IotEdgeModule1 módulos são implementados seu dispositivo. 


   ![Módulos de vista em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens de dispositivo

O código de IotEdgeModule1 recebe mensagens por meio da sua fila de entrada e transmite-los por meio da sua fila de saída. O manifesto de implantação declarado rotas que passado mensagens do tempSensor para IotEdgeModule1 e, depois, reencaminhados mensagens IotEdgeModule1 ao IoT Hub. As ferramentas do Azure IoT Edge para Visual Studio permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais. 

1. No cloud explorer do Visual Studio, selecione o nome do dispositivo IoT Edge que implementado. 

2. Na **ações** menu, selecione **iniciar a monitorização de evento ponto final incorporado**.

3. Veja a **saída** secção no Visual Studio para ver as mensagens que chegam ao IoT hub. 

   Pode demorar alguns minutos para que ambos os módulos começar. O runtime do IoT Edge precisa para receber o seu novo manifesto de implantação, obter as imagens de módulo do Runtime de contentor e comece a cada novo módulo. Se 

   ![Ver dispositivo de entrada para mensagens de cloud](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se pretender ver o que acontece no seu próprio dispositivo, utilize os comandos nesta secção para inspecionar o runtime do IoT Edge e os módulos em execução no seu dispositivo. 

Os comandos nesta secção são para o seu dispositivo IoT Edge, não sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligá-la agora. No Azure, aceda à página de descrição geral da máquina virtual e selecione **Connect** para acessar a conexão de área de trabalho remota. No dispositivo, abra uma janela do PowerShell ou comando para executar o `iotedge` comandos.

* Todos os módulos implementados no seu dispositivo para ver e verificar o respetivo estado:

   ```cmd
   iotedge list
   ```

   Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, tempSensor e IotEdgeModule1. Todos os quatro deverão ser listados em execução.

* Inspecione os registos para um módulo específico:

   ```cmd
   iotedge logs <module name>
   ```

   Módulos do IoT Edge diferenciam maiúsculas de minúsculas. 

   Os registos de IotEdgeModule1 e tempSensor devem mostrar as mensagens que estão a processar. O módulo de edgeAgent é responsável por iniciar outros módulos, para que os seus registos tenham informações sobre a implementação o manifesto de implantação. Se qualquer módulo não estiver listado ou não está em execução, os registos de edgeAgent provavelmente terão os erros. O módulo de edgeHub é responsável pela comunicação entre os módulos e o IoT Hub. Se os módulos estão operacionais e em execução, mas as mensagens não estão a chegar ao seu hub IoT, os registos de edgeHub provavelmente terão os erros. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurar o Visual Studio 2019 no computador de desenvolvimento e implementou o seu primeiro módulo do IoT Edge do mesmo. Agora que já conhece os conceitos básicos, experimente adicionar funcionalidade a um módulo para que ele pode analisar os dados transmitidos através do mesmo. Selecione o idioma preferencial: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
