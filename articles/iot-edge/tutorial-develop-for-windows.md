---
title: Tutorial - Desenvolver módulo para dispositivos Windows usando o Edge Azure IoT
description: Este tutorial passa pela criação da sua máquina de desenvolvimento e recursos em nuvem para desenvolver módulos IoT Edge utilizando recipientes Windows para dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76772257"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Tutorial: Desenvolver módulos IoT Edge para dispositivos Windows

Utilize o Visual Studio para desenvolver e implementar código para dispositivos Windows que executam o IoT Edge.

No arranque rápido, criou um dispositivo IoT Edge utilizando uma máquina virtual do Windows e implementou um módulo pré-construído a partir do Azure Marketplace. Este tutorial percorre o que é preciso para desenvolver e implementar o seu próprio código para um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre linguagens de programação específicas ou serviços Azure.

Este tutorial usa o exemplo de implementação de um **módulo C# para um dispositivo Windows**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum. Se você está interessado em desenvolver-se em uma língua diferente, ou planejar implementar serviços Azure como módulos, este tutorial ainda será útil para aprender sobre as ferramentas de desenvolvimento. Assim que compreender os conceitos de desenvolvimento, pode escolher o seu serviço de idioma ou Azure preferido para mergulhar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Instale a sua máquina de desenvolvimento.
> * Utilize as ferramentas IoT Edge para o Estúdio Visual para criar um novo projeto.
> * Construa o seu projeto como recipiente e guarde-o num registo de contentores Azure.
> * Implemente o seu código num dispositivo IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IoT Edge,* ou às vezes apenas *módulo* para abreviar, é um recipiente que contém código executável. Pode implantar um ou mais módulos num dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados a partir de sensores, realizar análisede dados ou operações de limpeza de dados, ou enviar mensagens para um hub IoT. Para mais informações, consulte [os módulos Understand Azure IoT Edge](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo ioT Edge alvo onde o módulo será eventualmente implantado. O recipiente que constrói para manter o seu código de módulo deve coincidir com o sistema operativo (OS) do *dispositivo-alvo*. Para o desenvolvimento de contentores Windows, este conceito é mais simples porque os recipientes windows funcionam apenas em sistemas operativos Windows. Mas poderia, por exemplo, utilizar a sua máquina de desenvolvimento do Windows para construir módulos para dispositivos Linux IoT Edge. Nesse cenário, terias de ter a certeza que a tua máquina de desenvolvimento estava a gerir contentores Linux. Ao passar por este tutorial, lembre-se da diferença entre a máquina de *desenvolvimento OS* e o os *recipiente S*.

Este tutorial tem como alvo dispositivos Windows que executam o IoT Edge. Os dispositivos Windows IoT Edge utilizam recipientes Windows. Recomendamos a utilização do Visual Studio para desenvolver dispositivos Windows, por isso é isso que este tutorial irá utilizar. Também pode utilizar o Visual Studio Code, embora existam diferenças de suporte entre as duas ferramentas.

A tabela seguinte lista os cenários de desenvolvimento suportados para **recipientes Windows** em Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Estúdio Visual 2017/2019 |
| - | ------------------ | ------------------ |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics |   |
| **Linguagens** | C# (depuração não suportada) | C <br> C# |
| **Mais informações** | [Borda Azure IoT para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas de borda Azure IoT para Estúdio Visual 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Ferramentas de borda Azure IoT para Estúdio Visual 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento:

* Windows 10 com atualização 1809 ou mais recente.
* Pode utilizar o seu próprio computador ou uma máquina virtual, dependendo das suas preferências de desenvolvimento.
  * Certifique-se de que a sua máquina de desenvolvimento suporta a virtualização aninhada. Esta capacidade é necessária para a funcionamento de um motor de contentor, que instala na secção seguinte.
* Instale [Git](https://git-scm.com/).

Um dispositivo Azure IoT Edge no Windows:

* Recomendamos que não faça o IoT Edge na sua máquina de desenvolvimento, mas utilize um dispositivo separado. Esta distinção entre a máquina de desenvolvimento e o dispositivo IoT Edge espelha com mais precisão um verdadeiro cenário de implementação, e ajuda a manter os diferentes conceitos corretos.
* Se não tiver um segundo dispositivo disponível, utilize o artigo quickstart para criar um dispositivo IoT Edge em Azure com uma [máquina virtual Windows](quickstart.md).

Recursos da cloud:

* Um [centro ioT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de nível padrão em Azure.

## <a name="install-container-engine"></a>Instalar o motor do recipiente

Os módulos IoT Edge são embalados como recipientes, pelo que é necessário um motor de contentor na sua máquina de desenvolvimento para construir e gerir os contentores. Recomendamos a utilização do Docker Desktop para o desenvolvimento devido às suas muitas funcionalidades e popularidade como motor de contentores. Com o Docker Desktop num computador Windows, pode alternar entre os contentores Linux e windows para que possa desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Utilize a documentação do Docker para instalar na sua máquina de desenvolvimento:

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para windows, é-lhe perguntado se pretende utilizar recipientes Linux ou Windows. Para este tutorial, utilize **recipientes Windows**. Para mais informações, consulte [a Switch entre os recipientes Windows e Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Configurar o Estúdio Visual e as ferramentas

As extensões IoT para O Estúdio Visual ajudam-no a desenvolver módulos IoT Edge. Estas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implementação e permitem-lhe monitorizar e gerir dispositivos IoT Edge. Nesta secção, instala o Visual Studio e a extensão IoT Edge e, em seguida, configura a sua conta Azure para gerir os recursos do IoT Hub a partir do Visual Studio.

Este tutorial ensina os passos de desenvolvimento do Visual Studio 2019. Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), os passos são semelhantes. Se preferir utilizar o Código do Estúdio Visual, consulte as instruções no Código do [Estúdio Visual para desenvolver e depurar módulos para O Edge Azure IoT](how-to-vs-code-develop-module.md).

1. Prepare o Visual Studio 2019 na sua máquina de desenvolvimento.

   * Se ainda não tiver o Visual Studio na sua máquina de desenvolvimento, instale o [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) com as seguintes cargas de trabalho:

      * Desenvolvimento do Azure
      * Desenvolvimento do ambiente de trabalho com C++
      * Desenvolvimento de várias plataformas .NET core

   * Se já tem o Visual Studio 2019 na sua máquina de desenvolvimento, siga os passos no [Estúdio Visual Modificado](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para adicionar as cargas de trabalho necessárias.

2. Descarregue e instale a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) para o Visual Studio 2019.

   Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), descarregue e instale as Ferramentas De [Borda Azure IoT para O Estúdio Visual 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Quando as suas instalações estiverem concluídas, abra o Visual Studio 2019 e selecione **Continue sem código**.

4. Selecione **Ver** > **Cloud Explorer**.

5. Selecione o ícone de perfil no explorador de nuvem e inscreva-se na sua conta Azure se ainda não tiver assinado.

6. Assim que iniciar sessão, as suas assinaturas Azure estão listadas. Expanda a subscrição que tem o seu hub IoT.

7. Sob a sua subscrição, expanda **o IoT Hubs** e o seu hub IoT. Você deve ver uma lista dos seus dispositivos IoT, e pode usar este explorador para geri-los.

   ![Acesso aos recursos do Hub IoT no Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão Azure IoT Edge Tools fornece modelos de projeto para todos os idiomas de módulos IoT Edge suportados no Estúdio Visual. Estes modelos têm todos os ficheiros e códigos que precisa para implementar um módulo de trabalho para testar o IoT Edge, ou dar-lhe um ponto de partida para personalizar o modelo com a sua própria lógica de negócio.

1. Selecione **File** > **New** > **Project...**

2. Na nova janela do projeto, procure **ioT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64).** Clique em **Seguinte**.

   ![Criar um novo projeto Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Na configuração da sua nova janela de projeto, mude o nome do projeto e solução para algo descritivo como **cSharpTutorialApp**. Clique em **Criar** para criar o projeto.

   ![Configure um novo projeto Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. Na janela Adicionar Módulo, configure o seu projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Modelo de estúdio visual | Selecione **Módulo C#**. |
   | Nome do Módulo | Aceite o **iotEdgeModule1**predefinido . |
   | Url repositório | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor é pré-povoada a partir do valor do nome do projeto do módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode recuperar o valor do **servidor Login** a partir da página **de visão geral** do seu registo de contentores no portal Azure. <br><br> O repositório de \<imagem final\>parece o nome do registo .azurecr.io/iotedgemodule1. |

      ![Configure o seu projeto para dispositivo-alvo, tipo de módulo e registo de contentores](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecione **Adicionar** para criar o módulo.

Assim que o seu novo projeto estiver na janela do Estúdio Visual, tenha um momento para se familiarizar com os ficheiros que criou:

* Um projeto IoT Edge chamado **CSharpTutorialApp**.
  * A pasta **Módulos** contém indicações para os módulos incluídos no projeto. Neste caso, deve ser apenas IotEdgeModule1.
  * O ficheiro **.env** oculto contém as credenciais do seu registo de contentores. Estas credenciais são partilhadas com o seu dispositivo IoT Edge para que tenha acesso a imagens do recipiente.
  * O ficheiro **deployment.template.json** é um modelo para ajudá-lo a criar um manifesto de implantação. Um manifesto de *implantação* é um ficheiro que define exatamente quais os módulos que pretende implementar num dispositivo, como devem ser configurados, e como podem comunicar uns com os outros e a nuvem.
    > [!TIP]
    > Na secção de credenciais de registo, o endereço é preenchido automaticamente a partir da informação que forneceu quando criou a solução. No entanto, as variáveis de referência do nome de utilizador e da palavra-passe armazenadas no ficheiro .env. Isto é para a segurança, uma vez que o ficheiro .env é ignorado, mas o modelo de implementação não é.
* Um projeto de módulo IoT Edge chamado **IotEdgeModule1**.
  * O ficheiro **program.cs** contém o código de módulo C# padrão que vem com o modelo do projeto. O módulo predefinido retira a entrada de uma fonte e passa-a para o IoT Hub.
  * O ficheiro **module.json** guarda detalhes sobre o módulo, incluindo o repositório de imagem completo, versão de imagem, e que o Dockerfile usar para cada plataforma suportada.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça as suas credenciais de registo ao agente IoT Edge

O tempo de execução do IoT Edge necessita das suas credenciais de registo para puxar as imagens do seu recipiente para o dispositivo IoT Edge. A extensão IoT Edge tenta retirar as informações do registo de contentores do Azure e povoá-la no modelo de implantação.

1. Abra o ficheiro **deployment.template.json** na sua solução de módulo.

1. Encontre a propriedade **registryCredenciais** no $edgeAgent propriedades desejadas. Deve ter o seu endereço de registo preenchido automaticamente a partir das informações fornecidas ao criar o projeto, e, em seguida, os campos de nome de utilizador e palavra-passe devem conter nomes variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o ficheiro **.env** na solução do módulo. (Está escondido por defeito no Solution Explorer, por isso poderá ser necessário selecionar o botão **Mostrar Todos os Ficheiros** para o exibir.)

1. Adicione o nome de **utilizador** e os valores de **palavra-passe** que copiou do registo de contentores Azure.

1. Guarde as alterações no ficheiro .env.

### <a name="review-the-sample-code"></a>Reveja o código da amostra

O modelo de solução que criou inclui código de amostra para um módulo IoT Edge. Este módulo de amostra simplesmente recebe mensagens e depois transmite-as. A funcionalidade do pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam entre si.

Cada módulo pode ter várias filas de *entrada* e *saída* declaradas no seu código. O hub IoT Edge que funciona no dispositivo encaminha as mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar inputs e saídas varia entre línguas, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre o encaminhamento entre módulos, consulte [as rotas de declaração.](module-composition.md#declare-routes)

O código C# da amostra que vem com o modelo de projeto utiliza a [Classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do IoT Hub SDK para .NET.

1. No ficheiro **program.cs,** encontre o método **SetInputMessageHandlerAsync.**

2. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Reveja este método e veja como inicializa uma fila de entrada chamada **entrada1**.

   ![Encontre o nome de entrada no construtor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Em seguida, encontre o método **SendEventAsync.**

4. Os processos do método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) receberam mensagens e configuram uma fila de saída para as transmitir. Reveja este método e veja se inicializa uma fila de saída chamada **output1**.

   ![Encontre o nome de saída no construtor SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Abra o ficheiro **deployment.template.json.**

6. Encontre a propriedade dos **módulos** do $edgeAgent propriedades desejadas.

   Deve haver dois módulos listados aqui. O primeiro é **SimulatedTemperatureSensor**, que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que pode usar para testar os seus módulos. O segundo é o módulo **IotEdgeModule1** que criou como parte deste projeto.

   Esta propriedade de módulos declara quais os módulos que devem ser incluídos na implementação para o seu dispositivo ou dispositivos.

7. Encontre a propriedade de **rotas** dos $edgeHub propriedades desejadas.

   Uma das funções do módulo hub IoT Edge é encaminhar mensagens entre todos os módulos numa implementação. Reveja os valores na propriedade das rotas. A primeira rota, **IotEdgeModule1ToIoTHub,** utiliza**\*** um personagem wildcard ( ) para incluir qualquer mensagem vinda de qualquer fila de saída no módulo IotEdgeModule1. Estas mensagens vão para *$upstream,* que é um nome reservado que indica IoT Hub. A segunda rota, **sensorToIotEdgeModule1,** recebe mensagens provenientes do módulo SimuladoSensor de Temperatura e encaminha-as para a fila de entrada de *entrada 1* do módulo IotEdgeModule1.

   ![Rever rotas em deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Construa e empurre a sua solução

Reviu o código do módulo e o modelo de implementação para entender alguns conceitos de implementação chave. Agora, está pronto para construir a imagem do recipiente IotEdgeModule1 e empurrá-la para o registo do seu contentor. Com a extensão das ferramentas IoT para o Estúdio Visual, este passo também gera o manifesto de implementação com base na informação no ficheiro do modelo e na informação do módulo a partir dos ficheiros de solução.

### <a name="sign-in-to-docker"></a>Inscreva-se no Docker

Forneça as suas credenciais de registo de contentores ao Docker na sua máquina de desenvolvimento para que possa empurrar a sua imagem de contentor para ser armazenada no registo.

1. Open PowerShell ou um pedido de comando.

2. Inscreva-se no Docker com as credenciais de registo de contentores Azure que guardou após a criação do registo.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança `--password-stdin`recomendando a utilização de . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Construir e empurrar

A sua máquina de desenvolvimento tem agora acesso ao seu registo de contentores, e os seus dispositivos IoT Edge também. É hora de transformar o código do projeto numa imagem de contentor.

1. Clique à direita na pasta do projeto **CSharpTutorialApp** e selecione Módulos de **Borda Build e Push IoT**.

   ![Construir e empurrar módulos IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   O comando de construção e impulso inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informação no modelo de implementação e outros ficheiros de solução. Em segundo `docker build` lugar, funciona para construir a imagem do recipiente com base no arquivo apropriado para a sua arquitetura alvo. Em seguida, `docker push` corre para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos da primeira vez, mas é mais rápido da próxima vez que executar os comandos.

2. Abra o ficheiro **deployment.windows-amd64.json** na pasta config recém-criada. (A pasta config pode não aparecer no Solution Explorer no Estúdio Visual. Se for esse o caso, selecione o ícone **mostrar todos os ficheiros** na barra de tarefas do Solution Explorer.)

3. Encontre o parâmetro de **imagem** da secção IotEdgeModule1. Note que a imagem contém o repositório de imagem completo com o nome, versão e etiqueta de arquitetura do ficheiro module.json.

4. Abra o ficheiro **módulo.json** na pasta IotEdgeModule1.

5. Altere o número da versão para a imagem do módulo. (A versão, não a versão $schema.) Por exemplo, incremente o número da versão patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões do módulo permitem o controlo da versão e permitem testar alterações num pequeno conjunto de dispositivos antes de implementar atualizações para a produção. Se não incrementar a versão do módulo antes de construir e empurrar, então substitui o repositório no registo do seu contentor.

6. Guarde as alterações no ficheiro module.json.

7. Clique novamente na pasta do projeto **CSharpTutorialApp** e selecione novamente **os módulos Build e Push IoT Edge.**

8. Abra o ficheiro **deployment.windows-amd64.json** novamente. Reparem que um novo ficheiro não foi criado quando dirigiu o comando de construção e pressão novamente. Pelo contrário, o mesmo ficheiro foi atualizado para refletir as alterações. A imagem IotEdgeModule1 aponta agora para a versão 0.0.2 do recipiente. Esta alteração no manifesto de implantação é como se diz ao dispositivo IoT Edge que há uma nova versão de um módulo para puxar.

9. Para verificar mais sobre o que o comando de construção e impulso fez, vá ao [portal Azure](https://portal.azure.com) e navegue até ao seu registo de contentores.

10. No seu registo de contentores, selecione **Repositórios** e depois **iotedgemodule1**. Verifique se ambas as versões da imagem foram empurradas para o registo.

    ![Ver ambas as versões de imagem no registo de contentores](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar erros ao construir e empurrar a imagem do módulo, muitas vezes tem a ver com a configuração do Docker na sua máquina de desenvolvimento. Utilize as seguintes verificações para rever a sua configuração:

* Dirigiu o `docker login` comando usando as credenciais que copiou do registo de contentores? Estas credenciais são diferentes das que usa para assinar no Azure.
* O seu repositório de contentores está correto? Tem o seu nome de registo de contentores correto e o nome correto do módulo? Abra o ficheiro **módulo.json** na pasta IotEdgeModule1 para verificar. O valor do repositório deve parecer o ** \<nome\>do registo .azurecr.io/iotedgemodule1**.
* Se usou um nome diferente do **IotEdgeModule1** para o seu módulo, esse nome é consistente em toda a solução?
* A sua máquina está a funcionar com o mesmo tipo de contentores que está a construir? Este tutorial destina-se a dispositivos Windows IoT Edge, pelo que os ficheiros do Estúdio Visual devem ter a extensão **windows-amd64** e o Docker Desktop deve estar a executar os recipientes Windows.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Verificou que as imagens do contentor construído estão armazenadas no registo do seu contentor, por isso é hora de as implantar num dispositivo. Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. Abra o Cloud Explorer no Estúdio Visual e expanda os detalhes para o seu hub IoT.

2. Selecione o nome do dispositivo para o que pretende implementar. Na lista **de Ações,** selecione **Criar Implementação**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-windows/create-deployment.png)

3. No explorador de ficheiros, navegue para a pasta de config do seu projeto e selecione o ficheiro **deployment.windows-amd64.json.** Este ficheiro é frequentemente localizado em`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Não utilize o ficheiro deployment.template.json, que não tem os valores de imagem completos do módulo.

4. Expanda os detalhes para o seu dispositivo IoT Edge no Cloud Explorer para ver os módulos no seu dispositivo.

5. Utilize o botão **Refresh** para atualizar o estado do dispositivo para ver se os módulos SimuladosTemperatureSensor e IotEdgeModule1 estão implantados no seu dispositivo.

   ![Ver módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Ver mensagens a partir do dispositivo

O código IotEdgeModule1 recebe mensagens através da sua fila de entrada e passa-as através da sua fila de saída. O manifesto de implementação declarou rotas que passaram mensagens do Sensor simulado de temperatura para iotEdgeModule1 e, em seguida, reencaminhou mensagens de IotEdgeModule1 para IoT Hub. As ferramentas Azure IoT Edge para O Estúdio Visual permitem-lhe ver mensagens à medida que chegam ao IoT Hub a partir dos seus dispositivos individuais.

1. No explorador de nuvem do Estúdio Visual, selecione o nome do dispositivo IoT Edge para o que implementou.

2. No menu **Ações,** selecione **Iniciar monitorização do ponto final**do evento incorporado .

3. Assista à secção **de saída** no Estúdio Visual para ver as mensagens que chegam ao seu centro IoT.

   Pode levar alguns minutos para ambos os módulos começarem. O tempo de funcionamento do IoT Edge precisa de receber o seu novo manifesto de implantação, puxar as imagens do módulo a partir do tempo de funcionamento do recipiente e, em seguida, iniciar cada novo módulo.

   ![Ver dispositivo de entrada para mensagens em nuvem](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Ver alterações no dispositivo

Se quiser ver o que se passa no próprio dispositivo, utilize os comandos nesta secção para inspecionar o tempo de funcionamento do IoT Edge e os módulos em funcionamento no seu dispositivo.

Os comandos nesta secção são para o seu dispositivo IoT Edge, não para a sua máquina de desenvolvimento. Se estiver a utilizar uma máquina virtual para o seu dispositivo IoT Edge, ligue-se agora a ele. No Azure, aceda à página geral da máquina virtual e selecione **Connect** para aceder à ligação remota de ambiente de trabalho. No dispositivo, abra uma janela de comando `iotedge` ou PowerShell para executar os comandos.

* Veja todos os módulos implantados no seu dispositivo e verifique o seu estado:

   ```cmd
   iotedge list
   ```

   Deve ver quatro módulos: os dois módulos de tempo de execução IoT Edge, Sensor Simulado de Temperatura e IotEdgeModule1. Todos os quatro devem ser listados como correndo.

* Inspecione os registos para obter um módulo específico:

   ```cmd
   iotedge logs <module name>
   ```

   Os módulos IoT Edge são sensíveis a casos.

   Os registos SimuladoTemperatureSensor e IotEdgeModule1 devem mostrar as mensagens que estão a processar. O módulo EdgeAgent é responsável por iniciar os outros módulos, pelo que os seus registos terão informações sobre a implementação do manifesto de implementação. Se algum módulo não estiver listado ou não estiver em execução, os registos edgeAgent provavelmente terão os erros. O módulo EdgeHub é responsável pelas comunicações entre os módulos e o IoT Hub. Se os módulos estiverem a funcionar, mas as mensagens não chegarem ao seu hub IoT, os registos edgeHub provavelmente terão os erros.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura o Visual Studio 2019 na sua máquina de desenvolvimento e implantou o seu primeiro módulo IoT Edge a partir dele. Agora que conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que possa analisar os dados que passam por ele. Escolha a sua língua preferida:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
