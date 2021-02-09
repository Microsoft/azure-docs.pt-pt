---
title: Tutorial de IoT Connected logística | Microsoft Docs
description: Um tutorial do modelo de aplicação logística conectada para ioT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831625"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação logística conectada

Este tutorial mostra-lhe como começar com o modelo de aplicação *logística conectada* IoT Central. Você vai aprender a implementar e usar o modelo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação logística conectada.
> * Utilize as principais funcionalidades da aplicação.
> * Utilize o Dashboard para mostrar a atividade crítica de operações do dispositivo logístico.
> * Use o modelo do dispositivo
> * Seguir regras
> * Utilizar empregos

## <a name="prerequisites"></a>Pré-requisitos

* Não existem requisitos específicos para implementar esta aplicação.
* Pode utilizar o plano de preços gratuito ou utilizar uma subscrição Azure.

## <a name="create-connected-logistics-application"></a>Criar aplicação logística conectada

Crie a aplicação utilizando os seguintes passos:

1. Navegue até ao sítio central de [construção Azure IoT.](https://aka.ms/iotcentral) Em seguida, inscreva-se com uma conta pessoal, de trabalho ou escola da Microsoft. Selecione **Construir** a partir da barra de navegação à esquerda e, em seguida, selecione o **separador 'Varejo':**

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Modelo de logística conectado":::

1. Selecione **Criar aplicação** sob **aplicação logística conectada.**

1. **Criar app** abre o novo formulário **de aplicação.** Introduza os seguintes detalhes:


    * **Nome da aplicação**: pode utilizar o nome sugerido por defeito ou introduzir o nome da sua aplicação amigável.
    * **URL**: pode utilizar URL padrão sugerido ou inserir o seu AMIGÁVEL URL memorável único. Em seguida, recomenda-se a definição predefinida se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se a um plano de preços padrão a qualquer momento antes que o trilho gratuito expire.
    * **Billing Info**: O diretório, a subscrição do Azure e os detalhes da região são necessários para a disponibilização dos recursos.
    * **Criar:** Selecione criar na parte inferior da página para implementar a sua aplicação.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Modelo de aplicativo de logística conectado":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Informação de faturação logística conectada":::

## <a name="walk-through-the-application"></a>Caminhe pela aplicação

Abaixo está a imagem que mostra como selecionar o modelo de aplicação logística conectada.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando como selecionar o modelo de aplicação de logística conectada](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

As secções seguintes acompanham-no através das principais características da aplicação.

### <a name="dashboard"></a>Dashboard

Depois de implementar o modelo de aplicação, o seu painel de instrumentos padrão é um portal focado no operador de logística conectado. Northwind Trader é um fornecedor de logística fictício que gere uma frota de carga no mar e em terra. Neste painel de instrumentos, vemos dois gateways diferentes que fornecem telemetria a partir de envios, juntamente com comandos, empregos e ações associados.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando como criar uma aplicação a partir do modelo de aplicação de logística conectada](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando as opções de faturação quando cria a aplicação](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Este painel está pré-configurado para mostrar a atividade crítica de operações de dispositivos logísticos.

O painel permite duas diferentes operações de gestão de dispositivos de gateway:

* Veja as rotas logísticas para envios de camiões e os detalhes da localização dos envios do oceano.
* Veja o estado do gateway e outras informações relevantes.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Painel de logística conectado":::

* Pode rastrear o número total de gateways, etiquetas ativas e desconhecidas.
* Pode fazer operações de gestão de dispositivos como: atualizar firmware, desativar e ativar sensores, atualizar um limiar de sensor, atualizar intervalos de telemetria e atualizar contratos de serviços de dispositivos.
* Ver consumo de bateria do dispositivo.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Estado do painel de instrumentos de logística conectado":::

#### <a name="device-template"></a>Modelo de dispositivo

Selecione **modelos de dispositivo** para ver o modelo de capacidade do gateway. Um modelo de capacidade é estruturado em torno das interfaces **gateway Telemetria & Property** e Gateway **Commands.**

**Gateway Telemetria & Property** - Esta interface define toda a telemetria relacionada com sensores, localização e informações do dispositivo. A interface também define as capacidades de propriedade gémea do dispositivo, tais como limiares de sensor e intervalos de atualização.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Interface de telemetria e propriedade":::

**Comandos Gateway** - Esta interface organiza todas as capacidades de comando gateway:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Interface de comandos gateway":::

### <a name="rules"></a>Regras

Selecione o separador **Regras** para as regras deste modelo de aplicação. Estas regras são configuradas para notificações por e-mail aos operadores para mais investigações:

**Alerta de roubo de** gateway : Esta regra dispara quando há uma deteção inesperada de luz pelos sensores durante a viagem. Os operadores devem ser notificados imediatamente para investigar possíveis roubos.

**Gateway sem resposta**: Esta regra dispara se o portal não se reportar à nuvem por um período prolongado. O gateway pode não responder devido à bateria fraca, à perda de conectividade ou a danos no dispositivo.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Definições de regras":::

### <a name="jobs"></a>Tarefas

Selecione o **separador Jobs** para ver os trabalhos desta aplicação:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Empregos a gerir":::

Pode usar trabalhos para fazer operações em toda a aplicação. Os trabalhos desta aplicação utilizam comandos de dispositivos e capacidades gémeas para fazer tarefas como desativar sensores específicos em todos os gateways ou modificar o limiar do sensor dependendo do modo de envio e rota:

* É uma operação padrão para desativar sensores de choque durante o transporte do oceano para conservar a bateria ou um limiar de temperatura mais baixo durante o transporte em cadeia fria.

* Os trabalhos permitem-lhe fazer operações a nível do sistema, tais como atualizar firmware nos gateways ou atualizar o contrato de serviço para se manter atualizado nas atividades de manutenção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando  >  **as definições de Aplicação da** Administração e selecione **Delete**.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Limpeza de modelos":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

> [!div class="nextstepaction"]
> [Conceitos logísticos conectados](./architecture-connected-logistics.md)
