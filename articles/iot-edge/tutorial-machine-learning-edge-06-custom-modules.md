---
title: 'Tutorial: Criar e implementar módulos personalizados - Machine Learning on Azure IoT Edge'
description: Este tutorial mostra como criar e implementar módulos IoT Edge que processam dados de dispositivos de folhas através de um modelo de aprendizagem automática e, em seguida, enviam os insights para o IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772368"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutorial: Criar e implementar módulos IoT Edge personalizados

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, criamos três módulos IoT Edge que recebem mensagens de dispositivos de folhas, executam os dados através do seu modelo de aprendizagem automática e, em seguida, reencaminhamos insights para o IoT Hub.

O hub IoT Edge facilita a comunicação do módulo. A utilização do hub IoT Edge como corretor de mensagens mantém os módulos independentes uns dos outros. Os módulos apenas precisam especificar as inputs em que aceitam mensagens e as saídas para as quais escrevem mensagens.

Queremos que o dispositivo IoT Edge realize quatro coisas para nós:

* Receba dados dos dispositivos folha
* Preveja a vida útil remanescente (RUL) para o dispositivo que enviou os dados
* Envie uma mensagem apenas com o RUL para o dispositivo para o IoT Hub (esta função só pode ser modificada para enviar dados se o RUL descer abaixo de algum nível)
* Guarde os dados do dispositivo de folha para um ficheiro local no dispositivo IoT Edge. Este ficheiro de dados é periodicamente enviado para o IoT Hub através do upload de ficheiros para refinar o treino do modelo de aprendizagem automática. Usar o upload de ficheiros em vez de streaming de mensagens constantes é mais rentável.

Para realizar estas tarefas, utilizamos três módulos personalizados:

* **CLASSE RUL:** O módulo turboFanRulClassifier que criámos em [Train e implantamos um modelo de Machine Learning Azure](tutorial-machine-learning-edge-04-train-model.md) é um módulo de aprendizagem automática padrão, que expõe uma entrada chamada "amlInput" e uma saída chamada "amlOutput". O "amlInput" espera que a sua entrada se pareça exatamente com a entrada que enviámos para o serviço web baseado em ACI. Da mesma forma, "amlOutput" devolve os mesmos dados que o serviço web.

* **Escritor avro:** Este módulo recebe mensagens na entrada "avroModuleInput" e persiste a mensagem em formato Avro para disco riscar para posterior upload para IoT Hub.

* **Módulo router:** O módulo do router recebe mensagens de dispositivos de folhas a jusante, depois forma tostões e envia as mensagens para o classificador. O módulo recebe então as mensagens do classificador e envia a mensagem para o módulo de escritor Avro. Finalmente, o módulo envia apenas a previsão RUL para o IoT Hub.

  * Inputs:
    * **dispositivoInput**: recebe mensagens de dispositivos folha
    * **rulInput:** recebe mensagens do "amlOutput"

  * Saídas:
    * **Classificar:** envia mensagens para "amlInput"
    * **writeAvro:** envia mensagens para "avroModuleInput"
    * **toIotHub:** envia mensagens para $upstream, que passa as mensagens para o Hub IoT conectado

O diagrama abaixo mostra os módulos, inputs, saídas e as rotas do IoT Edge Hub para a solução completa:

![IoT Edge três módulos diagrama de arquitetura](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Os passos neste artigo são normalmente realizados por um desenvolvedor de nuvem.

## <a name="create-a-new-iot-edge-solution"></a>Criar uma nova solução IoT Edge

Durante a execução do segundo dos nossos dois Cadernos Azure, criámos e publicámos uma imagem de contentor contendo o nosso modelo RUL. O Azure Machine Learning, no âmbito do processo de criação de imagem, embalou esse modelo para que a imagem seja implantável como módulo Azure IoT Edge. Neste passo, vamos criar uma solução Azure IoT Edge utilizando o módulo "Azure Machine Learning" e apontar o módulo para a imagem que publicámos usando cadernos Azure.

1. Abra uma sessão remota de ambiente de trabalho para a sua máquina de desenvolvimento.

2. Pasta aberta **C:\\fonte\\IoTEdgeAndMlSample** no Código do Estúdio Visual.

3. Clique à direita no painel do explorador (no espaço em branco) e selecione **New IoT Edge Solution**.

    ![Criar nova solução IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Aceite o nome de solução predefinida **EdgeSolution**.

5. Escolha o **Azure Machine Learning** como modelo de módulo.

6. Nomeie o módulo **turbofanRulClassifier**.

7. Escolha o seu espaço de trabalho de aprendizagem automática.

8. Selecione a imagem que criou durante a execução do Caderno Azure.

9. Veja a solução e repare nos ficheiros que foram criados:

   * **deployment.template.json:** Este ficheiro contém a definição de cada um dos módulos na solução. Há três secções a prestar atenção neste ficheiro:

     * **Credenciais de registo:** Define o conjunto de registos de contentores personalizados que está a utilizar na sua solução. Neste momento, deve conter o registo do seu espaço de trabalho de aprendizagem automática, que é onde a sua imagem de Aprendizagem automática Azure foi armazenada. Você pode ter qualquer número de registos de contentores, mas para a simplicidade vamos usar este registo para todos os módulos

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Módulos:** Esta secção contém o conjunto de módulos definidos pelo utilizador que combinam com esta solução. Note que esta secção contém atualmente dois módulos: SimuladoSensor de Temperatura e turbofanRulClassifier. O Sensor De Temperatura Simulado foi instalado pelo modelo de Código do Estúdio Visual, mas não precisamos dele para esta solução. Pode eliminar a definição do módulo SimuladoSensor de Temperatura da secção de módulos. Note que a definição do módulo turbofanRulClassifier aponta para a imagem no registo do seu recipiente. À medida que adicionamos mais módulos à solução, eles vão aparecer nesta secção.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Rotas:** vamos trabalhar com rotas bastante neste tutorial. As rotas definem como os módulos comunicam uns com os outros. As duas vias definidas pelo modelo não correspondem ao encaminhamento que precisamos. A primeira rota envia todos os dados de qualquer saída do classificador para o IoT Hub ($upstream). A outra rota é para o Sensor de Temperatura Simulado, que acabamos de eliminar. Elimine as duas rotas predefinidas.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** este ficheiro é a versão depurada de deployment.template.json. Devemos espelhar todas as alterações do deployment.template.json neste ficheiro.

   * **.env:** este ficheiro é onde deve fornecer o nome de utilizador e a palavra-passe para aceder ao seu registo.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Clique certo no ficheiro deployment.template.json no explorador de Código de Estúdio Visual e selecione **Build IoT Edge Solution**.

11. Note que este comando cria uma pasta de config com um ficheiro deployment.amd64.json. Este ficheiro é o modelo de implementação de betão para a solução.

## <a name="add-router-module"></a>Adicionar módulo Router

Em seguida, adicionamos o módulo Router à nossa solução. O módulo Router lida com várias responsabilidades para a nossa solução:

* **Receba mensagens de dispositivos de folhas:** à medida que as mensagens chegam ao dispositivo IoT Edge a partir de dispositivos a jusante, o módulo Router recebe a mensagem e começa a orquestrar o encaminhamento da mensagem.
* **Envie mensagens para o módulo RUL Classifier:** quando uma nova mensagem é recebida a partir de um dispositivo a jusante, o módulo Router transforma a mensagem no formato que o RUL Classifier espera. O Router envia a mensagem ao RUL Classifier para uma previsão rul. Uma vez que o classificador tenha feito uma previsão, envia a mensagem de volta para o módulo Router.
* **Envie mensagens RUL ao IoT Hub:** quando o Router recebe mensagens do classificador, transforma a mensagem para conter apenas a informação essencial, id do dispositivo e RUL, e envia a mensagem abreviada para o centro IoT. Um novo refinamento, que não fizemos aqui, só enviaria mensagens para o IoT Hub quando a previsão rul fica abaixo de um limiar (por exemplo, quando o RUL é inferior a 100 ciclos). A filtragem desta forma reduziria o volume de mensagens e reduziria o custo do centro IoT.
* **Envie mensagem ao módulo Avro Writer:** para preservar todos os dados enviados pelo dispositivo a jusante, o módulo Router envia toda a mensagem recebida do classificador para o módulo Avro Writer, que persistirá e carregará os dados utilizando o upload de ficheiros IoT Hub.

> [!NOTE]
> A descrição das responsabilidades do módulo pode fazer com que o processamento pareça sequencial, mas o fluxo é baseado em mensagem/evento. É por isso que precisamos de um módulo de orquestração como o nosso módulo Router.

### <a name="create-module-and-copy-files"></a>Criar ficheiros de módulos e cópias

1. Clique certo na pasta dos módulos no Código do Estúdio Visual e escolha **Adicionar módulo De borda IoT**.

2. Escolha **o módulo C#**.

3. Nomeie o módulo **turbofanRouter**.

4. Quando solicitado para o seu Repositório de Imagem Docker, utilize o registo a partir do espaço de trabalho de aprendizagem automática (pode encontrar o registo no nó de credenciais de registo do seu ficheiro *deployment.template.json).* Este valor é o endereço totalmente qualificado para o registo, como ** \<o seu registo\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Neste artigo, utilizamos o Registo de Contentores Azure criado pelo espaço de trabalho Azure Machine Learning, que usávamos para treinar e implantar o nosso classificador. Isto é apenas por conveniência. Poderíamos ter criado um novo registo de contentores e publicado os nossos módulos lá.

5. Abra uma nova janela de terminal no Visual Studio Code (**Ver** > **Terminal)** e copie ficheiros do diretório dos módulos.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Quando solicitado a substituir program.cs, pressione `y` e bata. `Enter`

### <a name="build-router-module"></a>Construir módulo de router

1. No Código do Estúdio Visual, selecione **Terminal** > **Configure Predefinido Tarefa**de Construção .

2. Clique em **Criar tarefas.json ficheiro a partir do modelo**.

3. Clique em **.NET Core**.

4. Quando as tarefas.json abre, substitua o conteúdo com:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Salvar e fechar tarefas.json.

6. Executar `Ctrl + Shift + B` construção com ou tarefa de**construção**de ensaio de **terminal** > .

### <a name="set-up-module-routes"></a>Configurar rotas de módulos

Como mencionado acima, o tempo de execução do IoT Edge utiliza rotas configuradas no ficheiro *deployment.template.json* para gerir a comunicação entre módulos vagamente acoplados. Nesta secção, perfuramos como configurar as rotas para o módulo turbofanRouter. Cobriremos primeiro as rotas de entrada e depois avançaremos com as saídas.

#### <a name="inputs"></a>Entradas

1. No método init() de Program.cs registamos duas chamadas para o módulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. A primeira chamada ouve as mensagens enviadas para o **dispositivoInput** sink. A partir do diagrama acima, vemos que queremos direcionar as mensagens de qualquer dispositivo de folha para esta entrada. No ficheiro *deployment.template.json,* adicione uma rota que diga ao hub de borda para encaminhar qualquer mensagem recebida pelo dispositivo IoT Edge que não tenha sido enviada por um módulo IoT Edge para a entrada chamada "deviceInput" no módulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Em seguida, adicione uma rota para mensagens do módulo rulClassifier no módulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Saídas

Adicione quatro rotas adicionais ao parâmetro de rota $edgeHub, para lidar com as saídas do módulo Router.

1. Program.cs define o método SendMessageToClassifier(), que utiliza o cliente do módulo para enviar uma mensagem ao classificador RUL utilizando a rota:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. O SendRulMessageToIotHub () utiliza o cliente do módulo para enviar apenas os dados rul do dispositivo para o IoT Hub através da rota:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. O SendMessageToAvroWriter () utiliza o cliente do módulo para enviar a mensagem com os dados RUL adicionados ao módulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() envia mensagens falhadas a montante do IoT Hub onde podem ser encaminhados para mais tarde.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Com todas as rotas feitas em conjunto, o seu nó "$edgeHub" deve parecer o seguinte JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> A adição do módulo turbofanRouter `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`criou a seguinte rota adicional: . Remova esta rota, deixando apenas as rotas acima listadas no seu ficheiro deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copiar rotas para deployment.debug.template.json

Como um passo final, para manter os nossos ficheiros sincronizados, espelhar as alterações que fez para deployment.template.json in deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Adicione módulo Avro Writer

O módulo Avro Writer tem duas responsabilidades na nossa solução, para armazenar mensagens e carregar ficheiros.

* **Armazenar mensagens**: quando o módulo Avro Writer recebe uma mensagem, escreve a mensagem para o sistema de ficheiros local em formato Avro. Utilizamos um suporte de ligação, que monta um diretório (neste caso/dados/avrofiles) num caminho no recipiente do módulo. Este suporte permite que o módulo escreva para um caminho local (/avrofiles) e tenha os ficheiros acessíveis diretamente a partir do dispositivo IoT Edge.

* **Upload de ficheiros**: o módulo Avro Writer utiliza a funcionalidade de upload de ficheiros Azure IoT Hub para fazer o upload de ficheiros para uma conta de armazenamento Azure. Uma vez que um ficheiro é carregado com sucesso, o módulo elimina o ficheiro do disco

### <a name="create-module-and-copy-files"></a>Criar ficheiros de módulos e cópias

1. Na paleta de comando, procure e selecione **Python: Selecione Intérprete**.

1. Escolha o intérprete encontrado\\em C: Python37.

1. Abra novamente a paleta de comando e procure e, em seguida, selecione **Terminal: Selecione 'Padrão Shell**' .

1. Quando solicitado, escolha **O Pedido**de Comando .

1. Abra uma nova concha terminal, **Terminal** > **Novo Terminal.**

1. Clique certo na pasta dos módulos no Código do Estúdio Visual e escolha **Adicionar módulo De borda IoT**.

1. Escolha **Python Module** (Módulo de Python).

1. Nomeie o módulo "avroFileWriter".

1. Quando solicitado para o seu Repositório de Imagem De Docker, utilize o mesmo registo que utilizou ao adicionar o módulo Router.

1. Copiar ficheiros do módulo de amostra para a solução.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Se for solicitado a substituir `y` main.py, `Enter`escreva e bata em .

1. Note-se que filemanager.py e schema.py foram adicionados à solução e main.py foi atualizado.

> [!NOTE]
> Quando abrir um ficheiro Python, pode ser solicitado a instalar pylint. Não precisa de instalar o linter para completar este tutorial.

### <a name="bind-mount-for-data-files"></a>Montagem de ligação para ficheiros de dados

Como mencionado na introdução, o módulo de escritor conta com a presença de montagem de ligação para escrever ficheiros Avro para o sistema de ficheiros do dispositivo.

#### <a name="add-directory-to-device"></a>Adicione diretório ao dispositivo

1. Ligue-se ao seu dispositivo IoT Edge VM utilizando SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Crie o diretório que irá segurar as mensagens do dispositivo de folhas guardadas.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Atualizar permissões de diretório para torná-lo reescrevível pelo recipiente.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Validar o diretório agora tem (w) permissão para utilizador, grupo e proprietário.

   ```bash
   ls -la /data
   ```

   ![Permissões de diretório para avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Adicione diretório ao módulo

Para adicionar o diretório ao recipiente do módulo, modificaremos os Ficheiros Docker associados ao módulo avroFileWriter. Existem três Dockerfiles associados ao módulo: Dockerfile.amd64, Dockerfile.amd64.debug e Dockerfile.arm32v7. Estes ficheiros devem ser mantidos em sincronização no caso de pretendermos depurar ou implantar num dispositivo arm32. Para este artigo, concentre-se apenas em Dockerfile.amd64.

1. Na sua máquina de desenvolvimento, abra o ficheiro **Dockerfile.amd64.**

2. Modifique o ficheiro de modo a que se pareça com o seguinte exemplo:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Os `mkdir` `chown` comandos e comandos instruem o processo de construção do Docker para criar um diretório de alto nível chamado /avrofiles na imagem e, em seguida, fazer dos módulos o proprietário desse diretório. É importante que estes comandos sejam inseridos após a adição `useradd` do utilizador do módulo à imagem com o comando e antes que o contexto mude para o utilizador do módulo (user useruser).

3. Faça as alterações correspondentes para Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

O passo final para criar o encaixe é atualizar os ficheiros deployment.template.json (e deployment.debug.template.json) com a informação de ligação.

1. Abra a implementação.template.json.

2. Modifique a definição do módulo para `Binds` avroFileWriter adicionando o parâmetro que aponta o diretório /avrofiles do recipiente para o diretório local no dispositivo de borda. A definição do seu módulo deve corresponder a este exemplo:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Faça as alterações correspondentes para deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Montagem de ligação para acesso a config.yaml

Precisamos adicionar mais um laço para o módulo de escritor. Esta ligação dá ao módulo acesso para ler a cadeia de ligação a partir do ficheiro /etc/iotedge/config.yaml no dispositivo IoT Edge. Precisamos da cadeia de ligação para criar um IoTHubClient\_para que possamos ligar para o método de async blob de upload\_para o hub IoT. Os passos para adicionar este encaixe são semelhantes aos da secção anterior.

#### <a name="update-directory-permission"></a>Atualização da permissão de diretório

1. Ligue-se ao seu dispositivo IoT Edge utilizando o SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Adicione a permissão de leitura ao ficheiro config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Valide as permissões corretamente definidas.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Certifique-se de que as permissões para config.yaml são **-r-r--r--**- .

#### <a name="add-directory-to-module"></a>Adicione diretório ao módulo

1. Na sua máquina de desenvolvimento, abra o ficheiro **Dockerfile.amd64.**

2. Adicione um conjunto `mkdir` `chown` adicional de e comandos ao ficheiro de modo que se pareça com:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Faça as alterações correspondentes para Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

1. Abra o ficheiro **deployment.template.json.**

2. Modifique a definição do módulo para avroFileWriter adicionando uma segunda linha ao `Binds` parâmetro que aponta o diretório do contentor (/app/iotconfig) ao diretório local do dispositivo (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Faça as alterações correspondentes para deployment.debug.template.json.

## <a name="install-dependencies"></a>Instalar dependências

O módulo de escritor tem uma dependência de duas bibliotecas Python, fastavro e PyYAML. Precisamos instalar as dependências da nossa máquina de desenvolvimento e instruir o processo de construção do Docker para instalá-las à imagem do nosso módulo.

### <a name="pyyaml"></a>PyyAML

1. Na sua máquina de desenvolvimento, abra os **requisitos.txt** e adicione pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Abra o ficheiro **Dockerfile.amd64** e adicione um `pip install` comando para atualizar os conjuntos.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Faça as alterações correspondentes para Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Instale pyyaml localmente abrindo um terminal em Visual Studio Code e dactilografia

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Rio Fastavro

1. Em requisitos.txt, adicione fastavro após pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Instale fastavro na sua máquina de desenvolvimento utilizando o terminal Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurar o Hub IoT

Ao introduzir o dispositivo IoT Edge e os módulos no sistema, mudámos as nossas expectativas sobre quais os dados que serão enviados para o hub e para que finalidade. Temos de reconfigurar o encaminhamento no centro para lidar com a nossa nova realidade.

> [!NOTE]
> Reconfiguramos o hub antes de implantar módulos porque algumas das definições do hub, especificamente o upload de ficheiros, precisam de ser corretamente configuradas para que o módulo AvroFileWriter seja executado corretamente

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurar rota para mensagens RUL no IoT Hub

Com o router e o classifier no lugar, esperamos receber mensagens regulares contendo apenas o ID do dispositivo e a previsão RUL para o dispositivo. Queremos encaminhar os dados rul para o seu próprio local de armazenamento, onde podemos monitorizar o estado dos dispositivos, construir relatórios e alertas de incêndio conforme necessário. Ao mesmo tempo, queremos que quaisquer dados do dispositivo que ainda estão a ser enviados diretamente por um dispositivo de folha que ainda não tenha sido ligado ao nosso dispositivo IoT Edge continuem a encaminhar-se para o local de armazenamento atual.

#### <a name="create-a-rul-message-route"></a>Criar uma rota de mensagem RUL

1. No portal Azure, navegue até ao seu Hub IoT.

2. A partir da navegação à esquerda, escolha **o encaminhamento de mensagem**.

3. Selecione **Adicionar**.

4. Nomeie a rota **RulMessageRoute**.

5. **Selecione Adicionar** ao lado do seletor **endpoint** e escolher o **armazenamento Blob**.

6. No formulário Adicionar um ponto final de **armazenamento,** nomeie o ponto final **ruldata**.

7. Selecione **Escolher um recipiente**.

8. Escolha a conta de armazenamento utilizada ao longo deste tutorial, que é nomeado como **sufixo\<\>único iotedgeandml**.

9. Escolha o recipiente **ruldata** e clique **em Selecionar**.

10. Clique em **Criar** para criar o ponto final de armazenamento.

11. Para a consulta de **Encaminhamento,** insira a seguinte consulta:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Expanda a secção **de Teste** e, em seguida, a secção do corpo da **mensagem.** Substitua a mensagem por este exemplo das nossas mensagens esperadas:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selecione **rota de teste**. Se o teste for bem sucedido, vês "A mensagem corresponde à consulta."

14. Clique em **Guardar**.

#### <a name="update-turbofandevicetostorage-route"></a>Atualizar a rota turbofanDeviceToStorage

Não queremos encaminhar os novos dados de previsão para o nosso antigo local de armazenamento, por isso atualize a rota para os prevenir.

1. Na página de **encaminhamento** de mensagens IoT Hub, selecione o separador **Rotas.**

2. Selecione **turbofanDeviceDataToStorage**, ou qualquer nome que tenha dado à sua rota inicial de dados do dispositivo.

3. Atualizar a consulta de encaminhamento para

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Expanda a secção **de Teste** e, em seguida, a secção do corpo da **mensagem.** Substitua a mensagem por este exemplo das nossas mensagens esperadas:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Selecione **rota de teste**. Se o teste for bem sucedido, vês "A mensagem corresponde à consulta."

6. Selecione **Guardar**.

### <a name="configure-file-upload"></a>Configurar o carregamento de ficheiros

Configure a funcionalidade de upload de ficheiros IoT Hub para permitir que o módulo de autor do ficheiro faça o upload dos ficheiros para armazenamento.

1. Do navegador esquerdo no seu Hub IoT, escolha **upload de ficheiro**.

2. Selecione **recipiente de armazenamento Azure**.

3. Selecione a sua conta de armazenamento na lista.

4. Selecione o recipiente **uploadturbofanfiles** e clique **em Selecionar**.

5. Selecione **Guardar**. O portal notifica-o quando o salvamento estiver completo.

> [!Note]
> Não estamos a ligar a notificação de upload para este tutorial, mas consulte [receber uma notificação](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) de upload de ficheiro para obter detalhes sobre como lidar com a notificação de upload de ficheiros.

## <a name="build-publish-and-deploy-modules"></a>Construir, publicar e implementar módulos

Agora que fizemos as alterações de configuração, estamos prontos para construir as imagens e publicá-las no nosso registo de contentores Azure. O processo de construção utiliza o ficheiro deployment.template.json para determinar quais os módulos que precisam de ser construídos. As definições para cada módulo, incluindo a versão, encontram-se no ficheiro módulo.json na pasta do módulo. O processo de construção executa primeiro uma construção do Docker nos Dockerfiles que correspondem à configuração atual encontrada no ficheiro módulo.json para criar uma imagem. Em seguida, publica a imagem para o registo a partir do ficheiro module.json com uma etiqueta de versão correspondente à do ficheiro módulo.json. Finalmente, produz um manifesto de implantação específico para a configuração (por exemplo, implementação.amd64.json), que iremos implantar para o dispositivo IoT Edge. O dispositivo IoT Edge lê as informações do manifesto de implementação e, com base nas instruções, irá descarregar os módulos, configurar as rotas e definir quaisquer propriedades desejadas. Este método de implantação tem dois efeitos colaterais que deve estar ciente:

* Lag de **implementação:** uma vez que o tempo de funcionamento do IoT Edge deve reconhecer a alteração das suas propriedades desejadas antes de começar a reconfigurar, pode demorar algum tempo depois de implementar os seus módulos até que o tempo de funcionamento os recolha e comece a atualizar o dispositivo IoT Edge.

* **As versões** do módulo importam: se publicar uma nova versão do contentor de um módulo no seu registo de contentores utilizando as mesmas etiquetas de versão que o módulo anterior, o tempo de execução não descarregará a nova versão do módulo. Faz uma comparação da etiqueta versão da imagem local e da imagem desejada do manifesto de implantação. Se essas versões coincidirem, o tempo de execução não tem ação. Por isso, é importante incrementar a versão do seu módulo sempre que desejar implementar novas alterações. Incremente a versão alterando a propriedade da **versão** sob a propriedade de **tag** no ficheiro module.json para o módulo que está a mudar. Em seguida, construir e publicar o módulo.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Construir e publicar

1. No Visual Studio Code sobre o seu desenvolvimento VM, abra uma janela de terminal de código de estúdio visual e faça login no registo do seu contentor.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. No Código do Estúdio Visual, clique à direita em deployment.template.json e escolha **Build and Push IoT Edge Solution**.

### <a name="view-modules-in-the-registry"></a>Ver módulos no registo

Assim que a construção estiver concluída com sucesso, poderemos utilizar o portal Azure para rever os nossos módulos publicados.

1. No portal Azure, navegue até ao seu espaço de trabalho azure machine learning e clique na hiperligação para **registro**.

    ![Navegue para registrar a partir do espaço de trabalho do serviço de aprendizagem automática](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Do navegador do lado do registo, selecione **Repositórios**.

3. Note que ambos os módulos que criou, **avrofilewriter** e **turbofanrouter,** aparecem como repositórios.

4. Selecione **turbofanrouter** e note que publicou uma imagem marcada como 0.0.1-amd64.

   ![Ver primeira versão marcada do turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Implementar módulos para dispositivo IoT Edge

Construímos e configuramos os módulos na nossa solução, agora vamos implantar os módulos para o dispositivo IoT Edge.

1. No Código do Estúdio Visual, clique no ficheiro **deployment.amd64.json** na pasta config.

2. Escolha **criar a implementação para um único dispositivo**.

3. Escolha o seu dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

4. Refresque o painel de dispositivos Azure IoT Hub no explorador de Código de Estúdio Visual. Deve ver que os três novos módulos estão implantados, mas ainda não estão a funcionar.

5. Refresque-se depois de alguns minutos e verá os módulos em funcionamento.

   ![Ver módulos de execução no Código do Estúdio Visual](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Pode levar vários minutos para os módulos começarem e instalarem-se num estado de funcionamento constante. Durante esse tempo, poderá ver os módulos a iniciar e a parar à medida que tentam estabelecer uma ligação com o módulo hub IoT Edge.

## <a name="diagnosing-failures"></a>Diagnóstico de falhas

Nesta secção, partilhamos algumas técnicas para entender o que correu mal com um módulo ou módulos. Muitas vezes, uma falha pode ser detetada pela primeira vez a partir do estado no Código do Estúdio Visual.

### <a name="identify-failed-modules"></a>Identificar módulos falhados

* **Código de estúdio visual:** Veja o painel de dispositivos Azure IoT Hub. Se a maioria dos módulos estiver em estado de funcionamento, mas um está parado, precisa investigar ainda mais o módulo parado. Se todos os módulos estiverem em estado de paragem por um longo período de tempo, pode indicar também falhas.

* **Portal Azure:** Ao navegar até ao seu hub IoT no portal e, em seguida, encontrar a página de detalhes do dispositivo (sob ioT Edge, perfurar no seu dispositivo) pode descobrir que um módulo reportou um erro ou nunca reportou nada ao hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnóstico do dispositivo

Ao iniciar sessão no dispositivo IoT Edge, pode ter acesso a uma boa quantidade de informações sobre o estado dos seus módulos. O principal mecanismo que usamos são os comandos Docker que nos permitem examinar os recipientes e imagens no dispositivo.

1. Lista rume todos os contentores de corrida. Esperamos ver um recipiente para cada módulo com um nome que corresponda ao módulo. Além disso, este comando lista a imagem exata para o recipiente, incluindo a versão, para que possa corresponder às suas expectativas. Também pode listar imagens substituindo "imagem" por "recipiente" no comando.

   ```bash
   sudo docker container ls
   ```

2. Pegue os troncos para um recipiente. Este comando produz o que foi escrito para StdErr e StdOut no recipiente. Este comando funciona para contentores que começaram e depois morreram por alguma razão. Também é útil para entender o que tem acontecido com os contentores edgeAgent ou edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspecione um recipiente. Este comando dá uma tonelada de informação sobre a imagem. Os dados podem ser filtrados dependendo do que procura. Como exemplo, se quiser ver se os laços do avroFileWriter estão corretos, pode utilizar o comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Ligue-se a um recipiente de corrida. Este comando pode ser útil se quiser examinar o recipiente enquanto estiver em funcionamento:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criámos uma IoT Edge Solution em Código de Estúdio Visual com três módulos, um classificador, um router e um escritor/uploader de ficheiros. Montámos as rotas para permitir que os módulos se comunicassem entre si no dispositivo de borda, modificámos a configuração do dispositivo de borda, e atualizámos os Dockerfiles para instalar dependências e adicionar suportes de ligação aos recipientes dos módulos. Em seguida, atualizámos a configuração do IoT Hub para direcionar as nossas mensagens com base no tipo e para lidar com uploads de ficheiros. Com tudo no lugar, implantamos os módulos para o dispositivo IoT Edge e garantimos que os módulos estavam a funcionar corretamente.

Mais informações podem ser encontradas nas seguintes páginas:

* [Saiba como implementar módulos e estabelecer rotas no IoT Edge](module-composition.md)
* [Sintaxe de consulta do encaminhamento de mensagens do Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub message routing: now with routing on message body](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Carregar ficheiros com o Hub IoT](../iot-hub/iot-hub-devguide-file-upload.md)
* [Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Continue para o próximo artigo para começar a enviar dados e ver a sua solução em ação.

> [!div class="nextstepaction"]
> [Enviar dados através de gateway transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)
