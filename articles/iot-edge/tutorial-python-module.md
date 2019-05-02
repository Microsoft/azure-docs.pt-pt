---
title: Criar o módulo Python personalizado - Azure IoT Edge | Documentos da Microsoft
description: Este tutorial mostra como criar um módulo do IoT Edge com código Python e implementá-lo num dispositivo Edge.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 82da44409c4500ff097805efec33cec8cf6bbedd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575637"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver e implementar um módulo de Python IoT Edge para dispositivos do Linux

Utilize o Visual Studio Code para desenvolver o código C e implementá-la num dispositivo de Linux com o Azure IoT Edge. 

Pode utilizar os módulos do Azure IoT Edge para implementar código que aplica a sua lógica de negócio diretamente aos seus dispositivos IoT Edge. Este tutorial explica-lhe criar e implementar um módulo do IoT Edge que filtra dados de sensores no dispositivo IoT Edge que configurou no guia de introdução. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilizar o Visual Studio Code para criar um módulo do IoT Edge com Python.
> * Utilize o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no seu registo.
> * Implemente o módulo no seu dispositivo IoT Edge.
> * Veja os dados gerados.


O módulo do IoT Edge que criou neste tutorial filtra os dados de temperatura que são gerados pelo seu dispositivo. Envia apenas mensagens de origem caso a temperatura seja superior a um limiar especificado. Este tipo de análise no Edge é útil para reduzir a quantidade de dados que são comunicados e armazenados na cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Âmbito de solução

Este tutorial demonstra como desenvolver um módulo numa **Python** usando **Visual Studio Code**e como implementá-la para um **dispositivo Linux**. IoT Edge não suporta os módulos de Python para dispositivos Windows. 

Utilize a tabela seguinte para compreender as opções para desenvolver e implantar módulos de Python para Linux: 

| Python | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Utilizar o VS Code para módulos de Python no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 do Linux** | ![Utilizar o VS Code para módulos de Python no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve já leu o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contentores do Linux: [Desenvolver módulos do IoT Edge para dispositivos de Linux](tutorial-develop-for-linux.md). Ao concluir qualquer um desses tutoriais, deve ter os seguintes pré-requisitos no local: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* A [dispositivo de Linux com o Azure IoT Edge](quickstart-linux.md)
* Um registo de contentor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com o [ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores do Linux.

Para desenvolver um módulo do IoT Edge no Python, instale os seguintes pré-requisitos adicionais no computador de desenvolvimento: 

* [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para o Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar pacotes do Python (tipicamente incluído com a instalação Python).

>[!Note]
>Certifique-se de que a sua pasta `bin` está no seu caminho para a plataforma. Normalmente `~/.local/` para UNIX e macOS, ou `%APPDATA%\Python` no Windows.

## <a name="create-a-module-project"></a>Criar um projeto de módulo
Os passos seguintes criam um módulo do IoT Edge Python com o Visual Studio Code e as ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Utilize o pacote do Python **cookiecutter** para criar um modelo de solução do Python que pode ser criado sobre ele. 

1. No Visual Studio Code, selecione **Vista** > **Terminal** para abrir o terminal integrado do VS Code.

2. No terminal, introduza o seguinte comando para instalar (ou atualizar) **cookiecutteru**, que utiliza para criar o modelo de solução de IoT Edge:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Certifique-se o diretório onde será instalado cookiecutteru está no caminho de seu ambiente para que seja possível invocá-lo a partir de um prompt de comando. O diretório é parte da saída do script de instalação, por exemplo `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Reinicie o Visual Studio Code para recolher as alterações ao caminho. 

3. Selecione **Ver** > **Paleta de Comandos** para abrir a paleta de comandos do VS Code. 

4. Na paleta de comandos, introduza e execute o comando **Azure: Inicie sessão no** e siga as instruções para iniciar sessão na sua conta do Azure. Se já iniciou sessão, pode ignorar este passo.

5. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções e forneça as seguintes informações para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolha **Python Module** (Módulo de Python). |
   | Indicar um nome para o módulo | Nomeie o seu módulo **PythonModule**. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida do nome que indicou no último passo. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome do registo\>.azurecr.io/pythonmodule. |
 
   ![Fornecer repositório de imagens do Docker](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu repositório de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o ficheiro **.env**. 
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry. 
3. Guarde o ficheiro. env. 

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos de C para dispositivos AMD64 de Linux e ARM32v7 de Linux. Tem de selecionar qual arquitetura visa com cada solução, uma vez que o contentor é criado e executado de forma diferente para cada tipo de arquitetura. A predefinição é AMD64 de Linux. 

1. Abra a paleta de comandos e procure **Azure IoT Edge: Definir a plataforma de destino predefinido para solução de ponta**, ou selecione o ícone de atalho na barra do lado na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos a utilizar uma máquina virtual do Ubuntu como o dispositivo do IoT Edge, para que irá manter a predefinição **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

Cada modelo inclui o código de exemplo, que assume os dados simulados do sensor, do módulo **tempSensor**, e encaminha-os para o hub IoT. Nesta secção, irá adicionar o código que expande o **PythonModule** para analisar as mensagens antes de as enviar. 

1. No explorador do VS Code, abra **módulos** > **PythonModule** > **main.py**.

2. Na parte superior do ficheiro **main.py**, importe a biblioteca **json**:

    ```python
    import json
    ```

3. Adicione as variáveis **TEMPERATURE_THRESHOLD** e **TWIN_CALLBACKS** nos contadores globais. O limiar de temperatura do computador define o valor que a temperatura medida tem de exceder para os dados serem enviados para o hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Substitua a função **receive_message_callback** pelo seguinte código:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Adicione uma nova função com o nome **module_twin_callback**. Esta função é invocada quando as propriedades pretendidas forem atualizadas.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Na classe **HubManager**, adicione uma nova linha ao método **__init__** para inicializar a função **module_twin_callback** que acabou de adicionar:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Guarde o ficheiro de main.py.

8. No explorador do VS Code, abra o ficheiro **deployment.template.json** na área de trabalho da solução do IoT Edge. 

9. Adicione o módulo duplo **PythonModule** ao manifesto de implementação. Insira o seguinte conteúdo JSON na parte inferior da secção **moduleContent**, após o módulo duplo **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adicionar módulo duplo ao modelo de implementação](./media/tutorial-python-module/module-twin.png)

10. Guarde o ficheiro de deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar por push o seu módulo

Na secção anterior, criou uma solução de IoT Edge e adicionar código para o PythonModule que filtrará as mensagens onde a temperatura comunicada máquina está dentro dos limites aceitáveis. Agora, tem de criar a solução como uma imagem de contentor e enviá-la para o registo de contentor.

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

4. Clique no botão Atualizar. Deverá ver o novo **PythonModule** em execução, juntamente com o módulo **TempSensor**, bem como **$edgeAgent** e **$edgeHub**. 

## <a name="view-generated-data"></a>Ver os dados gerados

Depois de aplicar o manifesto de implementação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo recolhe as novas informações de implementação e começa a ser executado no mesmo. Quaisquer módulos em execução no dispositivo que não estão incluídos no manifesto de implementação são parados. Quaisquer módulos em falta do dispositivo são iniciados.

Pode ver o estado do seu dispositivo do IoT Edge com a secção **Dispositivos do Hub IoT do Azure** do explorador do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implementados e em execução.

1. No Explorador do Visual Studio Code, clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **iniciar mensagens D2C monitorização**.

2. Ver as mensagens que chegam ao IoT Hub. Pode demorar algum tempo para as mensagens chegam, porque o dispositivo do IoT Edge tem de receber a sua nova implementação e comece a todos os módulos. Em seguida, as alterações feitas no código PythonModule Aguarde até a temperatura de máquina atinge 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **alerta** para todas as mensagens que atingir esse limite de temperatura. 

## <a name="edit-the-module-twin"></a>Editar o módulo duplo

Usamos o duplo do módulo de PythonModule no manifesto de implantação para definir o limiar de temperatura em graus 25. Pode utilizar o módulo duplo para alterar a funcionalidade sem ter de atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução. 

2. Com o botão direito **PythonModule** e selecione **editar módulo duplo**. 

3. Encontrar **TemperatureThreshold** nas propriedades pretendidas. Altere-o para uma nova temperatura graus de 5 a 10 graus de mais do que a temperatura comunicada mais recente. 

4. Guarde o ficheiro de duplo do módulo.

5. Com o botão direito em qualquer lugar na edição de painel e selecione o módulo duplo **duplo do módulo de atualização**. 

5. Monitorize as mensagens do dispositivo para a nuvem de entrada. Deverá ver as mensagens parar até que seja atingido o limiar de temperatura de novo. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que utilizou neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma função do módulo do IoT Edge que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, pode saber mais sobre [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio Code](how-to-vs-code-develop-module.md). Pode continuar para os tutoriais seguintes para saber como Azure IoT Edge pode ajudá-lo a implementar serviços cloud do Azure para processar e analisar dados na periferia.

> [!div class="nextstepaction"]
> [As funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [serviço de visão personalizada](tutorial-deploy-custom-vision.md)