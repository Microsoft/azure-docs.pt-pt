---
title: Criar e implementar módulos personalizados - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Criar e implementar os módulos do IoT Edge que processam dados a partir de dispositivos de folha através de um modelo de aprendizagem automática e, em seguida, enviam as informações para o IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6c4636fe370a4046b1c5020aee249529f1498639
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155518"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutorial: Criar e implementar módulos personalizados do IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Neste artigo, vamos criar três módulos do IoT Edge que recebem mensagens de dispositivos de folha, execute os dados por meio de seu modelo de aprendizagem automática e, em seguida, reencaminhe insights para o IoT Hub.

Hub do IoT Edge facilita a comunicação de módulo de módulo. Utilizar o IoT Edge hub como um mediador de mensagens mantém módulos independentes entre si. Módulos só precisam de especificar as entradas em que aceite mensagens e as saídas para que eles escrevem mensagens.

Queremos que o dispositivo do IoT Edge para realizar quatro coisas para nós:

* Receba dados de dispositivos de folha
* Prever a RUL para o dispositivo que enviou dados
* Enviar uma mensagem com apenas a RUL do dispositivo ao IoT Hub (essa função pode ser modificada para enviar apenas os dados se a RUL cair abaixo de algum nível)
* Salve os dados de dispositivos de folha para um ficheiro local no dispositivo IoT Edge. Este ficheiro de dados é carregado periodicamente para o IoT Hub através do carregamento de ficheiros para refinar o treinamento de modelo de machine learning. Com o carregamento de ficheiros em vez de mensagem constante de transmissão em fluxo é mais económico.

Para realizar essas tarefas, usamos três módulos personalizados:

* **Classificador RUL:** O módulo de turboFanRulClassifier que criámos no [Train e implementar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) é um módulo de aprendizagem máquina padrão, que expõe uma entrada chamado "amlInput" e uma saída chamado "amlOutput". "amlInput" espera que a sua entrada parecer exatamente como a entrada que foi enviado para o serviço web baseado no ACI. Da mesma forma, "amlOutput" devolve os mesmos dados que o serviço web.

* **Escritor de Avro:** Este módulo recebe mensagens de entrada "avroModuleInput" e persistir a mensagem no formato Avro para o disco para o upload posterior ao IoT Hub.

* **Módulo de roteador:** O módulo do roteador recebe mensagens de dispositivos de folha a jusante, em seguida, formata e envia as mensagens para o classificador. O módulo, em seguida, recebe as mensagens do classificador e encaminha a mensagem para o módulo de escritor do Avro. Por fim, o módulo envia apenas a predição de RUL no hub IoT.

  * Entradas:
    * **deviceInput**: recebe mensagens de dispositivos de folha
    * **rulInput:** recebe mensagens de "amlOutput"

  * Saídas:
    * **classificar:** envia mensagens para "amlInput"
    * **writeAvro:** sends messages "avroModuleInput"
    * **toIotHub:** envia mensagens para $ a montante, que passa as mensagens para o IoT Hub ligado

O diagrama abaixo mostra os módulos, entradas, saídas e as rotas de Hub do IoT Edge para a solução completa:

![Diagrama de arquitetura de módulos do IoT Edge três](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Os passos neste artigo são, normalmente realizados por um desenvolvedor de cloud.

## <a name="create-a-new-iot-edge-solution"></a>Criar uma nova solução de IoT Edge

Durante a execução da segunda de nossos dois blocos de notas do Azure, criados e publicados de uma imagem de contentor que contém o nosso modelo RUL. O Azure Machine Learning, como parte do processo de criação da imagem, criado em partes para criar a imagem implementáveis como um módulo do Azure IoT Edge. Neste passo, vai criar uma solução do Azure IoT Edge através do módulo "Azure Machine Learning" e apontar o módulo para a imagem que publicamos com blocos de notas do Azure.

1. Abra uma sessão de área de trabalho remota para o seu computador de desenvolvimento.

2. Abrir pasta **c:\\origem\\IoTEdgeAndMlSample** no Visual Studio Code.

3. Clique com o botão direito do rato no painel do explorer (no espaço em branco) e selecione **nova solução do IoT Edge**.

    ![Criar nova solução de IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Aceite o nome da solução predefinida **EdgeSolution**.

5. Escolher **do Azure Machine Learning** como o modelo de módulo.

6. Nome do módulo **turbofanRulClassifier**.

7. Escolha a área de trabalho do machine learning.

8. Selecione a imagem que criou ao executar o bloco de notas do Azure.

9. Veja a solução e observe os ficheiros que foram criados:

   * **deployment.template.json:** Este ficheiro contém a definição de cada um dos módulos na solução. Existem três secções de prestar atenção neste ficheiro:

     * **Credenciais do registo:** Define o conjunto de registos de contentor personalizado que estiver a utilizar na sua solução. Direita agora, esta deve conter o registo da área de trabalho do machine learning, que é onde sua imagem do Azure Machine Learning foi armazenada. Pode ter qualquer número de registos de contentores, mas para simplificar utilizaremos esta um registo para todos os módulos

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Módulos:** Esta secção contém o conjunto de módulos definidos pelo utilizador que acompanham esta solução. Observará que esta secção contém atualmente dois módulos: tempSensor e turbofanRulClassifier. O tempSensor foi instalado pelo modelo do Visual Studio Code, mas não precisamos dela para esta solução. É possível eliminar a definição de módulo tempSensor da seção de módulos. Tenha em atenção que a definição de módulo turbofanRulClassifier aponta para a imagem no seu registo de contentor. À medida que adicionamos mais módulos para a solução, estes serão apresentados nesta secção.

       ```json
       "modules": {
         "tempSensor": {
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

     * **Rotas:** vamos trabalhar com rotas bastante neste tutorial. Rotas de definem a forma como os módulos comunicam entre si. As duas rotas definidas pelo modelo não coincide com o encaminhamento que é necessário. A primeira rota envia todos os dados a partir de qualquer saída do classificador para o IoT Hub (a montante$). A outra rota destina-se a tempSensor, o que estamos apenas de ser eliminados. Elimine as rotas dois predefinidas.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment.Debug.Template.JSON:** este ficheiro é a versão de depuração do deployment.template.json. Podemos deve espelhar todas as alterações da deployment.template.json neste ficheiro.

   * **. env:** esse arquivo é onde deve fornecer o nome de utilizador e palavra-passe para aceder ao seu registo.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Clique com o botão direito do rato no ficheiro deployment.template.json no explorer do Visual Studio Code e selecione **Build Solution do IoT Edge**.

11. Tenha em atenção que este comando cria uma pasta de configuração com um arquivo de deployment.amd64.json. Este ficheiro é o modelo de implementação concreta da solução.

## <a name="add-router-module"></a>Adicionar módulo do roteador

Em seguida, adicionamos o módulo do roteador para nossa solução. O módulo do roteador lida com várias responsabilidades para a nossa solução:

* **Receber mensagens de dispositivos de folha:** quando chegarem novas mensagens no dispositivo IoT Edge de downstream dispositivos, o módulo do roteador recebe a mensagem e começa a orquestrar o encaminhamento da mensagem.
* **Enviar mensagens para o módulo de classificador de RUL:** quando uma nova mensagem é recebida a partir de um dispositivo de downstream, o módulo do roteador transforma a mensagem para o formato que o classificador de RUL espera. O roteador envia a mensagem para o classificador de RUL para uma predição de RUL. Assim que o classificador fez uma previsão, envia a mensagem de volta para o módulo do roteador.
* **Enviar mensagens RUL ao IoT Hub:** quando o roteador recebe mensagens do classificador, transforma a mensagem para conter apenas as informações essenciais, ID de dispositivo e a RUL e envia a mensagem abreviada para o hub IoT. Um refinamento adicional, que não fizemos aqui, seria enviar mensagens para o IoT Hub apenas quando a predição de RUL cai abaixo de um limite (por exemplo, quando a RUL for ciclos de menos de 100). A filtragem desta forma, seria reduzir o volume de mensagens e reduzir os custos do IoT hub.
* **Enviar mensagem para o módulo de escritor do Avro:** para preservar a todos os dados enviados pelo dispositivo downstream, o módulo do roteador envia a mensagem inteira recebida a partir do classificador para o módulo de escritor do Avro, que irá manter e carregar os dados usando o arquivo do IoT Hub carrega.

> [!NOTE]
> A descrição das responsabilidades de módulo pode fazer o processamento parece seqüencial, mas o fluxo é baseado em mensagens/eventos. É por isso precisamos de um módulo de orquestração, como o nosso módulo de roteador.

### <a name="create-module-and-copy-files"></a>Criar o módulo e copia os ficheiros

1. Clique com o botão direito do rato na pasta de módulos do Visual Studio Code e escolha **Adicionar módulo do IoT Edge**.

2. Escolher  **C# módulo**.

3. Nome do módulo **turbofanRouter**.

4. Quando lhe for pedido para o seu repositório de imagens do Docker, utilize o registo da área de trabalho de aprendizagem (pode encontrar o registo no nó registryCredentials da sua *deployment.template.json* ficheiro). Este valor é o endereço totalmente qualificado para o registo, como  **\<seu registo\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Neste artigo, utilizamos o Azure Container Registry criados pelo Azure Machine Learning serviço área de trabalho, que usamos para preparar e implementar a nossa classificador. Isso é puramente para sua comodidade. Podemos poderia ter criado um novo registo de contentor e publicada aqui nossa módulos.

5. Abra uma nova janela de terminal no Visual Studio Code (**View** > **Terminal**) e copie os ficheiros do diretório de módulos.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Quando lhe for pedido para substituir program.cs, prima `y` e, em seguida, prima `Enter`.

### <a name="build-router-module"></a>Criar o módulo de router

1. No Visual Studio Code, selecione **Terminal** > **configurar predefinido criar tarefa**.

2. Clique em **tasks.json de criar ficheiro de modelo**.

3. Clique em **.NET Core**.

4. Quando abre tasks.json substitua os conteúdos com:

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

5. Guarde e feche tasks.json.

6. Execução de compilação com `Ctrl + Shift + B` ou **Terminal** > **executar tarefa de compilação**.

### <a name="set-up-module-routes"></a>Configurar as rotas de módulo

Conforme mencionado acima, o runtime do IoT Edge utiliza rotas configuradas no *deployment.template.json* ficheiros para gerir a comunicação entre livremente acoplado módulos. Nesta secção, vamos explorar como configurar as rotas para o módulo de turbofanRouter. Iremos abordar as rotas de entrada pela primeira vez e, em seguida, avançar as saídas.

#### <a name="inputs"></a>Entradas

1. No método Init () do Program.cs registramos dois retornos de chamada para o módulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. O retorno de chamada de primeira escuta as mensagens enviadas para o **deviceInput** sink. O diagrama acima, podemos ver que queremos para rotear mensagens de qualquer dispositivo de folha para esta entrada. Na *deployment.template.json* de ficheiros, adicione uma rota que informa ao hub do edge para encaminhar qualquer mensagem recebida pelo dispositivo IoT Edge que não foi enviado por um módulo do IoT Edge para a entrada chamada "deviceInput" no módulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Em seguida adicione uma rota para mensagens do módulo rulClassifier no módulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/classifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Saídas

Adicione quatro rotas adicionais para o parâmetro de rota $edgeHub, para processar as saídas do módulo do roteador.

1. Program.cs define o método SendMessageToClassifier(), que usa o cliente do módulo para enviar uma mensagem para o classificador RUL usando a rota:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/classifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() utiliza o cliente do módulo para enviar apenas os dados RUL para o dispositivo ao IoT Hub através da rota:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() utiliza o cliente do módulo para enviar a mensagem com os dados RUL adicionados ao módulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() envia mensagens com falha do IoT Hub em que pode ser encaminhadas para utilizar mais tarde.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Com todas as rotas juntas "$edgeHub" nó deve ser semelhante o seguinte JSON:

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
> Adicionar o módulo de turbofanRouter criado a rota adicional seguinte: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Remover esta rota, deixar apenas as rotas listados acima no seu ficheiro deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copiar a rotas para deployment.debug.template.json

Como passo final, para manter nossos arquivos em sincronia, refletir as alterações que efetuou para deployment.template.json no deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Adicionar o módulo de escritor do Avro

O módulo de escritor do Avro tem duas responsabilidades em nossa solução, para armazenar as mensagens e carregar ficheiros.

* **Store mensagens**: quando o módulo de escritor do Avro recebe uma mensagem, escreve a mensagem para o sistema de arquivos local no formato Avro. Utilizamos uma montagem de bind, que monta um diretório (em /data/avrofiles neste caso) num caminho de contentor do módulo. Este montagem permite que o módulo escrever um caminho local (/ avrofiles) e têm esses arquivos acessíveis diretamente a partir do dispositivo IoT Edge.

* **Carregar ficheiros**: o módulo de escritor do Avro usa a funcionalidade de carregamento de ficheiros do IoT Hub do Azure para carregar ficheiros para uma conta de armazenamento do Azure. Depois de um ficheiro é carregado com êxito, o módulo elimina o ficheiro do disco

### <a name="create-module-and-copy-files"></a>Criar o módulo e copia os ficheiros

1. Na paleta de comandos, procure, em seguida, selecione **Python: Selecione o interpretador**.

1. Selecione o interpretador encontrado em c:\\Python37.

1. Abra novamente a paleta de comandos e procure em seguida, selecione **Terminal: Selecione a Shell predefinida**.

1. Quando lhe for pedido, escolha **linha de comandos**.

1. Abra um novo shell de terminal **Terminal** > **novo Terminal**.

1. Clique com o botão direito do rato na pasta de módulos do Visual Studio Code e escolha **Adicionar módulo do IoT Edge**.

1. Escolha **Python Module** (Módulo de Python).

1. Nomeie o módulo "avroFileWriter".

1. Quando lhe for pedido para o seu repositório de imagens do Docker, utilize o mesmo registo como é utilizada quando adicionar o módulo do roteador.

1. Copie ficheiros do módulo de exemplo para a solução.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Se lhe for pedido para substituir main.py, escreva `y` e, em seguida, prima `Enter`.

1. Observe que filemanager.py e schema.py foram adicionados à solução e main.py foi atualizado.

> [!NOTE]
> Quando abre um ficheiro de Python, poderá ser solicitado a instalar pylint. Não é necessário instalar o linter para concluir este tutorial.

### <a name="bind-mount-for-data-files"></a>Vincular a montagem para ficheiros de dados

Conforme mencionado na introdução, o módulo de escritor baseia-se na presença de montagem de enlace para escrever ficheiros Avro para sistema de ficheiros do dispositivo.

#### <a name="add-directory-to-device"></a>Adicionar o diretório para o dispositivo

1. Ligar ao seu dispositivo IoT Edge VM através de SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Crie o diretório que irá conter a folha guardada mensagens do dispositivo.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Atualize as permissões de diretório para torná-lo gravável, o contentor.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Validar o diretório tem agora a permissão de escrita (w) de utilizador, grupo e proprietário.

   ```bash
   ls -la /data
   ```

   ![Permissões para avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Adicionar o diretório para o módulo

Para adicionar o diretório para o contentor do módulo, modificamos o Dockerfiles associados com o módulo de avroFileWriter. Existem três Dockerfiles associados com o módulo: Dockerfile.AMD64 Dockerfile.amd64.debug e Dockerfile.arm32v7. Esses arquivos devem ser mantidos em sincronia no caso de deseja depurar ou implementar um dispositivo de arm32. Neste artigo, concentre-se apenas em Dockerfile.amd64.

1. No computador de desenvolvimento, abra a **Dockerfile.amd64** ficheiro.

2. Modifique o ficheiro, de modo que ela se pareça com o exemplo seguinte:

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

   O `mkdir` e `chown` comandos instruir o processo de compilação do Docker para criar um diretório de nível superior chamado /avrofiles na imagem e, em seguida, para tornar o moduleuser o proprietário desse diretório. É importante que estes comandos são inseridos depois do utilizador de módulo é adicionado à imagem com o `useradd` comando e antes das Alternâncias de contexto para o moduleuser (utilizador moduleuser).

3. Faça as alterações de correspondentes à Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

A etapa final de criar a enlace é atualizar os ficheiros de deployment.template.json (e deployment.debug.template.json) com as informações de enlace.

1. Abra deployment.template.json.

2. Modificar a definição de módulo para avroFileWriter adicionando o `Binds` parâmetro que aponta o /avrofiles de diretório do contentor para o diretório local no dispositivo de limite. A definição de módulo deve corresponder a este exemplo:

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

3. Faça as alterações de correspondentes à deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Vincular de montagem para o acesso ao config.yaml

É necessário adicionar um enlace mais para o módulo de escritor. Esta associação dá ao módulo acesso para ler a cadeia de ligação do arquivo /etc/iotedge/config.yaml no dispositivo IoT Edge. É necessário que a cadeia de ligação para criar um IoTHubClient, de modo que podemos chamar o carregamento\_BLOBs\_método async para carregar ficheiros para o hub IoT. Os passos para adicionar este enlace são semelhantes na secção anterior.

#### <a name="update-directory-permission"></a>Atualizar permissão do diretório

1. Ligar ao seu dispositivo IoT Edge através de SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Adicione a permissão de leitura para o ficheiro de config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Validar as permissões estão definidas corretamente.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Certifique-se de que as permissões para config.yaml **- r - r - r -** .

#### <a name="add-directory-to-module"></a>Adicione o diretório de módulo

1. No computador de desenvolvimento, abra a **Dockerfile.amd64** ficheiro.

2. Adicionar um conjunto adicional de `mkdir` e `chown` comandos para o ficheiro por isso, ou seja se parece com:

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

3. Faça as alterações de correspondentes à Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

1. Abra o **deployment.template.json** ficheiro.

2. Modificar a definição de módulo para avroFileWriter ao adicionar uma segunda linha para o `Binds` parâmetro aponta o diretório do contentor (/ aplicação/iotconfig) para o diretório local no dispositivo (/ etc/iotedge).

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

3. Faça as alterações de correspondentes à deployment.debug.template.json.

## <a name="install-dependencies"></a>Instalar dependências

O módulo de escritor assume uma dependência em duas bibliotecas de Python, fastavro e PyYAML. É necessário instalar as dependências em nossa máquina de desenvolvimento e instruir o processo de compilação do Docker para instalá-los na imagem de nosso módulo.

### <a name="pyyaml"></a>PyYAML

1. No computador de desenvolvimento, abra a **Requirements. txt** de ficheiros e adicionar pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Abra o **Dockerfile.amd64** de ficheiros e adicionar um `pip install` comando para atualizar setuptools.

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

3. Faça as alterações de correspondentes à Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Instalar pyyaml localmente ao abrir um terminal no Visual Studio Code e digitar

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. No Requirements. txt, adicione fastavro depois pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Instale fastavro para o seu computador de desenvolvimento com o Visual Studio Code terminal.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurar o IoT Hub

Com a introdução do dispositivo IoT Edge e os módulos no sistema, alteramos a nossas expectativas sobre os dados que serão enviados para o hub e com que finalidade. É necessário reconfigurar o encaminhamento no hub para lidar com a nossa nova realidade.

> [!NOTE]
> Podemos reconfigurar o hub antes de implementar módulos porque algumas das definições do hub, especificamente ficheiro carregar, tem de estar corretamente configurados para o módulo de avroFileWriter seja executado corretamente

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurar a rota para mensagens RUL no IoT Hub

Com o roteador e classificador in-loco, podemos esperar receber mensagens regulares, que contém apenas o ID de dispositivo e a predição de RUL para o dispositivo. Queremos encaminhar os dados RUL para sua própria localização de armazenamento onde pode monitorizar o estado dos dispositivos, desenvolvemos os relatórios e são acionados alertas, conforme necessário. Ao mesmo tempo, queremos que os dados de dispositivo que ainda estão a ser enviados diretamente por um dispositivo de folha que ainda não foi anexado ao nosso dispositivo IoT Edge para continuar para encaminhar para a localização de armazenamento atual.

#### <a name="create-a-rul-message-route"></a>Criar uma rota de mensagem RUL

1. No portal do Azure, navegue até ao seu IoT Hub.

2. No painel de navegação à esquerda, escolha **roteamento de mensagens**.

3. Selecione **Adicionar**.

4. Nome da rota **RulMessageRoute**.

5. Selecione **adicionar** junto a **ponto final** Seletor e escolha **armazenamento de BLOBs**.

6. Na **adicionar um ponto de final de armazenamento** formam, nomeie o ponto final **ruldata**.

7. Selecione **escolher um contentor**.

8. Selecione a conta de armazenamento utilizada neste tutorial, o que é chamado, como **iotedgeandml\<sufixo exclusivo\>** .

9. Escolha o **ruldata** contentor e clique em **selecione**.

10. Clique em **criar** para criar o ponto final de armazenamento.

11. Para o **consulta de encaminhamento**, introduza a seguinte consulta:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Expanda a **teste** secção e, em seguida, o **corpo da mensagem** secção. Substitua a mensagem com este exemplo de nossas mensagens esperados:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selecione **rota de teste**. Se o teste for bem sucedido, verá "correspondido a consulta de mensagem."

14. Clique em **Guardar**.

#### <a name="update-turbofandevicetostorage-route"></a>Atualizar a rota de turbofanDeviceToStorage

Não queremos encaminhar os novos dados de previsão para o local de armazenamento antigo, portanto, atualizar a rota preveni-lo.

1. No IoT Hub **roteamento de mensagens** página, selecione a **rotas** separador.

2. Selecione **turbofanDeviceDataToStorage**, ou qualquer nome deu para sua rota de dados inicial do dispositivo.

3. Atualizar a consulta de encaminhamento para

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Expanda a **teste** secção e, em seguida, o **corpo da mensagem** secção. Substitua a mensagem com este exemplo de nossas mensagens esperados:

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

5. Selecione **rota de teste**. Se o teste for bem sucedido, verá "correspondido a consulta de mensagem."

6. Selecione **Guardar**.

### <a name="configure-file-upload"></a>Configurar o carregamento de ficheiros

Configure a funcionalidade de carregamento de arquivo do IoT Hub para ativar o módulo de escritor do ficheiro carregar ficheiros para o armazenamento.

1. A partir do navegador à esquerda do seu IoT Hub, escolha **carregamento de ficheiros**.

2. Selecione **contentor de armazenamento do Azure**.

3. Selecione a sua conta de armazenamento na lista.

4. Selecione o **uploadturbofanfiles** contentor e clique em **selecione**.

5. Selecione **Guardar**. O portal notifica-o quando o salvamento for concluído.

> [!Note]
> Nós não ativar a notificação de carregamento para este tutorial, mas ver [receber uma notificação de carregamento do ficheiro](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) para obter detalhes sobre como lidar com o ficheiro de notificação de carregar.

## <a name="build-publish-and-deploy-modules"></a>Criar, publicar e implementar módulos

Agora que tenhamos feito as alterações de configuração, estamos prontos para criar as imagens e publique-os para o nosso registo de contentor do Azure. O processo de compilação usa o arquivo de deployment.template.json para determinar quais módulos devem ser criados. As definições para cada módulo, incluindo a versão, são encontradas no arquivo Module cab da pasta do módulo. O processo de compilação primeiro executa uma compilação do Docker no Dockerfiles correspondentes a configuração atual, foi encontrada no arquivo Module para criar uma imagem. Em seguida, publica a imagem para o registo do arquivo Module com uma etiqueta de versão que correspondam o um no arquivo Module. Por fim, ele produz um manifesto de implantação de configuração específicas (por exemplo, deployment.amd64.json), que vamos implementar no dispositivo IoT Edge. O dispositivo do IoT Edge lê as informações da implementação manifesto e com base nas instruções irão transferir os módulos, configurar as rotas e definir quaisquer propriedades pretendidas. Este método de implementação tem dois efeitos colaterais que deve estar atento:

* **Atraso de implementação:** , uma vez que o runtime do IoT Edge deve reconhecer a alteração para as respetivas propriedades pretendidas, antes de iniciar a reconfigurar, pode demorar algum período de tempo depois de implementar seus módulos até que o tempo de execução escolhe-los e começa a atualizar o IoT Edge dispositivo.

* **Questão de versões do módulo:** se publicar uma nova versão do contentor de um módulo de registo do contentor com as mesmas etiquetas de versão como o módulo anterior, o tempo de execução não irá transferir a nova versão do módulo. Ele faz uma comparação da marca a versão da imagem local e a imagem pretendida a partir do manifesto de implantação. Se corresponderem a essas versões, o tempo de execução não faz nada. Portanto, é importante incrementar a versão do seu módulo sempre que pretender implementar as novas alterações. Incrementar a versão, alterando a **versão** propriedade sob a **marca** propriedade no arquivo Module para o módulo está a alterar. Em seguida, criar e publicar o módulo.

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

### <a name="build-and-publish"></a>Criar e publicar

1. No Visual Studio Code, no desenvolvimento da sua VM, abra uma janela de terminal do Visual Studio Code e o início de sessão para o seu registo de contentor.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. No Visual Studio Code, clique com o botão direito no deployment.template.json e escolha **compilação e enviar por Push o IoT Edge solução**.

### <a name="view-modules-in-the-registry"></a>Módulos de exibição no Registro

Assim que a compilação for concluída com êxito, será capazes de utilizar o portal do Azure para rever a nossa módulos publicados.

1. No portal do Azure, navegue até à sua área de trabalho do serviço do Azure Machine Learning e clique na hiperligação para **Registro**.

    ![Navegue para o registo da área de trabalho do serviço de aprendizagem automática](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. A partir do navegador de lado o registro, selecione **repositórios**.

3. Tenha em atenção que ambos os módulos que criou, **avrofilewriter** e **turbofanrouter**, são apresentados como repositórios.

4. Selecione **turbofanrouter** e tenha em atenção que publicou uma imagem marcada como 0.0.1-amd64.

   ![Vista etiquetados primeiro a versão do turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Implementar módulos no dispositivo IoT Edge

Temos criado e configurado os módulos em nossa solução, agora, vamos implementar os módulos no dispositivo IoT Edge.

1. No Visual Studio Code, clique com botão direito sobre os **deployment.amd64.json** ficheiro na pasta config.

2. Escolher **criar a implementação de único dispositivo**.

3. Escolha o seu dispositivo do IoT Edge **aaTurboFanEdgeDevice**.

4. Atualize o painel de dispositivos do IoT Hub do Azure no explorer do Visual Studio Code. Deverá ver que os três novos módulos são implementados, mas não ainda está em execução.

5. Atualize novamente após alguns minutos e verá os módulos em execução.

   ![Ver os módulos em execução no Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Pode demorar alguns minutos para que os módulos começar e criar um Estado de execução constante. Durante esse tempo, poderá ver módulos iniciar e parar à medida que tentam estabelecer uma ligação com o módulo de hub do IoT Edge.

## <a name="diagnosing-failures"></a>Diagnosticar falhas

Nesta secção, vamos compartilhar algumas técnicas para entender o que deu errado com um módulo ou módulos. Muitas vezes, uma falha em primeiro lugar pode ser identificada de estado no Visual Studio Code.

### <a name="identify-failed-modules"></a>Identificar módulos com falhas

* **Código do Visual Studio:** Ver o painel de dispositivos do IoT Hub do Azure. Se a maioria dos módulos estão num Estado de execução, mas um está parado, necessárias para investigar esse módulo parado ainda mais. Se todos os módulos estão no estado parado por um longo período de tempo, tal poderá indicar falha também.

* **Portal do Azure:** Ao navegar para o seu hub IoT no portal e, em seguida, localizar a página de detalhes do dispositivo (no IoT Edge, faça uma busca no seu dispositivo) pode achar que um módulo comunicou um erro ou se nunca tiver comunicado qualquer nada para o hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnóstico do dispositivo

Ao iniciar sessão no dispositivo IoT Edge, pode obter acesso a uma boa dose de informações sobre o estado dos seus módulos. O principal mecanismo que usamos se os comandos de Docker que vamos examinar os contentores e imagens no dispositivo.

1. Liste todos os contentores em execução. Podemos esperar um contentor para cada módulo com um nome que corresponde ao módulo. Além disso, este comando lista na imagem exata para o contentor, incluindo a versão, para que pode combinar com suas expectativas. Também pode listar as imagens ao substituir "imagem" para "contentor" no comando.

   ```bash
   sudo docker container ls
   ```

2. Obter os registos para um contentor. Este comando produz tudo o que foi escrito para StdErr e StdOut no contentor. Este comando funciona para contêineres que tenham iniciado e, em seguida, morreu por algum motivo. Também é útil para entender o que tem sido acontecer com os contentores edgeAgent ou edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspecione um contentor. Este comando fornece uma tonelada de informações sobre a imagem. Os dados podem ser filtrados consoante o que está procurando. Por exemplo, se quiser ver se une no avroFileWriter está corretos pode utilizar o comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Ligar a um contentor em execução. Este comando pode ser útil se quiser examinar o contêiner durante a execução:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criamos uma solução de IoT Edge no Visual Studio Code com módulos de três, um classificador, um router e um escritor/carregador do ficheiro. Vamos configurar as rotas para permitir que os módulos para comunicarem entre si no dispositivo edge, alterou a configuração do dispositivo de limite e atualizado Dockerfiles instalar dependências e adicionar o enlace monta a contentores dos módulos. Em seguida, vamos atualizar a configuração do IoT Hub para encaminhar as nossas mensagens com base no tipo e lidar com carregamentos de ficheiros. Com tudo instalado, implementado os módulos no dispositivo IoT Edge e certificar-se de que os módulos estavam sendo executados corretamente.

Obter mais informações podem ser encontradas nas seguintes páginas:

* [Saiba como implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md)
* [Sintaxe de consulta de encaminhamento de mensagens de IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Roteamento de mensagens do IoT Hub: agora com o encaminhamento no corpo da mensagem](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Carregar ficheiros com o Hub IoT](../iot-hub/iot-hub-devguide-file-upload.md)
* [Carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Avance para o artigo seguinte para começar a enviar dados e ver a sua solução em ação.

> [!div class="nextstepaction"]
> [Enviar dados através do gateway transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)
