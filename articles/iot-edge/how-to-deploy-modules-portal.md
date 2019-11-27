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
ms.openlocfilehash: 6d915145e64a5f1a097f38cf79b19426c3acbaf2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457438"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementar módulos do Azure IoT Edge do portal do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como o portal do Azure orienta-o ao longo da criação de um manifesto de implantação e enviar a implementação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
* Um [dispositivo IOT Edge](how-to-register-device.md#register-in-the-azure-portal) com o IOT Edge Runtime instalado.

## <a name="select-your-device"></a>Selecione o seu dispositivo

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu.
1. Clique no ID de dispositivo de destino na lista de dispositivos.
1. Selecione **Definir Módulos**.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que explica como criar o manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três etapas: **Adicionar módulos**, **especificar rotas**e **examinar a implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **configurações de registro de contêiner** da página, forneça as credenciais para acessar qualquer registro de contêiner privado que contenha suas imagens de módulo.

1. Na seção **módulos de implantação** da página, selecione **Adicionar**.

1. Examinar os tipos de módulos na lista pendente:

   * **Módulo IOT Edge** -a opção padrão.
   * **Azure Stream Analytics** módulos somente de módulo gerados de uma carga de trabalho de Azure Stream Analytics.
   * **Azure Machine Learning** imagens de modelo somente de módulo geradas por meio de um espaço de trabalho Azure Machine Learning.

1. Selecione o **módulo IOT Edge**.

1. Forneça um nome para o módulo, em seguida, especifique a imagem de contentor. Por exemplo:

   * **Nome** -SimulatedTemperatureSensor
   * **URI da imagem** -MCR.Microsoft.com/azureiotedge-Simulated-temperature-sensor:1.0

1. Preencha os campos opcionais, se necessário. Para obter mais informações sobre opções de criação de contêiner, política de reinicialização e status desejado, consulte [EdgeAgent Desired Properties](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o módulo número de atualizações, consulte [definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).

1. Selecione **Guardar**.

1. Repita os passos 2 a 6 para adicionar módulos adicionais à sua implementação.

1. Selecione **Avançar** para continuar na seção rotas.

### <a name="specify-routes"></a>Especificar rotas

Por padrão, o assistente fornece uma rota chamada **rota** e definido como **de/* para $upstream * *, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Adicione ou atualize as rotas com informações de [declarar rotas](module-composition.md#declare-routes)e, em seguida, selecione **Avançar** para continuar na seção revisão.

### <a name="review-deployment"></a>Implementação de revisão

A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Observe que há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Esses dois módulos compõem o [tempo de execução de IOT Edge](iot-edge-runtime.md) e são padrões necessários em cada implantação.

Examine as informações de implantação e, em seguida, selecione **Enviar**.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na página de **detalhes do dispositivo** do Portal. Esta página apresenta o nome de cada módulo implementado, bem como informações úteis, como o código de estado e de saída de implementação.

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

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md)
