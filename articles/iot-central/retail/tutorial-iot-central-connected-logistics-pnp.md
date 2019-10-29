---
title: Tutorial de logística conectada por IoT | Microsoft Docs
description: Um tutorial do modelo de aplicativo de logística conectado para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 0fc9291fcb17d6200b32bb04a0a3ffeed5dea713
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027340"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Tutorial: implantar e percorrer um modelo de aplicativo de logística conectado

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como começar implantando um modelo de aplicativo de **logística IOT central conectado** . Você aprenderá a implantar o modelo, o que está incluído prontamente e o que você talvez queira fazer em seguida.

Neste tutorial, você aprenderá a, 
* criar aplicativo de logística conectado 
* percorrer o aplicativo 

## <a name="prerequisites"></a>Pré-requisitos
* Nenhum pré-requisito específico necessário para implantar este aplicativo
* É recomendável ter a assinatura do Azure, mas você pode até mesmo tentar sem ela

## <a name="create-connected-logistics-application-template"></a>Criar modelo de aplicativo de logística conectado
Você pode criar um aplicativo usando as etapas a seguir
1. Navegue até o site do Azure IoT Central Application Manager. Selecione **criar** na barra de navegação à esquerda e, em seguida, clique na guia **varejo** .

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Selecione **criar aplicativo** em **aplicativo de logística conectado**

3. **Criar aplicativo** abrirá o formulário novo aplicativo e preencherá os detalhes solicitados, conforme mostrado abaixo.
   * **Nome do aplicativo**: você pode usar o nome sugerido padrão ou inserir seu nome de aplicativo amigável.
   * **URL**: você pode usar a URL padrão sugerida ou inserir sua URL de memorização exclusiva e amigável. Em seguida, a configuração padrão é recomendada se você já tiver uma assinatura do Azure. caso contrário, você pode começar com uma avaliação gratuita de sete dias e optar por converter o pré-pago a qualquer momento antes da expiração da trilha gratuita.
   * **Informações de cobrança**: o diretório, a assinatura do Azure e os detalhes da região são necessários para provisionar os recursos.
   * **Criar** : selecione criar na parte inferior da página para implantar seu aplicativo.

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

## <a name="walk-through-the-application"></a>percorrer o aplicativo 

## <a name="dashboard"></a>Dashboard

Depois de implantar com êxito o modelo de aplicativo, o painel padrão é um portal centrado no operador de logística. A Northwind Traders é um provedor de logística fictícia Gerenciando a frota de carga no oceano e no terreno. Nesse painel, você verá dois gateways diferentes fornecendo telemetria sobre remessas, juntamente com comandos, trabalhos e ações associados que você pode executar. Este painel é pré-configurado para demonstrar a atividade de operações de dispositivo de logística crítica.
O painel é dividido logicamente entre duas operações diferentes de gerenciamento de dispositivo de gateway, 
   * Rota de logística para remessa de caminhão e detalhes de local da remessa do oceano é um elemento essencial para todo o transporte de várias modalidades
   * Exibir o status do gateway & informações relevantes 

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Você pode acompanhar facilmente o número total de gateways, ativos e marcas desconhecidas.
   * Você pode executar operações de gerenciamento de dispositivo, como atualizar o firmware, desabilitar o sensor, habilitar o sensor, atualizar o limite do sensor, atualizar intervalos de telemetria & Atualizar contratos de serviço de dispositivo.
   * Exibir consumo de bateria do dispositivo

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo

Clique na guia modelos de dispositivo e você verá o modelo de funcionalidade do gateway. Um modelo de funcionalidade é estruturado em cerca de duas interfaces diferentes de **telemetria de gateway & Propriedade** e **comandos de gateway**

**Propriedade de & telemetria de gateway** – essa interface representa toda a telemetria relacionada a sensores, informações de local e dispositivo, bem como a funcionalidade de Propriedade do dispositivo ", como limites de sensor & intervalos de atualização.

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Comandos de gateway** – essa interface organiza todos os recursos de comando do gateway

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regras
Selecione a guia regras para ver duas regras diferentes que existem neste modelo de aplicativo. Essas regras são configuradas para enviar notificações por email aos operadores para investigações adicionais.
 
**Alerta de roubo de gateway**: essa regra é disparada quando há detecção de luz inesperada pelos sensores durante a jornada. Os operadores precisam ser notificados ASAP para investigar possíveis roubos.
 
**Gateway sem resposta**: essa regra será disparada se o gateway não relatar à nuvem por um período prolongado. O gateway pode não estar respondendo devido ao modo de bateria fraca, perda de conectividade e integridade do dispositivo.

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Tarefas
Selecione a guia trabalhos para ver cinco trabalhos diferentes que existem como parte deste modelo de aplicativo:

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Você pode aproveitar o recurso de trabalhos para executar operações em toda a solução. Aqui, os trabalhos estão usando os comandos do dispositivo & a funcionalidade de "My" para executar tarefas como desabilitar sensores específicos em todo o gateway ou modificar o limite do sensor dependendo do modo de remessa e da rota. 
   * É uma operação padrão para desabilitar os sensores de choque durante a remessa do oceano para conservar a bateria ou diminuir o limite de temperatura durante o transporte de cadeia Cold. 
 
   * Os trabalhos permitem que você execute operações em todo o sistema, como atualizar o firmware nos gateways ou atualizar o contrato de serviço para permanecer atualizado em atividades de manutenção.

## <a name="clean-up-resources"></a>Limpar recursos
Se você não for continuar a usar este aplicativo, exclua o modelo de aplicativo visitando **administração** > **configurações do aplicativo** e clique em **excluir**.

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [conceito de logística conectada](./architecture-connected-logistics-pnp.md)
* Saiba mais sobre outros [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre a [visão geral de IOT central](../core/overview-iot-central-pnp.md)
