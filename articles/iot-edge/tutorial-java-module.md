---
title: Tutorial de criar o módulo de Java personalizado - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código Java e implementá-lo num dispositivo Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 9a541f42670b3ccf83331e3e2e9069289bb9b4b3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224078"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver um módulo do IoT Edge do Java e implementar no seu dispositivo simulado

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe como criar e implementar um módulo do IoT Edge que filtra dados de sensores. Vai utilizar o dispositivo IoT Edge simulado que criou na Implementação do Azure IoT Edge num dispositivo simulado em inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo Java IoT Edge baseado no pacote de modelos maven Azure IoT Edge e no SDK de dispositivos Java Azure IoT.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode configurar um dispositivo IoT Edge ao seguir os passos nos rápidos para [Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* Para o IoT Edge em dispositivos Windows, versão 1.0.5 não suporta os módulos de Java. Para obter mais informações, consulte [notas de versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para obter passos sobre como instalar uma versão específica, consulte [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Pacote de Extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para o Visual Studio Code.
* [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Se estiver a desenvolver num dispositivo Windows, certifique-se de que o Docker está [configurado para utilizar contentores Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


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
   | Grupo de recursos | Para a gestão mais simples, utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante o IoT Edge guias de introdução e tutoriais. Por exemplo, **IoTEdgeResources**. |
   | Localização | Escolha uma localização perto de si. |
   | Utilizador admin | Defina para **Ativar**. |
   | SKU | Selecione **Básico**. | 

5. Selecione **Criar**.

6. Depois de o registo de contentores ser criado, navegue para o mesmo e selecione **Chaves de acesso**. 

7. Copie os valores de **Servidor de início de sessão**, **Nome de utilizador** e **Palavra-passe**. Utilize estes valores mais tarde no tutorial para fornecer acesso ao registo de contentor. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo do IoT Edge
Os passos seguintes criam um projeto de módulo do IoT Edge que se baseia o pacote de modelo do Azure IoT Edge maven e o SDK de dispositivo Java do Azure IoT. Criar o projeto com o Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

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

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador do VS Code, abra **módulos** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Adicione o seguinte código na parte superior do ficheiro para importar novas as novas classes referenciadas.

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

5. Adicione a seguinte definição à classe **App**. Esta variável define um limite de temperatura. A temperatura de máquina de medida não reportada para o IoT Hub até que vai passar este valor. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Substitua o método de execução de **MessageCallbackMqtt** pelo seguinte código. Este método é chamado sempre que o módulo recebe uma mensagem MQTT do hub do IoT Edge. Ele filtra as mensagens que comunicam temperaturas inferiores ao limiar de temperatura definido através do módulo duplo.

    ```java
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
    ```

8. Adicione as seguintes duas classes internas estáticas à classe **App**. Essas classes de atualizar a variável tempThreshold quando o módulo duplo pretendida alterações de propriedade. Todos os módulos têm o seu próprio módulo duplo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da cloud.

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

9. Adicione as seguintes linhas ao método **main** após **client.open()** para subscrever as atualizações do módulo duplo.

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

11. Guarde o ficheiro App. Java.

12. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. Este ficheiro diz ao agente do IoT Edge para quais módulos para implementar, neste caso **tempSensor** e **JavaModule**e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code povoa automaticamente a maior parte das informações que precisa no modelo de implementação, mas certifique-se de que tudo está correta para a sua solução: 

   1. A plataforma padrão do seu IoT Edge está definida como **amd64** no seu estado do VS Code barra, que significa que seu **JavaModule** está definido como Linux amd64 versão da imagem. Alterar a plataforma de predefinição na barra de status da **amd64** ao **arm32v7** ou **windows-amd64** se de que é arquitetura de seu dispositivo IoT Edge. 

      ![Plataforma de imagem do módulo de atualização](./media/tutorial-java-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto e não o nome predefinido **SampleModule** que alterou quando criou a solução IoT Edge.

   3. O **registryCredentials** secção armazena as suas credenciais do registo de Docker, para que o agente do IoT Edge pode extrair a sua imagem do módulo. Os pares de nome de utilizador e palavra-passe reais são armazenados no ficheiro .env, ignorado pelo git. Se ainda não o fez, adicione as suas credenciais para o ficheiro. env.  

   4. Se quiser obter mais informações sobre manifestos de implantação, consulte [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

13. Adicione o módulo **JavaModule** ao manifesto da implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **moduleContent**, após o módulo duplo **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-java-module/module-twin.png)

14. Guarde o ficheiro de deployment.template.json.

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Na secção anterior, criou uma solução de IoT Edge e adicionar código para o **JavaModule** para filtrar mensagens onde a temperatura comunicada máquina está abaixo do limite aceitável. Agora, crie a solução como uma imagem de contentor e envie-a para o seu registo de contentor. 

1. Inicie sessão no Docker, introduzindo o seguinte comando no Visual Studio Code terminal. Em seguida, pode emitir a sua imagem do módulo para o registo de contentor do Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilize o nome do utilizador, palavra-passe e servidor de início de sessão que copiou do registo de contentor do Azure na primeira secção. Também pode obter estes valores a partir da secção **Chaves de acesso** do registo no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro deployment.template.json e selecione **Criar e Emitir solução do IoT Edge**. 

Quando indicar ao Visual Studio Code para criar a solução, este utiliza primeiro as informações no modelo de implementação e gera um ficheiro deployment.json numa nova pasta com o nome **config**. Em seguida, executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos criam o código, colocam a aplicação Java em contentores e enviam-no para o registo de contentor que especificou quando inicializou a solução. 

Pode ver o endereço da imagem de contentor completo com a etiqueta no terminal integrado do VS Code. O endereço da imagem baseia-se nas informações no ficheiro module.json, com o formato \<repositório\>:\<versão\>-\<plataforma\>. Neste tutorial, deve ser semelhante a registryname.azurecr.io/javamodule:0.0.1-amd64.

>[!TIP]
>Se receber um erro ao tentar criar e enviar por push o seu módulo, tome as seguintes verificações:
>* Iniciou sessão ao Docker no Visual Studio Code, utilizando as credenciais do seu registo de contentor? Estas credenciais são diferentes dos que utilizar para iniciar sessão no portal do Azure.
>* O repositório do contentor está correto? Open **módulos** > **cmodule** > **Module** e encontre a **repositório** campo. O repositório de imagens deve ser semelhante à  **\<registryname\>.azurecr.io/javamodule**. 
>* Está a criar o mesmo tipo de contentores que está a executar o seu computador de desenvolvimento? Visual Studio Code é predefinida como contentores do Linux amd64. Se o computador de desenvolvimento está a executar contentores do Windows ou Linux arm32v7 contentores, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code, de acordo com sua plataforma de contentores.

## <a name="deploy-and-run-the-solution"></a>Implementar e executar a solução

No artigo de início rápido que utilizou para configurar o seu dispositivo IoT Edge, implementou um módulo com o portal do Azure. Também pode implementar módulos com a extensão do Kit de ferramentas do Azure IoT Hub (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT) para Visual Studio Code. Já tem um manifesto de implementação preparado para o seu cenário, o ficheiro **deployment.json**. Agora tudo o que precisa de fazer é selecionar um dispositivo para receber a implementação.

1. Na paleta de comandos VS Code, execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

2. Na paleta de comandos VS Code, execute **IoT Hub do Azure: Selecione o IoT Hub**. 

3. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar. 

4. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

5. Carregue com o botão direito do rato no nome do seu dispositivo do IoT Edge e, em seguida, selecione **Criar Implementação para o Dispositivo Único**. 

   ![Criar implementação para dispositivo único](./media/tutorial-java-module/create-deployment.png)

6. Selecione o ficheiro **deployment.json** na pasta **config** e, em seguida, clique em **Selecionar Manifesto de Implementação do Edge**. Não utilize o ficheiro deployment.template.json. 

7. Clique no botão Atualizar. Deverá ver o novo **JavaModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados. 

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução. 

No dispositivo IoT Edge, pode ver o estado dos seus módulos de implementação, utilizando o comando `iotedge list`. Deverá ver quatro módulos: os dois módulos de runtime do IoT Edge, o tempSensor e o módulo personalizado que criou neste tutorial. Pode demorar alguns minutos para que todos os módulos comecem, por isso, execute novamente o comando se não os vir todos inicialmente. 

Para ver as mensagens que são geradas por qualquer módulo, utilize o comando `iotedge logs <module name>`. 

Pode ver as mensagens conforme chegam ao seu hub IoT através do Visual Studio Code. 

1. Para monitorizar os dados que são recebidos pelo hub IoT, selecione as reticências (**...** ) e, em seguida, selecione **Iniciar Monitorização de Mensagens D2C**.
2. Para monitorizar a mensagem D2C para um dispositivo específico, clique com o botão direito do rato na lista e selecione **Iniciar Monitorização de Mensagens D2C**.
3. Para parar a monitorização de dados, execute o comando **IoT Hub do Azure: Parar a monitorização de mensagens D2C** na paleta de comandos. 
4. Para ver ou atualizar o módulo duplo, clique com o botão direito do rato no módulo na lista e selecione **Editar módulo duplo**. Para atualizar o módulo duplo, guarde o ficheiro JSON duplo, clique com o botão direito do rato na área de editor e selecione **Atualizar Módulo Duplo**.
5. Para ver os registos do Docker, instale o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para o VS Code. Pode encontrar os módulos em execução localmente no explorador do Docker. No menu de contexto, selecione **Mostrar Registos** para ver no terminal integrado.
 
## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge com código para filtrar os dados não processados que são gerados pelo seu dispositivo IoT Edge. Pode continuar para os próximos tutoriais para aprender outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais na periferia.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com bases de dados do SQL Server](tutorial-store-data-sql-server.md)

