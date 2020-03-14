---
title: Módulos de implantação do portal Azure - Azure IoT Edge
description: Utilize o seu Hub IoT no portal Azure para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, tal como configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271450"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementar módulos do Azure IoT Edge do portal do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

Este artigo mostra como o portal do Azure orienta-o ao longo da criação de um manifesto de implantação e enviar a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que visa vários dispositivos com base nas suas etiquetas partilhadas, consulte [os módulos IoT Edge em escala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-in-the-azure-portal) com o tempo de execução do IoT Edge instalado.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que explica como criar o manifesto de implantação, em vez de criar o documento JSON manualmente. Tem três **passos: Adicionar módulos,** **especificar rotas,** e **rever a implementação**.

### <a name="select-device-and-add-modules"></a>Selecione dispositivo e adicione módulos

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. No painel esquerdo, selecione **IoT Edge** do menu.
1. Clique no ID de dispositivo de destino na lista de dispositivos.
1. Na barra superior, selecione **Módulos de Conjunto**.
1. Na secção Definições de Registo de **Contentores** da página, forneça as credenciais para aceder a quaisquer registos de contentores privados que contenham as imagens do seu módulo.
1. Na secção **Módulos IoT Edge** da página, selecione **Adicionar**.
1. Veja os tipos de módulos do menu suspenso:

   * **Módulo IoT Edge** - Fornece o nome do módulo e a imagem do recipiente URI. Por exemplo, a imagem URI para o módulo SimuladoSensor de Temperatura é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Se a imagem do módulo for armazenada num registo de contentores privados, adicione as credenciais desta página para aceder à imagem.
   * **Módulo marketplace** - Módulos hospedados no Mercado Azure. Alguns módulos de marketplace requerem configuração adicional, por isso reveja os detalhes do módulo na lista de [Módulos De Borda Do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Módulo de Análise De Fluxo Azure** - Módulos gerados a partir de uma carga de trabalho Azure Stream Analytics.

1. Depois de adicionar um módulo, selecione o nome do módulo da lista para abrir as definições do módulo. Preencha os campos opcionais, se necessário. Para obter mais informações sobre o recipiente, criar opções, reiniciar a política e o estado desejado ver [propriedades desejadas pelo EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o módulo twin consulte [Definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).
1. Se necessário, repita os passos 5 a 8 para adicionar módulos adicionais à sua implementação.
1. Selecione **Seguinte: Rotas** para continuar na secção rotas.

### <a name="specify-routes"></a>Especificar rotas

No separador **Rotas,** define como as mensagens são passadas entre os módulos e o IoT Hub. As mensagens são construídas utilizando pares de nome/valor. Por defeito, uma rota é chamada **de rota** e definida como **FROM/messages/\* INTO $upstream**, o que significa que qualquer saída de mensagens por quaisquer módulos é enviada para o seu hub IoT.  

Adicione ou atualize as rotas com informações das [rotas declarar,](module-composition.md#declare-routes)em seguida, selecione **Next: Review + criar** para continuar até ao próximo passo do assistente.

### <a name="review-deployment"></a>Implementação de revisão

A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Note que existem dois módulos declarados que não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o tempo de [funcionamento do IoT Edge](iot-edge-runtime.md) e são exigidos predefinidos em todas as implementações.

Reveja as informações de implementação e, em seguida, selecione **Criar**.

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los na página de detalhes do dispositivo do seu Hub IoT. Esta página apresenta o nome de cada módulo implementado, bem como informações úteis, como o código de estado e de saída de implementação.

## <a name="deploy-modules-from-azure-marketplace"></a>Implementar módulos do Azure Marketplace

O [Azure Marketplace](https://azuremarketplace.microsoft.com/) é um mercado de aplicações e serviços online onde pode navegar através de uma vasta gama de aplicações e soluções empresariais certificadas e otimizadas para funcionar no Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

Pode implantar um módulo IoT Edge a partir do Azure Marketplace e do seu IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implantação do Azure Marketplace

Percorra os módulos IoT Edge no mercado e quando encontrar o que deseja pode implantá-lo selecionando **Criar** ou **obtê-lo agora**. Proceda com as etapas do assistente de implementação que podem variar dependendo do módulo IoT Edge selecionado:

1. Reconheça os termos de utilização e política de privacidade do fornecedor selecionando **Continuar**. Pode primeiro ter de fornecer informações de contacto.
1. Escolha a sua subscrição e o IoT Hub ao qual o dispositivo alvo está ligado.
1. Escolha **o 'Enviar' para um dispositivo**.
1. Introduza o nome do dispositivo ou selecione **Encontrar dispositivo** para navegar entre os dispositivos registados com o hub.
1. Selecione **Criar** para continuar o processo padrão de configuração de um manifesto de implementação, incluindo adicionar outros módulos, se desejar. Os detalhes para o novo módulo, como a imagem URI, a criação de opções e as propriedades desejadas são predefinidos, mas podem ser alterados.

Verifique se o módulo está implantado no seu Hub IoT no portal Azure. Selecione o seu dispositivo, selecione **Módulos de Conjunto** e o módulo deve ser listado na secção **Módulos IoT Edge.**

### <a name="deploy-from-azure-iot-hub"></a>Implantação do Hub Azure IoT

Pode implantar rapidamente um módulo do Azure Marketplace no seu dispositivo no seu IoT Hub no portal Azure.

1. No portal Azure, navegue até ao seu Hub IoT.
1. No painel esquerdo, sob gestão automática de **dispositivos,** selecione **IoT Edge**.
1. Selecione o dispositivo IoT Edge que deverá receber a implementação.
1. Na barra superior, selecione **Módulos de Conjunto**.
1. Na secção **Módulos IoT Edge,** clique em **Adicionar**, e selecione **Módulo marketplace** a partir do menu suspenso.

![Adicione módulo em IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Escolha um módulo a partir da página Mercado do **Módulo IoT Edge.** O módulo que selecionar está automaticamente configurado para a sua subscrição, grupo de recursos e dispositivo. Em seguida, aparece na sua lista de módulos IoT Edge. Alguns módulos podem necessitar de configuração adicional.

> [!TIP]
> A informação sobre os módulos IoT Edge do Hub Azure IoT é limitada. Em primeiro lugar, poderá saber mais sobre os [módulos IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) no Azure Marketplace.

Selecione **Seguinte: Rotas** e continue com a implantação conforme descrito por [Especificar rotas](#specify-routes) e [Implementação](#review-deployment) de Revisão no início deste artigo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorizar módulos IoT Edge em escala](how-to-deploy-monitor.md)
