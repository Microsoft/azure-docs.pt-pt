---
title: Implementar módulos a partir do portal Azure - Azure IoT Edge
description: Utilize o seu Hub IoT no portal Azure para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 754c106db42f3f0695ad023e736993bee82e9757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82133925"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementar módulos Azure IoT Edge a partir do portal Azure

Uma vez criados módulos IoT Edge com a sua lógica de negócio, pretende implantá-los nos seus dispositivos para operar na borda. Se tiver vários módulos que trabalham em conjunto para recolher e processar dados, pode implantá-los todos de uma vez e declarar as regras de encaminhamento que os ligam.

Este artigo mostra como o portal Azure o guia através da criação de um manifesto de implantação e empurrando a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que direcione vários dispositivos com base nas suas etiquetas partilhadas, consulte [os módulos Implementar e monitorizar o IoT Edge à escala](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-in-the-azure-portal) com o tempo de execução IoT Edge instalado.

## <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte como os [módulos IoT Edge podem ser usados, configurados e reutilizados.](module-composition.md)

O portal Azure tem um assistente que o acompanha através da criação do manifesto de implantação, em vez de construir o documento JSON manualmente. Tem três etapas: **Adicionar módulos,** **especificar rotas**e **revisão.**

### <a name="select-device-and-add-modules"></a>Selecione o dispositivo e adicione módulos

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. No painel esquerdo, selecione **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo a partir da lista de dispositivos.
1. Na barra superior, selecione **Módulos de Conjunto**.
1. Na secção **de Definições** de Registo de Contentores da página, forneça as credenciais para aceder a quaisquer registos privados de contentores que contenham as imagens do módulo.
1. Na secção **módulos IoT Edge modules** da página, selecione **Adicionar**.
1. Veja os tipos de módulos do menu suspenso:

   * **Módulo IoT Edge** - Fornece o nome do módulo e a imagem do recipiente URI. Por exemplo, a imagem URI para o módulo Simulado DesteperatureSensor é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se a imagem do módulo for armazenada num registo de contentores privados, adicione as credenciais nesta página para aceder à imagem.
   * **Módulo marketplace** - Módulos alojados no Mercado Azure. Alguns módulos de marketplace requerem configuração adicional, por isso reveja os detalhes do módulo na lista de [Módulos de Borda IoT do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Módulo Azure Stream Analytics** - Módulos gerados a partir de uma carga de trabalho Azure Stream Analytics.

1. Depois de adicionar um módulo, selecione o nome do módulo da lista para abrir as definições do módulo. Preencha os campos opcionais, se necessário. Para obter mais informações sobre o contentor, crie opções, reinicie a política e o estado desejado consulte [as propriedades desejadas pela EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o módulo twin consulte [Definir ou atualizar as propriedades desejadas.](module-composition.md#define-or-update-desired-properties)
1. Se necessário, repita os passos 5 a 8 para adicionar módulos adicionais à sua implantação.
1. Selecione **Seguinte: Rotas** para continuar até à secção de rotas.

### <a name="specify-routes"></a>Especificar rotas

No separador **Rotas,** define como as mensagens são passadas entre os módulos e o Hub IoT. As mensagens são construídas utilizando pares de nome/valor. Por predefinição, uma rota é chamada **de rota** e definida como **FROM /messages/ \* INTO $upstream**, o que significa que qualquer saída de mensagens por quaisquer módulos é enviada para o seu hub IoT.  

Adicione ou atualize as rotas com informações das [rotas Declara,](module-composition.md#declare-routes)em seguida, selecione **Seguinte: Review + create** para continuar até ao próximo passo do assistente.

### <a name="review-deployment"></a>Implementação de revisão

A secção de revisão mostra-lhe o manifesto de implantação JSON que foi criado com base nas suas seleções nas duas secções anteriores. Note que existem dois módulos declarados que não acrescentou: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o [tempo de funcionamento do IoT Edge](iot-edge-runtime.md) e são necessários predefinidos em todas as implementações.

Reveja as suas informações de implementação e, em seguida, **selecione Criar**.

## <a name="view-modules-on-your-device"></a>Ver módulos no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los todos na página de detalhes do dispositivo do seu Hub IoT. Esta página apresenta o nome de cada módulo implantado, bem como informações úteis como o estado de implantação e o código de saída.

## <a name="deploy-modules-from-azure-marketplace"></a>Implementar módulos do Azure Marketplace

[O Azure Marketplace](https://azuremarketplace.microsoft.com/) é um mercado de aplicações e serviços online onde pode navegar através de uma vasta gama de aplicações e soluções empresariais que são certificadas e otimizadas para funcionar em Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

Pode implementar um módulo IoT Edge a partir do Azure Marketplace e do seu IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implantação a partir do Azure Marketplace

Perusear os módulos IoT Edge no mercado e quando encontrar o que pretende pode implantá-lo selecionando **Criar** ou **Obtê-lo agora**. Proceda com as etapas de assistente de implantação que podem variar dependendo do módulo IoT Edge selecionado:

1. Reconhecer os termos de utilização e política de privacidade do fornecedor selecionando **Continue**. Primeiro, poderá ter de fornecer informações de contacto.
1. Escolha a sua subscrição e o Hub IoT ao qual o dispositivo-alvo está ligado.
1. Escolha **implementar para um dispositivo**.
1. Introduza o nome do dispositivo ou selecione **Find Device** para navegar entre os dispositivos registados no hub.
1. Selecione **Criar** para continuar o processo padrão de configurar um manifesto de implantação, incluindo adicionar outros módulos se desejar. Os detalhes para o novo módulo, como o URI de imagem, criar opções e propriedades desejadas são predefinidos, mas podem ser alterados.

Verifique se o módulo está implantado no seu Hub IoT no portal Azure. Selecione o seu dispositivo, selecione **set Modules** e o módulo deve ser listado na secção **módulos IoT Edge Modules.**

### <a name="deploy-from-azure-iot-hub"></a>Implantação a partir do Azure IoT Hub

Pode implantar rapidamente um módulo do Azure Marketplace no seu dispositivo no seu Hub IoT no portal Azure.

1. No portal Azure, navegue para o seu Hub IoT.
1. No painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.
1. Selecione o dispositivo IoT Edge que deve receber a implementação.
1. Na barra superior, selecione **Módulos de Conjunto**.
1. Na secção **Módulos IoT Edge,** clique em **Adicionar**e selecione **o Módulo De Mercado** a partir do menu suspenso.

![Adicione módulo no IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Escolha um módulo na página **IoT Edge Module Marketplace.** O módulo que seleciona está automaticamente configurado para a sua subscrição, grupo de recursos e dispositivo. Em seguida, aparece na sua lista de módulos IoT Edge. Alguns módulos podem necessitar de configuração adicional.

> [!TIP]
> A informação sobre os módulos IoT Edge do Azure IoT Hub é limitada. Você pode primeiro aprender mais sobre os [módulos IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) no Azure Marketplace.

Selecione **Seguinte: Rotas** e continue com a implementação como descrito por [Especificar rotas](#specify-routes) e [Revisão Desacreva a implementação](#review-deployment) mais cedo neste artigo.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar e monitorizar os módulos IoT Edge à escala](how-to-deploy-at-scale.md)
