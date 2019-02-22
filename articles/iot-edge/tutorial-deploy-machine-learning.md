---
title: Tutorial do Azure Machine Learning quando implementar um dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, vai implementar o Azure Machine Learning como um módulo num dispositivo Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/21/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0f7201ffd71a6bc3e68f83f005c693cae4fef84a
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649005"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implementar o Azure Machine Learning como um módulo do IoT Edge (pré-visualização)

Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica como implementar um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de temperatura simulada no computador. Para obter mais informações sobre o serviço Azure Machine Learning do IoT Edge, veja [documentação do Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

O módulo do Azure Machine Learning que cria neste tutorial lê os dados ambientais gerados pelo seu dispositivo e marca as mensagens como anómalas, ou não.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um módulo do Azure Machine Learning
> * Emitir um contentor do módulo para um registo de contentor do Azure
> * Implementar um módulo do Azure Machine Learning no seu dispositivo IoT Edge
> * Ver os dados gerados

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge estão em pré-visualização pública.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge.
* O módulo do Azure Machine Learning não suporta processadores ARM.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Uma área de trabalho do Azure Machine Learning. Siga as instruções em [Preparar a implementação de modelos no IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md) para criar uma.


### <a name="disable-process-identification"></a>Desativar identificação de processo

>[!NOTE]
>
> Enquanto está na pré-visualização, o Azure Machine Learning não suporta a funcionalidade de segurança de identificação do processo ativada por predefinição com o IoT Edge.
> Seguem-se os passos para desativá-la. No entanto, não é adequado para utilização em produção. Estes passos só são necessários no Linux, uma vez que será concluído durante a instalação do runtime do Windows Edge.

Para desativar a identificação de processo no dispositivo IoT Edge, terá de indicar o endereço IP e a porta para **workload_uri** e **management_uri** na secção **ligar** da configuração daemon do IoT Edge.

Primeiro obtenha o endereço IP. Introduza `ifconfig` na sua linha de comandos e copie o endereço IP da interface **docker0**.

Edite o ficheiro de configuração do IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Atualize a secção **ligar** da configuração do endereço IP. Por exemplo:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Introduza os mesmos endereços na secção **escutar** da configuração. Por exemplo:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Crie uma variável de ambiente IOTEDGE_HOST com o endereço management_uri (Para defini-lo permanentemente, adicione-o a `/etc/environment`). Por exemplo:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-machine-learning-service-container"></a>Criar o contentor de serviço do Azure Machine Learning
Nesta secção, transferir os ficheiros de modelo preparado e convertê-los num contêiner de serviço do Azure Machine Learning.

Siga as instruções na documentação [Preparar a implementação de modelos no IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md) para criar um contentor do Docker com o modelo de machine learning.  Todos os componentes necessários para a imagem do Docker encontram-se no [repositório do Git “Toolkit de IA para o Azure IoT Edge”](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a name="view-the-container-repository"></a>Ver o repositório de contentor

Verifique se a imagem de contentor foi criada e armazenada com êxito no registo de Contentor do Azure que está associado ao seu ambiente de aprendizagem automática.

1. No [portal do Azure](https://portal.azure.com), aceda a **Todos os Serviços** e Selecione **Registos do contentor**.
2. Selecione o seu registo. O nome deve começar por **mlcr** e pertence ao grupo de subscrições, à localização e à subscrição que utilizou para configurar a Gestão do Módulo.
3. Selecione **Chaves de acesso**
4. Copie o **Servidor de início de sessão**, o **Nome de utilizador** e a **Palavra-passe**.  Precisa dos mesmos para aceder ao registo a partir dos dispositivos do Edge.
5. Selecione **Repositórios**
6. Selecione **machinelearningmodule**
7. Tem agora o caminho da imagem completa do contentor. Tome nota deste caminho de imagem para a secção seguinte. Deve ter o seguinte aspeto:  **<registry_name>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

1. No [portal do Azure](https://portal.azure.com), navegue para o seu hub IoT.

1. Aceda a **IoT Edge** e selecione o seu dispositivo IoT Edge.

1. Selecione **Definir módulos**.

1. Na secção **Definições de Registo**, adicione as credenciais que copiou do seu registo de contentor do Azure.

   ![Adicionar as credenciais do registo do manifesto](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Se já implementou o módulo do tempSensor no seu dispositivo IoT Edge, este poderá ser automaticamente populado. Se ainda não estiver na sua lista de módulos, adicione-o.

    1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
    2. No campo **Nome**, introduza `tempSensor`.
    3. No campo **URI da Imagem**, introduza `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Selecione **Guardar**.

1. Adicione o módulo de aprendizagem automática criado por si.

    1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
    1. No campo **Nome**, introduza `machinelearningmodule`
    1. No campo **Imagem**, introduza o endereço da sua imagem, como, por exemplo, `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Selecione **Guardar**.

1. Novamente no passo **Adicionar módulos**, selecione **Seguinte**.

1. No passo **Especificar Rotas**, copie o JSON abaixo para a caixa de texto. A primeira rota transporta mensagens do sensor de temperatura para o módulo de aprendizagem máquina através do ponto final "amlInput", que é o ponto final que utilizam todos os módulos do Azure Machine Learning. A segunda rota transporta mensagens do módulo de aprendizagem automática para o Hub IoT. Nesta rota, o "amlOutput" é o ponto final que todos os módulos do Azure Machine Learning utilizam para dados de saída e "$upstream" indica o Hub IoT.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Selecione **Seguinte**.

1. No passo **Rever Implementação**, selecione **Submeter**.

1. Regresse à página de detalhes do dispositivo e selecione **Atualizar**.  Deverá ver o módulo **machinelearningmodule** novo em execução, juntamente com o módulo **tempSensor** e os módulos do runtime do IoT Edge.

## <a name="view-generated-data"></a>Ver os dados gerados

Pode ver as mensagens geradas por cada módulo do IoT Edge e pode ver as mensagens que são entregues ao seu hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Ver dados no seu dispositivo do IoT Edge

No seu dispositivo do IoT Edge, pode ver as mensagens a enviar de cada módulo individual.

Se executar estes comandos num dispositivo Linux, poderá ter de utilizar `sudo` para permissões elevadas.

1. Veja todos os módulos no seu dispositivo do IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Veja as mensagens enviadas a partir de um dispositivo específico. Utilize o nome do módulo do resultado do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Veja os dados que chegam ao seu hub IoT

Pode ver as mensagens de dispositivo para a nuvem que recebe o seu hub IoT utilizando o [extensão de Kit de ferramentas do Azure IoT Hub para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente conhecido como extensão do Kit de ferramentas do Azure IoT).

Os passos seguintes mostram como configurar o Visual Studio Code para monitorizar as mensagens do dispositivo para cloud que chegam ao seu hub IoT.

1. No Visual Studio Code, selecione **Dispositivos do Hub IoT**.

2. Selecione **...** e, em seguida, selecione **Definir Cadeia de Ligação do Hub IoT** no menu.

   ![Definir cadeia de ligação do IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que se abre na parte superior da página, introduza a cadeia de ligação de iothubowner para o seu Hub IoT. O dispositivo do IoT Edge deve aparecer na lista de Dispositivos do Hub IoT.

4. Selecione **...** novamente e, em seguida, selecione **Iniciar monitorização da mensagem D2C**.

5. Observe as mensagens provenientes de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade denominada **anomalia** que o machinelearningmodule apresenta com um valor true ou false. A propriedade **AzureMLResponse** contém o valor "OK", se o modelo for executado com êxito.

   ![Resposta do serviço do Azure Machine Learning no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou um módulo do IoT Edge com tecnologia do Azure Machine Learning. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)

