---
title: Implantar módulos de portal do Azure Azure IoT Edge
description: Use o Hub IoT no portal do Azure para enviar por push um módulo de IoT Edge do Hub IoT para o dispositivo IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7a3280e11d40a361c5a3305d71e58661b37b8bd1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563433"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementar módulos do Azure IoT Edge do portal do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como o portal do Azure orienta-o ao longo da criação de um manifesto de implantação e enviar a implementação para um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure.
* Uma [dispositivo IoT Edge](how-to-register-device.md#register-in-the-azure-portal) com o runtime do IoT Edge instalado.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como o trabalho de manifestos de implantação e como criá-los, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que explica como criar o manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três passos: **adicionar módulos**, **especificar rotas**, e **rever implementação**.

### <a name="select-device-and-add-modules"></a>Selecionar dispositivo e adicionar módulos

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. No painel esquerdo, selecione **IOT Edge** no menu.
1. Clique no ID de dispositivo de destino na lista de dispositivos.
1. Na barra superior, selecione **definir módulos**.
1. Na seção **configurações de registro de contêiner** da página, forneça as credenciais para acessar qualquer registro de contêiner privado que contenha suas imagens de módulo.
1. Na seção **módulos IOT Edge** da página, selecione **Adicionar**.
1. Examine os tipos de módulos no menu suspenso:

   * **Módulo IOT Edge** -você fornece o nome do módulo e o URI da imagem de contêiner. Por exemplo, o URI da imagem para o módulo SimulatedTemperatureSensor de exemplo é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Se a imagem do módulo estiver armazenada em um registro de contêiner privado, adicione as credenciais nesta página para acessar a imagem. 
   * **Módulo do Marketplace** -módulos hospedados no Azure Marketplace. Alguns módulos do Marketplace exigem configuração adicional, portanto, examine os detalhes do módulo na lista de [módulos de IOT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Módulo Azure Stream Analytics** -módulos gerados de uma carga de trabalho de Azure Stream Analytics. 

1. Depois de adicionar um módulo, selecione o nome do módulo na lista para abrir as configurações do módulo. Preencha os campos opcionais, se necessário. Para obter mais informações sobre o contentor criar uma política de reinício de opções e ver o estado pretendido [propriedades pretendidas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o duplo do módulo, consulte [definir ou atualizar propriedades pretendidas](module-composition.md#define-or-update-desired-properties).
1. Se necessário, repita as etapas de 5 a 8 para adicionar mais módulos à sua implantação.
1. Selecione **Avançar: rotas** para continuar na seção rotas.

### <a name="specify-routes"></a>Especificar rotas

Na guia **rotas** , você define como as mensagens são passadas entre os módulos e o Hub IOT. As mensagens são construídas usando pares de nome/valor. Por padrão, uma rota é chamada de **rota** e definida como **de/messages/* para $upstream * *, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Adicione ou atualize as rotas com informações de [declarar rotas](module-composition.md#declare-routes)e, em seguida, selecione **Avançar: revisar + criar** para continuar na próxima etapa do assistente.

### <a name="review-deployment"></a>Implementação de revisão

A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Tenha em atenção que existem dois módulos declarado se não tiver adicionado: **$edgeAgent** e **$edgeHub**. Esses dois módulos formam o [runtime do IoT Edge](iot-edge-runtime.md) e são predefinições necessárias em todas as implementações.

Examine as informações de implantação e, em seguida, selecione **criar**.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na página de detalhes do dispositivo do Hub IoT. Esta página apresenta o nome de cada módulo implementado, bem como informações úteis, como o código de estado e de saída de implementação.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O [Azure Marketplace](https://azuremarketplace.microsoft.com/) é um Marketplace de aplicativos e serviços online, no qual você pode navegar por uma ampla variedade de aplicativos e soluções empresariais que são certificados e otimizados para serem executados no Azure, incluindo [módulos IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Você pode implantar um módulo IoT Edge do Azure Marketplace e de seu hub IoT.

### <a name="deploy-from-azure-marketplace"></a>Implantar do Azure Marketplace

Examinar os módulos de IoT Edge no Marketplace e quando você encontrar o que você deseja implantar, selecionando **criar** ou **obtê-lo agora**. Prossiga com as etapas do assistente de implantação que podem variar dependendo do módulo de IoT Edge selecionado:

1. Confirme os termos de uso e a política de privacidade do provedor selecionando **continuar**. Você pode primeiro precisar fornecer informações de contato.
1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.
1. Escolha **implantar em um dispositivo**.
1. Insira o nome do dispositivo ou selecione **Localizar dispositivo** para procurar entre os dispositivos registrados com o Hub.
1. Selecione **criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos, se desejado. Os detalhes do novo módulo, como o URI da imagem, as opções de criação e as propriedades desejadas, são predefinidos, mas podem ser alterados.

Verifique se o módulo está implantado em seu hub IoT no portal do Azure. Selecione seu dispositivo, selecione **definir módulos** e o módulo deve estar listado na seção **módulos de IOT Edge** .

### <a name="deploy-from-azure-iot-hub"></a>Implantar do Hub IoT do Azure

Você pode implantar rapidamente um módulo do Azure Marketplace em seu dispositivo no Hub IoT no portal do Azure.

1. No portal do Azure, navegue até o Hub IoT.
1. No painel esquerdo, em **Gerenciamento de dispositivo automático**, selecione **IOT Edge**.
1. Selecione o dispositivo IoT Edge que deve receber a implantação.
1. Na barra superior, selecione **definir módulos**.
1. Na seção **módulos IOT Edge** , clique em **Adicionar**e selecione **módulo do Marketplace** no menu suspenso.

![Adicionar módulo no Hub IoT](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Escolha um módulo na página **IOT Edge do Marketplace do módulo** . O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece na lista de módulos de IoT Edge. Alguns módulos podem exigir configuração adicional.

> [!TIP]
> As informações sobre os módulos de IoT Edge do Hub IoT do Azure são limitadas. Primeiro, você pode aprender mais sobre os [módulos de IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) no Azure Marketplace.

Selecione **Avançar: rotas** e continuar com a implantação conforme descrito por [especificar rotas](#specify-routes) e [examinar a implantação](#review-deployment) anteriormente neste artigo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md)
