---
title: Tutorial criar módulo personalizado do node. js - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código Node.js e implementá-lo num dispositivo Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 10026f0a9ff702ee45926ca097e9123ea3db06d5
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225931"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Tutorial: Desenvolver e implementar um módulo do IoT Edge do node. js para o seu dispositivo simulado

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou nos inícios rápidos. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge com Node.js
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo 
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge.
* Se estiver a executar o IoT Edge no Windows, o IoT Edge versão 1.0.5 não suporta módulos node. js. Para obter mais informações, consulte [notas de versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para obter passos sobre como instalar uma versão específica, consulte [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js e npm](https://nodejs.org). O pacote npm é distribuído com o Node.js, o que significa que quando transfere o Node.js, obtém automaticamente o npm instalado no seu computador.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Neste tutorial, vai utilizar as ferramentas de IoT do Azure para Visual Studio Code para criar um módulo e criar um **imagem de contentor** dos arquivos. Em seguida, vai enviar essa imagem para um **registo** que armazena e gere as suas imagens. Por fim, vai implementar a imagem a partir do registo para ser executada no seu dispositivo IoT Edge.  

Pode utilizar qualquer registo compatível com o Docker para armazenar as imagens de contentor. São dois populares serviços de registo de Docker [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) e [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Azure Container Registry. 

Se ainda não tiver um registo de contentor, siga estes passos para criar uma nova no Azure:

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contentores** > **Registo de Contentor**.

2. Indique os valores seguintes para criar o seu registo de contentor:

   | Campo | Valor |
   | ----- | ----- |
   | Nome de registo | Indique um nome exclusivo. |
   | Subscrição | Selecione uma subscrição na lista pendente. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Localização | Escolha uma localização perto de si. |
   | Utilizador admin | Defina para **Ativar**. |
   | SKU | Selecione **Básico**. |

5. Selecione **Criar**.

6. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**. 

7. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Utilize estes valores mais tarde no tutorial para fornecer acesso ao registo de contentor. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge
Os passos seguintes mostram como criar um módulo de node. js do IoT Edge com o Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Utilize o **npm** para criar um modelo de solução de Node.js que possa servir de base. 

1. No Visual Studio Code, selecione **Vista** > **Terminal Integrado** para abrir o terminal integrado do VS Code.

2. No terminal integrado, introduza o seguinte comando para instalar o **yeoman** e o gerador do módulo Node.js do Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

3. Na paleta de comandos, escreva e execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

4. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **módulo de node. js**. |
   | Indicar um nome para o módulo | Atribua o nome **NodeModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/nodemodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-node-module/repository.png)

A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código Node.js para o seu módulo, bem como Dockerfiles para criar o seu módulo como uma imagem de contentor. O ficheiro **\.env** armazena as credenciais do registo de contentor. O **deployment.template.json** ficheiro contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo e **deployment.debug.template.json** ficheiro contém a versão de depuração módulos. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial. 

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu repositório de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro **.env**. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Cada modelo inclui o código de exemplo, que captura os dados simulados do sensor do módulo **tempSensor** e os encaminha para o Hub IoT. Nesta secção, adicione código para o NodeModule analisar as mensagens antes de as enviar. 

1. No explorador do VS Code, abra **modules** > **NodeModule** > **app.js**.

5. Adicione uma variável de limiar de temperatura abaixo dos módulos de nó necessários. O limiar de temperatura define o valor que a temperatura medida tem de exceder para os dados serem enviados para o Hub IoT.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Substitua a função completa `PipeMessage` pela função `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Substitua o nome da função `pipeMessage` por `filterMessage` na função `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Copie o fragmento de código seguinte para a chamada de retorno da função `client.open()`, depois de `client.on()` dentro da instrução `else`. Esta função é invocada quando as propriedades pretendidas forem atualizadas.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Guarde o ficheiro App. js.

10. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. Este ficheiro diz ao agente do IoT Edge para quais módulos para implementar, neste caso **tempSensor** e **NodeModule**e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code povoa automaticamente a maior parte das informações que precisa no modelo de implementação, mas certifique-se de que tudo está correta para a sua solução: 

   1. A plataforma padrão do seu IoT Edge está definida como **amd64** no seu estado do VS Code barra, que significa que seu **NodeModule** está definido como Linux amd64 versão da imagem. Alterar a plataforma de predefinição na barra de status da **amd64** ao **arm32v7** ou **windows-amd64** se de que é arquitetura de seu dispositivo IoT Edge. 

      ![Plataforma de imagem do módulo de atualização](./media/tutorial-node-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto e não o nome predefinido **SampleModule** que alterou quando criou a solução IoT Edge.

   3. O **registryCredentials** secção armazena as suas credenciais do registo de Docker, para que o agente do IoT Edge pode extrair a sua imagem do módulo. Os pares de nome de utilizador e palavra-passe reais são armazenados no ficheiro .env, ignorado pelo git. Se ainda não o fez, adicione as suas credenciais para o ficheiro. env.  

   4. Se quiser obter mais informações sobre manifestos de implantação, consulte [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

11. Adicione o módulo duplo NodeModule ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-node-module/module-twin.png)

12. Guarde o ficheiro de deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução do IoT Edge e adicionou código ao NodeModule que filtrará mensagens onde a temperatura comunicada da máquina é inferior ao limiar aceitável. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor. 

1. Inicie sessão no Docker ao introduzir o comando seguinte no terminal integrado do Visual Studio Code, para que possa enviar a imagem do módulo para o ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilize o nome do utilizador, palavra-passe e servidor de início de sessão que copiou do Azure Container Registry na primeira secção. Também pode obtê-los novamente a partir da secção **Chaves de acesso** do registo no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**. 

Quando indicar ao Visual Studio Code para criar a solução, este utiliza primeiro as informações no modelo de implementação e gera um ficheiro `deployment.json` numa nova pasta **config**. Em seguida, executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos Compile seu código, colocar num contentor do seu código node. js e, em seguida, enviar por push para o registo de contentor que especificou quando a solução é inicializado. 

Pode ver o endereço da imagem de contentor completo com a etiqueta no comando `docker build` que é executado no terminal integrado do VS Code. O endereço da imagem baseia-se nas informações no ficheiro `module.json`, com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial, deve ser semelhante a **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

>[!TIP]
>Se receber um erro ao tentar criar e enviar por push o seu módulo, tome as seguintes verificações:
>* Iniciou sessão ao Docker no Visual Studio Code, utilizando as credenciais do seu registo de contentor? Estas credenciais são diferentes dos que utilizar para iniciar sessão no portal do Azure.
>* O repositório do contentor está correto? Open **módulos** > **cmodule** > **Module** e encontre a **repositório** campo. O repositório de imagens deve ser semelhante à  **\<registryname\>.azurecr.io/nodemodule**. 
>* Está a criar o mesmo tipo de contentores que está a executar o seu computador de desenvolvimento? Visual Studio Code é predefinida como contentores do Linux amd64. Se o computador de desenvolvimento está a executar contentores do Windows ou Linux arm32v7 contentores, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code, de acordo com sua plataforma de contentores.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com a extensão do Kit de ferramentas do Azure IoT Hub (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT) para Visual Studio Code. Já tem um manifesto de implementação preparado para o seu cenário, o ficheiro **deployment.json**. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Na paleta de comandos VS Code, execute **IoT Hub do Azure: Selecione o IoT Hub**. 

2. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar. 

3. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

4. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**. 

   ![Criar implementação para dispositivo único](./media/tutorial-node-module/create-deployment.png)

5. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json. 

6. Clique no botão Atualizar. Deverá ver o novo **NodeModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**. 


## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados. 

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução. 

No próprio dispositivo IoT Edge, pode ver o estado dos seus módulos de implementação com o comando `iotedge list`. Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, o tempSensor e o módulo personalizado que criou neste tutorial. Pode demorar alguns minutos para que todos os módulos comecem, por isso, execute novamente o comando se não os vir todos inicialmente. 

Para ver as mensagens que são geradas por qualquer módulo, utilize o comando `iotedge logs <module name>`. 

Pode ver as mensagens conforme chegam ao seu hub IoT através do Visual Studio Code. 

1. Para monitorizar os dados que chegam ao hub IoT, clique em **...** e selecione **Iniciar Monitorização de Mensagens D2C**.
2. Para monitorizar a mensagem D2C para um dispositivo específico, clique com o botão direito do rato na lista e selecione **Iniciar Monitorização de Mensagens D2C**.
3. Para parar a monitorização de dados, execute o comando **IoT Hub do Azure: Parar a monitorização de mensagens D2C** na paleta de comandos. 
4. Para ver ou atualizar o módulo duplo, clique com o botão direito do rato no módulo na lista e selecione **Editar módulo duplo**. Para atualizar o módulo duplo, guarde o ficheiro JSON duplo e clique com o botão direito do rato na área de editor e selecione **Atualizar Módulo Duplo**.
5. Para ver os registos do Docker, pode instalar o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para o VS Code e localizar os módulos em execução localmente no explorador do Docker. No menu de contexto, clique em **Mostrar Registos** para ver no terminal integrado. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Pode continuar para qualquer um dos seguintes tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Implementar a Função do Azure como um módulo](tutorial-deploy-function.md)
> [Implementar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)

