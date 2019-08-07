---
title: Implementar módulos do portal do Azure – Azure IoT Edge | Documentos da Microsoft
description: Utilize o portal do Azure para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f27484fd1d47a2e29aa5083a7d440e5c7dba11c1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839644"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementar módulos do Azure IoT Edge do portal do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como o portal do Azure orienta-o ao longo da criação de um manifesto de implantação e enviar a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação direcionada para vários dispositivos com base nas respetivas etiquetas partilhadas, consulte [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure.
* Uma [dispositivo IoT Edge](how-to-register-device-portal.md) com o runtime do IoT Edge instalado.

## <a name="select-your-device"></a>Selecione o seu dispositivo

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** no menu.
1. Clique no ID de dispositivo de destino na lista de dispositivos.
1. Selecione **Definir Módulos**.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como o trabalho de manifestos de implantação e como criá-los, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que explica como criar o manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três etapas: **Adicione módulos**, **especifique rotas**e **examine a implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **configurações de registro de contêiner** da página, forneça as credenciais para acessar qualquer registro de contêiner privado que contenha suas imagens de módulo.

1. Na seção **módulos de implantação** da página, selecione **Adicionar**.

1. Examinar os tipos de módulos na lista pendente:

   * **Módulo do IoT Edge** -a opção predefinida.
   * **Módulo do Azure Stream Analytics** -só os módulos gerados a partir de uma carga de trabalho do Azure Stream Analytics.
   * **Azure Machine Learning** imagens de modelo somente de módulo geradas por meio de um espaço de trabalho Azure Machine Learning.

1. Selecione o **módulo do IoT Edge**.

1. Forneça um nome para o módulo, em seguida, especifique a imagem de contentor. Por exemplo:

   * **Nome** -SimulatedTemperatureSensor
   * **URI da imagem** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Preencha os campos opcionais, se necessário. Para obter mais informações sobre o contentor criar uma política de reinício de opções e ver o estado pretendido [propriedades pretendidas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o duplo do módulo, consulte [definir ou atualizar propriedades pretendidas](module-composition.md#define-or-update-desired-properties).

1. Selecione **Guardar**.

1. Repita os passos 2 a 6 para adicionar módulos adicionais à sua implementação.

1. Selecione **seguinte** para continuar para a secção de rotas.

### <a name="specify-routes"></a>Especificar rotas

Por predefinição, o assistente apresenta uma rota chamada **rota** e definido como **FROM /* em $ a montante * *, que significa que todas as mensagens de saída por quaisquer módulos são enviadas ao seu hub IoT.  

Adicionar ou atualizar as rotas com as informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próxima** para continuar para a secção de revisão.

### <a name="review-deployment"></a>Implementação de revisão

A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Tenha em atenção que existem dois módulos declarado se não tiver adicionado: **$edgeAgent** e **$edgeHub**. Esses dois módulos formam o [runtime do IoT Edge](iot-edge-runtime.md) e são predefinições necessárias em todas as implementações.

Reveja as suas informações de implantação, em seguida, selecione **submeter**.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Após a implantação de módulos para o seu dispositivo, pode ver todos eles do **detalhes do dispositivo** página do portal. Esta página apresenta o nome de cada módulo implementado, bem como informações úteis, como o código de estado e de saída de implementação.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O Azure Marketplace é um Marketplace de aplicativos e serviços online, no qual você pode navegar por uma ampla variedade de aplicativos e soluções empresariais que são certificados e otimizados para serem executados no Azure, incluindo [módulos IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acessado por meio do portal do Azure em **criar um recurso**.

Você pode instalar um módulo IoT Edge do Azure Marketplace ou do portal do Azure:

1. Localize um módulo e inicie o processo de implantação.

   * Portal do Azure: Localize um módulo e selecione **criar**.

   * Azure Marketplace:

     1. Localize um módulo e selecione **obtê-lo agora**.
     1. Confirme os termos de uso e a política de privacidade do provedor selecionando **continuar**.

1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.

1. Escolha **implantar em um dispositivo**.

1. Insira o nome do dispositivo ou selecione **Localizar dispositivo** para procurar entre os dispositivos registrados com o Hub.

1. Selecione **criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos, se desejado. Os detalhes do novo módulo, como o URI da imagem, as opções de criação e as propriedades desejadas, são predefinidos, mas podem ser alterados.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)
