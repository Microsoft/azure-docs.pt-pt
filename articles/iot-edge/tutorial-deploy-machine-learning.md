---
title: Implementar o Azure Machine Learning para um dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, criar um modelo do Azure Machine Learning, em seguida, implementá-lo como um módulo para um dispositivo do edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 985f1f73fbfc8c75df8393615fca32f5d1c08b9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078317"
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

* Pode utilizar uma máquina virtual do Azure como um dispositivo IoT Edge ao seguir os passos no guia de introdução para [Linux](quickstart-linux.md).
* O módulo do Azure Machine Learning não suporta contentores do Windows.
* O módulo do Azure Machine Learning não suporta processadores ARM.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Uma área de trabalho do Azure Machine Learning. Siga as instruções em [utilizar o portal do Azure para começar a utilizar com o Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) para criar uma e saiba como usá-lo.
   * Anote o nome de área de trabalho, o grupo de recursos e o ID de subscrição. Estes valores são todas disponíveis na visão geral do espaço de trabalho no portal do Azure. Usará esses valores mais tarde no tutorial para ligar um bloco de notas do Azure aos recursos da sua área de trabalho. 


### <a name="disable-process-identification"></a>Desativar identificação de processo

>[!NOTE]
>
> Enquanto está na pré-visualização, o Azure Machine Learning não suporta a funcionalidade de segurança de identificação do processo ativada por predefinição com o IoT Edge.
> Seguem-se os passos para desativá-la. No entanto, não é adequado para utilização em produção. Estes passos apenas são necessários nos dispositivos de Linux. 

Para desativar a identificação de processo no seu dispositivo IoT Edge, terá de fornecer o endereço IP e a porta para **workload_uri** e **management_uri** no **ligar** secção de configuração do daemon do IoT Edge.

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

Guarde e feche o ficheiro de configuração.

Criar uma variável de ambiente IOTEDGE_HOST com o endereço de management_uri (configurá-lo permanentemente, adicione-o a `/etc/environment`). Por exemplo:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

Reinicie o serviço de IoT Edge para que as alterações entrem em vigor.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implementar módulo do Azure Machine Learning

Nesta secção, converter ficheiros de modelo treinada de aprendizagem e num Azure Machine Learning contentor de serviço. Todos os componentes necessários para a imagem do Docker encontram-se no [repositório do Git “Toolkit de IA para o Azure IoT Edge”](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estes passos para carregar esse repositório para blocos de notas do Microsoft Azure para criar o contentor e envie-a para o Azure Container Registry.


1. Navegue para seus projetos de blocos de notas do Azure. Pode obter seus na área de trabalho no serviço do Azure Machine Learning a [portal do Azure](https://portal.azure.com) ou ao iniciar sessão [blocos de notas do Microsoft Azure](https://notebooks.azure.com/home/projects) com a sua conta do Azure.

2. Selecione **carregar o repositório do GitHub**.

3. Forneça o nome do repositório GitHub seguinte: `Azure/ai-toolkit-iot-edge`. Desmarque os **público** caixa se quiser manter o seu projeto privadas. Selecione **importação**. 

4. Quando a importação for concluída, navegue para a nova **IA-Kit de ferramentas-iot-edge** do projeto e abra o **tutorial de deteção de anomalias do IoT Edge** pasta. 

5. Certifique-se de que o seu projeto está em execução. Se não estiver, selecione **executado na computação gratuita**.

   ![Executar no cálculo gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra o **aml_config/config.json** ficheiro.

7. Edite o ficheiro de configuração para incluir os valores para o seu ID de subscrição do Azure, um grupo de recursos na sua subscrição e o nome de área de trabalho do serviço Azure Machine Learning. Pode obter todos os estes valores a partir da **descrição geral** secção da área de trabalho no Azure. 

8. Guarde o ficheiro de configuração.

9. Abra o **00 anomalias-deteção tutorial.ipynb** ficheiro.

10. Quando lhe for pedido, selecione o **Python 3.6** kernel, em seguida, selecione **definir Kernel**.

11. Edite a primeira célula no bloco de notas, de acordo com as instruções nos comentários. Utilize o mesmo grupo de recursos, o ID de subscrição e o nome de área de trabalho que foi adicionado ao ficheiro de configuração.

12. Execute as células no bloco de notas, selecionando-os e selecionando **execute** ou premir `Shift + Enter`.

    >[!TIP]
    >Algumas das células no notebook tutorial da deteção de anomalias são opcionais, uma vez que eles criam os recursos que alguns utilizadores podem ou não podem ter ainda, como um IoT Hub. Se colocar suas informações de recursos existente na primeira célula, receberá erros se executar as células que criam novos recursos, porque o Azure não irá criar recursos duplicados. Isso é bom, e pode ignorar os erros ou ignorar completamente essas seções opcionais. 

Depois de concluir todos os passos no bloco de notas, terá preparado um modelo de deteção de anomalias, desenvolvido como uma imagem de contentor do Docker e enviou essa imagem para o Azure Container Registry. Em seguida, testou o modelo e finalmente implementou-a para o seu dispositivo IoT Edge. 

## <a name="view-container-repository"></a>Repositório de contentor do Vista

Verifique que a imagem de contentor foi criada e armazenada no registo de contentor do Azure que está associado ao seu ambiente de aprendizagem de máquina com êxito. O bloco de notas que utilizou na secção anterior automaticamente fornecidos a imagem de contentor e as credenciais de registo para o seu dispositivo IoT Edge, mas deve saber onde estão armazenados para que pode encontrar as informações por conta própria mais tarde. 

1. Na [portal do Azure](https://portal.azure.com), navegue até à sua área de trabalho do serviço de Machine Learning. 

2. O **descrição geral** secção lista os detalhes da área de trabalho como bem seus recursos associados. Selecione o **Registro** valor, que deve ser o seu nome de área de trabalho, seguido de números aleatórios. 

3. No registo de contentor, selecione **repositórios**. Deverá ver um repositório chamado **tempanomalydetection** que foi criada com o bloco de notas que foi executada na seção anterior. 

4. Selecione **tempanomalydetection**. Perceberá que o repositório tem uma etiqueta: **1**. 

   Agora que sabe o nome do registo, o nome do repositório e a etiqueta, sabe o caminho completos da imagem do contentor. Caminhos de imagens a aparência  **\<registry_name\>.azurecr.io/tempanomalydetection:1**. Pode utilizar o caminho de imagem a implementar este contentor nos dispositivos de IoT Edge. 

5. No registo de contentor, selecione **chaves de acesso**. Deverá ver um número de credenciais de acesso, incluindo **servidor de início de sessão** e o **nome de utilizador**, e **palavra-passe** para um utilizador administrador.

   Estas credenciais podem ser incluídas no manifesto de implantação para permitir que o IoT Edge dispositivo aceda ao extrair imagens de contentores do registo. 

Agora, sabe onde está armazenada a imagem de contentor do Machine Learning. A secção seguinte descreve os passos para ver como ele está sendo executada como um módulo implementado no seu dispositivo IoT Edge. 

## <a name="view-generated-data"></a>Ver os dados gerados

Pode ver as mensagens geradas por cada módulo do IoT Edge e pode ver as mensagens que são entregues ao seu hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Ver dados no seu dispositivo do IoT Edge

No seu dispositivo do IoT Edge, pode ver as mensagens a enviar de cada módulo individual.

Poderá ter de utilizar `sudo` para permissões elevadas executar `iotedge` comandos. Terminar sessão e voltar a iniciar sessão para o seu dispositivo automaticamente atualiza as suas permissões.

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

5. Observe as mensagens provenientes de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomalias**, que o machinelearningmodule fornece com um valor de VERDADEIRO ou FALSO. A propriedade **AzureMLResponse** contém o valor "OK", se o modelo for executado com êxito.

   ![Resposta do serviço do Azure Machine Learning no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou um módulo do IoT Edge com tecnologia do Azure Machine Learning. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)

