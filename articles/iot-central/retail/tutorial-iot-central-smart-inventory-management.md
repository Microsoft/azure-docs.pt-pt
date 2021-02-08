---
title: Tutorial da IoT Smart gestão de inventário | Microsoft Docs
description: Um tutorial do modelo de aplicação de gestão de inventário inteligente para ioT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 2b26b4390cb19b832349b49d4e95a5355caed594
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820301"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação de gestão de inventário inteligente

Este tutorial mostra-lhe como começar com a implementação de um modelo de aplicação **de gestão de inventário inteligente** IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, aprende-se a,

> [!div class="checklist"]
> criar aplicação de gestão de inventário inteligente caminhar através da aplicação 

## <a name="prerequisites"></a>Pré-requisitos

* Não são necessários pré-requisitos específicos para implementar esta aplicação
* Recomendado para ter subscrição Azure, mas pode até tentar sem ele

## <a name="create-smart-inventory-management-application-template"></a>Crie modelo de aplicação de gestão de inventário inteligente

Pode criar uma aplicação utilizando os seguintes passos

1. Navegue para o site do gestor de aplicações Azure IoT Central. **Selecione Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador 'Varejo'.**

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Screenshot mostrando como selecionar o modelo de aplicação de gestão de inventário inteligente":::

2. Selecione o separador **De Varejo** e selecione **Criar app** sob **gestão de inventário inteligente**

3. **Criar aplicação** abrirá novo formulário de aplicação e preencherá os detalhes solicitados como mostra abaixo.
    **Nome da aplicação**: pode utilizar o nome sugerido por defeito ou introduzir o nome da sua aplicação amigável.
    **URL**: pode utilizar URL padrão sugerido ou inserir o seu AMIGÁVEL URL memorável único. Em seguida, recomenda-se a definição predefinida se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se a um plano de preços padrão a qualquer momento antes que o trilho gratuito expire.
    **Informações de Faturação**: Os dados do Diretório, da Subscrição Azure e da Região são necessários para a disponibilização dos recursos.
    **Criar:** Selecione criar na parte inferior da página para implementar a sua aplicação.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Screenshot mostrando como criar uma app a partir do modelo de aplicação de gestão de inventário inteligente":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Screenshot mostrando as opções de faturação quando cria a aplicação":::

## <a name="walk-through-the-application"></a>Caminhe pela aplicação 

### <a name="dashboard"></a>Dashboard 

Depois de implementar com sucesso o modelo de aplicação, o seu painel de instrumentos padrão é um portal focado no operador de gestão de inventário inteligente. Northwind Trader é um fornecedor de inventário inteligente fictício que gere armazém com baixa energia Bluetooth (BLE) e loja de retalho com identificação de radiofrequência (RFID). Neste painel de instrumentos, você verá dois gateways diferentes fornecendo telemetria sobre inventário, juntamente com comandos, empregos e ações associados que você pode executar. Este dashboard está pré-configurado para mostrar a atividade crítica de gestão de dispositivos de gestão de inventário inteligente.
O painel está logicamente dividido entre duas diferentes operações de gestão de dispositivos de gateway, 
   * O armazém é implantado com uma porta BLE fixa & etiquetas BLE em paletes para rastrear & inventário de vestígios em uma instalação maior
   * A loja de retalho é implementada com uma porta de entrada RFID fixa & etiquetas RFID individualmente a nível de um item para rastrear e rastrear o stock em um outlet de loja
   * Ver a localização do gateway, estado & detalhes relacionados 

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a metade superior do quadro de gestão de inventário inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Você pode rastrear facilmente o número total de gateways, etiquetas ativas e desconhecidas.
   * Pode executar operações de gestão de dispositivos como firmware de atualização, sensor de desativação, sensor de ativação, limite de sensor de atualização, intervalos de telemetria de atualização & contratos de serviços de atualização de dispositivos
   * Os dispositivos Gateway podem executar a gestão do inventário a pedido com uma varredura completa ou incremental.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a metade inferior do quadro de gestão de inventário inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Clique no separador modelos do dispositivo e verá o modelo de capacidade do gateway. Um modelo de capacidade é estruturado em torno de duas interfaces diferentes **Gateway Telemetria & Property** e Gateway **Commands**

**Gateway Telemetria & Property** - Esta interface representa toda a telemetria relacionada com sensores, localização, informações do dispositivo e capacidade de propriedade dupla do dispositivo, tais como limiares de gateway e intervalos de atualização.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando o modelo do dispositivo de gateway de inventário na aplicação](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Comandos Gateway** - Esta interface organiza todas as capacidades de comando gateway

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a interface de comandos gateway no modelo do dispositivo de gateway de inventário](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regras
Selecione o separador regras para ver duas regras diferentes que existem neste modelo de aplicação. Estas regras estão configuradas para notificações por e-mail aos operadores para mais investigações.

**Gateway offline**: Esta regra irá desencadear se o portal não se reportar à nuvem por um período prolongado. Gateway pode não responder devido ao baixo modo de bateria, perda de conectividade, saúde do dispositivo.

**Etiquetas desconhecidas**: É fundamental rastrear todas as tags RFID & BLE associadas a um ativo. Se o gateway está a detetar demasiadas tags desconhecidas, é uma indicação de desafios de sincronização com aplicações de sourcing de etiquetas.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a lista de regras na aplicação de gestão de inventário inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Tarefas
Selecione o separador de empregos para ver cinco empregos diferentes que existem como parte deste modelo de aplicação: Você pode usar o recurso de emprego para executar operações em toda a solução. Aqui os trabalhos de gestão de inventário estão a usar os comandos do dispositivo e a capacidade dupla para executar tarefas como,
   * leitores incapacitantes em toda a porta de entrada
   * modificação do limiar de telemetria entre 
   * executar a procura de inventário em toda a solução.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a lista de empregos na aplicação de gestão de inventário inteligente](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando  >  **as definições de Aplicação da** Administração e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando como apagar a aplicação quando terminar com ela](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre gestão inteligente de inventário 
> [!div class="nextstepaction"]
> [Conceito inteligente de gestão de inventário](./architecture-smart-inventory-management.md)
* Saiba mais sobre outros [modelos de varejo IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral do IoT Central](../core/overview-iot-central.md)