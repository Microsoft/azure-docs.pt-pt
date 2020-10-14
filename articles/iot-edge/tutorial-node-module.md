---
title: Tutorial desenvolver módulo Node.js para Linux - Azure IoT Edge / Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código Node.js e implementá-lo num dispositivo Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-python, devx-track-js
ms.openlocfilehash: a4496ba46b486f183136381a51c6a523dd9e1b87
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047986"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver e implementar um módulo IoT Edge Node.js para dispositivos Linux

Utilize o Código do Estúdio Visual para desenvolver Node.js código e implemente-o num dispositivo Linux que executa o Azure IoT Edge.

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou nos inícios rápidos. Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge com Node.js
> * Utilizar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo
> * Implementar o módulo no seu dispositivo IoT Edge
> * Ver os dados gerados

O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados comunicados e armazenados na cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo em **Node.js** usando **o Código do Estúdio Visual,** e como implantá-lo num **dispositivo Linux**. O IoT Edge não suporta Node.js módulos para dispositivos Windows.

Utilize a seguinte tabela para compreender as suas opções de desenvolvimento e implantação de módulos Node.js:

| Node.js | Visual Studio Code | Estúdio Visual 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Utilize o Código VS para Node.js módulos no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Utilize o Código VS para Node.js módulos no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de recipientes Linux: [Desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao completar qualquer um desses tutoriais, deverá ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando Azure IoT Edge](quickstart-linux.md)
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Código de Estúdio Visual](https://code.visualstudio.com/) configurado com as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Para desenvolver um módulo IoT Edge em Node.js, instale os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [Node.js e npm.](https://nodejs.org) O pacote npm é distribuído com o Node.js, o que significa que quando transfere o Node.js, obtém automaticamente o npm instalado no seu computador.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Os passos a seguir mostram-lhe como criar um módulo IoT Edge Node.js utilizando o Código do Estúdio Visual e as Ferramentas IoT Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Utilize o **npm** para criar um modelo de solução de Node.js que possa servir de base.

1. No Código do Estúdio Visual, selecione **Ver**  >  **Terminal Integrado** para abrir o terminal integrado do Código VS.

2. No terminal integrado, introduza o seguinte comando para instalar o **yeoman** e o gerador do módulo Node.js do Azure IoT Edge:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Ver**  >  **Paleta de Comando** para abrir a paleta de comandos VS Code.

4. Na paleta de comandos, escreva e execute o comando **Azure: Iniciar sessão** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

5. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a **solução edges padrão**. |
   | Selecionar modelo de módulo | Escolha **Node.js Módulo.** |
   | Indicar um nome para o módulo | Atribua o nome **NodeModule** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A sua imagem de contentor está pré-apovoada do nome que forneceu no último passo. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br>O repositório de imagem final parece \<registry name\> .azurecr.io/nodemodule. |

   ![Fornecer repositório de imagens do Docker](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu repositório de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. No explorador de código VS, abra o ficheiro **.env.**
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Atualmente, o Visual Studio Code pode desenvolver módulos Node.js para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda**, ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64**padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Cada modelo vem com o código de amostra incluído, que recolhe dados simulados do sensor do módulo **SimulaçãoSteperatureSensor** e o encaminha para o IoT Hub. Nesta secção, adicione código para o NodeModule analisar as mensagens antes de as enviar.

1. No explorador de código VS, abra **os módulos**  >  **NodeModule**  >  **app.js**.

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

6. Guarde o ficheiro app.js.

7. No explorador de código VS, abra o **deployment.template.jsno** ficheiro no seu espaço de trabalho de solução IoT Edge.

8. Adicione o módulo duplo NodeModule ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção `moduleContent`, após o módulo duplo `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicione módulo twin ao modelo de implementação](./media/tutorial-node-module/module-twin.png)

9. Guarde o ficheiro deployment.template.json.

## <a name="build-and-push-your-module"></a>Construa e empurre o seu módulo

Na secção anterior, criou uma solução IoT Edge e adicionou código ao NodeModule que filtrará as mensagens onde a temperatura da máquina reportada está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

1. Abra o terminal integrado do Código VS selecionando o Terminal **de Visualização**  >  **Terminal**.

2. Inscreva-se no Docker introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, palavra-passe e servidor de login do seu registo de contentores Azure. Pode recuperar estes valores a partir da secção de **teclas de acesso** do seu registo no portal Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Pode receber um aviso de segurança recomendando a utilização de `--password-stdin` . Embora essa melhor prática seja recomendada para cenários de produção, está fora do âmbito deste tutorial. Para mais informações, consulte a referência de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

3. No explorador de código VS, clique com o botão direito ** no ficheirodeployment.template.js** e selecione a **Solução de Borda De Construção e Pressão IoT**.

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

## <a name="deploy-modules-to-device"></a>Implementar módulos para dispositivo

Utilize o explorador visual Studio Code e a extensão Azure IoT Tools para implantar o projeto do módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.amd64.jsficheiro na** pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está a funcionar.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione a **deployment.amd64.jsno** ficheiro na pasta **config** e, em seguida, clique em **Select Edge Deployment Manifest**. Não utilize o ficheiro deployment.template.json.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver o novo **NodeModule** a funcionar juntamente com o módulo **SimuladoSteperatureSensor** e o **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

## <a name="view-the-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No explorador visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Start Monitoring Built-in Event Endpoint**.

2. Veja as mensagens que chegam ao seu Hub IoT. Pode demorar um pouco até as mensagens chegarem. O dispositivo IoT Edge tem de receber a sua nova implementação e iniciar todos os módulos. Em seguida, as alterações que fizemos ao código NodeModule aguardem até que a temperatura da máquina atinja os 25 graus antes de enviar mensagens. Adiciona também o tipo de mensagem **Alerta** a todas as mensagens que atinjam esse limiar de temperatura.

## <a name="edit-the-module-twin"></a>Editar o módulo twin

Usamos o módulo NodeModule twin no manifesto de implantação para definir o limiar de temperatura em 25 graus. Pode utilizar o módulo twin para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Código do Estúdio Visual, expanda os detalhes no seu dispositivo IoT Edge para ver os módulos de execução.

2. Clique à direita **NodeModule** e **selecione Editar módulo twin**.

3. Encontre **temperaturaSRestém nas** propriedades desejadas. Mude o seu valor para uma nova temperatura de 5 graus a 10 graus mais alto do que a última temperatura reportada.

4. Guarde o ficheiro twin do módulo.

5. Clique com o botão direito em qualquer lugar do painel de edição twin do módulo e selecione **Update module twin**.

6. Monitorize as mensagens de entrada de dispositivo para nuvem. Deve ver as mensagens paradas até que o novo limiar de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge.

Pode continuar nos próximos tutoriais para saber como o Azure IoT Edge pode ajudá-lo a implementar serviços em nuvem Azure para processar e analisar dados no limite.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Aprendizagem automática](tutorial-deploy-machine-learning.md) 
>  [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)