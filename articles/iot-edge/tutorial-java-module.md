---
title: Tutorial de módulo Java personalizado-Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código Java e implementá-lo num dispositivo Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1aebe97b27902d37587ec6ac7009a5076b732f0f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840124"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver um módulo de IoT Edge Java para dispositivos Linux

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Você usará o dispositivo simulado IoT Edge criado no início rápido implantar Azure IoT Edge em um dispositivo simulado no [Linux](quickstart-linux.md) . Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo Java IoT Edge baseado no pacote de modelos maven Azure IoT Edge e no SDK de dispositivos Java Azure IoT.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo em **Java** usando **Visual Studio Code**e como implantá-lo em um **dispositivo Linux**. IoT Edge não oferece suporte a módulos Java para dispositivos Windows. 

Use a tabela a seguir para entender suas opções de desenvolvimento e implantação de módulos Java: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **AMD64 do Linux** | ![Usar VS Code para módulos Java no AMD64 do Linux](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 do Linux** | ![Usar VS Code para módulos Java no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolva módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir qualquer um desses tutoriais, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo Linux executando o Azure IOT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo IoT Edge em Java, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [Pacote de Extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para o Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Criar um projeto de módulo
As etapas a seguir criam um projeto de módulo IoT Edge baseado no pacote de modelo Azure IoT Edge Maven e no SDK do dispositivo Java IoT do Azure. Você cria o projeto usando Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução Java que pode personalizar com o seu próprio código. 

1. No Visual Studio Code, selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, insira e execute o comando **Azure IOT Edge: Nova solução**de IOT Edge. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **módulo do Java**. |
   | Forneça o valor para groupId | Introduza um valor de ID de grupo ou aceite a predefinição **com.edgemodule**. |
   | Indicar um nome para o módulo | Nome do seu módulo **JavaModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. Sua imagem de contêiner é preenchida previamente com base no nome que você forneceu na última etapa. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagens final é \<semelhante ao\>nome do registro. azurecr.Io/javamodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-java-module/repository.png)
   
Se é a primeira vez que cria o módulo Java, poderão ser necessários vários minutos para transferir os pacotes maven. A janela do VS Code carrega a área de trabalho da solução do IoT Edge. A área de trabalho da solução contém cinco componentes de nível superior. A pasta modules contém o código Java para seu módulo, bem como arquivos do Docker para criar seu módulo como uma imagem de contêiner. O ficheiro **\.env** armazena as credenciais do registo de contentor. O ficheiro **deployment.template.json** contém as informações que o runtime do IoT Edge utiliza para implementar módulos num dispositivo. E **deployment.debug.template.json** contentores de ficheiros a versão de depuração de módulos. Não irá editar a pasta **\.vscode** ou o ficheiro **\.gitignore** neste tutorial. 

Se não tiver especificado um registo de contentor ao criar a sua solução, mas aceitou o valor do localhost:5000 predefinido, não terá um ficheiro \.env. 

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro .env. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde este ficheiro. 

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos Java para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e procure **Azure IOT Edge: Defina a plataforma de destino padrão para**a solução de borda ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

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

3. Adicione a seguinte definição à classe **App**. Essa variável define um limite de temperatura. A temperatura da máquina medida não será relatada para o Hub IoT até que ela vá para esse valor. 

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

5. Adicione as seguintes duas classes internas estáticas à classe **App**. Essas classes atualizam a variável tempThreshold quando a propriedade desejada do módulo ' s é alterada. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

7. Salve o arquivo app. java.

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

10. Salve o arquivo Deployment. Template. JSON.

## <a name="build-and-push-your-module"></a>Crie e envie por push seu módulo

Na seção anterior, você criou uma solução de IoT Edge e adicionou o código ao **JavaModule** para filtrar mensagens em que a temperatura da máquina relatada está abaixo do limite aceitável. Agora, Compile a solução como uma imagem de contêiner e envie-a por push para o registro de contêiner. 

1. Abra o terminal integrado do VS Code ao selecionar **Ver** > **Terminal**.

1. Entre no Docker digitando o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu registro de contêiner do Azure. Você pode recuperar esses valores da seção **chaves de acesso** do registro no portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você pode receber um aviso de segurança recomendando o uso `--password-stdin`de. Embora essa prática recomendada seja recomendada para cenários de produção, ela está fora do escopo deste tutorial. Para obter mais informações, consulte a referência de [logon](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) do Docker.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**.

   O comando Build e Push inicia três operações. Primeiro, ele cria uma nova pasta na solução chamada **configuração** que contém o manifesto de implantação completa, criado sem informações no modelo de implantação e outros arquivos de solução. Em segundo lugar, `docker build` ele é executado para criar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele `docker push` é executado para enviar por push o repositório de imagens para o registro de contêiner.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o Visual Studio Code Explorer e a extensão de ferramentas de IoT do Azure para implantar o projeto de módulo em seu dispositivo de IoT Edge. Você já tem um manifesto de implantação preparado para seu cenário, o arquivo **Deployment. JSON** na pasta de configuração. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

Verifique se o dispositivo IoT Edge está em execução.

1. No Visual Studio Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** para ver sua lista de dispositivos IOT.

2. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**.

3. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json.

4. Clique no botão Atualizar. Você deve ver o novo **JavaModule** em execução junto com o módulo **SimulatedTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Visual Studio Code Explorer, clique com o botão direito do mouse no nome do dispositivo de IoT Edge e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.

2. Exiba as mensagens que chegam ao seu hub IoT. Pode levar algum tempo para que as mensagens cheguem, pois o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações feitas no código JavaModule esperam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o **alerta** de tipo de mensagem a todas as mensagens que atingem esse limite de temperatura. 

## <a name="edit-the-module-twin"></a>Edite o módulo...

Usamos o módulo JavaModule no manifesto de implantação para definir o limite de temperatura em 25 graus. Você pode usar o módulo "atualizar" para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. Em Visual Studio Code, expanda os detalhes em seu dispositivo de IoT Edge para ver os módulos em execução. 

2. Clique com o botão direito do mouse em **JavaModule** e selecione **Editar módulo**. 

3. Localize **TemperatureThreshold** nas propriedades desejadas. Altere seu valor para uma nova temperatura de 5 graus para 10 graus acima da temperatura mais recente relatada. 

4. Salve o arquivo de módulo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo e selecione **Atualizar módulo**. 

6. Monitore as mensagens de entrada do dispositivo para a nuvem. Você deve ver as mensagens param até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode aprender mais sobre como [desenvolver seus próprios módulos IOT Edge](module-development.md) ou como [desenvolver módulos com Visual Studio Code](how-to-vs-code-develop-module.md). Você pode continuar nos próximos tutoriais para saber como Azure IoT Edge pode ajudá-lo a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
