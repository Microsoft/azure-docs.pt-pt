---
title: Tutorial - Implemente machine learning azure para um dispositivo usando borda Azure IoT
description: Neste tutorial, cria-se um modelo de Aprendizagem automática Azure, e depois implanta-o como módulo para um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773006"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implementar o Azure Machine Learning como um módulo do IoT Edge (pré-visualização)

Utilize os Cadernos Azure para desenvolver um módulo de aprendizagem automática e implementá-lo num dispositivo Linux que executa o Azure IoT Edge.
Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica como implementar um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de temperatura simulada no computador. Para obter mais informações sobre o Azure Machine Learning no IoT Edge, consulte [a documentação de Aprendizagem automática de Azure.](../machine-learning/how-to-deploy-and-where.md)

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

* Pode utilizar uma máquina virtual Azure como dispositivo IoT Edge seguindo os passos no arranque rápido para [linux](quickstart-linux.md).
* O módulo de aprendizagem automática Azure não suporta recipientes Windows.
* O módulo de machine learning Azure não suporta processadores ARM.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Uma área de trabalho do Azure Machine Learning. Siga as instruções em [Utilizar o portal Azure para começar com](../machine-learning/tutorial-1st-experiment-sdk-setup.md) o Azure Machine Learning para criar um e aprender a usá-lo.
  * Tome nota do nome do espaço de trabalho, grupo de recursos e ID de subscrição. Estes valores estão todos disponíveis na visão geral do espaço de trabalho no portal Azure. Utilizará estes valores mais tarde no tutorial para ligar um caderno Azure aos seus recursos espaciais.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implementar módulo de aprendizagem automática Azure

Nesta secção, converte ficheiros de modelos de aprendizagem automática treinados e num recipiente de Aprendizagem automática Azure. Todos os componentes necessários para a imagem do Docker encontram-se no [repositório do Git “Toolkit de IA para o Azure IoT Edge”](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estes passos para enviar esse repositório para os Cadernos Microsoft Azure para criar o recipiente e empurrá-lo para o Registo de Contentores Azure.

1. Navegue para os seus projetos de Cadernos Azure. Pode chegar lá a partir do seu espaço de trabalho Azure Machine Learning no [portal Azure](https://portal.azure.com) ou ao iniciar sessão nos Notebooks do [Microsoft Azure](https://notebooks.azure.com/home/projects) com a sua conta Azure.

2. **Selecione Upload GitHub Repo**.

3. Forneça o seguinte nome de repositório GitHub: `Azure/ai-toolkit-iot-edge`. Desfaça a caixa **pública** se quiser manter o seu projeto privado. Selecione **Importar**.

4. Uma vez terminada a importação, navegue no novo projeto **ai-toolkit-iot-edge e** abra a pasta tutorial de deteção de **anomalias IoT Edge.**

5. Verifique se o seu projeto está em andamento. Caso contrário, selecione **Executar em Computação Gratuita**.

   ![Executar em computação gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra o ficheiro **aml_config/config.json.**

7. Edite o ficheiro config para incluir os valores para o seu ID de subscrição Azure, um grupo de recursos na sua subscrição, e o seu nome de espaço de trabalho Azure Machine Learning. Pode obter todos estes valores a partir da secção **de visão geral** do seu espaço de trabalho em Azure.

8. Guarde o ficheiro de config.

9. Abra o ficheiro **00-anomaly-detection-tutorial.ipynb.**

10. Quando solicitado, selecione o kernel **Python 3.6** e, em seguida, selecione **set Kernel**.

11. Editar a primeira célula no caderno de acordo com as instruções nos comentários. Utilize o mesmo grupo de recursos, ID de subscrição e nome de espaço de trabalho que adicionou ao ficheiro config.

12. Executar as células no caderno selecionando-as `Shift + Enter`e selecionando **Executar** ou premir .

    >[!TIP]
    >Algumas das células do caderno tutorial de deteção de anomalias são opcionais, porque criam recursos que alguns utilizadores podem ou não ter ainda, como um Hub IoT. Se colocar a informação de recursos existente na primeira célula, receberá erros se executar as células que criam novos recursos porque o Azure não criará recursos duplicados. Isto é bom, e pode ignorar os erros ou saltar completamente essas secções opcionais.

Ao completar todos os passos do caderno, treinou um modelo de deteção de anomalias, construiu-o como uma imagem de contentor Dodocker, e empurrou essa imagem para o Registo de Contentores Azure. Depois, testou o modelo e finalmente o colocou no seu dispositivo IoT Edge.

## <a name="view-container-repository"></a>Ver repositório de contentores

Verifique se a sua imagem de contentor foi criada com sucesso e armazenada no registo de contentores Azure associado ao seu ambiente de aprendizagem automática. O caderno que utilizou na secção anterior forneceu automaticamente a imagem do recipiente e as credenciais de registo para o seu dispositivo IoT Edge, mas deve saber onde estão armazenadas para que possa encontrar a informação mais tarde.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu espaço de trabalho de serviço de Machine Learning.

2. A secção **De visão geral** lista os detalhes do espaço de trabalho, bem como os seus recursos associados. Selecione o valor **do Registo,** que deve ser o seu nome de espaço de trabalho seguido de números aleatórios.

3. No registo do contentor, selecione **Repositórios**. Devia ver um repositório chamado **tempanomalydetection** que foi criado pelo caderno que executou na secção anterior.

4. Selecione **deteção temporária**. Deve ver que o repositório tem uma etiqueta: **1**.

   Agora que sabe o nome do registo, o nome do repositório e a etiqueta, conhece o caminho de imagem completo do contentor. Os caminhos de imagem parecem ** \<registry_name\>.azurecr.io/tempanomalydetection:1.** Pode utilizar o caminho da imagem para implementar este contentor para dispositivos IoT Edge.

5. No registo do recipiente, selecione **Teclas de acesso**. Deve ver várias credenciais de acesso, incluindo **o servidor de Login** e o Nome de **Utilizador,** e **palavra-passe** para um utilizador administrativo.

   Estas credenciais podem ser incluídas no manifesto de implementação para dar ao seu dispositivo IoT Edge acesso para solicitar imagens do contentor do registo.

Agora sabe onde a imagem do recipiente de aprendizagem automática está armazenada. A secção seguinte atravessa etapas para ver o recipiente a funcionar como um módulo no seu dispositivo IoT Edge.

## <a name="view-the-generated-data"></a>Ver os dados gerados

Pode ver as mensagens geradas por cada módulo do IoT Edge e pode ver as mensagens que são entregues ao seu hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Ver dados no seu dispositivo do IoT Edge

No seu dispositivo do IoT Edge, pode ver as mensagens a enviar de cada módulo individual.

Pode ser necessário `sudo` usar permissões `iotedge` elevadas para executar comandos. Iniciar sessão e voltar a entrar no seu dispositivo atualiza automaticamente as suas permissões.

1. Veja todos os módulos no seu dispositivo do IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Veja as mensagens enviadas a partir de um dispositivo específico. Utilize o nome do módulo do resultado do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Veja os dados que chegam ao seu hub IoT

Pode visualizar as mensagens dispositivo-to-cloud que o seu hub IoT recebe utilizando a [extensão Do Hub Azure IoT para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Os passos seguintes mostram como configurar o Visual Studio Code para monitorizar as mensagens do dispositivo para cloud que chegam ao seu hub IoT.

1. No Visual Studio Code, selecione **Dispositivos do Hub IoT**.

2. Selecione **...** e, em seguida, selecione **Definir Cadeia de Ligação do Hub IoT** no menu.

   ![Definir cadeia de ligação do hub iot](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que se abre na parte superior da página, introduza a cadeia de ligação de iothubowner para o seu Hub IoT. O dispositivo do IoT Edge deve aparecer na lista de Dispositivos do Hub IoT.

4. Selecione **...** novamente selecione **Start Monitoring Built-in Event Endpoint**.

5. Observe as mensagens provenientes de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomalia,** que o módulo de machinelearning fornece com um valor verdadeiro ou falso. A propriedade **AzureMLResponse** contém o valor "OK", se o modelo for executado com êxito.

   ![Resposta de aprendizagem automática azure no corpo de mensagens](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um módulo do IoT Edge com tecnologia do Azure Machine Learning. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
