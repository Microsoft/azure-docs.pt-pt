---
title: Implementar módulos do portal do Azure – Azure IoT Edge | Documentos da Microsoft
description: Utilize o portal do Azure para implementar módulos para um dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595245"
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

O portal do Azure tem um assistente que explica como criar o manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três passos: **Adicionar módulos**, **especificar rotas**, e **reveja implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na **definições de registo** secção da página, forneça as credenciais para aceder a quaisquer registos de contentores privado que contêm as imagens de módulo.

1. Na **módulos de implementação** secção da página, selecione **Add**.

1. Examinar os tipos de módulos na lista pendente:

   * **Módulo do IoT Edge** -a opção predefinida.
   * **Módulo do Azure Stream Analytics** -só os módulos gerados a partir de uma carga de trabalho do Azure Stream Analytics.
   * **Módulo do Azure Machine Learning** -apenas modelar imagens geradas a partir de uma área de trabalho do Azure Machine Learning.

1. Selecione o **módulo do IoT Edge**.

1. Forneça um nome para o módulo, em seguida, especifique a imagem de contentor. Por exemplo:

   * **Nome** -tempSensor
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

## <a name="deploy-modules-from-azure-marketplace"></a>Implementar módulos do Azure Marketplace

O Azure Marketplace é um mercado online de aplicações e serviços onde pode navegar por uma vasta gama de aplicações empresariais e soluções que estão certificadas e otimizadas para executar no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acedido através do portal do Azure em **criar um recurso**.

Pode instalar o módulo do IoT Edge do Azure Marketplace ou do portal do Azure:

1. Encontre um módulo e iniciar o processo de implantação.

   * Portal do Azure: Encontre um módulo e selecione **criar**.

   * Azure Marketplace:

     1. Encontre um módulo e selecione **obter agora**.
     1. Concorda em termos do fornecedor de política de privacidade e de utilização selecionando **continuar**.

1. Escolha a sua subscrição e o IoT Hub para que o dispositivo de destino está ligado.

1. Escolher **implementar num dispositivo**.

1. Introduza o nome do dispositivo ou selecione **localizar dispositivo** para navegar entre os dispositivos registados no hub.

1. Selecione **criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos se assim o desejar. Opções de criação de detalhes para o novo módulo como imagem URI, e as propriedades pretendidas estão predefinidas, mas podem ser alteradas.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)
