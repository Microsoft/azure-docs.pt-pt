---
title: Tutorial da IoT Smart gestão de inventários [ Microsoft Docs
description: Um tutorial de modelo de aplicação de gestão de inventário inteligente para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 430f477422e040e0e7e28fd69a1cbc18bce7d656
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000547"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Tutorial: Implementar e caminhar através de um modelo inteligente de aplicação de gestão de inventário



Este tutorial mostra-lhe como começar implantando um modelo de aplicação de **gestão** de inventário inteligente IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, aprende-se a, 
* criar aplicação inteligente de gestão de inventário 
* andar através da aplicação 

## <a name="prerequisites"></a>Pré-requisitos

* Não são necessários pré-requisitos específicos para implementar esta aplicação
* Recomendado para ter subscrição Azure, mas pode até tentar sem ele

## <a name="create-smart-inventory-management-application-template"></a>Criar modelo de aplicação de gestão de inventário inteligente

Pode criar aplicação usando os seguintes passos

1. Navegue para o site do gestor de aplicações Azure IoT Central. Selecione **Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador Retail.**

    > [!div class="mx-imgBorder"]
    > ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. **Selecione separador Retail** e selecione **Criar app** sob **gestão inteligente de inventário**

3. **A create app** abrirá novo formulário de aplicação e preencherá os detalhes solicitados como mostra abaixo.
   Nome da **aplicação**: pode usar o nome sugerido por defeito ou inserir o seu nome de aplicação amigável.
   **URL:** pode utilizar URL padrão sugerido ou introduzir o seu URL memorável único amigável. Em seguida, a definição predefinida é recomendada se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se em um plano de preços padrão a qualquer momento antes que o trilho livre expire.
   **Faturação Info**: Os detalhes do Diretório, Da Subscrição Azure e da Região são necessários para fornecer os recursos.
   **Criar**: Selecione criar na parte inferior da página para implementar a sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Informação de faturação de gestão de inventário inteligente](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Caminhe pela aplicação 

### <a name="dashboard"></a>Dashboard 

Depois de implementar com sucesso o modelo de aplicação, o seu dashboard padrão é um portal focado no operador de gestão de inventário inteligente. Northwind Trader é um fornecedor de inventário inteligente fictício que gere o armazém com energia bluetooth de baixa energia (BLE) e loja de retalho com identificação de radiofrequência (RFID). Neste painel de instrumentos, você verá dois gateways diferentes fornecendo telemetria sobre inventário juntamente com comandos, empregos e ações associados que você pode realizar. Este dashboard é pré-configurado para mostrar a atividade crítica de operações de gestão de dispositivos de gestão de inventário inteligente.
O painel de instrumentos está logicamente dividido entre duas diferentes operações de gestão de dispositivos gateway, 
   * O armazém é implantado com um gateway ble fixo & etiquetas BLE em paletes para rastrear & rastrear o inventário em uma instalação maior
   * A loja de retalho é implementada com um gateway RFID fixo & etiquetas RFID a nível individual de item para rastrear e rastrear o stock em uma tomada de loja
   * Ver a localização do gateway, estado & detalhes relacionados 

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Pode rastrear facilmente o número total de gateways, etiquetas ativas e desconhecidas.
   * Pode realizar operações de gestão de dispositivos, tais como firmware de atualização, desativar sensor, ativar sensor, atualizar o limiar do sensor, atualizar intervalos de telemetria & atualizar contratos de serviço de dispositivos
   * Os dispositivos Gateway podem realizar uma gestão de inventário a pedido com uma varredura completa ou incremental.

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Clique no separador de modelos do Dispositivo e verá o modelo de capacidade de gateway. Um modelo de capacidade é estruturado em torno de duas interfaces diferentes **Gateway Telemetria & Property** e Gateway **Commands**

**Gateway Telemettry & Property** - Esta interface representa toda a telemetria relacionada com sensores, localização, informação do dispositivo e capacidade de propriedade dupla do dispositivo, tais como limiares de gateway e intervalos de atualização.

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway Commands** - Esta interface organiza todas as capacidades de comando gateway

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regras
Selecione o separador de regras para ver duas regras diferentes que existem neste modelo de aplicação. Estas regras estão configuradas para notificações por e-mail aos operadores para mais investigações.

**Gateway offline**: Esta regra será desencadeada se o portal não reportar à nuvem por um período prolongado. O gateway pode não responder devido ao modo de bateria baixa, perda de conectividade, saúde do dispositivo.

**Etiquetas desconhecidas**: É fundamental rastrear todas as tags RFID & BLE associadas a um ativo. Se o portal está a detetar demasiadas etiquetas desconhecidas, é uma indicação de desafios de sincronização com aplicações de sourcing de etiquetas.

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Tarefas
Selecione o separador de empregos para ver cinco empregos diferentes que existem como parte deste modelo de aplicação: Pode utilizar a funcionalidade de empregos para realizar operações a nível de solução. Aqui os trabalhos de gestão de inventário estão a usar os comandos do dispositivo e a capacidade gémea para executar tarefas como, tais como,
   * leitores incapacitantes em toda a porta de entrada
   * modificando o limiar de telemetria entre 
   * realizar a varredura de inventário a pedido em toda a solução.

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando**as definições** de Aplicação de **Administração** > e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o conceito inteligente de gestão de inventário inteligente [de gestão](./architecture-smart-inventory-management.md) de inventário
* Saiba mais sobre outros [modelos de retalho IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../core/overview-iot-central.md)
