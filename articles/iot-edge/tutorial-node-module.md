---
title: Tutorial desenvolver módulo node. js para Linux-Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código Node.js e implementá-lo num dispositivo Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 16212f7229dc84b9495976bd40b6d48b3f2686bd
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457648"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Tutorial: desenvolver e implantar um módulo de IoT Edge do node. js para dispositivos Linux

Use Visual Studio Code para desenvolver o código node. js e implantá-lo em um dispositivo Linux que executa o Azure IoT Edge. 

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou nos inícios rápidos. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge com Node.js
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo 
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo no **node. js** usando **Visual Studio Code**e como implantá-lo em um **dispositivo Linux**. IoT Edge não dá suporte a módulos node. js para dispositivos Windows.

Use a tabela a seguir para entender suas opções de desenvolvimento e implantação de módulos do node. js: 

| Node.js | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **AMD64 do Linux** | ![Usar VS Code para módulos node. js no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 Linux** | ![Usar VS Code para módulos node. js no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para desenvolvimento de contêiner do Linux: [desenvolver módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir qualquer um desses tutoriais, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando o Azure IOT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo IoT Edge no node. js, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [Node.js e npm](https://nodejs.org). O pacote npm é distribuído com o Node.js, o que significa que quando transfere o Node.js, obtém automaticamente o npm instalado no seu computador.

## <a name="create-a-module-project"></a>Criar um projeto de módulo
As etapas a seguir mostram como criar um IoT Edge módulo node. js usando Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Utilize o **npm** para criar um modelo de solução de Node.js que possa servir de base. 

1. No Visual Studio Code, selecione **Vista** > **Terminal Integrado** para abrir o terminal integrado do VS Code.

2. No terminal integrado, introduza o seguinte comando para instalar o **yeoman** e o gerador do módulo Node.js do Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

3. Na paleta de comandos, escreva e execute o comando **Azure: Sign in** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

4. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Insira um nome descritivo para sua solução ou aceite o **EdgeSolution**padrão. |
   | Selecionar modelo de módulo | Escolha o **módulo node. js**. |
   | Indicar um nome para o módulo | Atribua o nome **NodeModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente com base no nome que você forneceu na última etapa. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagem final é semelhante a \<nome do registro\>. azurecr.io/nodemodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu repositório de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro **.env**. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos node. js para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IOT Edge: definir a plataforma de destino padrão para a solução de borda**ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Cada modelo vem com o código de exemplo incluído, que usa dados simulados de sensor do módulo **SimulatedTemperatureSensor** e o encaminha para o Hub IOT. Nesta secção, adicione código para o NodeModule analisar as mensagens antes de as enviar. 

1. No explorador do VS Code, abra **modules** > **NodeModule** > **app.js**.

2. Adicione uma variável de limiar de temperatura abaixo dos módulos de nó necessários. O limiar de temperatura define o valor que a temperatura medida tem de exceder para os dados serem enviados para o Hub IoT.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Substitua a função completa `PipeMessage` pela função `FilterMessage`.
    
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

4. Substitua o nome da função `pipeMessage` por `filterMessage` na função `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Copie o fragmento de código seguinte para a chamada de retorno da função `client.open()`, depois de `client.on()` dentro da instrução `else`. Esta função é invocada quando as propriedades pretendidas forem atualizadas.

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

6. Salve o arquivo app. js.

7. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge.

8. Adicione o módulo duplo NodeModule ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-node-module/module-twin.png)

9. Salve o arquivo Deployment. Template. JSON.


## <a name="build-and-push-your-module"></a>Crie e envie por push seu módulo

Na seção anterior, você criou uma solução de IoT Edge e adicionou o código ao NodeModule que filtrará as mensagens em que a temperatura da máquina relatada está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**.

1. Entre no Docker digitando o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu registro de contêiner do Azure. Você pode recuperar esses valores da seção **chaves de acesso** do registro no portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon do Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, ele executa `docker build` para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar por push o repositório de imagens para o registro de contêiner.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o Visual Studio Code Explorer e a extensão de ferramentas de IoT do Azure para implantar o projeto de módulo em seu dispositivo de IoT Edge. Você já tem um manifesto de implantação preparado para seu cenário, o arquivo **Deployment. JSON** na pasta de configuração. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Verifique se o dispositivo IoT Edge está em execução.

1. No Visual Studio Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** para ver sua lista de dispositivos IOT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. Você deve ver o novo **NodeModule** em execução junto com o módulo **SimulatedTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Visual Studio Code Explorer, clique com o botão direito do mouse no nome do dispositivo de IoT Edge e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.

2. Exiba as mensagens que chegam ao seu hub IoT. Pode levar algum tempo para que as mensagens cheguem, pois o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações feitas no código NodeModule esperam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o **alerta** de tipo de mensagem a todas as mensagens que atingem esse limite de temperatura. 

## <a name="edit-the-module-twin"></a>Edite o módulo...

Usamos o módulo NodeModule no manifesto de implantação para definir o limite de temperatura em 25 graus. Você pode usar o módulo "atualizar" para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. Em Visual Studio Code, expanda os detalhes em seu dispositivo de IoT Edge para ver os módulos em execução. 

2. Clique com o botão direito do mouse em **NodeModule** e selecione **Editar módulo**. 

3. Localize **TemperatureThreshold** nas propriedades desejadas. Altere seu valor para uma nova temperatura de 5 graus para 10 graus acima da temperatura mais recente relatada. 

4. Salve o arquivo de módulo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo e selecione **Atualizar módulo**. 

6. Monitore as mensagens de entrada do dispositivo para a nuvem. Você deve ver as mensagens param até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como [desenvolver seus próprios módulos IOT Edge](module-development.md) ou como [desenvolver módulos com Visual Studio Code](how-to-vs-code-develop-module.md). Você pode continuar nos próximos tutoriais para saber como Azure IoT Edge pode ajudá-lo a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [serviço de visão personalizada](tutorial-deploy-custom-vision.md)
