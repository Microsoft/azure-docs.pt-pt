---
title: Tutorial do gerenciamento de inventário inteligente do IoT | Microsoft Docs
description: Um tutorial do modelo de aplicativo de gerenciamento de inventário inteligente para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 57c08bdcc0b97d411b1519954cb26374fcb57a23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957294"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Tutorial: implantar e percorrer um modelo de aplicativo de gerenciamento de inventário inteligente

Este tutorial mostra como começar implantando um modelo de aplicativo de **Gerenciamento de inventário inteligente** IOT central. Você aprenderá a implantar o modelo, o que está incluído prontamente e o que você talvez queira fazer em seguida.

Neste tutorial, você aprenderá a, 
* criar aplicativo de gerenciamento de inventário inteligente 
* percorrer o aplicativo 

## <a name="prerequisites"></a>Pré-requisitos
* Nenhum pré-requisito específico necessário para implantar este aplicativo
* É recomendável ter a assinatura do Azure, mas você pode até mesmo tentar sem ela

## <a name="create-smart-inventory-management-application-template"></a>Criar modelo de aplicativo de gerenciamento de inventário inteligente

Você pode criar um aplicativo usando as etapas a seguir
1. Navegue até o site do Azure IoT Central Application Manager. Selecione **criar** na barra de navegação à esquerda e, em seguida, clique na guia **varejo** .

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Selecione a guia **varejo** e selecione **criar aplicativo** em * * gerenciamento de inventário inteligente * *

3. **Criar aplicativo** abrirá o formulário novo aplicativo e preencherá os detalhes solicitados, conforme mostrado abaixo.
   **Nome do aplicativo**: você pode usar o nome sugerido padrão ou inserir seu nome de aplicativo amigável.
   **URL**: você pode usar a URL padrão sugerida ou inserir sua URL de memorização exclusiva e amigável. Em seguida, a configuração padrão será recomendada se você já tiver uma assinatura do Azure ou se você puder começar com uma avaliação gratuita de sete dias e optar por converter o pré-pago a qualquer momento antes da expiração da trilha gratuita.
   **Informações de cobrança**: o diretório, a assinatura do Azure e os detalhes da região são necessários para provisionar os recursos.
   **Criar**: selecione criar na parte inferior da página para implantar seu aplicativo.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

## <a name="walk-through-the-application"></a>percorrer o aplicativo 

### <a name="dashboard"></a>Dashboard 
Depois de implantar com êxito o modelo de aplicativo, o painel padrão é um portal com foco no operador de gerenciamento de inventário inteligente. O Northwind Traders é um provedor de inventário inteligente fictício que gerencia o warehouse com BLE (Bluetooth de baixa energia) e loja de varejo com RFID (identificação de frequência de rádio). Nesse painel, você verá dois gateways diferentes que fornecem telemetria sobre o inventário, juntamente com comandos, trabalhos e ações associados que você pode executar. Este painel é pré-configurado para demonstrar a atividade crítica de operações do dispositivo de gerenciamento de inventário inteligente.
O painel é dividido logicamente entre duas operações diferentes de gerenciamento de dispositivo de gateway, 
   * O depósito é implantado com um gateway BLE fixo & marcas BLE em paletes para rastrear & inventário de rastreamento em um recurso maior
   * A loja de varejo é implementada com um gateway RFID fixo & marcas de RFID em um nível de item para acompanhar e rastrear o estoque em uma tomada de loja
   * Exibir o local do gateway, status & detalhes relacionados 

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Você pode acompanhar facilmente o número total de gateways, ativos e marcas desconhecidas.
   * Você pode executar operações de gerenciamento de dispositivo, como atualizar o firmware, desabilitar o sensor, habilitar o sensor, atualizar o limite do sensor, atualizar intervalos de telemetria & Atualizar contratos de serviço de dispositivo
   * Os dispositivos de gateway podem executar o gerenciamento de estoque sob demanda com uma verificação completa ou incremental.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Clique na guia modelos de dispositivo e você verá o modelo de funcionalidade do gateway. Um modelo de funcionalidade é estruturado em cerca de duas interfaces diferentes de **telemetria de gateway & Propriedade** e **comandos de gateway**

**Propriedade de & telemetria do gateway** – essa interface representa toda a telemetria relacionada a sensores, localização, informações do dispositivo, bem como funcionalidade de Propriedade do dispositivo ", como limites de gateway & intervalos de atualização.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Comandos de gateway** – essa interface organiza todos os recursos de comando do gateway

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regras
Selecione a guia regras para ver duas regras diferentes que existem neste modelo de aplicativo. Essas regras são configuradas para enviar notificações por email aos operadores para investigações adicionais.

**Gateway offline**: essa regra será disparada se o gateway não relatar à nuvem por um período prolongado. O gateway pode não estar respondendo devido ao modo de bateria fraca, perda de conectividade e integridade do dispositivo.

**Marcas desconhecidas**: é essencial rastrear cada RFID & marcas ble associadas a um ativo. Se o gateway estiver detectando muitas marcas desconhecidas, essa é uma indicação dos desafios de sincronização com os aplicativos de fornecimento de marca.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Tarefas
Selecione a guia trabalhos para ver cinco trabalhos diferentes que existem como parte deste modelo de aplicativo: você pode aproveitar o recurso de trabalhos para executar operações em toda a solução. Aqui, os trabalhos de gerenciamento de inventário estão usando os comandos do dispositivo & a funcionalidade de "My" para executar tarefas como,
   * Desabilitando os leitores em todo o gateway
   * modificando o limite de telemetria entre 
   * Execute a verificação de inventário sob demanda em toda a solução.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua o modelo de aplicativo visitando **administração** > **configurações do aplicativo** e clique em **excluir**.

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o conceito de [Gerenciamento de inventário inteligente](./architecture-smart-inventory-management-pnp.md) de gerenciamento de inventário inteligente
* Saiba mais sobre outros [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../core/overview-iot-central-pnp.md)
