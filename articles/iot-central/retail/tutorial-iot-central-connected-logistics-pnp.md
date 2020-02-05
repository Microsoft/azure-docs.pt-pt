---
title: Tutorial da Logística Conectada IoT [ Logística] Microsoft Docs
description: Um tutorial de modelo de aplicação logística conectada para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d7050cfdae2c726a3a5bd44b4399792a400572a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025525"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação logística conectado



Este tutorial mostra-lhe como começar implantando um modelo de aplicação **logística conectada** IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, aprenderá como,

* criar aplicação logística conectada
* andar através da aplicação 

## <a name="prerequisites"></a>Pré-requisitos

* Não são necessários pré-requisitos específicos para implementar esta aplicação
* Recomendado para ter subscrição Azure, mas pode até tentar sem ele

## <a name="create-connected-logistics-application-template"></a>Criar modelo de aplicação logística conectada

Pode criar aplicação usando os seguintes passos

1. Navegue para o site do gestor de aplicações Azure IoT Central. Selecione **Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador Retail.**

    > [!div class="mx-imgBorder"]
    > ![](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png) de painel de logística conectado

2. Selecione **Criar app** sob **aplicação logística conectada**

3. **A create app** abrirá novo formulário de aplicação e preencherá os detalhes solicitados como mostra abaixo.
   * Nome da **aplicação**: pode usar o nome sugerido por defeito ou inserir o seu nome de aplicação amigável.
   * **URL:** pode utilizar URL padrão sugerido ou introduzir o seu URL memorável único amigável. Em seguida, a definição predefinida é recomendada se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se em um plano de preços padrão a qualquer momento antes que o trilho livre expire.
   * **Faturação Info**: Os detalhes do Diretório, Da Subscrição Azure e da Região são necessários para fornecer os recursos.
   * **Criar**: Selecione criar na parte inferior da página para implementar a sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png) de painel de logística conectado

    > [!div class="mx-imgBorder"]
    > ![connected Logistics info info](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>andar através da aplicação 

## <a name="dashboard"></a>Dashboard

Depois de implementar com sucesso o modelo de aplicação, o seu dashboard padrão é um portal focado no operador logístico conectado. Northwind Trader é um fornecedor de logística fictícia que gere a frota de carga no oceano e na terra. Neste painel, você verá dois gateways diferentes fornecendo telemetria sobre envios juntamente com comandos associados, empregos e ações que você pode fazer. Este dashboard é pré-configurado para mostrar a atividade crítica de operações de dispositivos logísticos.
O painel de instrumentos está logicamente dividido entre duas diferentes operações de gestão de dispositivos gateway, 
   * A rota logística para envio de camiões e detalhes de localização do envio do oceano é um elemento essencial para todo o transporte multimodal
   * Ver o estado do gateway e informações relevantes 

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png) de painel de logística conectado

   * Pode rastrear facilmente o número total de gateways, etiquetas ativas e desconhecidas.
   * Pode efetuar operações de gestão de dispositivos, tais como firmware de atualização, desativar o sensor, ativar o sensor, atualizar o limiar do sensor, atualizar intervalos de telemetria e atualizar contratos de serviço de dispositivos.
   * Ver o consumo de bateria do dispositivo

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png) de painel de logística conectado

## <a name="device-template"></a>Modelo de dispositivo

Clique no separador de modelos do Dispositivo e verá o modelo de capacidade de gateway. Um modelo de capacidade é estruturado em torno de duas interfaces diferentes **Gateway Telemetria & Property** e **Gateway Commands**

**Gateway Telemettry & Property** - Esta interface representa toda a telemetria relacionada com sensores, localização e informações do dispositivo, bem como capacidade de propriedade dupla de dispositivos, tais como limiares de sensor e intervalos de atualização.

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png) de painel de logística conectado

**Gateway Commands** - Esta interface organiza todas as capacidades de comando gateway

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png) de painel de logística conectado

## <a name="rules"></a>Regras
Selecione o separador de regras para ver duas regras diferentes que existem neste modelo de aplicação. Estas regras estão configuradas para notificações por e-mail aos operadores para mais investigações.
 
**Alerta de roubo**de gateway : Esta regra é desencadeada quando há uma deteção inesperada de luz pelos sensores durante a viagem. Os operadores precisam de ser notificados o mais rápido possível para investigar potenciais roubos.
 
**Gateway sem resposta**: Esta regra será desencadeada se o portal não reportar à nuvem por um período prolongado. O gateway pode não responder devido ao modo de bateria baixa, perda de conectividade, saúde do dispositivo.

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png) de painel de logística conectado

## <a name="jobs"></a>Tarefas
Selecione o separador de empregos para ver cinco empregos diferentes que existem como parte deste modelo de aplicação:

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png) de painel de logística conectado

Pode utilizar a funcionalidade de trabalho para fazer operações a nível de solução. Aqui os trabalhos estão a usar os comandos do dispositivo e a capacidade gémea para fazer tarefas como desativar sensores específicos em toda a porta de entrada ou modificar o limiar do sensor dependendo do modo de envio e da rota. 
   * É uma operação padrão para desativar os sensores de choque durante o transporte do oceano para conservar a bateria ou diminuir o limiar de temperatura durante o transporte em cadeia fria. 
 
   * Os trabalhos permitem-lhe fazer operações em todo o sistema, tais como atualizar firmware nos gateways ou atualizar contrato de serviço para se manter atualizado em atividades de manutenção.

## <a name="clean-up-resources"></a>Limpar recursos
Se não vai continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando **as definições** de > Aplicação **da Administração** e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png) de painel de logística conectado

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [o conceito de logística conectado](./architecture-connected-logistics-pnp.md)
* Saiba mais sobre outros [modelos de retalho IoT Central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre [a visão geral da IoT Central](../core/overview-iot-central.md)
