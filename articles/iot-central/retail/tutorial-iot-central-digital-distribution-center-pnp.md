---
title: Tutorial do centro de distribuição digital de IoT | Microsoft Docs
description: Um tutorial do modelo de aplicativo do centro de distribuição digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d5b132be2a3719f746af253439f1d1bdff1c3c40
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965069"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Tutorial: implantar e percorrer um modelo de aplicativo do centro de distribuição digital

Este tutorial mostra como começar implantando um modelo de aplicativo do **centro de distribuição digital** IOT central. Você aprenderá a implantar o modelo, o que está incluído prontamente e o que você talvez queira fazer em seguida.

Neste tutorial, você aprenderá a, 
* Criar aplicativo do centro de distribuição digital 
* percorrer o aplicativo 

## <a name="prerequisites"></a>Pré-requisitos
* Nenhum pré-requisito específico necessário para implantar este aplicativo
* É recomendável ter a assinatura do Azure, mas você pode até mesmo tentar sem ela

## <a name="create-digital-distribution-center-application-template"></a>Criar modelo de aplicativo do centro de distribuição digital

Você pode criar um aplicativo usando as etapas a seguir

1. Navegue até o site do Azure IoT Central Application Manager. Selecione **criar** na barra de navegação à esquerda e, em seguida, clique na guia **varejo** .

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Selecione a guia **varejo** e selecione **criar aplicativo** em * * aplicativo do centro de distribuição digital * *

3. **Criar aplicativo** abrirá o formulário novo aplicativo e preencherá os detalhes solicitados, conforme mostrado abaixo.
   **Nome do aplicativo**: você pode usar o nome sugerido padrão ou inserir seu nome de aplicativo amigável.
   **URL**: você pode usar a URL padrão sugerida ou inserir sua URL exclusiva e fácil de memorizar. Em seguida, a configuração padrão é recomendada se você já tiver uma assinatura do Azure. Caso contrário, você pode começar com uma avaliação gratuita de sete dias e optar por converter pré-pago a qualquer momento antes da expiração da trilha gratuita.
   **Informações de cobrança**: o diretório, a assinatura do Azure e os detalhes da região são necessários para provisionar os recursos.
   **Criar**: selecione criar na parte inferior da página para implantar seu aplicativo.

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application-dashboard"></a>Percorrer o painel do aplicativo 

Depois de implantar com êxito o modelo de aplicativo, o painel padrão é um portal centrado no operador do centro de distribuição. A Northwind Traders é um provedor de soluções fictícia do centro de distribuição que gerencia sistemas de transmissão. 

Nesse painel, você verá um gateway e uma câmera atuando como um dispositivo IoT. O gateway está fornecendo telemetria sobre pacotes como válido, inválido, não identificado e com tamanho junto com as propriedades de conjunto de informações do dispositivo associado. Todos os comandos downstream são executados em dispositivos IoT, como uma câmera. Este painel é pré-configurado para demonstrar a atividade de operações de dispositivo do centro de distribuição crítico.

O painel é organizado logicamente para mostrar os recursos de gerenciamento de dispositivos do gateway IoT do Azure e do dispositivo IoT.  
   * Você pode executar o comando de gateway & tarefas de controle
   * Gerencie todas as câmeras que fazem parte da solução. 

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Modelo de dispositivo

Clique na guia modelos de dispositivo e você verá o modelo de funcionalidade do gateway. Um modelo de funcionalidade é estruturado em uma **câmera** de interfaces diferentes e **Gateway de distribuição digital**

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Câmera** -essa interface organiza todos os recursos de comando específicos à câmera 

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Gateway de distribuição digital** -essa interface representa toda a telemetria proveniente da câmera, propriedades de dispositivo e informações de gateway definidas pela nuvem.

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Comandos de gateway
Essa interface organiza todos os recursos de comando do gateway

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regras
Selecione a guia regras para ver duas regras diferentes que existem neste modelo de aplicativo. Essas regras são configuradas para enviar notificações por email aos operadores para investigações adicionais.

 **Alerta de muitos pacotes inválidos** -essa regra é disparada quando a câmera detecta um grande número de pacotes inválidos fluindo pelo sistema do transportador.
 
**Pacote grande** – essa regra será disparada se a câmera detectar um pacote enorme que não pode ser inspecionado quanto à qualidade. 

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Tarefas
Selecione a guia trabalhos para ver cinco trabalhos diferentes que existem como parte deste modelo de aplicativo: você pode aproveitar o recurso de trabalhos para executar operações em toda a solução. Aqui, os trabalhos do centro de distribuição digital estão usando os comandos do dispositivo & a funcionalidade de "My" para executar tarefas como,
   * câmera de calibragem antes de iniciar a detecção de pacote 
   * atualizando periodicamente o firmware da câmera
   * modificando o intervalo de telemetria para gerenciar o carregamento de dados

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Limpar recursos
Se você não for continuar a usar este aplicativo, exclua o modelo de aplicativo visitando **administração** > **configurações do aplicativo** e clique em **excluir**.

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura da solução do centro de distribuição digital [conceito do centro de distribuição digital](./architecture-digital-distribution-center-pnp.md)
* Saiba mais sobre outros [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../core/overview-iot-central-pnp.md)
