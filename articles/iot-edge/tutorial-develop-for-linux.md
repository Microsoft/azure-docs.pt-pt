---
title: Tutorial - Desenvolver módulo para dispositivos Linux usando Azure IoT Edge
description: Este tutorial percorre a criação da sua máquina de desenvolvimento e recursos em nuvem para desenvolver módulos IoT Edge utilizando recipientes Linux para dispositivos Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fea8f52ebf40ba8195de134098693f90315bb384
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461424"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Tutorial: Desenvolver módulos IoT Edge com recipientes Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utilize o Código do Estúdio Visual para desenvolver e implementar código para dispositivos que executam o IoT Edge.

No arranque rápido, criou um dispositivo IoT Edge e implementou um módulo a partir do Azure Marketplace. Este tutorial passa pelo desenvolvimento e implantação do seu próprio código para um dispositivo IoT Edge. Este artigo é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre linguagens de programação específicas ou serviços Azure.

Este tutorial utiliza o exemplo de implantação de um **módulo C# num dispositivo Linux**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum para soluções IoT Edge. Mesmo que planeie usar uma linguagem diferente ou implementar um serviço Azure, este tutorial ainda é útil para aprender sobre as ferramentas e conceitos de desenvolvimento. Complete esta introdução ao processo de desenvolvimento, em seguida, escolha o seu idioma preferido ou serviço Azure para mergulhar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Prepara a tua máquina de desenvolvimento.
> * Utilize as ferramentas IoT Edge para o Código do Estúdio Visual para criar um novo projeto.
> * Construa o seu projeto como recipiente e guarde-o num registo de contentores Azure.
> * Desloque o seu código para um dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Pode utilizar o seu próprio computador ou uma máquina virtual, dependendo das suas preferências de desenvolvimento.
  * Certifique-se de que a sua máquina de desenvolvimento suporta a virtualização aninhada. Esta capacidade é necessária para o funcionamento de um motor de contentores, que instale na secção seguinte.
* A maioria dos sistemas operativos que podem funcionar um motor de contentores podem ser usados para desenvolver módulos IoT Edge para dispositivos Linux. Este tutorial utiliza um computador Windows, mas aponta diferenças conhecidas no macOS ou Linux.
* Instale [Git](https://git-scm.com/), para puxar pacotes de modelos de módulos mais tarde neste tutorial.  
* [Extensão C# para Visual Studio Code (com tecnologia da OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

Um dispositivo Azure IoT Edge:

* Recomendamos que não coloque ioT Edge na sua máquina de desenvolvimento, mas use um dispositivo separado. Esta distinção entre máquina de desenvolvimento e dispositivo IoT Edge espelha com mais precisão um verdadeiro cenário de implementação, e ajuda a manter os diferentes conceitos em linha reta.
* Se não tiver um segundo dispositivo disponível, utilize o artigo de arranque rápido para criar um dispositivo IoT Edge em Azure com uma [máquina virtual Linux](quickstart-linux.md).

Recursos da cloud:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IoT Edge*, ou às vezes *apenas módulo* para abreviar, é um recipiente com código executável. Pode implantar um ou mais módulos num dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados a partir de sensores, limpar e analisar dados, ou enviar mensagens para um hub IoT. Para mais informações, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo IoT Edge alvo onde o módulo será eventualmente implantado. O recipiente que constrói para manter o código do módulo deve coincidir com o sistema operativo (OS) do *dispositivo-alvo*. Por exemplo, o cenário mais comum é alguém desenvolver um módulo num computador Windows que pretenda direcionar um dispositivo Linux com ioT Edge. Nesse caso, o sistema operativo do contentor seria o Linux. Ao passar por este tutorial, tenha em mente a diferença entre a *máquina de desenvolvimento OS* e o recipiente *OS*.

>[!TIP]
>Se estiver a utilizar [o IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md), então o dispositivo *alvo* no seu cenário é a máquina virtual Linux, e não o anfitrião do Windows.

Este tutorial visa dispositivos que executam IoT Edge com recipientes Linux. Pode utilizar o seu sistema operativo preferido desde que a sua máquina de desenvolvimento utilize recipientes Linux. Recomendamos a utilização do Código do Estúdio Visual para desenvolver com recipientes Linux, por isso é isso que este tutorial vai usar. Também pode utilizar o Visual Studio, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento suportados para **recipientes Linux** em Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Estúdio Visual 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivos Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Idiomas** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Ferramentas Azure IoT Edge para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>O suporte para dispositivos Linux ARM64 está disponível em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [os módulos Develop and debug ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Este tutorial ensina os passos de desenvolvimento para o Código do Estúdio Visual. Se preferir utilizar o Visual Studio, consulte as instruções no [Use Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge](how-to-visual-studio-develop-module.md).
## <a name="install-container-engine"></a>Instale o motor do contentor

Os módulos IoT Edge são embalados como recipientes, pelo que precisa de um motor de contentor na sua máquina de desenvolvimento para os construir e gerir. Recomendamos o Docker Desktop para desenvolvimento devido ao seu suporte de funcionalidade e popularidade. O Docker Desktop no Windows permite alternar entre recipientes Linux e recipientes Windows para que possa desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Utilize a documentação do Docker para instalar na sua máquina de desenvolvimento:

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para windows, perguntam-lhe se pretende utilizar recipientes Linux ou Windows. Esta decisão pode ser alterada a qualquer momento utilizando um interruptor fácil. Para este tutorial, usamos recipientes Linux porque os nossos módulos estão direcionados para dispositivos Linux. Para obter mais informações, consulte [alternar entre os recipientes Windows e Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

* [Instale o Docker Desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [sobre o Docker CE](https://docs.docker.com/install/) para obter informações de instalação em várias plataformas Linux.
  * Para o Subsistema Windows para Linux (WSL), instale o Docker Desktop para windows.

## <a name="set-up-vs-code-and-tools"></a>Configurar código VS e ferramentas

Utilize as extensões IoT para o Código do Estúdio Visual para desenvolver módulos IoT Edge. Estas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem monitorizar e gerir dispositivos IoT Edge. Nesta secção, instala o Código do Estúdio Visual e a extensão IoT e, em seguida, configura a sua conta Azure para gerir os recursos do IoT Hub a partir do Código do Estúdio Visual.

1. Instale [o Código do Estúdio Visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento.

2. Uma vez terminada a instalação, selecione  >  **Ver Extensões**.

3. Procure **ferramentas Azure IoT**, que na verdade é uma coleção de extensões que o ajudam a interagir com dispositivos IoT Hub e IoT, bem como desenvolver módulos IoT Edge.

4. Selecione **Instalar**. Cada extensão incluída instala-se individualmente.

5. Quando as extensões terminarem de instalar, abra a paleta de comando selecionando **a**  >  **Paleta de Comando ver**.

6. Na paleta de comando, procure e selecione **Azure: Iniciar sação .** Siga os avisos para iniciar sessão na conta do Azure.

7. Na paleta de comando novamente, procure e selecione **Azure IoT Hub: Selecione IoT Hub**. Siga as instruções para selecionar a subscrição do Azure e o hub IoT.

8. Abra a secção de explorador do Código do Estúdio Visual selecionando o ícone na barra de atividade à esquerda ou selecionando **o View**  >  **Explorer**.

9. Na parte inferior da secção explorador, expanda o menu **Azure IoT Hub / Dispositivos em** colapso. Deve ver os dispositivos e dispositivos IoT Edge associados ao hub IoT que selecionou através da paleta de comando.

   ![Ver dispositivos no seu hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão Azure IoT Tools fornece modelos de projeto para todas as línguas de módulos IoT Edge suportados no Código do Estúdio Visual. Estes modelos têm todos os ficheiros e códigos que você precisa para implementar um módulo de trabalho para testar IoT Edge, ou dar-lhe um ponto de partida para personalizar o modelo com a sua própria lógica de negócio.

Para este tutorial, usamos o modelo de módulo C# porque é o modelo mais utilizado.

### <a name="create-a-project-template"></a>Crie um modelo de projeto

Na paleta de comando Visual Studio Code, procure e selecione **Azure IoT Edge: New IoT Edge Solution**. Siga as instruções e use os seguintes valores para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a **solução edges padrão**. |
   | Selecionar modelo de módulo | Escolha **o módulo C. .** |
   | Indicar um nome para o módulo | Aceite a **amostra padrãoModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor está pré-apovoada do nome que forneceu no último passo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o valor do servidor de Login a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br> O repositório de imagem final parece \<registry name\> .azurecr.io/samplemodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Assim que a sua nova solução estiver carregada na janela Visual Studio Code, aproveite um momento para se familiarizar com os ficheiros que criou:

* A pasta **.vscode** contém um ficheiro chamado **launch.jsligado,** que é utilizado para depurar módulos.
* A pasta **de módulos** contém uma pasta para cada módulo na sua solução. Neste momento, isso deve ser **apenas SampleModule,** ou qualquer nome que tenha dado ao módulo. A pasta SampleModule contém o código de programa principal, os metadados do módulo e vários ficheiros Docker.
* O ficheiro **.env** contém as credenciais do registo do seu contentor. Estas credenciais são partilhadas com o seu dispositivo IoT Edge para que tenha acesso a retirar as imagens do contentor.
* Os **deployment.debug.template.jsno** ficheiro edeployment.template.js **no** ficheiro são modelos que o ajudam a criar um manifesto de implantação. Um *manifesto de implantação* é um ficheiro que define exatamente quais os módulos que pretende implantados num dispositivo, como devem ser configurados e como podem comunicar uns com os outros e a nuvem. Os ficheiros do modelo usam ponteiros para alguns valores. Quando transforma o modelo num verdadeiro manifesto de implantação, os ponteiros são substituídos por valores retirados de outros ficheiros de solução. Localize os dois espaços reservados comuns no seu modelo de implantação:

  * Na secção de credenciais de registo, o endereço é preenchido automaticamente a partir da informação que forneceu quando criou a solução. No entanto, o nome de utilizador e a palavra-passe referem as variáveis armazenadas no ficheiro .env. Esta configuração é para segurança, uma vez que o ficheiro .env é ignorado, mas o modelo de implementação não é.
  * Na secção SampleModule, a imagem do recipiente não é preenchida, apesar de ter fornecido o repositório de imagens quando criou a solução. Este espaço reservado aponta para a **module.jsno** ficheiro dentro da pasta SampleModule. Se for a esse ficheiro, verá que o campo de imagens contém o repositório, mas também um valor de etiqueta que é composto pela versão e pela plataforma do recipiente. Pode iterar a versão manualmente como parte do seu ciclo de desenvolvimento e selecionar a plataforma do recipiente utilizando um comutador que introduzimos mais tarde nesta secção.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo ao agente IoT Edge

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O tempo de funcionaamento necessita destas credenciais para puxar as imagens do seu contentor para o dispositivo IoT Edge.

>[!NOTE]
>Se não substituiu o valor **local:5000** pelo valor do servidor de login do registo do seu contentor Azure, no modelo de projeto Criar um passo de modelo de [**projeto,**](#create-a-project-template) o ficheiro **.env** e a secção de registoscredentais do manifesto de implantação desaparecerão. 

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. Abra o ficheiro **.env** na solução do módulo.
2. Adicione o **nome de utilizador** e os valores de **senha** que copiou do seu registo de contentores Azure.
3. Guarde as alterações no ficheiro .env.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C# para dispositivos Linux AMD64 e ARM32v7. Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque isso afeta a forma como o recipiente é construído e funciona. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda**, ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

   ![Selecione ícone de arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64** padrão.

### <a name="review-the-sample-code"></a>Rever o código de amostra

O modelo de solução que criou inclui código de amostra para um módulo IoT Edge. Este módulo de amostra simplesmente recebe mensagens e, em seguida, transmite-as. A funcionalidade do pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam entre si.

Cada módulo pode ter múltiplas filas *de entrada* e *saída* declaradas no seu código. O hub IoT Edge em funcionamento no dispositivo encaminha as mensagens da saída de um módulo para a entrada de um ou mais módulos. O código específico para declarar entradas e saídas varia entre as línguas, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [as rotas de Declarar](module-composition.md#declare-routes).

O código de amostra C# que vem com o modelo de projeto utiliza a [Classe MóduloClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) do IoT Hub SDK para .NET.

1. Abra o ficheiro **program.cs,** que está dentro dos **módulos/pasta SampleModule.**

2. No programa.cs, encontre o método **SetInputMessageHandlerAsync.**

3. O método [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) configura uma fila de entrada para receber mensagens recebidas. Reveja este método e veja como iniciaisiza uma fila de entrada chamada **input1**.

   ![Encontre o nome de entrada no construtor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Em seguida, encontre o método **SendEventAsync.**

5. O método [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) processa mensagens recebidas e configura uma fila de saída para as transmitir. Reveja este método e faça com que inicialize uma fila de saída chamada **output1**.

   ![Encontre o nome de saída em SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra a **deployment.template.jsarquivada.**

7. Encontre os **módulos** propriedade das propriedades $edgeAgent desejadas.

   Deve haver dois módulos listados aqui. Um deles é o módulo **SimulaedTemperatureSensor,** que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que pode usar para testar os seus módulos. O outro é o módulo **SampleModule** que criou como parte desta solução.

8. Na parte inferior do ficheiro, encontre as propriedades desejadas para o módulo **$edgeHub.**

   Uma das funções do módulo hub IoT Edge é encaminhar mensagens entre todos os módulos numa implementação. Reveja os valores na propriedade de **rotas.** Uma rota, **SampleModuleToIoTHub,** utiliza um caracteres wildcard (* *\** _) para indicar quaisquer mensagens provenientes de quaisquer filas de saída no módulo SampleModule. Estas mensagens entram em _$upstream*, que é um nome reservado que indica IoT Hub. A outra rota, **sensorToSampleModule,** recolhe mensagens provenientes do módulo SimulatedTemperatureSensor e encaminha-as para a fila de entrada *1* que viu inicializada no código SampleModule.

   ![Rever rotas em deployment.template.jsem](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Construa e empurre a sua solução

Reviu o código do módulo e o modelo de implementação para entender alguns conceitos de implementação chave. Agora, está pronto para construir a imagem do contentor SampleModule e empurrá-la para o registo do contentor. Com a extensão de ferramentas IoT para o Código do Estúdio Visual, este passo também gera o manifesto de implementação com base nas informações no ficheiro do modelo e nas informações do módulo a partir dos ficheiros de solução.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo do seu contentor ao Docker para que possa empurrar a imagem do seu contentor para ser armazenado no registo.

1. Abra o terminal integrado do Código do Estúdio Visual selecionando o Terminal **de Visualização**  >  .

2. Inscreva-se no Docker com as credenciais de registo do Contentor Azure que guardou após a criação do registo.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)
   
3. Iniciar sessão no Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Construir e empurrar

O Código do Estúdio Visual tem agora acesso ao registo do seu contentor, por isso está na altura de transformar o código da solução numa imagem de contentor.

1. No explorador visual Studio Code, clique comdeployment.template.js **no** ficheiro e selecione **Build and Push IoT Edge Solution**.

   ![Constroem e empurrem módulos IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

2. Abra a **deployment.amd64.jsficheiro** na pasta config recém-criada. O nome de arquivo reflete a arquitetura-alvo, por isso será diferente se você escolher uma arquitetura diferente.

3. Note que os dois parâmetros que tinham espaços reservados agora estão preenchidos com os seus valores adequados. A secção **de registoscondentais** tem o seu nome de utilizador de registo e palavra-passe retirados do ficheiro .env. O **SampleModule** tem o repositório de imagem completo com o nome, versão e etiqueta de arquitetura do module.jsem arquivo.

4. Abra a **module.jsno** ficheiro na pasta SampleModule.

5. Altere o número da versão para a imagem do módulo. (A versão, não a versão $schema.) Por exemplo, incremente o número da versão patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões do módulo permitem controlar a versão e permitem testar alterações num pequeno conjunto de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de construir e empurrar, então substitui o repositório no registo do seu contentor.

6. Guarde as alterações para a module.jsno ficheiro.

7. Clique com o botão direito **no ficheiro de** novodeployment.template.jse volte a selecionar Build and Push **IoT Edge Solution**.

8. Abra novamente o **deployment.amd64.jsarquivado.** Note que um novo ficheiro não foi criado quando executou o comando de construção e push novamente. Pelo contrário, o mesmo ficheiro foi atualizado para refletir as alterações. A imagem SampleModule aponta agora para a versão 0.0.2 do recipiente.

9. Para verificar ainda mais o que o comando de construção e pressão fez, vá ao [portal Azure](https://portal.azure.com) e navegue até ao registo do seu contentor.

10. No registo do seu contentor, **selecione Repositórios** e, em seguida, **samplemodule**. Verifique se ambas as versões da imagem foram empurradas para o registo.

    ![Ver ambas as versões de imagem no registo de contentores](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao construir e empurrar a imagem do módulo, muitas vezes tem a ver com a configuração do Docker na sua máquina de desenvolvimento. Utilize as seguintes verificações para rever a sua configuração:

* Executou o `docker login` comando usando as credenciais que copiou do registo do seu contentor? Estas credenciais são diferentes das que usa para assinar no Azure.
* O seu repositório de contentores está correto? Tem o nome correto do registo do contentor e o nome correto do módulo? Abra a **module.jsno** ficheiro na pasta SampleModule para verificar. O valor do repositório deve parecer **\<registry name\> .azurecr.io/samplemodule**.
* Se usou um nome diferente do **SampleModule** para o seu módulo, esse nome é consistente em toda a solução?
* A tua máquina está a funcionar com o mesmo tipo de contentores que estás a construir? Este tutorial é para dispositivos Linux IoT Edge, por isso o Código visual do estúdio deve dizer **amd64** ou **arm32v7** na barra lateral, e Docker Desktop deve estar a executar recipientes Linux.  

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Verificou que as imagens dos contentores construídos estão armazenadas no registo do seu contentor, por isso está na altura de as implantar num dispositivo. Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no dispositivo IoT Edge para o quais pretende implementar e, em seguida, selecione **Criar Implementação para dispositivo único**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No explorador de ficheiros, navegue na pasta **config** e, em seguida, selecione a **deployment.amd64.jsno** ficheiro.

   Não utilize o deployment.template.jsno ficheiro, que não tem as credenciais de registo do contentor ou os valores de imagem do módulo nele. Se estiver a apontar para um dispositivo Linux ARM32, o manifesto de implantação será nomeado deployment.arm32v7.js.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver os novos módulos SimulatedTemperatureSensor e SampleModule em funcionamento no seu dispositivo.

   Pode levar alguns minutos para os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

   ![Ver módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens do dispositivo

O código SampleModule recebe mensagens através da sua fila de entrada e passa-as através da sua fila de saída. O manifesto de implantação declarou rotas que passaram mensagens para SampleModule a partir de SimulatedTemperatureSensor, e depois reencaminharam mensagens da SampleModule para o IoT Hub. As ferramentas Azure IoT para Código de Estúdio Visual permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais.

1. No explorador visual Studio Code, clique com o botão direito no dispositivo IoT Edge que pretende monitorizar e, em seguida, selecione **Start Monitoring Built-in Event Endpoint**.

2. Observe a janela de saída no Código do Estúdio Visual para ver as mensagens que chegam ao seu hub IoT.

   ![Ver dispositivo de entrada para mensagens em nuvem](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se quiser ver o que se passa no seu próprio dispositivo, utilize os comandos desta secção para inspecionar o tempo de funcionamento do IoT Edge e os módulos em funcionamento no seu dispositivo.

Os comandos desta secção são para o seu dispositivo IoT Edge, não para a sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligue-o agora. Em Azure, aceda à página geral da máquina virtual e selecione **Connect** para aceder à ligação de concha segura.

* Veja todos os módulos implantados no seu dispositivo e verifique o seu estado:

   ```bash
   iotedge list
   ```

   Deverá ver quatro módulos: os dois módulos de tempo de execução IoT Edge, SimulatedTemperatureSensor e SampleModule. Todos os quatro devem estar listados como correndo.

* Inspecione os registos de um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Os módulos IoT Edge são sensíveis a maiíssquisos.

   Os registos SimulatedTemperatureSensor e SampleModule devem mostrar as mensagens que estão a processar. O módulo EdgeAgent é responsável por iniciar os outros módulos, pelo que os seus registos terão informações sobre a implementação do manifesto de implementação. Se algum módulo não estiver listado ou não estiver em execução, os registos edgeAgent provavelmente terão os erros. O módulo EdgeHub é responsável pelas comunicações entre os módulos e o IoT Hub. Se os módulos estiverem a funcionar, mas as mensagens não chegarem ao seu hub IoT, os registos edgeHub provavelmente terão os erros.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura o Código do Estúdio Visual na sua máquina de desenvolvimento e implementou o seu primeiro módulo IoT Edge a partir dele. Agora que conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que possa analisar os dados que passam por ele. Escolha o seu idioma preferido:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Rio Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Pitão](tutorial-python-module.md)
