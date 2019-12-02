---
title: 'Tutorial: treinar & implantar modelo-Azure IoT Edge & Machine Learning'
description: 'Tutorial: criar e implantar módulos de IoT Edge que processam dados de dispositivos folha por meio de um modelo de aprendizado de máquina e, em seguida, enviam as informações ao Hub IoT.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 371c897f0b4858a642322ff35a6008edbe9a651d
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664221"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutorial: criar e implantar módulos de IoT Edge personalizados

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, criamos três módulos IoT Edge que recebem mensagens de dispositivos folha, executam os dados por meio de seu modelo de aprendizado de máquina e encaminham insights ao Hub IoT.

IoT Edge Hub facilita a comunicação entre módulos e módulos. O uso do hub de IoT Edge como um agente de mensagem mantém os módulos independentes entre si. Os módulos só precisam especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens.

Queremos que o dispositivo IoT Edge realize quatro coisas para nós:

* Receber dados dos dispositivos folha
* Prever a vida útil restante (RUL) para o dispositivo que enviou os dados
* Enviar uma mensagem com apenas o RUL para o dispositivo para o Hub IoT (essa função poderá ser modificada para somente enviar dados se o RUL cair abaixo de algum nível)
* Salve os dados do dispositivo de folha em um arquivo local no dispositivo IoT Edge. Esse arquivo de dados é carregado periodicamente no Hub IoT por meio do carregamento de arquivo para refinar o treinamento do modelo de aprendizado de máquina. O uso do carregamento de arquivos em vez do streaming de mensagens constantes é mais econômico.

Para realizar essas tarefas, usamos três módulos personalizados:

* **Classificador RUL:** O módulo turboFanRulClassifier que criamos em [treinar e implantar um modelo de Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) é um módulo de aprendizado de máquina padrão, que expõe uma entrada chamada "amlInput" e uma saída chamada "amlOutput". O "amlInput" espera que sua entrada pareça exatamente como a entrada que enviamos para o serviço Web baseado em ACI. Da mesma forma, "amlOutput" retorna os mesmos dados que o serviço Web.

* **Gravador Avro:** Esse módulo recebe mensagens na entrada "avroModuleInput" e persiste a mensagem no formato Avro para o disco para upload posterior no Hub IoT.

* **Módulo do roteador:** O módulo roteador recebe mensagens de dispositivos de folha downstream e, em seguida, formata e envia as mensagens para o classificador. Em seguida, o módulo recebe as mensagens do classificador e encaminha a mensagem para o módulo do gravador do Avro. Por fim, o módulo envia apenas a previsão RUL para o Hub IoT.

  * Informações
    * **deviceInput**: recebe mensagens de dispositivos folha
    * **rulInput:** recebe mensagens do "amlOutput"

  * Produz
    * **classificar:** envia mensagens para "amlInput"
    * **writeAvro:** envia mensagens "avroModuleInput"
    * **toIotHub:** envia mensagens para $upstream, que passa as mensagens para o Hub IOT conectado

O diagrama a seguir mostra os módulos, entradas, saídas e as rotas de Hub de IoT Edge para a solução completa:

![Diagrama de arquitetura de três módulos IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

As etapas neste artigo normalmente são executadas por um desenvolvedor de nuvem.

## <a name="create-a-new-iot-edge-solution"></a>Criar uma nova solução de IoT Edge

Durante a execução do segundo dos dois Azure Notebooks, criamos e publicamos uma imagem de contêiner contendo nosso modelo RUL. Azure Machine Learning, como parte do processo de criação de imagem, empacotado esse modelo para que a imagem seja implantável como um módulo de Azure IoT Edge. Nesta etapa, vamos criar uma solução de Azure IoT Edge usando o módulo "Azure Machine Learning" e apontar o módulo para a imagem que publicamos usando Azure Notebooks.

1. Abra uma sessão de área de trabalho remota para seu computador de desenvolvimento.

2. Abra a pasta **C:\\fonte\\IoTEdgeAndMlSample** no Visual Studio Code.

3. Clique com o botão direito do mouse no painel do Explorer (no espaço em branco) e selecione **nova solução de IOT Edge**.

    ![Criar nova solução de IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Aceite o nome da solução padrão **EdgeSolution**.

5. Escolha **Azure Machine Learning** como o modelo de módulo.

6. Nomeie o módulo **turbofanRulClassifier**.

7. Escolha seu espaço de trabalho do Machine Learning.

8. Selecione a imagem que você criou ao executar o bloco de anotações do Azure.

9. Examine a solução e observe os arquivos que foram criados:

   * **implantação. Template. JSON:** Esse arquivo contém a definição de cada um dos módulos na solução. Há três seções para prestar atenção neste arquivo:

     * **Credenciais do registro:** Define o conjunto de registros de contêiner personalizado que você está usando em sua solução. No momento, ele deve conter o registro do seu espaço de trabalho do Machine Learning, que é onde sua imagem de Azure Machine Learning foi armazenada. Você pode ter qualquer quantidade de registros de contêineres, mas para manter a simplicidade, usaremos este registro para todos os módulos

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Módulos:** Esta seção contém o conjunto de módulos definidos pelo usuário que acompanham essa solução. Você observará que esta seção contém dois módulos, atualmente: SimulatedTemperatureSensor e turbofanRulClassifier. O SimulatedTemperatureSensor foi instalado pelo modelo de Visual Studio Code, mas não precisamos dele para essa solução. Você pode excluir a definição do módulo SimulatedTemperatureSensor da seção módulos. Observe que a definição do módulo turbofanRulClassifier aponta para a imagem no registro de contêiner. À medida que adicionarmos mais módulos à solução, eles aparecerão nesta seção.

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

     * **Rotas:** vamos trabalhar com as rotas um pouco neste tutorial. As rotas definem como os módulos se comunicam entre si. As duas rotas definidas pelo modelo não correspondem ao roteamento que precisamos. A primeira rota envia todos os dados de qualquer saída do classificador para o Hub IoT ($upstream). A outra rota é para SimulatedTemperatureSensor, que acabamos de excluir. Exclua as duas rotas padrão.

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

   * **implantação. Debug. Template. JSON:** esse arquivo é a versão de depuração de Deployment. Template. JSON. Devemos espelhar todas as alterações da implantação. Template. JSON nesse arquivo.

   * **. env:** esse arquivo é onde você deve fornecer o nome de usuário e a senha para acessar o registro.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Clique com o botão direito do mouse no arquivo Deployment. Template. JSON no Visual Studio Code Explorer e selecione **criar IOT Edge solução**.

11. Observe que esse comando cria uma pasta de configuração com um arquivo Deployment. AMD64. JSON. Este arquivo é o modelo de implantação concreto da solução.

## <a name="add-router-module"></a>Adicionar módulo de roteador

Em seguida, adicionamos o módulo do roteador à nossa solução. O módulo do roteador lida com várias responsabilidades para nossa solução:

* **Receber mensagens de dispositivos folha:** à medida que as mensagens chegam ao dispositivo de IOT Edge de dispositivos downstream, o módulo do roteador recebe a mensagem e começa a orquestrar o roteamento da mensagem.
* **Enviar mensagens para o módulo classificador RUL:** quando uma nova mensagem é recebida de um dispositivo downstream, o módulo do roteador transforma a mensagem no formato esperado pelo classificador RUL. O roteador envia a mensagem para o classificador RUL para uma previsão RUL. Depois que o classificador tiver feito uma previsão, ele enviará a mensagem de volta para o módulo do roteador.
* **Enviar mensagens RUL para o Hub IOT:** quando o roteador recebe mensagens do classificador, ele transforma a mensagem para conter apenas as informações essenciais, ID do dispositivo e RUL e envia a mensagem abreviada ao Hub IOT. Um refinamento adicional, que não fizemos aqui, enviaria mensagens para o Hub IoT somente quando a previsão RUL estiver abaixo de um limite (por exemplo, quando o RUL tiver menos de 100 ciclos). A filtragem dessa maneira reduziria o volume de mensagens e reduziria o custo do Hub IoT.
* **Enviar mensagem para o módulo do gravador do Avro:** para preservar todos os dados enviados pelo dispositivo downstream, o módulo do roteador envia a mensagem inteira recebida do classificador para o módulo de gravador do Avro, que persistirá e carregará os dados usando o carregamento de arquivo do Hub IOT.

> [!NOTE]
> A descrição das responsabilidades do módulo pode fazer com que o processamento pareça sequencial, mas o fluxo é baseado em mensagem/evento. É por isso que precisamos de um módulo de orquestração como nosso módulo de roteador.

### <a name="create-module-and-copy-files"></a>Criar módulo e copiar arquivos

1. Clique com o botão direito do mouse na pasta modules em Visual Studio Code e escolha **Adicionar módulo IOT Edge**.

2. Escolha  **C# módulo**.

3. Nomeie o módulo **turbofanRouter**.

4. Quando solicitado a fornecer o repositório de imagens do Docker, use o registro do espaço de trabalho Machine Learning (você pode encontrar o registro no nó registryCredentials do arquivo. *Template. JSON de implantação* ). Esse valor é o endereço totalmente qualificado para o registro, como **\<seu registro\>. azurecr.Io/turbofanrouter**.

    > [!NOTE]
    > Neste artigo, usamos o registro de contêiner do Azure criado pelo espaço de trabalho Azure Machine Learning, que usamos para treinar e implantar nosso classificador. Isso é puramente para conveniência. Poderíamos ter criado um novo registro de contêiner e publicado nossos módulos.

5. Abra uma nova janela de terminal no Visual Studio Code (**exibir** > **terminal**) e copie os arquivos do diretório modules.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Quando for solicitado a substituir program.cs, pressione `y` e, em seguida, clique em `Enter`.

### <a name="build-router-module"></a>Compilar módulo de roteador

1. Em Visual Studio Code, selecione **Terminal** > **Configurar a tarefa de compilação padrão**.

2. Clique no **arquivo criar tarefas. JSON do modelo**.

3. Clique em **.NET Core**.

4. Quando o Tasks. JSON é aberto, substitua o conteúdo por:

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

5. Salve e feche Tasks. JSON.

6. Execute Build com `Ctrl + Shift + B` ou **Terminal** > **Executar tarefa de compilação**.

### <a name="set-up-module-routes"></a>Configurar rotas de módulo

Conforme mencionado acima, o tempo de execução de IoT Edge usa rotas configuradas no arquivo *Deployment. Template. JSON* para gerenciar a comunicação entre módulos livremente acoplados. Nesta seção, analisaremos como configurar as rotas para o módulo turbofanRouter. Abordaremos as rotas de entrada primeiro e, em seguida, moveremos as saídas.

#### <a name="inputs"></a>Entradas

1. No método init () de Program.cs, registramos dois retornos de chamada para o módulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. O primeiro retorno de chamada escuta mensagens enviadas ao coletor **deviceInput** . No diagrama acima, vemos que queremos rotear mensagens de qualquer dispositivo de folha para essa entrada. No arquivo *Deployment. Template. JSON* , adicione uma rota que informa ao Hub do Edge para rotear qualquer mensagem recebida pelo dispositivo IOT Edge que não foi enviado por um módulo IOT Edge na entrada chamada "deviceInput" no módulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Em seguida, adicione uma rota para mensagens do módulo rulClassifier no módulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Saídas

Adicione quatro rotas adicionais ao parâmetro $edgeHub Route para lidar com saídas do módulo do roteador.

1. Program.cs define o método SendMessageToClassifier (), que usa o cliente de módulo para enviar uma mensagem para o classificador RUL usando a rota:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub () usa o cliente de módulo para enviar apenas os dados de RUL do dispositivo para o Hub IoT por meio da rota:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter () usa o cliente de módulo para enviar a mensagem com os dados de RUL adicionados ao módulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage () envia mensagens com falha upstream The IoT Hub, onde elas podem ser roteadas para mais tarde.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Com todas as rotas reunidas, o nó "$edgeHub" deve ser semelhante ao seguinte JSON:

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
> Adicionar o módulo turbofanRouter criou a seguinte rota adicional: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Remova essa rota, deixando apenas as rotas listadas acima em seu arquivo Deployment. Template. JSON.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copiar rotas para implantação. Debug. Template. JSON

Como etapa final, para manter nossos arquivos sincronizados, Espelhe as alterações feitas na implantação. Template. JSON em Deployment. Debug. Template. JSON.

## <a name="add-avro-writer-module"></a>Adicionar módulo do gravador do Avro

O módulo de gravador do Avro tem duas responsabilidades em nossa solução para armazenar mensagens e carregar arquivos.

* **Armazenar mensagens**: quando o módulo do gravador do Avro recebe uma mensagem, ele grava a mensagem no sistema de arquivos local no formato Avro. Usamos uma montagem de associação, que monta um diretório (nesse caso,/data/avrofiles) em um caminho no contêiner do módulo. Essa montagem permite que o módulo grave em um caminho local (/avrofiles) e tenha esses arquivos acessíveis diretamente do dispositivo IoT Edge.

* **Carregar arquivos**: o módulo de gravador do Avro usa o recurso de carregamento de arquivo do Hub IOT do Azure para carregar arquivos em uma conta de armazenamento do Azure. Depois que um arquivo é carregado com êxito, o módulo exclui o arquivo do disco

### <a name="create-module-and-copy-files"></a>Criar módulo e copiar arquivos

1. Na paleta de comandos, pesquise e selecione **Python: selecionar intérprete**.

1. Escolha o intérprete encontrado em C:\\Python37.

1. Abra a paleta de comandos novamente e procure, em seguida, selecione **terminal: selecione shell padrão**.

1. Quando solicitado, escolha **prompt de comando**.

1. Abra um novo Shell de terminal, **terminal** > **novo terminal**.

1. Clique com o botão direito do mouse na pasta modules em Visual Studio Code e escolha **Adicionar módulo IOT Edge**.

1. Escolha **Python Module** (Módulo de Python).

1. Nomeie o módulo "avroFileWriter".

1. Quando for solicitado o repositório de imagens do Docker, use o mesmo registro que você usou ao adicionar o módulo do roteador.

1. Copie os arquivos do módulo de exemplo na solução.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Se for solicitado a substituir main.py, digite `y` e, em seguida, pressione `Enter`.

1. Observe que filemanager.py e schema.py foram adicionados à solução e o main.py foi atualizado.

> [!NOTE]
> Ao abrir um arquivo Python, você pode ser solicitado a instalar o Pylint. Não é necessário instalar o pano para concluir este tutorial.

### <a name="bind-mount-for-data-files"></a>Montagem de associação para arquivos de dados

Conforme mencionado na introdução, o módulo gravador depende da presença da montagem de ligação para gravar arquivos Avro no sistema de arquivos do dispositivo.

#### <a name="add-directory-to-device"></a>Adicionar diretório ao dispositivo

1. Conecte-se à VM do dispositivo IoT Edge usando o SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Crie o diretório que manterá as mensagens do dispositivo de folha salvas.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Atualize as permissões do diretório para torná-las graváveis pelo contêiner.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Validar o diretório agora tem permissão de gravação (w) para usuário, grupo e proprietário.

   ```bash
   ls -la /data
   ```

   ![Permissões de diretório para avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Adicionar diretório ao módulo

Para adicionar o diretório ao contêiner do módulo, modificaremos o Dockerfiles associado ao módulo avroFileWriter. Há três Dockerfiles associadas ao módulo: Dockerfile. AMD64, Dockerfile. AMD64. Debug e Dockerfile. arm32v7. Esses arquivos devem ser mantidos em sincronia, caso queiramos Depurar ou implantar em um dispositivo arm32. Neste artigo, concentre-se apenas em Dockerfile. AMD64.

1. No computador de desenvolvimento, abra o arquivo **Dockerfile. AMD64** .

2. Modifique o arquivo para que seja semelhante ao exemplo a seguir:

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

   Os comandos `mkdir` e `chown` instruem o processo de Build do Docker para criar um diretório de nível superior chamado/avrofiles na imagem e, em seguida, tornar o moduleuser o proprietário desse diretório. É importante que esses comandos sejam inseridos depois que o usuário do módulo é adicionado à imagem com o comando `useradd` e antes de o contexto alternar para o moduleuser (usuário moduleuser).

3. Faça as alterações correspondentes em Dockerfile. AMD64. Debug e Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

A etapa final da criação da ligação é atualizar os arquivos de implantação. Template. JSON (e Deployment. Debug. Template. JSON) com as informações de ligação.

1. Abra Deployment. Template. JSON.

2. Modifique a definição do módulo para avroFileWriter adicionando o parâmetro `Binds` que aponta o diretório de contêiner/avrofiles para o diretório local no dispositivo de borda. A definição do módulo deve corresponder a este exemplo:

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

3. Faça as alterações correspondentes na implantação. Debug. Template. JSON.

### <a name="bind-mount-for-access-to-configyaml"></a>Montagem de associação para acesso a config. YAML

Precisamos adicionar mais uma ligação para o módulo gravador. Essa ligação dá ao módulo o acesso para ler a cadeia de conexão do arquivo/etc/iotedge/config.YAML no dispositivo IoT Edge. Precisamos da cadeia de conexão para criar um IoTHubClient para que possamos chamar o método upload\_blob\_Async para carregar arquivos no Hub IoT. As etapas para adicionar essa associação são semelhantes àquelas na seção anterior.

#### <a name="update-directory-permission"></a>Permissão de atualizar diretório

1. Conecte-se ao dispositivo IoT Edge usando o SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Adicione permissão de leitura ao arquivo config. YAML.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Valide se as permissões estão definidas corretamente.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Verifique se as permissões para config. YAML são **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Adicionar diretório ao módulo

1. No computador de desenvolvimento, abra o arquivo **Dockerfile. AMD64** .

2. Adicione um conjunto adicional de comandos `mkdir` e `chown` ao arquivo para que seja semelhante a:

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

3. Faça as alterações correspondentes em Dockerfile. AMD64. Debug e Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

1. Abra o arquivo **Deployment. Template. JSON** .

2. Modifique a definição do módulo para avroFileWriter adicionando uma segunda linha ao parâmetro `Binds` que aponta o diretório de contêiner (/App/iotconfig) para o diretório local no dispositivo (/etc/iotedge).

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

3. Faça as alterações correspondentes na implantação. Debug. Template. JSON.

## <a name="install-dependencies"></a>Instalar dependências

O módulo gravador assume uma dependência de duas bibliotecas Python, fastavro e PyYAML. Precisamos instalar as dependências em nosso computador de desenvolvimento e instruir o processo de Build do Docker para instalá-las na imagem do nosso módulo.

### <a name="pyyaml"></a>PyYAML

1. No computador de desenvolvimento, abra o arquivo **requirements. txt** e adicione PyYAML.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Abra o arquivo **Dockerfile. AMD64** e adicione um comando `pip install` para atualizar o setuptools.

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

3. Faça as alterações correspondentes em Dockerfile. AMD64. Debug. <!--may not be necessary. Add 'if needed'?-->

4. Instale o PyYAML localmente abrindo um terminal em Visual Studio Code e digitando

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Em requirements. txt, adicione fastavro após PyYAML.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Instale o fastavro em seu computador de desenvolvimento usando o terminal Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurar o Hub IoT

Ao introduzir o dispositivo e os módulos IoT Edge ao sistema, alteramos nossas expectativas sobre quais dados serão enviados ao Hub e para qual finalidade. Precisamos reconfigurar o roteamento no Hub para lidar com nossa nova realidade.

> [!NOTE]
> Reconfiguramos o Hub antes de implantar módulos porque algumas das configurações de Hub, especificamente o carregamento de arquivo, precisam ser configuradas corretamente para que o módulo avroFileWriter seja executado corretamente

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurar rota para mensagens RUL no Hub IoT

Com o roteador e o classificador em vigor, esperamos receber mensagens regulares contendo apenas a ID do dispositivo e a previsão de RUL para o dispositivo. Queremos rotear os dados de RUL para seu próprio local de armazenamento, onde podemos monitorar o status dos dispositivos, criar relatórios e acionar alertas conforme necessário. Ao mesmo tempo, queremos qualquer dado de dispositivo que ainda esteja sendo enviado diretamente por um dispositivo de folha que ainda não foi anexado ao nosso dispositivo de IoT Edge para continuar a rotear para o local de armazenamento atual.

#### <a name="create-a-rul-message-route"></a>Criar uma rota de mensagens do RUL

1. No portal do Azure, navegue até o Hub IoT.

2. No painel de navegação esquerdo, escolha **Roteamento de mensagens**.

3. Selecione **Adicionar**.

4. Nomeie a rota **RulMessageRoute**.

5. Selecione **Adicionar** ao lado do seletor de **ponto de extremidade** e escolha **armazenamento de BLOBs**.

6. No formulário **Adicionar um ponto de extremidade de armazenamento** , nomeie o ponto de extremidade **ruldata**.

7. Selecione **escolher um contêiner**.

8. Escolha a conta de armazenamento usada em todo este tutorial, que é nomeada como **iotedgeandml\<sufixo exclusivo\>** .

9. Escolha o contêiner **ruldata** e clique em **selecionar**.

10. Clique em **criar** para criar o ponto de extremidade de armazenamento.

11. Para a **consulta de roteamento**, insira a seguinte consulta:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Expanda a seção **teste** e, em seguida, a seção **corpo da mensagem** . Substitua a mensagem por este exemplo de nossas mensagens esperadas:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selecione **testar rota**. Se o teste for bem-sucedido, você verá "a mensagem correspondeu à consulta."

14. Clique em **Guardar**.

#### <a name="update-turbofandevicetostorage-route"></a>Atualizar rota turbofanDeviceToStorage

Não queremos rotear os novos dados de previsão para o nosso local de armazenamento antigo, portanto, atualize a rota para evitá-lo.

1. Na página roteamento de **mensagens** do Hub IOT, selecione a guia **rotas** .

2. Selecione **turbofanDeviceDataToStorage**ou qualquer nome que você tenha fornecido à sua rota de dados de dispositivo inicial.

3. Atualizar a consulta de roteamento para

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Expanda a seção **teste** e, em seguida, a seção **corpo da mensagem** . Substitua a mensagem por este exemplo de nossas mensagens esperadas:

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

5. Selecione **testar rota**. Se o teste for bem-sucedido, você verá "a mensagem correspondeu à consulta."

6. Selecione **Guardar**.

### <a name="configure-file-upload"></a>Configurar carregamento de ficheiro

Configure o recurso de upload de arquivo do Hub IoT para habilitar o módulo gravador de arquivo para carregar arquivos no armazenamento.

1. No navegador esquerdo do Hub IoT, escolha upload de **arquivo**.

2. Selecione **contêiner de armazenamento do Azure**.

3. Selecione sua conta de armazenamento na lista.

4. Selecione o contêiner **uploadturbofanfiles** e clique em **selecionar**.

5. Selecione **Guardar**. O portal notifica quando o salvamento é concluído.

> [!Note]
> Não estamos ativando a notificação de carregamento para este tutorial, mas veja [receber uma notificação de upload de arquivo](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) para obter detalhes sobre como lidar com a notificação de upload de arquivo.

## <a name="build-publish-and-deploy-modules"></a>Compilar, publicar e implantar módulos

Agora que fizemos as alterações de configuração, estamos prontos para criar as imagens e publicá-las no registro de contêiner do Azure. O processo de compilação usa o arquivo Deployment. Template. JSON para determinar quais módulos precisam ser compilados. As configurações para cada módulo, incluindo a versão, são encontradas no arquivo module. JSON na pasta do módulo. O processo de compilação primeiro executa um Build do Docker no Dockerfiles correspondente à configuração atual encontrada no arquivo module. JSON para criar uma imagem. Em seguida, ele publica a imagem no registro do arquivo module. JSON com uma marca de versão correspondente à do arquivo module. JSON. Por fim, ele produz um manifesto de implantação específico da configuração (por exemplo, Deployment. AMD64. JSON), que iremos implantar no dispositivo IoT Edge. O dispositivo IoT Edge lê as informações do manifesto de implantação e, com base nas instruções, baixará os módulos, configurará as rotas e definirá as propriedades desejadas. Esse método de implantação tem dois efeitos colaterais que você deve estar ciente:

* **Atraso de implantação:** como o tempo de execução de IOT Edge deve reconhecer a alteração para suas propriedades desejadas antes de começar a reconfigurar, pode levar algum tempo depois de implantar seus módulos até que o tempo de execução os pegue e comece a atualizar o dispositivo de IOT Edge.

* **Versões de módulo importantes:** se você publicar uma nova versão do contêiner de um módulo no registro de contêiner usando as mesmas marcas de versão do módulo anterior, o tempo de execução não baixará a nova versão do módulo. Ele faz uma comparação da marca de versão da imagem local e da imagem desejada do manifesto de implantação. Se essas versões corresponderem, o tempo de execução não executará nenhuma ação. Portanto, é importante incrementar a versão do seu módulo cada vez que você desejar implantar novas alterações. Aumente a versão alterando a propriedade **version** na propriedade **tag** no arquivo module. JSON para o módulo que você está alterando. Em seguida, compile e publique o módulo.

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

### <a name="build-and-publish"></a>Compilar e publicar

1. Em Visual Studio Code em sua VM de desenvolvimento, abra uma janela Visual Studio Code terminal e faça logon no registro de contêiner.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Em Visual Studio Code, clique com o botão direito do mouse em Deployment. Template. JSON e escolha **criar e enviar por Push IOT Edge solução**.

### <a name="view-modules-in-the-registry"></a>Exibir módulos no registro

Depois que a compilação for concluída com êxito, poderáremos usar o portal do Azure para examinar nossos módulos publicados.

1. Na portal do Azure, navegue até o espaço de trabalho Azure Machine Learning e clique no hiperlink para o **registro**.

    ![Navegar até o registro do espaço de trabalho do serviço de Machine Learning](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. No navegador do lado do registro, selecione **repositórios**.

3. Observe que os dois módulos que você criou, **avrofilewriter** e **turbofanrouter**, aparecem como repositórios.

4. Selecione **turbofanrouter** e observe que você publicou uma imagem marcada como 0.0.1-AMD64.

   ![Exibir a primeira versão marcada do turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Implantar módulos no dispositivo IoT Edge

Criamos e configuramos os módulos em nossa solução. agora, implantaremos os módulos no dispositivo IoT Edge.

1. Em Visual Studio Code, clique com o botão direito do mouse no arquivo **Deployment. AMD64. JSON** na pasta config.

2. Escolha **criar implantação para um único dispositivo**.

3. Escolha o dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

4. Atualize o painel dispositivos do Hub IoT do Azure no Visual Studio Code Explorer. Você verá que os três novos módulos foram implantados, mas ainda não estão em execução.

5. Atualize novamente após alguns minutos e você verá os módulos em execução.

   ![Exibir módulos em execução no Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Pode levar vários minutos para que os módulos sejam iniciados e sejam liquidados em um estado de execução estável. Durante esse tempo, você poderá ver os módulos iniciarem e parar enquanto tentam estabelecer uma conexão com o módulo de Hub de IoT Edge.

## <a name="diagnosing-failures"></a>Diagnosticando falhas

Nesta seção, compartilhamos algumas técnicas para entender o que deu errado com um módulo ou módulos. Geralmente, uma falha pode ser comparada primeiro a partir do status no Visual Studio Code.

### <a name="identify-failed-modules"></a>Identificar módulos com falha

* **Visual Studio Code:** Examine o painel dispositivos do Hub IoT do Azure. Se a maioria dos módulos estiver em um estado de execução, mas um for interrompido, você precisará investigar mais o módulo parado. Se todos os módulos estiverem em um estado parado por um longo período de tempo, isso também poderá indicar uma falha.

* **Portal do Azure:** Navegando até o Hub IoT no portal e localizando a página de detalhes do dispositivo (em IoT Edge, aprofunde-se em seu dispositivo) você pode descobrir que um módulo relatou um erro ou nunca relatou nada para o Hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnosticando do dispositivo

Ao fazer logon no dispositivo de IoT Edge, você pode obter acesso a uma boa dose de informações sobre o status de seus módulos. O mecanismo principal que usamos são os comandos do Docker que nos permitem examinar os contêineres e as imagens no dispositivo.

1. Lista todos os contêineres em execução. Esperamos ver um contêiner para cada módulo com um nome que corresponde ao módulo. Além disso, esse comando lista a imagem exata para o contêiner, incluindo a versão, para que você possa corresponder à sua expectativa. Você também pode listar imagens substituindo "Image" por "container" no comando.

   ```bash
   sudo docker container ls
   ```

2. Obter os logs de um contêiner. Esse comando gera qualquer coisa que tenha sido gravada em StdErr e StdOut no contêiner. Esse comando funciona para contêineres que foram iniciados e, em seguida, morreu por algum motivo. Ele também é útil para entender o que está acontecendo com os contêineres edgeAgent ou edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspecione um contêiner. Esse comando fornece uma infinidade de informações sobre a imagem. Os dados podem ser filtrados dependendo do que você está procurando. Por exemplo, se você quiser ver se as associações no avroFileWriter estão corretas, use o comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Conecte-se a um contêiner em execução. Esse comando poderá ser útil se você quiser examinar o contêiner enquanto ele estiver em execução:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criamos uma solução IoT Edge em Visual Studio Code com três módulos, um classificador, um roteador e um gravador/carregador de arquivos. Configuramos as rotas para permitir que os módulos se comuniquem entre si no dispositivo de borda, modificamos a configuração do dispositivo de borda e atualizamos o Dockerfiles para instalar dependências e adicionar montagens de ligação aos contêineres dos módulos. Em seguida, atualizamos a configuração do Hub IoT para rotear nossas mensagens com base no tipo e manipular carregamentos de arquivos. Com tudo em vigor, implantamos os módulos no dispositivo IoT Edge e garantimos que os módulos estivessem sendo executados corretamente.

Mais informações podem ser encontradas nas seguintes páginas:

* [Saiba como implementar módulos e estabelecer rotas no IoT Edge](module-composition.md)
* [Sintaxe de consulta de roteamento de mensagens do Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Roteamento de mensagens do Hub IoT: agora com roteamento no corpo da mensagem](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Carregar ficheiros com o Hub IoT](../iot-hub/iot-hub-devguide-file-upload.md)
* [Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT](../iot-hub/iot-hub-python-python-file-upload.md)

Continue no próximo artigo para começar a enviar dados e ver sua solução em ação.

> [!div class="nextstepaction"]
> [Enviar dados por meio do gateway transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)
