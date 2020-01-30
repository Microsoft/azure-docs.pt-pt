---
title: Tutorial – implantar Azure Machine Learning em um dispositivo usando Azure IoT Edge
description: Neste tutorial, você criará um modelo de Azure Machine Learning e, em seguida, o implantará como um módulo em um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773006"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implementar o Azure Machine Learning como um módulo do IoT Edge (pré-visualização)

Use Azure Notebooks para desenvolver um módulo de aprendizado de máquina e implantá-lo em um dispositivo Linux que executa o Azure IoT Edge.
Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica como implementar um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de temperatura simulada no computador. Para obter mais informações sobre Azure Machine Learning em IoT Edge, consulte [Azure Machine Learning documentação](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge estão em pré-visualização pública.

O módulo do Azure Machine Learning que cria neste tutorial lê os dados ambientais gerados pelo seu dispositivo e marca as mensagens como anómalas, ou não.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um módulo do Azure Machine Learning
> * Emitir um contentor do módulo para um registo de contentor do Azure
> * Implementar um módulo do Azure Machine Learning no seu dispositivo IoT Edge
> * Ver os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Você pode usar uma máquina virtual do Azure como um dispositivo IoT Edge seguindo as etapas no guia de início rápido para [Linux](quickstart-linux.md).
* O módulo Azure Machine Learning não dá suporte a contêineres do Windows.
* O módulo Azure Machine Learning não dá suporte a processadores ARM.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Uma área de trabalho do Azure Machine Learning. Siga as instruções em [usar o portal do Azure para](../machine-learning/tutorial-1st-experiment-sdk-setup.md) começar a usar o Azure Machine Learning para criar um e aprender a usá-lo.
  * Anote o nome do espaço de trabalho, o grupo de recursos e a ID da assinatura. Esses valores estão disponíveis na visão geral do espaço de trabalho no portal do Azure. Você usará esses valores posteriormente no tutorial para conectar um bloco de anotações do Azure aos recursos do espaço de trabalho.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implantar o módulo Azure Machine Learning

Nesta seção, você converterá arquivos de modelo de aprendizado de máquina treinados e em um contêiner Azure Machine Learning. Todos os componentes necessários para a imagem do Docker encontram-se no [repositório do Git “Toolkit de IA para o Azure IoT Edge”](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estas etapas para carregar esse repositório em Microsoft Azure Notebooks para criar o contêiner e enviá-lo por push para o registro de contêiner do Azure.

1. Navegue até seus projetos de Azure Notebooks. Você pode chegar lá de seu espaço de trabalho do Azure Machine Learning no [portal do Azure](https://portal.azure.com) ou entrando no [Microsoft Azure notebooks](https://notebooks.azure.com/home/projects) com sua conta do Azure.

2. Selecione **carregar repositório GitHub**.

3. Forneça o seguinte nome do repositório GitHub: `Azure/ai-toolkit-iot-edge`. Desmarque a caixa **público** se desejar manter seu projeto privado. Selecione **Import**.

4. Quando a importação for concluída, navegue até o novo projeto **ia-Toolkit-IOT-Edge** e abra a pasta **IOT Edge tutorial de detecção de anomalias** .

5. Verifique se o projeto está em execução. Caso contrário, selecione **executar em computação gratuita**.

   ![Executar em computação gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra o ficheiro **aml_config/config.json.**

7. Edite o arquivo de configuração para incluir os valores de sua ID de assinatura do Azure, um grupo de recursos em sua assinatura e o nome do espaço de trabalho Azure Machine Learning. Você pode obter todos esses valores da seção **visão geral** do seu espaço de trabalho no Azure.

8. Salve o arquivo de configuração.

9. Abra o arquivo **00-anomaliay-Detection-tutorial. ipynb** .

10. Quando solicitado, selecione o kernel **Python 3,6** e, em seguida, selecione **definir kernel**.

11. Edite a primeira célula no bloco de anotações de acordo com as instruções nos comentários. Use o mesmo grupo de recursos, ID da assinatura e nome do espaço de trabalho que você adicionou ao arquivo de configuração.

12. Execute as células no bloco de anotações selecionando-as e selecionando **executar** ou pressionando `Shift + Enter`.

    >[!TIP]
    >Algumas das células no notebook tutorial de detecção de anomalias são opcionais, pois elas criam recursos que alguns usuários podem ou não ter ainda, como um hub IoT. Se você colocar as informações de recurso existentes na primeira célula, receberá erros se executar as células que criam novos recursos, pois o Azure não criará recursos duplicados. Isso é bom e você pode ignorar os erros ou ignorar totalmente as seções opcionais.

Ao concluir todas as etapas no bloco de anotações, você treinou um modelo de detecção de anomalias, criou-o como uma imagem de contêiner do Docker e enviou por push essa imagem para o registro de contêiner do Azure. Em seguida, você testou o modelo e, por fim, o implantou em seu dispositivo IoT Edge.

## <a name="view-container-repository"></a>Exibir repositório de contêiner

Verifique se a imagem de contêiner foi criada e armazenada com êxito no registro de contêiner do Azure associado ao seu ambiente de aprendizado de máquina. O bloco de anotações que você usou na seção anterior forneceu automaticamente a imagem de contêiner e as credenciais de registro para seu dispositivo IoT Edge, mas você deve saber onde elas estão armazenadas para que você possa encontrar as informações posteriormente.

1. Na [portal do Azure](https://portal.azure.com), navegue até seu espaço de trabalho de serviço do Machine Learning.

2. A seção de **visão geral** lista os detalhes do espaço de trabalho, bem como seus recursos associados. Selecione o valor **do registro** , que deve ser o nome do seu espaço de trabalho seguido de números aleatórios.

3. No registro de contêiner, selecione **repositórios**. Você deve ver um repositório chamado **tempanomalydetection** que foi criado pelo notebook que você executou na seção anterior.

4. Selecione **tempanomalydetection**. Você verá que o repositório tem uma marca: **1**.

   Agora que você sabe o nome do registro, o nome do repositório e a marca, sabe o caminho completo da imagem do contêiner. Os caminhos de imagem parecem **\<registry_name\>.azurecr.io/tempanomalydetection:1.** Pode utilizar o caminho de imagem para implantar este recipiente em dispositivos IoT Edge.

5. No registro de contêiner, selecione **chaves de acesso**. Você deverá ver várias credenciais de acesso, incluindo o **servidor de logon** e o **nome**de usuário, e a **senha** para usuários administradores.

   Estas credenciais podem ser incluídas no manifesto de implantação para dar ao seu dispositivo IoT Edge acesso para retirar imagens de contentores do registo.

Agora você sabe onde a imagem de contêiner de Machine Learning está armazenada. A próxima seção percorre as etapas para exibir o contêiner em execução como um módulo em seu dispositivo IoT Edge.

## <a name="view-the-generated-data"></a>Ver os dados gerados

Pode ver as mensagens geradas por cada módulo do IoT Edge e pode ver as mensagens que são entregues ao seu hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Ver dados no seu dispositivo do IoT Edge

No seu dispositivo do IoT Edge, pode ver as mensagens a enviar de cada módulo individual.

Talvez seja necessário usar `sudo` para permissões elevadas para executar comandos `iotedge`. Sair e entrar novamente em seu dispositivo atualiza automaticamente suas permissões.

1. Veja todos os módulos no seu dispositivo do IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Veja as mensagens enviadas a partir de um dispositivo específico. Utilize o nome do módulo do resultado do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Veja os dados que chegam ao seu hub IoT

Você pode exibir as mensagens do dispositivo para a nuvem que seu hub IoT recebe usando a [extensão do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Os passos seguintes mostram como configurar o Visual Studio Code para monitorizar as mensagens do dispositivo para cloud que chegam ao seu hub IoT.

1. No Visual Studio Code, selecione **Dispositivos do Hub IoT**.

2. Selecione **...** e, em seguida, selecione **Definir Cadeia de Ligação do Hub IoT** no menu.

   ![Definir cadeia de ligação do IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que se abre na parte superior da página, introduza a cadeia de ligação de iothubowner para o seu Hub IoT. O dispositivo do IoT Edge deve aparecer na lista de Dispositivos do Hub IoT.

4. Selecione **...** novamente e selecione **Iniciar Monitoramento do ponto de extremidade do evento interno**.

5. Observe as mensagens provenientes de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomalia,** que o módulo de machinelearning fornece com um valor verdadeiro ou falso. A propriedade **AzureMLResponse** contém o valor "OK", se o modelo for executado com êxito.

   ![Azure Machine Learning resposta no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um módulo do IoT Edge com tecnologia do Azure Machine Learning. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
