---
title: Tutorial - Implementar Azure Machine Learning para um dispositivo que utilize a borda IoT Azure
description: Neste tutorial, você cria um modelo Azure Machine Learning, em seguida, implantá-lo como um módulo para um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fd297ec5cfb7831a438fc51e72e3c2fc163eff49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271282"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implementar o Azure Machine Learning como um módulo do IoT Edge (pré-visualização)

Utilize cadernos Azure para desenvolver um módulo de aprendizagem automática e implantá-lo num dispositivo Linux que executa a Azure IoT Edge.
Pode utilizar os módulos do IoT Edge para implementar código que aplica a sua lógica de negócio diretamente nos seus dispositivos IoT Edge. Este tutorial explica como implementar um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de temperatura simulada no computador. Para obter mais informações sobre Azure Machine Learning on IoT Edge, consulte [a documentação de Aprendizagem automática do Azure.](../machine-learning/how-to-deploy-and-where.md)

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge estão em pré-visualização pública.

O módulo do Azure Machine Learning que cria neste tutorial lê os dados ambientais gerados pelo seu dispositivo e marca as mensagens como anómalas, ou não.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um módulo de aprendizagem automática Azure.
> * Empurre um recipiente de módulo para um registo de contentores Azure.
> * Coloque um módulo de aprendizagem automática Azure no seu dispositivo IoT Edge.
> * Veja os dados gerados.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode utilizar uma máquina virtual Azure como dispositivo IoT Edge seguindo os passos no arranque rápido do [Linux](quickstart-linux.md).
* O módulo Azure Machine Learning não suporta recipientes Windows.
* O módulo Azure Machine Learning não suporta processadores ARM.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Uma área de trabalho do Azure Machine Learning. Siga as instruções em [Utilizar o portal Azure para começar com a Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) para criar uma e aprender a usá-la.
  * Tome nota do nome do espaço de trabalho, grupo de recursos e ID de subscrição. Estes valores estão todos disponíveis na visão geral do espaço de trabalho no portal Azure. Você usará estes valores mais tarde no tutorial para ligar um arquivo Azure Notebooks aos seus recursos do espaço de trabalho.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implementar módulo de aprendizagem automática Azure

Nesta secção, converte-se ficheiros de modelos de aprendizagem automática treinados e num recipiente de Aprendizagem automática Azure. Todos os componentes necessários para a imagem do Docker encontram-se no [repositório do Git “Toolkit de IA para o Azure IoT Edge”](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estes passos para enviar o repositório para os cadernos da Microsoft Azure para criar o recipiente e empurrá-lo para o Registo de Contentores Azure.

1. Navegue para os seus projetos Azure Notebooks. Pode chegar lá a partir do seu espaço de trabalho Azure Machine Learning no [portal Azure](https://portal.azure.com) ou insinuá-lo no [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) com a sua conta Azure.

2. **Selecione Upload GitHub Repo**.

3. Forneça o seguinte nome do repositório GitHub: `Azure/ai-toolkit-iot-edge` . Desmarque a caixa **pública** se quiser manter o seu projeto privado. Selecione **Import** (Importar).

4. Assim que a importação estiver concluída, navegue no novo projeto **ai-toolkit-iot-edge** e abra a pasta **tutorial de deteção de anomalias IoT Edge.**

5. Verifique se o seu projeto está em andamento. Caso contrário, selecione **Executar em Cálculo Gratuito**.

   ![Executar em computação gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra a **aml_config/config.jsno** ficheiro.

7. Edite o ficheiro config para incluir os valores do seu ID de subscrição Azure, um grupo de recursos na sua subscrição e o nome do espaço de trabalho Azure Machine Learning. Pode obter todos estes valores na secção **De Visão Geral** do seu espaço de trabalho em Azure.

8. Guarde o ficheiro config.

9. Abra o ficheiro **00-anomalia-detecção tutorial.ipynb.**

10. Quando solicitado, selecione o núcleo **Python 3.6** e, em seguida, selecione **set Kernel**.

11. Edite a primeira célula no caderno de acordo com as instruções nos comentários. Utilize o mesmo grupo de recursos, ID de subscrição e nome do espaço de trabalho que adicionou ao ficheiro config.

12. Executar as células no caderno selecionando-as e selecionando **Run** ou premindo `Shift + Enter` .

    >[!TIP]
    >Algumas das células do caderno tutorial de deteção de anomalias são opcionais, pois criam recursos que alguns utilizadores podem ou não ter ainda, como um Hub IoT. Se colocar a informação de recursos existente na primeira célula, receberá erros se executar as células que criam novos recursos porque o Azure não criará recursos duplicados. Isto é bom, e pode ignorar os erros ou saltar completamente essas secções opcionais.

Ao completar todos os passos do caderno, treinou um modelo de deteção de anomalias, construiu-o como uma imagem de contentor docker, e empurrou essa imagem para o Registo do Contentor de Azure. Depois, testou o modelo e finalmente implantou-o no seu dispositivo IoT Edge.

## <a name="view-container-repository"></a>Ver repositório de contentores

Verifique se a sua imagem de recipiente foi criada e armazenada com sucesso no registo de contentores Azure associado ao seu ambiente de aprendizagem automática. O caderno que usou na secção anterior forneceu automaticamente a imagem do contentor e as credenciais de registo para o seu dispositivo IoT Edge, mas deve saber onde estão armazenadas para que possa encontrar a informação mais tarde.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu espaço de trabalho de serviço machine learning.

2. A **secção De Visão** Geral lista os detalhes do espaço de trabalho, bem como os seus recursos associados. Selecione o valor **do Registo,** que deve ser o nome do seu espaço de trabalho seguido de números aleatórios.

3. No registo de contentores, em **Serviços,** **selecione Repositórios**. Você deve ver um repositório chamado **tempanomalydetection** que foi criado pelo caderno que você executou na secção anterior.

4. **Selecione a deteção tempanomaly**. Deve ver que o repositório tem uma etiqueta: **1**.

   Agora que conhece o nome do registo, o nome do repositório e a etiqueta, conhece o caminho de imagem completo do contentor. Os caminhos de imagem parecem ** \<registry_name\> .azurecr.io/tempanomalydetection:1**. Pode utilizar o caminho da imagem para implementar este contentor para dispositivos IoT Edge.

5. No registo do contentor, em **Definições,** selecione **teclas de acesso**. Deverá ver várias credenciais de acesso, incluindo **o servidor de Login** e o nome de **utilizador,** e **a Palavra-passe** para um utilizador administrativo.

   Estas credenciais podem ser incluídas no manifesto de implementação para dar ao seu dispositivo IoT Edge acesso para solicitar imagens do contentor do registo.

Agora sabe onde está armazenada a imagem do recipiente de Aprendizagem automática. A secção seguinte percorre etapas para ver o recipiente funcionando como um módulo no seu dispositivo IoT Edge.

## <a name="view-the-generated-data"></a>Ver os dados gerados

Pode ver as mensagens geradas por cada módulo do IoT Edge e pode ver as mensagens que são entregues ao seu hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Ver dados no seu dispositivo do IoT Edge

No seu dispositivo do IoT Edge, pode ver as mensagens a enviar de cada módulo individual.

Pode ser necessário utilizar `sudo` para permissões elevadas para executar `iotedge` comandos. A assinatura e a inscrição no seu dispositivo atualizam automaticamente as suas permissões.

1. Veja todos os módulos no seu dispositivo do IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Veja as mensagens enviadas a partir de um dispositivo específico. Utilize o nome do módulo do resultado do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Veja os dados que chegam ao seu hub IoT

Pode ver as mensagens dispositivo-a-nuvem que o seu hub IoT recebe utilizando a [extensão Azure IoT Hub para Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Os passos seguintes mostram como configurar o Visual Studio Code para monitorizar as mensagens do dispositivo para cloud que chegam ao seu hub IoT.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Start Monitoring Built-in Event Endpoint**.

3. Observe as mensagens provenientes de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade denominada **anomaly** (anomalia) que o machinelearningmodule apresenta com um valor verdadeiro ou falso. A propriedade **AzureMLResponse** contém o valor "OK" se o modelo for executado com êxito.

   ![Resposta de aprendizagem de máquina azure no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um módulo do IoT Edge com tecnologia do Azure Machine Learning. Pode continuar para qualquer um dos outros tutoriais para saber mais sobre outras formas em que o Azure IoT Edge o pode ajudar a transformar os dados em informações empresariais no Edge.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
