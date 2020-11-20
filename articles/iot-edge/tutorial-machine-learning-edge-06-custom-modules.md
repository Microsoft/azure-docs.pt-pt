---
title: 'Tutorial: Criar e implementar módulos personalizados - Machine Learning on Azure IoT Edge'
description: Este tutorial mostra como criar e implantar módulos IoT Edge que processam dados de dispositivos de folha através de um modelo de machine learning e, em seguida, enviam as insights para o IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 199da0586a061bccdf8a6ff8a1f53df2f703512f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959446"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutorial: Criar e implementar módulos IoT Edge personalizados

Neste artigo, criamos três módulos IoT Edge que recebem mensagens de dispositivos IoT de folhas, executam os dados através do seu modelo de aprendizagem automática e, em seguida, encaminhamos insights para o IoT Hub.

O hub IoT Edge facilita a comunicação do módulo para o módulo. A utilização do hub IoT Edge como corretor de mensagens mantém os módulos independentes uns dos outros. Os módulos apenas precisam de especificar as entradas nas quais aceitam mensagens e as saídas às quais escrevem mensagens.

Queremos que o dispositivo IoT Edge realize quatro coisas para nós:

* Receba dados dos dispositivos de folha.
* Prever a vida útil restante (RUL) para o dispositivo que enviou os dados.
* Envie uma mensagem com a RUL para o dispositivo para o IoT Hub. Esta função só pode ser modificada para enviar dados se o RUL descer abaixo de um nível especificado.
* Guarde os dados do dispositivo de folha para um ficheiro local no dispositivo IoT Edge. Este ficheiro de dados é periodicamente enviado para o IoT Hub para aperfeiçoar o treino do modelo de machine learning. A utilização do upload de ficheiros em vez de um streaming de mensagens constantes é mais rentável.

Para realizar estas tarefas, utilizamos três módulos personalizados:

* **Classificador RUL:** O módulo turboFanRulClassifier que criamos no [Comboio e implantamos um modelo Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) é um módulo de aprendizagem automática padrão, que expõe uma entrada chamada "amlInput" e uma saída chamada "amlOutput". O "amlInput" espera que a sua entrada se pareça exatamente com a entrada que enviámos para o serviço web baseado em ACI. Da mesma forma, "amlOutput" devolve os mesmos dados que o serviço web.

* **Escritor avro:** Este módulo recebe mensagens na entrada "avroModuleInput" e persiste a mensagem no formato Avro para o disco para posterior carregamento para IoT Hub.

* **Módulo router:** O módulo router recebe mensagens de dispositivos de folha a jusante, em seguida, formatos e envia as mensagens para o classificador. Em seguida, o módulo recebe as mensagens do classificador e encaminha a mensagem para o módulo de escritor Avro. Finalmente, o módulo envia apenas a previsão RUL para o IoT Hub.

  * Entradas:
    * **dispositivoInput**: recebe mensagens de dispositivos de folha
    * **rulInput:** recebe mensagens do "amlOutput"

  * Saídas:
    * **classificar:** envia mensagens para "amlInput"
    * **writeAvro:** envia mensagens para "avroModuleInput"
    * **toIotHub:** envia mensagens para $upstream, que transmite as mensagens para o IoT Hub conectado

O diagrama a seguir mostra os módulos, entradas, saídas e as rotas IoT Edge Hub para a solução completa:

![Diagrama de arquitetura IoT Edge de três módulos](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Os passos deste artigo são normalmente realizados por um desenvolvedor de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se já chegou a este artigo diretamente, visite o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="create-a-new-iot-edge-solution"></a>Criar uma nova solução IoT Edge

Durante a execução do segundo dos nossos dois Cadernos Azure, criámos e publicámos uma imagem de contentor contendo o nosso modelo RUL. A Azure Machine Learning, como parte do processo de criação de imagem, embalou este modelo para que a imagem seja desdobrável como um módulo Azure IoT Edge.

Neste passo, vamos criar uma solução Azure IoT Edge utilizando o módulo "Azure Machine Learning" e apontar o módulo para a imagem que publicámos usando os Cadernos Azure.

1. Abra uma sessão de desktop remota para o seu VM de desenvolvimento.

1. Abertura da pasta **C: \\ fonte \\ IoTEdgeAndMlSample** em Código de Estúdio Visual.

1. Clique à direita no painel do explorador (no espaço em branco) e selecione **New IoT Edge Solution**.

    ![Criar nova solução IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Aceite o nome de solução padrão **EdgeSolution**.

1. Escolha **Azure Machine Learning** como modelo do módulo.

1. Nomeie o módulo **turbofanRulClassifier**.

1. Escolha o seu espaço de trabalho de aprendizagem automática. Este espaço de trabalho é o espaço de trabalho **turboFanDemo** que criou em [Tutorial: Treinar e implementar um modelo de Aprendizagem automática Azure](tutorial-machine-learning-edge-04-train-model.md)

1. Selecione a imagem que criou durante a execução do Caderno Azure.

1. Veja a solução e note os ficheiros que foram criados:

   * **deployment.template.js:** Este ficheiro contém a definição de cada um dos módulos na solução. Há três secções a que deve estar atento neste ficheiro:

     * **Credenciais de registo:** Define o conjunto de registos de contentores personalizados que está a utilizar na sua solução. Neste momento, deve conter o registo do seu espaço de trabalho de aprendizagem automática, que é onde a sua imagem de Aprendizagem automática Azure foi armazenada. Pode ter vários registos de contentores, mas para simplificar, usaremos este registo para todos os módulos.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Módulos:** Esta secção contém o conjunto de módulos definidos pelo utilizador que acompanham esta solução. A definição do módulo turbofanRulClassifier aponta para a imagem no registo do seu contentor. À medida que adicionamos mais módulos à solução, eles vão aparecer nesta secção.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Rotas:** vamos trabalhar bastante com rotas neste tutorial. As rotas definem como os módulos comunicam entre si. A rota existente definida pelo modelo não corresponde ao encaminhamento que precisamos. Apague a `turbofanRulClassifierToIoTHub` rota.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.js:** este ficheiro é a versão depurrão de deployment.template.js. Normalmente, devemos manter este ficheiro sincronizado com o conteúdo do deployment.template.jsem arquivo, mas fazê-lo não é necessário para este tutorial.

   * **.env:** este ficheiro é onde deve fornecer o nome de utilizador e a palavra-passe para aceder ao seu registo.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Clique à direita no deployment.template.jsno ficheiro no explorador visual Studio Code e selecione **Build IoT Edge Solution**.

1. Note que este comando cria uma pasta config com um deployment.amd64.jsno ficheiro. Este ficheiro é o modelo de implementação de betão para a solução.

## <a name="add-router-module"></a>Adicionar módulo Router

Em seguida, adicionamos o módulo Router à nossa solução. O módulo Router lida com várias responsabilidades pela nossa solução:

* **Receber mensagens de dispositivos de folha:** à medida que as mensagens chegam ao dispositivo IoT Edge a partir de dispositivos a jusante, o módulo Router recebe a mensagem e começa a orquestrar o encaminhamento da mensagem.
* **Enviar mensagens para o módulo classificador RUL:** quando uma nova mensagem é recebida de um dispositivo a jusante, o módulo Router transforma a mensagem no formato que o Classificador RUL espera. O Router envia a mensagem ao Classificador RUL para uma previsão RUL. Uma vez que o classificador tenha feito uma previsão, envia a mensagem de volta para o módulo Router.
* **Enviar mensagens RUL para IoT Hub:** quando o Router recebe mensagens do classificador, transforma a mensagem para conter apenas as informações essenciais, o ID do dispositivo e a RUL, e envia a mensagem abreviada para o hub IoT. Um novo refinamento, que não fizemos aqui, enviaria mensagens para o IoT Hub apenas quando a previsão RUL cair abaixo de um limiar (por exemplo, quando o RUL é inferior a 100 ciclos). A filtragem desta forma reduziria o volume de mensagens e reduziria o custo do hub IoT.
* **Enviar mensagem para o módulo Avro Writer:** para preservar todos os dados enviados pelo dispositivo a jusante, o módulo Router envia toda a mensagem recebida do classificador para o módulo Avro Writer, que irá persistir e carregar os dados usando o upload de ficheiros IoT Hub.

O módulo Router é uma peça importante da solução que garante que as mensagens são processadas na ordem correta.

### <a name="create-the-module-and-copy-files"></a>Criar o módulo e copiar ficheiros

1. Clique à direita na pasta de módulos no Código do Estúdio Visual e escolha **adicionar o Módulo de Borda IoT**.

1. Escolha **o módulo C#** para o modelo do módulo.

1. Nomeie o módulo **turbofanRouter**.

1. Quando solicitado para o seu Repositório de Imagem Docker, utilize o registo do espaço de trabalho de aprendizagem automática (pode encontrar o registo no nó de registos do seu *deployment.template.jsno* ficheiro). Este valor é o endereço totalmente qualificado para o registo, como **\<your registry\> .azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Neste artigo, utilizamos o Registo de Contentores Azure que foi criado pelo espaço de trabalho Azure Machine Learning. Isto é puramente por conveniência. Podíamos ter criado um novo registo de contentores e publicado lá os nossos módulos.

1. No Terminal utilizando uma concha de pedido de comando, copie os ficheiros do módulo de amostra para a solução.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Aceite o pedido para substituir o ficheiro program.cs.

### <a name="build-router-module"></a>Construa módulo de router

1. No Código do Estúdio Visual, selecione **Terminal**  >  **Configure Default Build Task**.

1. Selecione **Criar tasks.jsno ficheiro a partir do modelo**.

1. **Selecione .NET Core**.

1. Substitua o conteúdo da tasks.jspelo seguinte código.

    ```json
    {
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

1. Salve e feche tasks.js.

1. Executar a construção com `Ctrl + Shift + B` ou a Tarefa de Construção de **Terminal**  >  **Execução** terminal .

### <a name="set-up-module-routes"></a>Configurar rotas de módulos

Como mencionado acima, o tempo de execução IoT Edge utiliza rotas configuradas no *deployment.template.jsem* ficheiro para gerir a comunicação entre módulos vagamente acopdos. Nesta secção, perfuramos como configurar as rotas para o módulo turbofanRouter. Cobrimos primeiro as rotas de entrada e depois avançaremos para as saídas.

#### <a name="inputs"></a>Entradas

1. No método Init de Program.cs registamos duas chamadas para o módulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. A primeira chamada ouve mensagens enviadas para o dispositivoA pia **de identificação.** Do diagrama acima, vemos que queremos encaminhar mensagens de qualquer dispositivo de folha para esta entrada. No *deployment.template.jsno* ficheiro, adicione uma rota que indique ao hub de borda para encaminhar qualquer mensagem recebida pelo dispositivo IoT Edge que não tenha sido enviada por um módulo IoT Edge na entrada chamada "deviceInput" no módulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Em seguida, adicione uma rota para mensagens do módulo RulClassifier no módulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Saídas

Adicione quatro rotas adicionais ao parâmetro de rota $edgeHub, para lidar com as saídas do módulo Router.

1. Program.cs define o método SendMessageToClassifier(), que utiliza o cliente do módulo para enviar uma mensagem ao classificador RUL utilizando a rota:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() utiliza o cliente do módulo para enviar apenas os dados RUL do dispositivo para o IoT Hub através da rota:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() utiliza o cliente do módulo para enviar a mensagem com os dados RUL adicionados ao módulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() envia mensagens falhadas a montante do Hub IoT, onde podem ser encaminhadas para mais tarde.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Com todas as rotas tomadas em conjunto o seu nó "$edgeHub" deve parecer o seguinte JSON:

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
  > A adição do módulo turbofanRouter criou a seguinte rota adicional: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream` . Remova esta rota, deixando apenas as rotas listadas acima no seu deployment.template.jsem arquivo.

## <a name="add-avro-writer-module"></a>Adicionar módulo de escritor Avro

O módulo Avro Writer tem duas responsabilidades na nossa solução, para armazenar mensagens e carregar ficheiros.

* **Armazenar mensagens**: quando o módulo Avro Writer recebe uma mensagem, escreve a mensagem para o sistema de ficheiros local em formato Avro. Utilizamos um suporte de encaixe, que monta um diretório (neste caso /dados/avrofiles) num caminho no recipiente do módulo. Este suporte permite que o módulo escreva para um caminho local (/avrofiles) e tenha esses ficheiros acessíveis diretamente a partir do dispositivo IoT Edge.

* **Upload de ficheiros**: o módulo Avro Writer utiliza a funcionalidade de upload de ficheiros Azure IoT Hub para enviar ficheiros para uma conta de armazenamento Azure. Uma vez que um ficheiro é carregado com sucesso, o módulo elimina o ficheiro do disco

### <a name="create-module-and-copy-files"></a>Criar módulos e ficheiros de cópia

1. No Código do Estúdio Visual, selecione **'Ver**  >  **Paleta de Comando'** e, em seguida, procure e selecione **Python: Selecione Interpreter**.

1. Selecione a versão 3.7 ou mais tarde da Pitão instalada.

1. Clique à direita na pasta de módulos no Código do Estúdio Visual e escolha **adicionar o Módulo de Borda IoT**.

1. Escolha **Python Module** (Módulo de Python).

1. Nomeie o `avroFileWriter` módulo.

1. Quando solicitado para o seu Repositório de Imagem Docker, utilize o mesmo registo que utilizou ao adicionar o módulo Router.

1. Copie os ficheiros do módulo de amostra para a solução.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Aceite o excesso de main.py.

1. Note-se que filemanager.py e schema.py foram adicionados à solução e main.py foi atualizado.

> [!NOTE]
> Quando abrir um ficheiro Python poderá ser solicitado a instalar o pylint. Não é necessário instalar o linter para completar este tutorial.

### <a name="bind-mount-for-data-files"></a>Encaixe o conjunto para ficheiros de dados

Como mencionado anteriormente, o módulo de escritor baseia-se na presença de um suporte de ligação para escrever ficheiros Avro para o sistema de ficheiros do dispositivo.

#### <a name="add-directory-to-device"></a>Adicionar diretório ao dispositivo

1. No portal Azure, inicie o seu VM do dispositivo IoT Edge se não estiver a funcionar. Ligue-o usando SSH. A ligação requer o nome DNS que pode copiar a partir da página geral para o VM no portal Azure.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. Depois de iniciar sessão, crie o diretório que irá conter as mensagens do dispositivo de folhas guardadas.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Atualizar permissões de diretório para torná-lo escrito pelo recipiente.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Validar o diretório agora tem (w) permissão de escrita para utilizador, grupo e proprietário.

   ```bash
   ls -la /data
   ```

   ![Permissões de diretório para avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Adicionar diretório ao módulo

Para adicionar o diretório ao recipiente do módulo, modificaremos os Ficheiros Docker associados ao módulo AvroFileWriter. Existem três Dockerfiles associados ao módulo: Dockerfile.amd64, Dockerfile.amd64.debug e Dockerfile.arm32v7. Estes ficheiros devem ser mantidos em sincronização no caso de pretendermos depurar ou implantar num dispositivo arm32. Para este artigo, concentre-se apenas no Dockerfile.amd64.

1. No seu desenvolvimento VM, abra o ficheiro **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64.**

1. Modificar o ficheiro de modo a que se pareça com o seguinte exemplo:

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

   Os `mkdir` `chown` comandos instruem o processo de construção do Docker para criar um diretório de alto nível chamado /avrofiles na imagem e, em seguida, fazer do módulo o proprietário desse diretório. É importante que estes comandos sejam inseridos após a adição do utilizador do módulo à imagem com o `useradd` comando e antes que o contexto mude para o módulouser (utilizador do módulo).

1. Se necessário, esconda as alterações correspondentes a Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Adicione a configuração de ligação ao avroFileWriter

O passo final da criação da ligação é atualizar o deployment.template.jsem ficheiros (e deployment.debug.template.js) com a informação de ligação.

1. Abra deployment.template.js.

2. Modifique a definição do módulo para avroFileWriter adicionando o `Binds` parâmetro que aponta o diretório /avrofiles do contentor para o diretório local no dispositivo de borda. A definição do módulo deve coincidir com este exemplo:

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

### <a name="bind-mount-for-access-to-configyaml"></a>Montagem de encaixe para acesso a config.yaml

Precisamos adicionar mais uma ligação para o módulo de escritor. Esta ligação dá ao módulo acesso para ler a cadeia de ligação a partir do ficheiro /etc/iotedge/config.yaml no dispositivo IoT Edge. Precisamos da cadeia de ligação para criar um IoTHubClient para que possamos chamar o método de \_ assínc blob de upload \_ para enviar ficheiros para o hub IoT. Os passos para a adição desta ligação são semelhantes aos da secção anterior.

#### <a name="update-directory-permission"></a>Atualizar a permissão do diretório

1. Ligue-se ao seu dispositivo IoT Edge utilizando O SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Adicione permissão de leitura ao ficheiro config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Validar as permissões estão corretamente definidas.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Certifique-se de que as permissões para config.yaml são **-r-r-r-r-r--**.

#### <a name="add-directory-to-module"></a>Adicionar diretório ao módulo

1. Na sua máquina de desenvolvimento, abra o ficheiro **Dockerfile.amd64.**

1. Adicione um conjunto adicional `mkdir` de e `chown` comandos ao ficheiro de modo que se pareça com:

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

1. Escve as alterações correspondentes a Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

1. Abra a **deployment.template.jsarquivada.**

1. Modifique a definição do módulo para avroFileWriter adicionando uma segunda linha ao `Binds` parâmetro que aponta o diretório do contentor (/app/iotconfig) ao diretório local no dispositivo (/etc/iotedge).

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

1. Esmudamos as alterações correspondentes ao deployment.debug.template.js.

## <a name="install-dependencies"></a>Instalar dependências

O módulo de escritor tem uma dependência de duas bibliotecas Python, fastavro e PyYAML. Precisamos instalar as dependências da nossa máquina de desenvolvimento e instruir o processo de construção do Docker para instalá-los na imagem do nosso módulo.

### <a name="pyyaml"></a>PyyAML

1. Na sua máquina de desenvolvimento, abra o `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` ficheiro e adicione "pyaml" numa nova linha no ficheiro.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Abra o ficheiro **Dockerfile.amd64** e adicione um `pip install` comando para atualizar os blocos de configuração.

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

1. A um pedido de comando, instale o pyaml na sua máquina de desenvolvimento.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Em requirements.txt, adicione fastavro após pyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Instale fastavro na sua máquina de desenvolvimento.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurar o Hub IoT

Ao introduzir o dispositivo e módulos IoT Edge no sistema, mudamos as nossas expectativas sobre que dados serão enviados para o hub e para que finalidade. Temos de reconfigurar o encaminhamento no centro para lidar com a nossa nova realidade.

> [!NOTE]
> Reconfiguramos o hub antes de implantar os módulos porque algumas das definições do hub, especificamente o upload de ficheiros, precisam de ser corretamente configuradas para que o módulo avroFileWriter seja executado corretamente

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurar rota para mensagens RUL no IoT Hub

Com o router e o classificador no lugar, esperamos receber mensagens regulares contendo apenas o ID do dispositivo e a previsão RUL para o dispositivo. Queremos encaminhar os dados rul para o seu próprio local de armazenamento, onde podemos monitorizar o estado dos dispositivos, construir relatórios e alertas de incêndio, se necessário. Ao mesmo tempo, queremos que quaisquer dados do dispositivo que ainda sejam enviados diretamente por um dispositivo de folha que ainda não tenha sido ligado ao nosso dispositivo IoT Edge continuem a encaminhar-se para o local de armazenamento atual.

#### <a name="create-a-rul-message-route"></a>Criar uma rota de mensagem RUL

1. No portal Azure, navegue para o seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Mensagens,** selecione **Message roting**.

1. No **separador Rotas,** selecione **Adicionar**.

1. Nomeie a rota **RulMessageRoute**.

1. **Selecione Adicionar ponto final** à direita do seletor **Endpoint** e escolha **o Armazenamento**.

1. Na página **De adicionar um ponto final de armazenamento,** nomeie o ponto final **ruldata**.

1. **Selecione Escolha um recipiente**.

1. Na página **de contas de Armazenamento,** encontre a conta de armazenamento que está a usar ao longo deste tutorial, que é nomeado como **iotedgeandml \<unique suffix\>**.

1. Selecione o recipiente **ruldata** e clique **em Selecionar**.

1. De volta à página **de ponto final de armazenamento,** selecione **Criar** para criar o ponto final de armazenamento.

1. De volta à página **De rota Adicionar,** para a **consulta de encaminhamento,** substitua-a `true` pela seguinte consulta:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Expanda a secção **de Teste** e, em seguida, a secção do corpo **da mensagem.** Substitua o corpo da mensagem por este exemplo das nossas mensagens esperadas:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Selecione **rota de teste**. Se o teste for bem sucedido, vê "A mensagem corresponde à consulta."

1. Clique em **Guardar**.

#### <a name="update-turbofandevicedatatostorage-route"></a>Atualizar rota turbofanDeviceDataToStorage

Não queremos encaminhar os novos dados de previsão para o nosso antigo local de armazenamento, por isso atualize a rota para o impedir.

1. Na página de **encaminhamento** de mensagens IoT Hub, selecione o **separador Rotas.**

1. Selecione **turbofanDeviceDataToStorage,** ou qualquer nome que tenha dado à sua rota inicial de dados do dispositivo.

1. Atualizar a consulta de encaminhamento para

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Expanda a secção **de Teste** e, em seguida, a secção do corpo **da mensagem.** Substitua a mensagem por este exemplo das nossas mensagens esperadas:

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

1. Selecione **rota de teste**. Se o teste for bem sucedido, vê "A mensagem corresponde à consulta."

1. Selecione **Guardar**.

### <a name="configure-file-upload"></a>Configurar o carregamento de ficheiros

Configure a funcionalidade de upload de ficheiros IoT Hub para permitir que o módulo de autor de ficheiros carrequite carre fique com ficheiros para armazenamento.

1. A partir do menu do painel esquerdo no seu IoT Hub, em **Mensagens,** escolha **o upload de ficheiros**.

1. Selecione **O recipiente de armazenamento Azure**.

1. Selecione a sua conta de armazenamento na lista.

1. Selecione o recipiente que começa com **a loja azureml blobstore** anexado com um guia e clique em **Select**.

1. Selecione **Guardar**. O portal notifica-o quando a poupança estiver completa.

> [!Note]
> Não estamos a ligar a notificação de upload para este tutorial, mas ver [Receber uma notificação de upload de ficheiros](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) para mais detalhes sobre como lidar com a notificação de upload de ficheiros.

## <a name="build-publish-and-deploy-modules"></a>Construir, publicar e implementar módulos

Agora que fizemos as alterações de configuração, estamos prontos para construir as imagens e publicá-las no nosso registo de contentores Azure. O processo de construção utiliza o deployment.template.jsem ficheiro para determinar quais os módulos que precisam de ser construídos. As definições de cada módulo, incluindo a versão, encontram-se no module.jsno ficheiro na pasta do módulo. O processo de construção executa primeiro um Docker construir sobre os Dockerfiles correspondendo à configuração atual encontrada no module.jsno ficheiro para criar uma imagem. Em seguida, publica a imagem no registo a partir do module.jsem ficheiro com uma etiqueta de versão correspondente à do module.jsem ficheiro. Finalmente, produz um manifesto de implementação específico de configuração (por exemplo, deployment.amd64.jsligado), que iremos implantar no dispositivo IoT Edge. O dispositivo IoT Edge lê as informações do manifesto de implantação, e com base nas instruções irá descarregar os módulos, configurar as rotas e definir quaisquer propriedades desejadas. Este método de implantação tem dois efeitos colaterais que deve estar ciente:

* **Atraso de implementação:** uma vez que o tempo de funcionamento do IoT Edge deve reconhecer a alteração das propriedades desejadas antes de começar a reconfigurar, pode demorar algum tempo depois de implantar os seus módulos até que o tempo de execução os apanhe e comece a atualizar o dispositivo IoT Edge.

* **As versões** do módulo importam: se publicar uma nova versão do contentor de um módulo para o registo do seu contentor utilizando as mesmas etiquetas de versão que o módulo anterior, o tempo de funcionaamento não descarregará a nova versão do módulo. Faz uma comparação com a etiqueta de versão da imagem local e a imagem desejada a partir do manifesto de implantação. Se estas versões corresponderem, o tempo de execução não interajo. Por isso, é importante incrementar a versão do seu módulo sempre que pretende implementar novas alterações. Incremente a versão alterando a propriedade da **versão** sob **a** propriedade tag no module.jsno ficheiro para o módulo que está a mudar. Em seguida, construa e publique o módulo.

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

1. No seu desenvolvimento VM, inicie o Docker se não estiver a funcionar.

1. No Código do Estúdio Visual, inicie um novo terminal com uma solicitação de comando e faça login no seu registo de contentores Azure (ACR).

  Pode encontrar os valores necessários do nome de utilizador, palavra-passe e servidor de login no portal Azure. O nome do registo do contentor tem o formato "turbofandemo". \<unique id\> A partir do menu do painel esquerdo, em **Definições,** selecione **teclas de acesso** para as visualizar.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. No Código do Estúdio Visual, clique deployment.template.jsà direita e escolha **a Solução De IoT Edge**.

### <a name="view-modules-in-the-registry"></a>Ver módulos no registo

Assim que a construção estiver concluída com sucesso, poderemos usar o portal Azure para rever os nossos módulos publicados.

1. Abra o Registo do Contentor Azure para este tutorial. O nome do registo do contentor tem o formato "turbofandemo". \<unique id\> 

1. A partir do menu do painel esquerdo, em **Serviços,** **selecione Repositórios**.

1. Note que ambos os módulos que criou, **avrofilewriter** e **turbofanrouter,** aparecem como repositórios.

1. Selecione **turbofanrouter** e note que publicou uma imagem marcada como 0.0.1-amd64.

   ![Ver primeira versão marcada de turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Implementar módulos para o dispositivo IoT Edge

Construímos e configuramos os módulos na nossa solução, agora vamos implantar os módulos para o dispositivo IoT Edge.

1. No Código do Estúdio Visual, clique no **deployment.amd64.jsno** ficheiro na pasta config.

1. Escolha **criar a implementação para um único dispositivo**.

1. Escolha o seu dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

1. Refresque o painel de dispositivos Azure IoT Hub no explorador visual Studio Code. Deve ver se os três novos módulos estão implantados, mas ainda não estão a funcionar.

1. Refresque novamente após alguns minutos e verá os módulos em funcionamento.

   ![Ver módulos de execução no Código do Estúdio Visual](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Pode levar vários minutos para que os módulos comecem e se instalem num estado de funcionamento constante. Durante esse tempo, poderá ver os módulos a iniciar e a parar enquanto tentam estabelecer uma ligação com o módulo hub IoT Edge.

## <a name="diagnosing-failures"></a>Falhas de diagnóstico

Nesta secção, partilhamos algumas técnicas para entender o que correu mal com um módulo ou módulos. Muitas vezes, uma falha pode ser detetada pela primeira vez a partir do estado no Código do Estúdio Visual.

### <a name="identify-failed-modules"></a>Identificar módulos falhados

* **Código do Estúdio Visual:** Veja o painel de dispositivos Azure IoT Hub. Se a maioria dos módulos estiver em funcionamento, mas um está parado, você precisa investigar que parou ainda mais o módulo. Se todos os módulos estiverem em estado de paragem por um longo período de tempo, também pode indicar falha.

* **Portal Azure:** Ao navegar para o seu hub IoT no portal e, em seguida, encontrar a página de detalhes do dispositivo (em IoT Edge, perfurar o seu dispositivo) poderá descobrir que um módulo reportou um erro ou nunca reportou nada ao hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnóstico do dispositivo

Ao iniciar sessão no dispositivo IoT Edge (o Linux VM no nosso caso), pode obter acesso a uma boa quantidade de informação sobre o estado dos seus módulos. O mecanismo principal que usamos são os comandos Docker que nos permitem examinar os contentores e imagens do dispositivo.

1. Faça login no seu dispositivo IoT Edge:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Lista todos os contentores de corrida. Esperamos ver um recipiente para cada módulo com um nome que corresponda ao módulo. Além disso, este comando lista a imagem exata do recipiente, incluindo a versão, para que possa corresponder às suas expectativas. Também pode listar imagens substituindo "imagem" por "recipiente" no comando.

   ```bash
   sudo docker container ls
   ```

1. Pegue os troncos para um contentor. Este comando produz o que tiver sido escrito para StdErr e StdOut no recipiente. Este comando funciona para contentores que começaram e morreram por alguma razão. Também é útil para entender o que tem acontecido com os recipientes edgeAgent ou edgeHub.

   ```bash
   sudo docker container logs <container id>
   ```

1. Inspecione um recipiente. Este comando dá uma tonelada de informação sobre a imagem. Os dados podem ser filtrados dependendo do que procura. Como exemplo, se quiser ver se as ligações no avroFileWriter estão corretas, pode utilizar o comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Ligue-se a um recipiente de funcionamento. Este comando pode ser útil se quiser examinar o recipiente enquanto este está em funcionamento:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criámos uma Solução IoT Edge em Código de Estúdio Visual com três módulos: um classificador, um router e um carregador de ficheiros. Configuramos as rotas para permitir que os módulos se comuniquem uns com os outros no dispositivo de borda. Modificámos a configuração do dispositivo de borda e atualizámos os Dockerfiles para instalar dependências e adicionar suportes de ligação aos contentores dos módulos. 

Em seguida, atualizámos a configuração do Hub IoT para encaminhar as nossas mensagens com base no tipo e para lidar com uploads de ficheiros. Com tudo no lugar, implantamos os módulos para o dispositivo IoT Edge e garantimos que os módulos estavam a funcionar corretamente.

Continue até ao próximo artigo para começar a enviar dados e ver a sua solução em ação.

> [!div class="nextstepaction"]
> [Enviar dados via gateway transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)