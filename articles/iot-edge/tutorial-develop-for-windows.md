---
title: Tutorial-desenvolver módulo para dispositivos Windows usando Azure IoT Edge
description: Este tutorial explica como configurar seu computador de desenvolvimento e recursos de nuvem para desenvolver IoT Edge módulos usando contêineres do Windows para dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772257"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Tutorial: desenvolver módulos IoT Edge para dispositivos Windows

Use o Visual Studio para desenvolver e implantar código em dispositivos Windows que executam o IoT Edge.

No início rápido, você criou um dispositivo IoT Edge usando uma máquina virtual do Windows e implantou um módulo pré-compilado do Azure Marketplace. Este tutorial percorre o que é necessário para desenvolver e implantar seu próprio código em um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para os outros tutoriais, que entram em mais detalhes sobre linguagens de programação específicas ou serviços do Azure.

Este tutorial usa o exemplo de implantação de um  **C# módulo em um dispositivo Windows**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum. Se você estiver interessado em desenvolver em um idioma diferente ou planejar a implantação de serviços do Azure como módulos, este tutorial ainda será útil para aprender sobre as ferramentas de desenvolvimento. Depois de entender os conceitos de desenvolvimento, você pode escolher seu idioma preferencial ou o serviço do Azure para se aprofundar nos detalhes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Configure seu computador de desenvolvimento.
> * Use o IoT Edge Tools para Visual Studio para criar um novo projeto.
> * Compile seu projeto como um contêiner e armazene-o em um registro de contêiner do Azure.
> * Implante seu código em um dispositivo IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Conceitos-chave

Este tutorial percorre o desenvolvimento de um módulo IoT Edge. Um *módulo IOT Edge*ou, às vezes, apenas *módulo* para curto, é um contêiner que contém código executável. Você pode implantar um ou mais módulos em um dispositivo IoT Edge. Os módulos executam tarefas específicas como ingerir dados de sensores, executar operações de análise de dados ou limpeza de dados ou enviar mensagens para um hub IoT. Para obter mais informações, consulte [entender Azure IOT Edge módulos](iot-edge-modules.md).

Ao desenvolver módulos IoT Edge, é importante entender a diferença entre a máquina de desenvolvimento e o dispositivo de IoT Edge de destino em que o módulo será eventualmente implantado. O contêiner que você cria para armazenar o código do módulo deve corresponder ao sistema operacional (SO) do *dispositivo de destino*. Para o desenvolvimento de contêiner do Windows, esse conceito é mais simples porque os contêineres do Windows só são executados em sistemas operacionais Windows. Mas você pode, por exemplo, usar o computador de desenvolvimento do Windows para criar módulos para dispositivos Linux IoT Edge. Nesse cenário, você precisaria certificar-se de que o computador de desenvolvimento estava executando contêineres do Linux. Ao percorrer este tutorial, tenha em mente a diferença entre o *sistema operacional do computador de desenvolvimento* e o *sistema operacional do contêiner*.

Este tutorial destina-se a dispositivos Windows que executam o IoT Edge. Os dispositivos Windows IoT Edge usam contêineres do Windows. É recomendável usar o Visual Studio para desenvolver para dispositivos Windows, portanto, esse tutorial será usado. Você também pode usar Visual Studio Code, embora haja diferenças no suporte entre as duas ferramentas.

A tabela a seguir lista os cenários de desenvolvimento com suporte para **contêineres do Windows** no Visual Studio Code e no Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Serviços do Azure** | Funções do Azure <br> Azure Stream Analytics |   |
| **Idiomas** | C#(não há suporte para depuração) | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas de Azure IoT Edge para o Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Ferramentas de Azure IoT Edge para o Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento:

* Windows 10 com atualização 1809 ou mais recente.
* Você pode usar seu próprio computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
  * Verifique se o computador de desenvolvimento dá suporte à virtualização aninhada. Esse recurso é necessário para executar um mecanismo de contêiner, que você instala na próxima seção.
* Instale o [Git](https://git-scm.com/).

Um dispositivo Azure IoT Edge no Windows:

* Recomendamos que você não execute IoT Edge em seu computador de desenvolvimento, mas use um dispositivo separado. Essa distinção entre o computador de desenvolvimento e IoT Edge dispositivo espelha mais precisamente um cenário de implantação real e ajuda a manter os diferentes conceitos diretamente.
* Se você não tiver um segundo dispositivo disponível, use o artigo de início rápido para criar um dispositivo IoT Edge no Azure com uma [máquina virtual do Windows](quickstart.md).

Recursos da cloud:

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada Standard no Azure.

## <a name="install-container-engine"></a>Instalar mecanismo de contêiner

IoT Edge módulos são empacotados como contêineres, portanto, você precisa de um mecanismo de contêiner em seu computador de desenvolvimento para criar e gerenciar os contêineres. É recomendável usar o Docker desktop para desenvolvimento devido a seus muitos recursos e popularidade como um mecanismo de contêiner. Com o Docker desktop em um computador Windows, você pode alternar entre contêineres do Linux e contêineres do Windows para que seja possível desenvolver facilmente módulos para diferentes tipos de dispositivos IoT Edge.

Use a documentação do Docker para instalar o em seu computador de desenvolvimento:

* [Instale o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Ao instalar o Docker desktop para Windows, você será perguntado se deseja usar contêineres do Linux ou do Windows. Para este tutorial, use **contêineres do Windows**. Para obter mais informações, consulte [alternar entre contêineres do Windows e do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Configurar o Visual Studio e as ferramentas

As extensões de IoT para o Visual Studio o ajudam a desenvolver módulos de IoT Edge. Essas extensões fornecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem que você monitore e gerencie dispositivos IoT Edge. Nesta seção, você instala o Visual Studio e a extensão IoT Edge e, em seguida, configura sua conta do Azure para gerenciar recursos do Hub IoT de dentro do Visual Studio.

Este tutorial ensina as etapas de desenvolvimento para o Visual Studio 2019. Se estiver a utilizar o Visual Studio 2017 (versão 15.7 ou superior), os passos são semelhantes. Se preferir usar Visual Studio Code, consulte as instruções em [usar Visual Studio Code para desenvolver e depurar módulos para Azure IOT Edge](how-to-vs-code-develop-module.md).

1. Prepare o Visual Studio 2019 em seu computador de desenvolvimento.

   * Se você ainda não tiver o Visual Studio em seu computador de desenvolvimento, [Instale o visual studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) com as seguintes cargas de trabalho:

      * Desenvolvimento do Azure
      * Desenvolvimento de ambiente de trabalho comC++
      * Desenvolvimento de várias plataformas .NET core

   * Se você já tiver o Visual Studio 2019 em seu computador de desenvolvimento, siga as etapas em [Modificar o Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para adicionar as cargas de trabalho necessárias.

2. Baixe e instale a extensão de [ferramentas de Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) para o Visual Studio 2019.

   Se você estiver usando o Visual Studio 2017 (versão 15,7 ou superior), baixe e instale o [Azure IOT Edge Tools para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Quando suas instalações forem concluídas, abra o Visual Studio 2019 e selecione **continuar sem código**.

4. Selecione **exibir** > **Cloud Explorer**.

5. Selecione o ícone de perfil no Cloud Explorer e entre em sua conta do Azure se você ainda não tiver entrado.

6. Depois de entrar, suas assinaturas do Azure são listadas. Expanda a assinatura que tem o Hub IoT.

7. Em sua assinatura, expanda os **hubs IOT** e o Hub IOT. Você deve ver uma lista de seus dispositivos IoT e pode usar este Gerenciador para gerenciá-los.

   ![Acessar recursos do Hub IoT no Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um novo projeto de módulo

A extensão de ferramentas de Azure IoT Edge fornece modelos de projeto para todas as linguagens de módulo IoT Edge com suporte no Visual Studio. Esses modelos têm todos os arquivos e códigos de que você precisa para implantar um módulo de trabalho para testar IoT Edge ou fornecer um ponto de partida para personalizar o modelo com sua própria lógica de negócios.

1. Selecione **arquivo** > **novo** projeto de >  **...**

2. Na janela novo projeto, procure **IOT Edge** e escolha o projeto **Azure IOT Edge (Windows AMD64)** . Clique em **Seguinte**.

   ![Criar um novo projeto de Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Na janela configurar seu novo projeto, renomeie o projeto e a solução como algo descritivo, como **CSharpTutorialApp**. Clique em **criar** para criar o projeto.

   ![Configurar um novo projeto de Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. Na janela Adicionar módulo, configure o projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Modelo do Visual Studio | Selecione  **C# módulo**. |
   | Nome do módulo | Aceite o **IotEdgeModule1**padrão. |
   | URL do repositório | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente a partir do valor do nome do projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Você pode recuperar o valor do **servidor de logon** na página **visão geral** do registro de contêiner no portal do Azure. <br><br> O repositório de imagem final é semelhante a \<nome do registro\>. azurecr.io/iotedgemodule1. |

      ![Configurar seu projeto para o dispositivo de destino, o tipo de módulo e o registro de contêiner](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecione **Adicionar** para criar o módulo.

Depois que o novo projeto for carregado na janela do Visual Studio, Reserve um tempo para se familiarizar com os arquivos que criou:

* Um projeto IoT Edge chamado **CSharpTutorialApp**.
  * A pasta **modules** contém ponteiros para os módulos incluídos no projeto. Nesse caso, deve ser apenas IotEdgeModule1.
  * O arquivo Hidden **. env** contém as credenciais para o registro de contêiner. Essas credenciais são compartilhadas com seu dispositivo IoT Edge para que ele tenha acesso para efetuar pull das imagens de contêiner.
  * O arquivo **Deployment. Template. JSON** é um modelo para ajudá-lo a criar um manifesto de implantação. Um *manifesto de implantação* é um arquivo que define exatamente quais módulos você deseja que sejam implantados em um dispositivo, como eles devem ser configurados e como eles podem se comunicar entre si e com a nuvem.
    > [!TIP]
    > Na seção credenciais do registro, o endereço é preenchido de forma automática a partir das informações fornecidas quando você criou a solução. No entanto, as variáveis de referência de nome de usuário e senha são armazenadas no arquivo. env. Isso é para segurança, pois o arquivo. env é git ignorado, mas o modelo de implantação não é.
* Um projeto de módulo IoT Edge chamado **IotEdgeModule1**.
  * O arquivo **Program.cs** contém o código C# de módulo padrão que vem com o modelo de projeto. O módulo padrão usa a entrada de uma origem e a transmite ao Hub IoT.
  * O arquivo **Module. JSON** contém detalhes sobre o módulo, incluindo o repositório de imagem completo, a versão da imagem e quais Dockerfile usar para cada plataforma com suporte.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça suas credenciais de registro para o agente de IoT Edge

O tempo de execução de IoT Edge precisa de suas credenciais de registro para efetuar pull de suas imagens de contêiner para o dispositivo de IoT Edge. A extensão IoT Edge tenta efetuar pull das informações do registro de contêiner do Azure e preenchê-las no modelo de implantação.

1. Abra o arquivo **Deployment. Template. JSON** em sua solução de módulo.

1. Localize a propriedade **registryCredentials** no $edgeAgent propriedades desejadas. Ele deve ter seu endereço de registro preenchido de forma automática a partir das informações fornecidas durante a criação do projeto, e os campos de nome de usuário e senha devem conter nomes de variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o arquivo **. env** na sua solução de módulo. (Ele fica oculto por padrão na Gerenciador de Soluções, portanto, talvez seja necessário selecionar o botão **Mostrar todos os arquivos** para exibi-lo.)

1. Adicione os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.

1. Salve as alterações no arquivo. env.

### <a name="review-the-sample-code"></a>Reveja o código de exemplo

O modelo de solução que você criou inclui um código de exemplo para um módulo IoT Edge. Esse módulo de exemplo simplesmente recebe mensagens e as passa. A funcionalidade de pipeline demonstra um conceito importante em IoT Edge, que é como os módulos se comunicam entre si.

Cada módulo pode ter várias filas de *entrada* e *saída* declaradas em seu código. O Hub de IoT Edge em execução no dispositivo roteia as mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar entradas e saídas varia entre linguagens, mas o conceito é o mesmo em todos os módulos. Para obter mais informações sobre roteamento entre módulos, consulte [declarar rotas](module-composition.md#declare-routes).

O código C# de exemplo que vem com o modelo de projeto usa a [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IOT para .net.

1. No arquivo **Program.cs** , localize o método **SetInputMessageHandlerAsync** .

2. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Examine esse método e veja como ele Inicializa uma fila de entrada chamada **entrada1**.

   ![Localizar o nome de entrada no Construtor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Em seguida, localize o método **SendEventAsync** .

4. O método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) processa mensagens recebidas e configura uma fila de saída para passá-las. Examine esse método e veja que ele Inicializa uma fila de saída chamada **Saída1**.

   ![Localize o nome de saída no Construtor SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Abra o arquivo **Deployment. Template. JSON** .

6. Localize a propriedade **modules** do $edgeAgent propriedades desejadas.

   Deve haver dois módulos listados aqui. O primeiro é **SimulatedTemperatureSensor**, que é incluído em todos os modelos por padrão para fornecer dados de temperatura simulados que você pode usar para testar seus módulos. O segundo é o módulo **IotEdgeModule1** que você criou como parte deste projeto.

   Esta propriedade modules declara quais módulos devem ser incluídos na implantação para seu dispositivo ou dispositivos.

7. Localize a propriedade **Routes** do $edgeHub propriedades desejadas.

   Uma das funções do módulo Hub de IoT Edge é rotear mensagens entre todos os módulos em uma implantação. Examine os valores na Propriedade Routes. A primeira rota, **IotEdgeModule1ToIoTHub**, usa um caractere curinga ( **\*** ) para incluir qualquer mensagem proveniente de qualquer fila de saída no módulo IotEdgeModule1. Essas mensagens entram em *$upstream*, que é um nome reservado que indica o Hub IOT. A segunda rota, **sensorToIotEdgeModule1**, pega as mensagens provenientes do módulo SimulatedTemperatureSensor e as roteia para a fila de entrada *Entrada1* do módulo IotEdgeModule1.

   ![Examinar rotas em Deployment. Template. JSON](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Criar e emitir sua solução

Você examinou o código do módulo e o modelo de implantação para entender alguns dos principais conceitos de implantação. Agora, você está pronto para criar a imagem de contêiner IotEdgeModule1 e enviá-la por push para o registro de contêiner. Com a extensão de ferramentas de IoT para Visual Studio, essa etapa também gera o manifesto de implantação com base nas informações no arquivo de modelo e nas informações do módulo dos arquivos da solução.

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça suas credenciais de registro de contêiner para o Docker no computador de desenvolvimento para que ele possa enviar por push sua imagem de contêiner para ser armazenada no registro.

1. Abra o PowerShell ou um prompt de comando.

2. Entre no Docker com as credenciais do registro de contêiner do Azure que você salvou depois de criar o registro.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Criar e enviar por push

Sua máquina de desenvolvimento agora tem acesso ao registro de contêiner e seus dispositivos IoT Edge também. É hora de transformar o código do projeto em uma imagem de contêiner.

1. Clique com o botão direito do mouse na pasta do projeto **CSharpTutorialApp** e selecione **compilar e enviar IOT Edge módulos**.

   ![Compilar e enviar por push módulos de IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, ele executa `docker build` para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar por push o repositório de imagens para o registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas é mais rápido na próxima vez que você executar os comandos.

2. Abra o arquivo **Deployment. Windows-AMD64. JSON** na pasta de configuração recém-criada. (A pasta de configuração pode não aparecer no Gerenciador de Soluções no Visual Studio. Se esse for o caso, selecione o ícone **Mostrar todos os arquivos** na barra de tarefas do Gerenciador de soluções.)

3. Localize o parâmetro **Image** da seção IotEdgeModule1. Observe que a imagem contém o repositório de imagem completo com o nome, a versão e a marca de arquitetura do arquivo module. JSON.

4. Abra o arquivo **Module. JSON** na pasta IotEdgeModule1

5. Altere o número de versão da imagem do módulo. (A versão, não a $schema-Version.) Por exemplo, aumente o número de versão do patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo.

   >[!TIP]
   >As versões de módulo habilitam o controle de versão e permitem que você teste alterações em um pequeno conjunto de dispositivos antes de implantar atualizações na produção. Se você não incrementar a versão do módulo antes de compilar e enviar por push, você substituirá o repositório no registro de contêiner.

6. Salve as alterações no arquivo module. JSON.

7. Clique com o botão direito do mouse na pasta do projeto **CSharpTutorialApp** novamente e selecione **compilar e enviar IOT Edge módulos** novamente.

8. Abra o arquivo **Deployment. Windows-AMD64. JSON** novamente. Observe que um novo arquivo não foi criado quando você executou o comando criar e enviar por push novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. A imagem IotEdgeModule1 agora aponta para a versão 0.0.2 do contêiner. Essa alteração no manifesto de implantação é como você informa ao dispositivo IoT Edge que há uma nova versão de um módulo para efetuar pull.

9. Para verificar melhor o que o comando Build e Push fez, acesse o [portal do Azure](https://portal.azure.com) e navegue até o registro de contêiner.

10. No registro de contêiner, selecione **repositórios** e, em seguida, **iotedgemodule1**. Verifique se ambas as versões da imagem foram enviadas por push para o registro.

    ![Exibir as duas versões de imagem no registro de contêiner](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Resolução de problemas

Se você encontrar erros ao compilar e enviar por push a imagem do módulo, isso geralmente terá a ver com a configuração do Docker em seu computador de desenvolvimento. Use as seguintes verificações para examinar sua configuração:

* Você executou o comando `docker login` usando as credenciais que você copiou do registro de contêiner? Essas credenciais são diferentes das que você usa para entrar no Azure.
* Seu repositório de contêineres está correto? Ele tem o nome do registro de contêiner correto e o nome correto do módulo? Abra o arquivo **Module. JSON** na pasta IotEdgeModule1 para verificar. O valor do repositório deve ser semelhante a **\<nome do registro\>. azurecr.Io/iotedgemodule1**.
* Se você usou um nome diferente do **IotEdgeModule1** para seu módulo, esse nome é consistente em toda a solução?
* Seu computador está executando o mesmo tipo de contêiner que você está criando? Este tutorial é para dispositivos Windows IoT Edge, de modo que os arquivos do Visual Studio devem ter a extensão **Windows-AMD64** e o Docker desktop deve estar executando contêineres do Windows.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Você verificou que as imagens de contêiner criadas são armazenadas no registro de contêiner, portanto, é hora de implantá-las em um dispositivo. Verifique se o dispositivo IoT Edge está em execução.

1. Abra o Cloud Explorer no Visual Studio e expanda os detalhes para o Hub IoT.

2. Selecione o nome do dispositivo no qual você deseja implantar. Na lista **ações** , selecione **criar implantação**.

   ![Criar implementação para dispositivo único](./media/tutorial-develop-for-windows/create-deployment.png)

3. No explorador de arquivos, navegue até a pasta config do seu projeto e selecione o arquivo **Deployment. Windows-AMD64. JSON** . Esse arquivo geralmente está localizado em `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Não use o arquivo Deployment. Template. JSON, que não tem os valores de imagem de módulo completos.

4. Expanda os detalhes de seu dispositivo IoT Edge no Cloud Explorer para ver os módulos em seu dispositivo.

5. Use o botão **Atualizar** para atualizar o status do dispositivo para ver que os módulos SimulatedTemperatureSensor e IotEdgeModule1 são implantados no dispositivo.

   ![Exibir módulos em execução no seu dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Exibir mensagens do dispositivo

O código IotEdgeModule1 recebe mensagens por meio de sua fila de entrada e as passa por meio de sua fila de saída. O manifesto de implantação declarava rotas que passaram mensagens de SimulatedTemperatureSensor para IotEdgeModule1 e, em seguida, mensagens encaminhadas do IotEdgeModule1 para o Hub IoT. As ferramentas de Azure IoT Edge para o Visual Studio permitem que você veja mensagens à medida que elas chegam no Hub IoT de seus dispositivos individuais.

1. No Visual Studio Cloud Explorer, selecione o nome do dispositivo de IoT Edge que você implantou.

2. No menu **ações** , selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.

3. Assista à seção de **saída** no Visual Studio para ver as mensagens que chegam ao seu hub IOT.

   Pode levar alguns minutos para que ambos os módulos sejam iniciados. O tempo de execução do IoT Edge precisa receber seu novo manifesto de implantação, extrair as imagens do módulo do tempo de execução do contêiner e, em seguida, iniciar cada novo módulo.

   ![Exibir o dispositivo de entrada para mensagens na nuvem](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Exibir alterações no dispositivo

Se você quiser ver o que está acontecendo no próprio dispositivo, use os comandos nesta seção para inspecionar o tempo de execução IoT Edge e os módulos em execução no seu dispositivo.

Os comandos nesta seção são para seu dispositivo IoT Edge, não para seu computador de desenvolvimento. Se você estiver usando uma máquina virtual para seu dispositivo IoT Edge, conecte-se a ela agora. No Azure, acesse a página Visão geral da máquina virtual e selecione **conectar** para acessar a conexão de área de trabalho remota. No dispositivo, abra um comando ou janela do PowerShell para executar os comandos de `iotedge`.

* Exiba todos os módulos implantados em seu dispositivo e verifique seu status:

   ```cmd
   iotedge list
   ```

   Você deve ver quatro módulos: os dois módulos de tempo de execução IoT Edge, SimulatedTemperatureSensor e IotEdgeModule1. Todos os quatro devem estar listados como em execução.

* Inspecione os logs de um módulo específico:

   ```cmd
   iotedge logs <module name>
   ```

   Módulos IoT Edge diferenciam maiúsculas de minúsculas.

   Os logs de SimulatedTemperatureSensor e IotEdgeModule1 devem mostrar as mensagens que estão processando. O módulo edgeAgent é responsável por iniciar os outros módulos, de modo que seus logs terão informações sobre a implementação do manifesto de implantação. Se qualquer módulo não estiver listado ou não estiver em execução, os logs do edgeAgent provavelmente terão os erros. O módulo edgeHub é responsável por comunicações entre os módulos e o Hub IoT. Se os módulos estiverem em execução, mas as mensagens não chegarem ao seu hub IoT, os logs do edgeHub provavelmente terão os erros.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura o Visual Studio 2019 em seu computador de desenvolvimento e implantou seu primeiro módulo IoT Edge a partir dele. Agora que você conhece os conceitos básicos, tente adicionar funcionalidade a um módulo para que ele possa analisar os dados que passam por ele. Escolha seu idioma preferencial:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
