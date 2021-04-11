---
title: Tutorial - Desenvolver módulo para dispositivos Windows utilizando Azure IoT Edge
description: Este tutorial percorre a criação da sua máquina de desenvolvimento e recursos em nuvem para desenvolver módulos IoT Edge utilizando recipientes Windows para dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a2d8a215dfc34073783a5d8a6233ec8dbae4ec17
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218877"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>Tutorial: Desenvolver módulos IoT Edge utilizando recipientes Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Utilize o Visual Studio para desenvolver e implementar código para dispositivos Windows que executam ioT Edge.

>[!NOTE]
>IoT Edge 1.1 LTS é o último canal de lançamento que suporta recipientes Windows. A partir da versão 1.2, os recipientes windows não são suportados. Considere usar ou mover-se para [IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md) para executar IoT Edge em dispositivos Windows.

Este tutorial percorre o que é preciso para desenvolver e implementar o seu próprio código num dispositivo IoT Edge. Este tutorial é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre linguagens de programação específicas ou serviços Azure.

Este tutorial utiliza o exemplo de implantação de um **módulo C# num dispositivo Windows**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum. Se você está interessado em desenvolver em uma linguagem diferente, ou planejar implementar serviços Azure como módulos, este tutorial ainda será útil para aprender sobre as ferramentas de desenvolvimento. Assim que compreender os conceitos de desenvolvimento, poderá escolher o seu idioma preferido ou o serviço Azure para mergulhar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Prepara a tua máquina de desenvolvimento.
> * Utilize as ferramentas IoT Edge para o Visual Studio para criar um novo projeto.
> * Construa o seu projeto como recipiente e guarde-o num registo de contentores Azure.
> * Desloque o seu código para um dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Windows 10 com atualização 1809 ou mais recente.
* Pode utilizar o seu próprio computador ou uma máquina virtual, dependendo das suas preferências de desenvolvimento.
  * Certifique-se de que a sua máquina de desenvolvimento suporta a virtualização aninhada. Esta capacidade é necessária para o funcionamento de um motor de contentores, que instale na secção seguinte.
* Instale [git](https://git-scm.com/).

Um dispositivo Azure IoT Edge no Windows:

* [Instale e gere a borda Azure IoT com recipientes Windows](how-to-install-iot-edge-windows-on-windows.md).
* Recomendamos que não coloque o IoT Edge na sua máquina de desenvolvimento, mas utilize um dispositivo separado, se possível. Esta distinção entre máquina de desenvolvimento e dispositivo IoT Edge espelha com mais precisão um verdadeiro cenário de implementação, e ajuda a manter os diferentes conceitos em linha reta.

Recursos da cloud:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IoT Edge*, ou às vezes *apenas módulo* para abreviar, é um recipiente que contém código executável. Pode implantar um ou mais módulos num dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados a partir de sensores, realizar análises de dados ou operações de limpeza de dados, ou enviar mensagens para um hub IoT. Para mais informações, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo IoT Edge alvo onde o módulo será eventualmente implantado. O recipiente que constrói para manter o código do módulo deve coincidir com o sistema operativo (OS) do *dispositivo-alvo*. Para o desenvolvimento de contentores windows, este conceito é mais simples porque os contentores do Windows só funcionam em sistemas operativos Windows. Mas pode, por exemplo, utilizar a sua máquina de desenvolvimento Windows para construir módulos para dispositivos Linux IoT Edge. Nesse cenário, tens de te certificar que a tua máquina de desenvolvimento estava a funcionar com contentores Linux. Ao passar por este tutorial, tenha em mente a diferença entre a *máquina de desenvolvimento OS* e o recipiente *OS*.

Este tutorial destina-se a dispositivos Windows que executam ioT Edge. Os dispositivos Windows IoT Edge utilizam recipientes Windows. Recomendamos a utilização do Visual Studio para desenvolver dispositivos Windows, por isso é isso que este tutorial irá usar. Também pode utilizar o Código do Estúdio Visual, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento suportados para **recipientes Windows** em Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Estúdio Visual 2017/2019 |
| - | ------------------ | ------------------ |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics |   |
| **Idiomas** | C# (depurar não suportado) | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Ferramentas Azure IoT Edge para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>Instale o motor do contentor

Os módulos IoT Edge são embalados como recipientes, pelo que precisa de um motor de contentor na sua máquina de desenvolvimento para construir e gerir os contentores. Recomendamos a utilização do Docker Desktop para desenvolvimento devido às suas muitas funcionalidades e popularidade como motor de contentores. Com o Docker Desktop num computador Windows, pode alternar entre recipientes Linux e recipientes Windows para que possa desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Utilize a documentação do Docker para instalar na sua máquina de desenvolvimento:

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para windows, perguntam-lhe se pretende utilizar recipientes Linux ou Windows. Para este tutorial, utilize **recipientes Windows**. Para obter mais informações, consulte [alternar entre os recipientes Windows e Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="set-up-visual-studio-and-tools"></a>Configurar o Visual Studio e as ferramentas

As extensões IoT para Visual Studio ajudam-no a desenvolver módulos IoT Edge. Estas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem monitorizar e gerir dispositivos IoT Edge. Nesta secção, instala o Visual Studio e a extensão IoT Edge e, em seguida, configura a sua conta Azure para gerir os recursos do IoT Hub a partir do Estúdio Visual.

Este tutorial ensina os passos de desenvolvimento do Visual Studio 2019. Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), os passos são semelhantes. Se preferir utilizar o Código do Estúdio Visual, consulte as instruções em [Use Visual Studio Code para desenvolver e depurar módulos para Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Prepare o Visual Studio 2019 na sua máquina de desenvolvimento.

   * Se ainda não tiver o Visual Studio na sua máquina de desenvolvimento, instale o [Visual Studio 2019](/visualstudio/install/install-visual-studio) com as seguintes cargas de trabalho:

      * Desenvolvimento do Azure
      * Desenvolvimento de desktop com C++
      * Desenvolvimento de várias plataformas .NET core

   * Se já tem o Visual Studio 2019 na sua máquina de desenvolvimento, siga os passos no [Modificar Visual Studio](/visualstudio/install/modify-visual-studio) para adicionar as cargas de trabalho necessárias.

2. Descarregue e instale a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) para Visual Studio 2019.

   Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), descarregue e instale as [Ferramentas Azure IoT Edge para o Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Quando as suas instalações estiverem concluídas, abra o Visual Studio 2019 e selecione **Continue sem código**.

4. Selecione **Ver**  >  **Cloud Explorer**.

5. Selecione o ícone de perfil no explorador de nuvem e inscreva-se na sua conta Azure se ainda não estiver inscrito.

6. Assim que iniciar seduca, as suas assinaturas Azure estão listadas. Expanda a subscrição que tem o seu hub IoT.

7. Sob a sua subscrição, **expanda os Hubs IoT** e, em seguida, o seu hub IoT. Você deve ver uma lista dos seus dispositivos IoT, e pode usar este explorador para geri-los.

   ![Acesso recursos do Hub IoT no Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão Azure IoT Edge Tools fornece modelos de projeto para todas as línguas de módulos IoT Edge suportados no Estúdio Visual. Estes modelos têm todos os ficheiros e códigos que você precisa para implementar um módulo de trabalho para testar IoT Edge, ou dar-lhe um ponto de partida para personalizar o modelo com a sua própria lógica de negócio.

1. Selecione **Arquivo**  >  **Novo**  >  **Projeto...**

2. Na nova janela do projeto, procure **o IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64).** Clique em **Seguinte**.

   ![Criar um novo projeto Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Na configuração da sua nova janela do projeto, mude o nome do projeto e solução para algo descritivo como **o CSharpTutorialApp.** Clique em **Criar** para criar o projeto.

   ![Configure um novo projeto Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. Na janela 'Adicionar Módulo', configuure o seu projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Modelo de estúdio visual | Selecione **C# Módulo**. |
   | Nome do Módulo | Aceite o **iotEdgeModule1** padrão . |
   | Url repositório | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de recipiente é pré-apultada do valor do nome do projeto do módulo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o valor do servidor de Login a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br> O repositório de imagem final parece \<registry name\> .azurecr.io/iotedgemodule1. |

      ![Configure o seu projeto para dispositivo alvo, tipo de módulo e registo de contentores](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. **Selecione Adicionar** para criar o módulo.

Assim que o seu novo projeto estiver carregado na janela do Estúdio Visual, aproveite um momento para se familiarizar com os ficheiros que criou:

* Um projeto IoT Edge chamado **CSharpTutorialApp**.
  * A pasta **Módulos** contém ponteiros para os módulos incluídos no projeto. Neste caso, deve ser apenas IotEdgeModule1.
  * O ficheiro **.env** escondido contém as credenciais do registo do seu contentor. Estas credenciais são partilhadas com o seu dispositivo IoT Edge para que tenha acesso a retirar as imagens do contentor.
  * O **deployment.template.jsno** ficheiro é um modelo para ajudá-lo a criar um manifesto de implantação. Um *manifesto de implantação* é um ficheiro que define exatamente quais os módulos que pretende implantados num dispositivo, como devem ser configurados e como podem comunicar uns com os outros e a nuvem.
    > [!TIP]
    > Na secção de credenciais de registo, o endereço é preenchido automaticamente a partir da informação que forneceu quando criou a solução. No entanto, o nome de utilizador e as variáveis de referência de palavra-passe armazenadas no ficheiro .env. Isto é para segurança, uma vez que o ficheiro .env é ignorado, mas o modelo de implementação não é.
* Um projeto de módulo IoT Edge chamado **IotEdgeModule1**.
  * O ficheiro **.cs programa** contém o código de módulo C# padrão que vem com o modelo de projeto. O módulo predefinido retira a entrada de uma fonte e passa-a para o IoT Hub.
  * A **module.jsem** ficheiros detêm detalhes sobre o módulo, incluindo o repositório de imagem completo, a versão de imagem, e que o Dockerfile usar para cada plataforma suportada.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo ao agente IoT Edge

O tempo de funcionaamento do IoT Edge necessita das suas credenciais de registo para puxar as imagens do seu contentor para o dispositivo IoT Edge. A extensão IoT Edge tenta retirar as informações do registo do seu contentor do Azure e povoá-la no modelo de implantação.

1. Abra a **deployment.template.jsno** ficheiro na solução do módulo.

1. Encontre a **propriedade registroscredentais** nas propriedades $edgeAgent desejadas. Deve ter o seu endereço de registo preenchido automaticamente a partir das informações fornecidas ao criar o projeto, e, em seguida, os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o ficheiro **.env** na solução do módulo. (Está escondido por padrão no Solution Explorer, pelo que poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir.)

1. Adicione o **nome de utilizador** e os valores de **senha** que copiou do seu registo de contentores Azure.

1. Guarde as alterações no ficheiro .env.

### <a name="review-the-sample-code"></a>Rever o código de amostra

O modelo de solução que criou inclui código de amostra para um módulo IoT Edge. Este módulo de amostra simplesmente recebe mensagens e, em seguida, transmite-as. A funcionalidade do pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam entre si.

Cada módulo pode ter múltiplas filas *de entrada* e *saída* declaradas no seu código. O hub IoT Edge em funcionamento no dispositivo encaminha as mensagens da saída de um módulo para a entrada de um ou mais módulos. O código específico para declarar entradas e saídas varia entre as línguas, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [as rotas de Declarar](module-composition.md#declare-routes).

O código de amostra C# que vem com o modelo de projeto utiliza a [Classe MóduloClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) do IoT Hub SDK para .NET.

1. No ficheiro **.cs programa,** encontre o método **SetInputMessageHandlerAsync.**

2. O método [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) configura uma fila de entrada para receber mensagens recebidas. Reveja este método e veja como iniciaisiza uma fila de entrada chamada **input1**.

   ![Encontre o nome de entrada no construtor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Em seguida, encontre o método **SendEventAsync.**

4. O método [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) processa mensagens recebidas e configura uma fila de saída para as transmitir. Reveja este método e faça com que inicialize uma fila de saída chamada **output1**.

   ![Encontre o nome de saída no construtor SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Abra a **deployment.template.jsarquivada.**

6. Encontre os **módulos** propriedade das propriedades $edgeAgent desejadas.

   Deve haver dois módulos listados aqui. Um deles é o módulo **SimulaedTemperatureSensor,** que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que pode usar para testar os seus módulos. O outro é o módulo **IotEdgeModule1** que criou como parte deste projeto.

   A propriedade deste módulo declara quais os módulos que devem ser incluídos na implementação do seu dispositivo ou dispositivos.

7. Encontre os **percursos** propriedade das propriedades $edgeHub desejadas.

   Uma das funções do módulo hub IoT Edge é encaminhar mensagens entre todos os módulos numa implementação. Reveja os valores na propriedade de rotas. Uma rota, **IotEdgeModule1ToIoTHub,** usa um personagem wildcard (* *\** _) para incluir qualquer mensagem proveniente de qualquer fila de saída no módulo IotEdgeModule1. Estas mensagens entram em _$upstream*, que é um nome reservado que indica IoT Hub. A outra rota, **sensorToIotEdgeModule1,** recebe mensagens provenientes do módulo SimulatedTemperatureSensor e encaminha-as para a fila de entrada *1* do módulo IotEdgeModule1.

   ![Rever rotas em deployment.template.jsem](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Construa e empurre a sua solução

Reviu o código do módulo e o modelo de implementação para entender alguns conceitos de implementação chave. Agora, estás pronto para construir a imagem do contentor IotEdgeModule1 e empurrá-la para o registo do contentor. Com a extensão de ferramentas IoT para o Visual Studio, este passo também gera o manifesto de implementação com base nas informações no ficheiro do modelo e nas informações do módulo a partir dos ficheiros de solução.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo de contentores ao Docker na sua máquina de desenvolvimento para que possa empurrar a imagem do seu recipiente para ser armazenado no registo.

1. Abrir PowerShell ou um pedido de comando.

2. Inscreva-se no Docker com as credenciais de registo do contentor Azure que guardou depois de criar o registo.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

A sua máquina de desenvolvimento tem agora acesso ao seu registo de contentores, e os seus dispositivos IoT Edge também. É hora de transformar o código do projeto numa imagem de contentor.

1. Clique com o botão direito na pasta do projeto **CSharpTutorialApp** e selecione **Módulos de Borda De Construção e Pressão**.

   ![Constroem e empurrem módulos IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

2. Abra a **deployment.windows-amd64.jsno** ficheiro na pasta config recém-criada. (A pasta config pode não aparecer no Solution Explorer em Visual Studio. Se for esse o caso, selecione o ícone **mostrar todos os ficheiros** na barra de tarefas Do Explorador de Soluções.)

3. Encontre o parâmetro de **imagem** da secção IotEdgeModule1. Note que a imagem contém o repositório de imagem completo com o nome, versão e etiqueta de arquitetura do module.jsem arquivo.

4. Abra a **module.jsno** ficheiro na pasta IotEdgeModule1.

5. Altere o número da versão para a imagem do módulo. (A versão, não a versão $schema.) Por exemplo, incremente o número da versão patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões do módulo permitem controlar a versão e permitem testar alterações num pequeno conjunto de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de construir e empurrar, então substitui o repositório no registo do seu contentor.

6. Guarde as alterações para a module.jsno ficheiro.

7. Clique com o botão direito na pasta do projeto **CSharpTutorialApp** novamente e selecione Constroem e empurrem novamente **os módulos de borda IoT.**

8. Abra novamente o **deployment.windows-amd64.jsarquivado.** Note que um novo ficheiro não foi criado quando executou o comando de construção e push novamente. Pelo contrário, o mesmo ficheiro foi atualizado para refletir as alterações. A imagem IotEdgeModule1 aponta agora para a versão 0.0.2 do recipiente. Esta alteração no manifesto de implementação é como se diz ao dispositivo IoT Edge que há uma nova versão de um módulo para puxar.

9. Para verificar ainda mais o que o comando de construção e pressão fez, vá ao [portal Azure](https://portal.azure.com) e navegue até ao registo do seu contentor.

10. No registo do seu contentor, **selecione Repositórios** e, em seguida, **iotedgemodule1**. Verifique se ambas as versões da imagem foram empurradas para o registo.

    ![Ver ambas as versões de imagem no registo de contentores](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao construir e empurrar a imagem do módulo, muitas vezes tem a ver com a configuração do Docker na sua máquina de desenvolvimento. Utilize as seguintes verificações para rever a sua configuração:

* Executou o `docker login` comando usando as credenciais que copiou do registo do seu contentor? Estas credenciais são diferentes das que usa para assinar no Azure.
* O seu repositório de contentores está correto? Tem o nome correto do registo do contentor e o nome correto do módulo? Abra a **module.jsno** ficheiro na pasta IotEdgeModule1 para verificar. O valor do repositório deve parecer **\<registry name\> .azurecr.io/iotedgemodule1**.
* Se usou um nome diferente do **IotEdgeModule1** para o seu módulo, esse nome é consistente em toda a solução?
* A tua máquina está a funcionar com o mesmo tipo de contentores que estás a construir? Este tutorial destina-se a dispositivos Windows IoT Edge, pelo que os seus ficheiros Visual Studio devem ter a extensão **windows-amd64** e o Docker Desktop deve estar a executar contentores Windows.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Verificou que as imagens dos contentores construídos estão armazenadas no registo do seu contentor, por isso está na altura de as implantar num dispositivo. Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. Abra o Cloud Explorer no Visual Studio e expanda os detalhes para o seu hub IoT.

2. Selecione o nome do dispositivo para o quais pretende implantar. Na lista **de Ações,** selecione **Criar Implementação**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-windows/create-deployment.png)

3. No explorador de ficheiros, navegue para a pasta config do seu projeto e selecione a **deployment.windows-amd64.jsno** ficheiro. Este ficheiro é frequentemente localizado em `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Não utilize o deployment.template.jsno ficheiro, que não tem os valores de imagem do módulo completos.

4. Expanda os detalhes do seu dispositivo IoT Edge no Cloud Explorer para ver os módulos do seu dispositivo.

5. Utilize o botão **Refresh** para atualizar o estado do dispositivo para ver se os módulos SimulatedTemperatureSensor e IotEdgeModule1 estão implantados no seu dispositivo.

   ![Ver módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens do dispositivo

O código IotEdgeModule1 recebe mensagens através da sua fila de entrada e passa-as através da sua fila de saída. O manifesto de implantação declarou rotas que passaram mensagens de SimulatedTemperatureSensor a IotEdgeModule1, e depois reencaminharam mensagens de IotEdgeModule1 para IoT Hub. As ferramentas Azure IoT Edge para o Visual Studio permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais.

1. No Visual Studio Cloud Explorer, selecione o nome do dispositivo IoT Edge para o quais implementou.

2. No menu **Ações,** selecione **Start Monitoring Built-in Event Endpoint**.

3. Assista à secção **de saída** no Estúdio Visual para ver mensagens que chegam ao seu hub IoT.

   Pode levar alguns minutos para ambos os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

   ![Ver dispositivo de entrada para mensagens em nuvem](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se quiser ver o que se passa no seu próprio dispositivo, utilize os comandos desta secção para inspecionar o tempo de funcionamento do IoT Edge e os módulos em funcionamento no seu dispositivo.

Os comandos desta secção são para o seu dispositivo IoT Edge, não para a sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligue-o agora. Em Azure, aceda à página geral da máquina virtual e selecione **Connect** para aceder à ligação remota do ambiente de trabalho. No dispositivo, abra um comando ou uma janela PowerShell para executar os `iotedge` comandos.

* Veja todos os módulos implantados no seu dispositivo e verifique o seu estado:

   ```cmd
   iotedge list
   ```

   Deverá ver quatro módulos: os dois módulos de tempo de execução IoT Edge, SimulatedTemperatureSensor e IotEdgeModule1. Todos os quatro devem estar listados como correndo.

* Inspecione os registos de um módulo específico:

   ```cmd
   iotedge logs <module name>
   ```

   Os módulos IoT Edge são sensíveis a maiíssquisos.

   Os registos SimulatedTemperatureSensor e IotEdgeModule1 devem mostrar as mensagens que estão a processar. O módulo EdgeAgent é responsável por iniciar os outros módulos, pelo que os seus registos terão informações sobre a implementação do manifesto de implementação. Se algum módulo não estiver listado ou não estiver em execução, os registos edgeAgent provavelmente terão os erros. O módulo EdgeHub é responsável pelas comunicações entre os módulos e o IoT Hub. Se os módulos estiverem a funcionar, mas as mensagens não chegarem ao seu hub IoT, os registos edgeHub provavelmente terão os erros.

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura o Visual Studio 2019 na sua máquina de desenvolvimento e implementou o seu primeiro módulo IoT Edge a partir dele. Agora que conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que possa analisar os dados que passam por ele. Escolha o seu idioma preferido:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md) 
>  [C#](tutorial-csharp-module-windows.md)