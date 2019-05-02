---
title: Tutorial de módulo de Java personalizado - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código Java e implementá-lo num dispositivo Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7f659240e7de729c6f64acf41d76530475fb810f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569465"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver um módulo de Java IoT Edge para dispositivos do Linux

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Irá utilizar o dispositivo IoT Edge simulado que criou na implementar o Azure IoT Edge num dispositivo simulado [Linux](quickstart-linux.md) início rápido. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo Java IoT Edge baseado no pacote de modelos maven Azure IoT Edge e no SDK de dispositivos Java Azure IoT.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo numa **Java** usando **Visual Studio Code**e como implementá-la para um **dispositivo Linux**. IoT Edge não suporta os módulos de Java para dispositivos Windows. 

Utilize a tabela seguinte para compreender as opções para desenvolver e implantar módulos de Java: 

| Java | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Utilizar o VS Code para módulos de Java no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 do Linux** | ![Utilizar o VS Code para módulos de Java no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve já leu o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contentores do Linux: [Desenvolver módulos do IoT Edge para dispositivos de Linux](tutorial-develop-for-linux.md). Ao concluir qualquer um desses tutoriais, deve ter os seguintes pré-requisitos no local: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [dispositivo de Linux com o Azure IoT Edge](quickstart-linux.md)
* Um registo de contentor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com o [ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores do Linux.

Para desenvolver um módulo do IoT Edge no Java, instale os seguintes pré-requisitos adicionais no computador de desenvolvimento: 

* [Pacote de Extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para o Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Criar um projeto de módulo
Os passos seguintes criam um projeto de módulo do IoT Edge que se baseia o pacote de modelo do Azure IoT Edge maven e o SDK de dispositivo Java do Azure IoT. Criar o projeto com o Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução Java que pode personalizar com o seu próprio código. 

1. No Visual Studio Code, selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **módulo do Java**. |
   | Forneça o valor para groupId | Introduza um valor de ID de grupo ou aceite a predefinição **com.edgemodule**. |
   | Indicar um nome para o módulo | Nome do seu módulo **JavaModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/javamodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-java-module/repository.png)
   
Se é a primeira vez que cria o módulo Java, poderão ser necessários vários minutos para transferir os pacotes maven. A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. O **módulos** pasta que contém o código de Java para seu módulo, bem como os ficheiros de Docker para a criação de seu módulo como uma imagem de contentor. O ficheiro **\.env** armazena as credenciais do registo de contentor. O ficheiro **deployment.template.json** contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo. E **deployment.debug.template.json** contentores de ficheiros a versão de depuração de módulos. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial. 

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env. 

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro .env. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos de Java para os dispositivos AMD64 de Linux e ARM32v7 de Linux. Tem de selecionar qual arquitetura visa com cada solução, uma vez que o contentor é criado e executado de forma diferente para cada tipo de arquitetura. A predefinição é AMD64 de Linux. 

1. Abra a paleta de comandos e procure **Azure IoT Edge: Definir a plataforma de destino predefinido para solução de ponta**, ou selecione o ícone de atalho na barra do lado na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos a utilizar uma máquina virtual do Ubuntu como o dispositivo do IoT Edge, para que irá manter a predefinição **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador do VS Code, abra **módulos** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Adicione o seguinte código na parte superior do ficheiro para importar novas as novas classes referenciadas.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Adicione a seguinte definição à classe **App**. Esta variável define um limite de temperatura. A temperatura de máquina de medida não reportada para o IoT Hub até que vai passar este valor. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Substitua o método de execução de **MessageCallbackMqtt** pelo seguinte código. Este método é chamado sempre que o módulo recebe uma mensagem MQTT do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Adicione as seguintes duas classes internas estáticas à classe **App**. Essas classes de atualizar a variável tempThreshold quando o módulo duplo pretendida alterações de propriedade. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Adicione as seguintes linhas ao método **main** após **client.open()** para subscrever as atualizações do módulo duplo.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Guarde o ficheiro App. Java.

8. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge.

9. Adicione o módulo **JavaModule** ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **moduleContent**, após o módulo duplo **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-java-module/module-twin.png)

10. Guarde o ficheiro de deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar por push o seu módulo

Na secção anterior, criou uma solução de IoT Edge e adicionar código para o **JavaModule** para filtrar mensagens onde a temperatura comunicada máquina está abaixo do limite aceitável. Agora, crie a solução como uma imagem de contentor e envie-a para o seu registo de contentor. 

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**.

1. Inicie sessão no Docker, introduzindo o seguinte comando no terminal. Inicie sessão com o nome de utilizador, a palavra-passe e o servidor de início de sessão do seu registo de contentor do Azure. Pode obter estes valores a partir da **chaves de acesso** secção do seu registo no portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Poderá receber um aviso de segurança recomenda a utilização do `--password-stdin`. Embora essa prática recomendada é recomendada para cenários de produção, é fora do âmbito deste tutorial. Para obter mais informações, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referência.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   O comando de compilação e emissão inicia três operações. Primeiro, ele cria uma nova pasta na solução denominada **config** que contém os ficheiros de manifesto, criados fora das informações no modelo de implementação e outras soluções de implantação completa. Em segundo lugar, ele é executado `docker build` para criar a imagem de contentor com base no dockerfile apropriado para a sua arquitetura de destino. Em seguida, ele é executado `docker push` para enviar por push o repositório de imagens para o seu registo de contentor.

## <a name="deploy-modules-to-device"></a>Implementar módulos no dispositivo

Utilize o Explorador do Visual Studio Code e a extensão das ferramentas de IoT do Azure para implementar o projeto de módulo no seu dispositivo IoT Edge. Já tem um manifesto de implantação preparado para o seu cenário, o **deployment.json** ficheiro na pasta config. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Certifique-se de que o seu dispositivo IoT Edge está em execução.

1. No Explorador do Visual Studio Code, expanda o **dispositivos do Azure IoT Hub** secção para ver a lista de dispositivos de IoT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. Deverá ver o novo **JavaModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Explorador do Visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **iniciar mensagens D2C monitorização**.

2. Ver as mensagens que chegam ao IoT Hub. Pode demorar algum tempo para as mensagens chegam, porque o dispositivo do IoT Edge tem de receber a sua nova implementação e comece a todos os módulos. Em seguida, as alterações feitas no código JavaModule Aguarde até a temperatura de máquina atinge 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **alerta** para todas as mensagens que atingir esse limite de temperatura. 

## <a name="edit-the-module-twin"></a>Editar o módulo duplo

Usamos o duplo do módulo de JavaModule no manifesto de implantação para definir o limiar de temperatura em graus 25. Pode utilizar o módulo duplo para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução. 

2. Com o botão direito **JavaModule** e selecione **editar módulo duplo**. 

3. Encontrar **TemperatureThreshold** nas propriedades pretendidas. Altere-o para uma nova temperatura graus de 5 a 10 graus de mais do que a temperatura comunicada mais recente. 

4. Guarde o ficheiro de duplo do módulo.

5. Com o botão direito em qualquer lugar na edição de painel e selecione o módulo duplo **duplo do módulo de atualização**. 

5. Monitorize as mensagens do dispositivo para a nuvem de entrada. Deverá ver as mensagens parar até que seja atingido o limiar de temperatura de novo. 
 
## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, pode saber mais sobre [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio Code](how-to-vs-code-develop-module.md). Pode continuar para os tutoriais seguintes para saber como Azure IoT Edge pode ajudá-lo a implementar serviços cloud do Azure para processar e analisar dados na periferia.

> [!div class="nextstepaction"]
> [As funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [serviço de visão personalizada](tutorial-deploy-custom-vision.md)
