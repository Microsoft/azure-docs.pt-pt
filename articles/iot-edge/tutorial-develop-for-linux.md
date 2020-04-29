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
ms.openlocfilehash: 478d9c0485125870f8d5ffb4132f46476b4bb4ef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384369"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: Desenvolver módulos IoT Edge para dispositivos Linux

Utilize o Código do Estúdio Visual para desenvolver e implementar código para dispositivos Linux que executam o IoT Edge.

No arranque rápido, criou um dispositivo IoT Edge utilizando uma máquina virtual Linux e implementou um módulo a partir do Azure Marketplace. Este tutorial passa por desenvolver e implementar o seu próprio código para um dispositivo IoT Edge. Este artigo é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre línguas de programação específicas ou serviços Azure.

Este tutorial usa o exemplo de implantação de um **módulo C# para um dispositivo Linux**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum para soluções IoT Edge. Mesmo que planeie utilizar uma linguagem diferente ou implementar um serviço Azure, este tutorial ainda é útil para aprender sobre as ferramentas e conceitos de desenvolvimento. Complete esta introdução ao processo de desenvolvimento e, em seguida, escolha o seu serviço de idioma ou Azure preferido para mergulhar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Instale a sua máquina de desenvolvimento.
> * Utilize as ferramentas IoT Edge para visual studio code para criar um novo projeto.
> * Construa o seu projeto como recipiente e guarde-o num registo de contentores Azure.
> * Implemente o seu código num dispositivo IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IoT Edge,* ou às vezes apenas *módulo* para abreviar, é um recipiente com código executável. Pode implantar um ou mais módulos num dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados a partir de sensores, limpar e analisar dados ou enviar mensagens para um hub IoT. Para mais informações, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo ioT Edge alvo onde o módulo será eventualmente implantado. O recipiente que constrói para manter o seu código de módulo deve coincidir com o sistema operativo (OS) do *dispositivo-alvo*. Por exemplo, o cenário mais comum é alguém desenvolver um módulo num computador Windows que pretenda direcionar um dispositivo Linux em execução IoT Edge. Nesse caso, o sistema operativo do contentor seria o Linux. Ao passar por este tutorial, lembre-se da diferença entre a máquina de *desenvolvimento OS* e o *osrecipiente .*

Este tutorial tem como alvo dispositivos Linux que executam o IoT Edge. Pode utilizar o seu sistema operativo preferido desde que a sua máquina de desenvolvimento utilize os recipientes Linux. Recomendamos a utilização do Visual Studio Code para desenvolver dispositivos Linux, por isso é isso que este tutorial vai usar. Também pode utilizar o Visual Studio, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento suportados para **contentores Linux** em Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Estúdio Visual 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivo sinuoso** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Linguagens** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [Borda Azure IoT para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas de borda Azure IoT para Estúdio Visual 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Ferramentas de borda Azure IoT para Estúdio Visual 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>O suporte para dispositivos Arm64 Linux está disponível em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para mais informações, consulte [Desenvolver e depurar módulos ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Este tutorial ensina os passos de desenvolvimento do Código do Estúdio Visual. Se preferir utilizar o Visual Studio, consulte as instruções do [Use Visual Studio 2019 para desenvolver e depurar módulos para O Edge Azure IoT](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Pode utilizar o seu próprio computador ou uma máquina virtual, dependendo das suas preferências de desenvolvimento.
  * Certifique-se de que a sua máquina de desenvolvimento suporta a virtualização aninhada. Esta capacidade é necessária para a funcionamento de um motor de contentor, que instala na secção seguinte.
* A maioria dos sistemas operativos que podem funcionar um motor de contentores pode ser usado para desenvolver módulos IoT Edge para dispositivos Linux. Este tutorial utiliza um computador Windows, mas aponta diferenças conhecidas no MacOS ou no Linux.
* Instale [git,](https://git-scm.com/)para puxar pacotes de modelo seleções mais tarde neste tutorial.  
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

Um dispositivo Azure IoT Edge em Linux:

* Recomendamos que não faça o IoT Edge na sua máquina de desenvolvimento, mas utilize um dispositivo separado. Esta distinção entre a máquina de desenvolvimento e o dispositivo IoT Edge espelha com mais precisão um verdadeiro cenário de implementação, e ajuda a manter os diferentes conceitos corretos.
* Se não tiver um segundo dispositivo disponível, utilize o artigo quickstart para criar um dispositivo IoT Edge em Azure com uma [máquina virtual Linux](quickstart-linux.md).

Recursos da cloud:

* Um [centro ioT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.

## <a name="install-container-engine"></a>Instalar o motor do recipiente

Os módulos IoT Edge são embalados como recipientes, por isso precisa de um motor de contentor na sua máquina de desenvolvimento para construí-los e geri-los. Recomendamos o Docker Desktop para o desenvolvimento devido ao seu suporte de funcionalidades e popularidade. O Docker Desktop no Windows permite-lhe alternar entre os contentores Linux e windows para que possa desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Utilize a documentação do Docker para instalar na sua máquina de desenvolvimento:

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para windows, é-lhe perguntado se pretende utilizar recipientes Linux ou Windows. Esta decisão pode ser alterada a qualquer momento utilizando um interruptor fácil. Para este tutorial, usamos recipientes Linux porque os nossos módulos estão direcionados para dispositivos Linux. Para mais informações, consulte [a Switch entre os recipientes Windows e Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instale o Docker Desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [sobre docker CE](https://docs.docker.com/install/) para informações de instalação em várias plataformas Linux.
  * Para o subsistema Windows para Linux (WSL), instale o Docker Desktop para windows.

## <a name="set-up-vs-code-and-tools"></a>Configurar código vs e ferramentas

Utilize as extensões IoT para o Visual Studio Code para desenvolver módulos IoT Edge. Estas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implementação e permitem-lhe monitorizar e gerir dispositivos IoT Edge. Nesta secção, instala o Visual Studio Code e a extensão IoT e, em seguida, configura a sua conta Azure para gerir os recursos do IoT Hub a partir do Código do Estúdio Visual.

1. Instale [o Código do Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento.

2. Uma vez terminada a instalação, selecione **'Ver** > **Extensões**'.

3. Procure **ferramentas Azure IoT**, que na verdade é uma coleção de extensões que o ajudam a interagir com dispositivos IoT Hub e IoT, bem como desenvolver módulos IoT Edge.

4. Selecione **Instalar**. Cada extensão incluída instala-se individualmente.

5. Quando as extensões estiverem prontas, abra a paleta de comando selecionando a Paleta de**Comando** **de Visualização** > .

6. Na paleta de comando, procure e selecione **Azure: Inscreva-se**. Siga os avisos para iniciar sessão na conta do Azure.

7. Na paleta de comando novamente, procure e selecione **Azure IoT Hub: Selecione IoT Hub**. Siga as instruções para selecionar a sua subscrição Azure e o seu hub IoT.

8. Abra a secção exploradora do Visual Studio Code selecionando o ícone na barra de atividade à esquerda, ou selecionando **o View** > **Explorer**.

9. Na parte inferior da secção exploradora, expanda o menu de **dispositivos Hub Azure IoT** em colapso. Deve ver os dispositivos e dispositivos IoT Edge associados ao hub IoT que selecionou através da paleta de comando.

   ![Ver dispositivos no seu hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão Azure IoT Tools fornece modelos de projeto para todos os idiomas suportados do módulo IoT Edge no Código do Estúdio Visual. Estes modelos têm todos os ficheiros e códigos que precisa para implementar um módulo de trabalho para testar o IoT Edge, ou dar-lhe um ponto de partida para personalizar o modelo com a sua própria lógica de negócio.

Para este tutorial, usamos o modelo de módulo C# porque é o modelo mais utilizado.

### <a name="create-a-project-template"></a>Criar um modelo de projeto

Na paleta de comando visual Studio Code, procure e **selecione Azure IoT Edge: New IoT Edge Solution**. Siga as instruções e utilize os seguintes valores para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite o **EdgeSolution**predefinido . |
   | Selecionar modelo de módulo | Escolha **módulo C#**. |
   | Indicar um nome para o módulo | Aceite o Módulo de **Amostra**padrão . |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor está pré-povoada a partir do nome que forneceu no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br> O repositório de \<imagem final\>parece o nome do registo .azurecr.io/samplemodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Assim que a sua nova solução estiver na janela Do Código do Estúdio Visual, tenha um momento para se familiarizar com os ficheiros que criou:

* A pasta **.vscode** contém um ficheiro chamado **launch.json,** que é usado para depurar módulos.
* A pasta dos **módulos** contém uma pasta para cada módulo na sua solução. Neste momento, isso só deve ser **SampleModule**, ou qualquer nome que tenha dado ao módulo. A pasta SampleModule contém o código principal do programa, os metadados do módulo e vários ficheiros Docker.
* O ficheiro **.env** contém as credenciais do seu registo de contentores. Estas credenciais são partilhadas com o seu dispositivo IoT Edge para que tenha acesso a imagens do recipiente.
* O ficheiro **implement.debug.template.json** e o ficheiro **de implementação.template.json** são modelos que o ajudam a criar um manifesto de implantação. Um manifesto de *implantação* é um ficheiro que define exatamente quais os módulos que pretende implementar num dispositivo, como devem ser configurados, e como podem comunicar uns com os outros e a nuvem. Os ficheiros do modelo usam ponteiros para alguns valores. Quando transforma o modelo num verdadeiro manifesto de implantação, os ponteiros são substituídos por valores retirados de outros ficheiros de soluções. Localize os dois espaços reservados comuns no seu modelo de implementação:

  * Na secção de credenciais de registo, o endereço é preenchido automaticamente a partir da informação que forneceu quando criou a solução. No entanto, o nome de utilizador e a palavra-passe referem as variáveis armazenadas no ficheiro .env. Esta configuração é para a segurança, uma vez que o ficheiro .env é ignorado, mas o modelo de implementação não é.
  * Na secção SampleModule, a imagem do recipiente não é preenchida, apesar de ter fornecido o repositório de imagem quando criou a solução. Este espaço reservado aponta para o ficheiro **module.json** dentro da pasta SampleModule. Se for a esse ficheiro, verá que o campo de imagem contém o repositório, mas também um valor de etiqueta que é composto pela versão e pela plataforma do contentor. Pode iterar a versão manualmente como parte do seu ciclo de desenvolvimento e seleciona a plataforma do recipiente utilizando um comutador que introduzimos mais tarde nesta secção.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo ao agente IoT Edge

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O tempo de execução precisa destas credenciais para puxar as imagens do seu recipiente para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as suas credenciais de registo de contentores do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. Abra o ficheiro **.env** na solução do módulo.
2. Adicione o nome de **utilizador** e os valores de **palavra-passe** que copiou do registo de contentores Azure.
3. Guarde as alterações no ficheiro .env.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C# para dispositivos Linux AMD64 e ARM32v7. Você precisa selecionar qual a arquitetura que você está alvo com cada solução, porque isso afeta a forma como o recipiente é construído e executado. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure o **Azure IoT Edge: Definir**a Plataforma de Destino Padrão para Solução de Borda , ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

   ![Selecione ícone de arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comando, selecione a arquitetura alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manterá o **amd64**padrão .

### <a name="review-the-sample-code"></a>Reveja o código da amostra

O modelo de solução que criou inclui código de amostra para um módulo IoT Edge. Este módulo de amostra simplesmente recebe mensagens e depois transmite-as. A funcionalidade do pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam entre si.

Cada módulo pode ter várias filas de *entrada* e *saída* declaradas no seu código. O hub IoT Edge que funciona no dispositivo encaminha as mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar inputs e saídas varia entre línguas, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [as rotas de declaração.](module-composition.md#declare-routes)

O código C# da amostra que vem com o modelo de projeto utiliza a [Classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do IoT Hub SDK para .NET.

1. Abra o ficheiro **Program.cs,** que está dentro dos **módulos/SampleModule/pasta.**

2. Em program.cs, encontre o método **SetInputMessageHandlerAsync.**

3. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Reveja este método e veja como inicializa uma fila de entrada chamada **entrada1**.

   ![Encontre o nome de entrada no construtor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Em seguida, encontre o método **SendEventAsync.**

5. Os processos do método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) receberam mensagens e configuram uma fila de saída para as transmitir. Reveja este método e veja se inicializa uma fila de saída chamada **output1**.

   ![Encontre o nome de saída em SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra o ficheiro **deployment.template.json.**

7. Encontre a propriedade dos **módulos** do $edgeAgent propriedades desejadas.

   Deve haver dois módulos listados aqui. O primeiro é **SimulatedTemperatureSensor**, que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que pode usar para testar os seus módulos. O segundo é o módulo **SampleModule** que criou como parte desta solução.

8. Na parte inferior do ficheiro, encontre as propriedades desejadas para o módulo **$edgeHub.**

   Uma das funções do módulo hub IoT Edge é encaminhar mensagens entre todos os módulos numa implementação. Reveja os valores na propriedade das **rotas.** A primeira rota, **SampleModuleToIoTHub,** utiliza**\*** um personagem wildcard para indicar quaisquer mensagens provenientes de quaisquer filas de saída no módulo SampleModule. Estas mensagens vão para *$upstream,* que é um nome reservado que indica IoT Hub. A segunda rota, sensorToSampleModule, retira mensagens provenientes do módulo SimuladoSensor de Temperatura e encaminha-as para a fila de entrada de *entrada 1* que viu inicializada no código SampleModule.

   ![Rever rotas em deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Construa e empurre a sua solução

Reviu o código do módulo e o modelo de implementação para entender alguns conceitos de implementação chave. Agora, está pronto para construir a imagem do recipiente SampleModule e empurrá-la para o registo do seu contentor. Com a extensão das ferramentas IoT para o Código do Estúdio Visual, este passo também gera o manifesto de implementação com base na informação no ficheiro do modelo e na informação do módulo dos ficheiros de solução.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo de contentores ao Docker para que possa empurrar a sua imagem de contentor para ser armazenada no registo.

1. Abra o terminal integrado do Código do Estúdio Visual selecionando o**Terminal** **de Visualização** > .

2. Inscreva-se no Docker com as credenciais de registo de contentores Azure que guardou após a criação do registo.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança `--password-stdin`recomendando a utilização de . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

O Visual Studio Code tem agora acesso ao registo do seu contentor, por isso é hora de transformar o código de solução numa imagem de contentor.

1. No explorador de Código de Estúdio Visual, clique no ficheiro **deployment.template.json** e selecione **Build and Push IoT Edge Solution**.

   ![Construir e empurrar módulos IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando de construção e impulso inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informação no modelo de implementação e outros ficheiros de solução. Em segundo `docker build` lugar, funciona para construir a imagem do recipiente com base no arquivo apropriado para a sua arquitetura alvo. Em seguida, `docker push` corre para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos da primeira vez, mas é mais rápido da próxima vez que executar os comandos.

2. Abra o ficheiro **deployment.amd64.json** na pasta config recém-criada. O nome de ficheiro reflete a arquitetura alvo, por isso será diferente se escolher uma arquitetura diferente.

3. Note que os dois parâmetros que tinham espaços reservados estão agora preenchidos com os seus valores adequados. A secção **de credenciais** de registo tem o seu nome de utilizador e senha de registo retirados do ficheiro .env. O **SampleModule** tem o repositório de imagem completo com o nome, versão e etiqueta de arquitetura do ficheiro module.json.

4. Abra o ficheiro **módulo.json** na pasta SampleModule.

5. Altere o número da versão para a imagem do módulo. (A versão, não a versão $schema.) Por exemplo, incremente o número da versão patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões do módulo permitem o controlo da versão e permitem testar alterações num pequeno conjunto de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de construir e empurrar, então substitui o repositório no registo do seu contentor.

6. Guarde as alterações no ficheiro module.json.

7. Clique na direita no ficheiro **deployment.template.json** novamente e, novamente, selecione **Build and Push IoT Edge Solution**.

8. Abra o ficheiro **deployment.amd64.json** novamente. Reparem que um novo ficheiro não foi criado quando dirigiu o comando de construção e pressão novamente. Pelo contrário, o mesmo ficheiro foi atualizado para refletir as alterações. A imagem SampleModule aponta agora para a versão 0.0.2 do recipiente.

9. Para verificar mais sobre o que o comando de construção e impulso fez, vá ao [portal Azure](https://portal.azure.com) e navegue até ao seu registo de contentores.

10. No registo do seu recipiente, selecione **Repositórios** e depois **samplemodule**. Verifique se ambas as versões da imagem foram empurradas para o registo.

    ![Ver ambas as versões de imagem no registo de contentores](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao construir e empurrar a imagem do módulo, muitas vezes tem a ver com a configuração do Docker na sua máquina de desenvolvimento. Utilize as seguintes verificações para rever a sua configuração:

* Dirigiu o `docker login` comando usando as credenciais que copiou do registo de contentores? Estas credenciais são diferentes das que usa para assinar no Azure.
* O seu repositório de contentores está correto? Tem o seu nome de registo de contentores correto e o nome correto do módulo? Abra o ficheiro **módulo.json** na pasta SampleModule para verificar. O valor do repositório deve parecer o ** \<nome\>do registo .azurecr.io/samplemodule**.
* Se usou um nome diferente do **SampleModule** para o seu módulo, esse nome é consistente em toda a solução?
* A sua máquina está a funcionar com o mesmo tipo de contentores que está a construir? Este tutorial é para dispositivos Linux IoT Edge, por isso o Visual Studio Code deve dizer **amd64** ou **arm32v7** na barra lateral, e Docker Desktop deve estar executando recipientes Linux.  

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Verificou que as imagens do contentor construído estão armazenadas no registo do seu contentor, por isso é hora de as implantar num dispositivo. Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No explorador de Código de Estúdio Visual, expanda a secção de Dispositivos Hub Azure IoT.

2. Clique à direita no dispositivo IoT Edge que pretende implementar e, em seguida, **selecione Create Deployment para dispositivo único**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No explorador de ficheiros, navegue na pasta **de config** e, em seguida, selecione o ficheiro **deployment.amd64.json.**

   Não utilize o ficheiro deployment.template.json, que não tenha as credenciais de registo do recipiente ou valores de imagem do módulo. Se estiver a apontar para um dispositivo Arm32 Linux, o manifesto de implantação será nomeado deployment.arm32v7.json.

4. Expanda os detalhes para o seu dispositivo IoT Edge e, em seguida, expanda a lista de **Módulos** para o seu dispositivo.

5. Utilize o botão de atualização para atualizar a visão do dispositivo até ver os módulos SimuladosSensor de Temperatura e SampleModule a funcionar no seu dispositivo.

   Pode levar alguns minutos para ambos os módulos começarem. O tempo de funcionamento do IoT Edge precisa de receber o seu novo manifesto de implantação, puxar as imagens do módulo a partir do tempo de funcionamento do recipiente e, em seguida, iniciar cada novo módulo.

   ![Ver módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens a partir do dispositivo

O código SampleModule recebe mensagens através da sua fila de entrada e passa-as através da sua fila de saída. O manifesto de implementação declarou rotas que passaram mensagens para SampleModule do Sensor de Temperatura Simulada e, em seguida, reencaminhou mensagens do SampleModule para o IoT Hub. As ferramentas Azure IoT para O Código do Estúdio Visual permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais.

1. No explorador de Código de Estúdio Visual, clique no dispositivo IoT Edge que pretende monitorizar e, em seguida, **selecione Start Monitoring Built-in Event Endpoint**.

2. Veja a janela de saída no Visual Studio Code para ver mensagens que chegam ao seu hub IoT.

   ![Ver dispositivo de entrada para mensagens em nuvem](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se quiser ver o que se passa no próprio dispositivo, utilize os comandos nesta secção para inspecionar o tempo de funcionamento do IoT Edge e os módulos em funcionamento no seu dispositivo.

Os comandos nesta secção são para o seu dispositivo IoT Edge, não para a sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligue-se agora a ele. No Azure, aceda à página geral da máquina virtual e selecione **Connect** para aceder à ligação segura à concha.

* Veja todos os módulos implantados no seu dispositivo e verifique o seu estado:

   ```bash
   iotedge list
   ```

   Deve ver quatro módulos: os dois módulos de tempo de execução IoT Edge, Sensor De Temperatura Simulado e SampleModule. Todos os quatro devem ser listados como correndo.

* Inspecione os registos para obter um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Os módulos IoT Edge são sensíveis a casos.

   Os registos SimuladosSensor de Temperatura e SampleModule devem mostrar as mensagens que estão a processar. O módulo EdgeAgent é responsável por iniciar os outros módulos, pelo que os seus registos terão informações sobre a implementação do manifesto de implementação. Se algum módulo não estiver listado ou não estiver em execução, os registos edgeAgent provavelmente terão os erros. O módulo EdgeHub é responsável pelas comunicações entre os módulos e o IoT Hub. Se os módulos estiverem a funcionar, mas as mensagens não chegarem ao seu hub IoT, os registos edgeHub provavelmente terão os erros.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurao Código de Estúdio Visual na sua máquina de desenvolvimento e implementou a partir dele o seu primeiro módulo IoT Edge. Agora que conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que possa analisar os dados que passam por ele. Escolha a sua língua preferida:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
