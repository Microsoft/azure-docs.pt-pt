---
title: Tutorial do IoT Digital Distribution Center [ IoT Digital Distribution Center ] Microsoft Docs
description: Um tutorial do modelo de aplicação do centro de distribuição digital para ioT central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 90d171e3353061ffd855d9132c8a7fe40116e3cd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984110"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação de centro de distribuição digital

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra-lhe como começar implantando um modelo de aplicação do centro de **distribuição digital** IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, aprende-se a, 
* Criar aplicação de centro de distribuição digital 
* andar através da aplicação 

## <a name="prerequisites"></a>Pré-requisitos
* Não são necessários pré-requisitos específicos para implementar esta aplicação
* Recomendado para ter subscrição Azure, mas pode até tentar sem ele

## <a name="create-digital-distribution-center-application-template"></a>Criar modelo de aplicação do centro de distribuição digital

Pode criar aplicação usando os seguintes passos

1. Navegue para o site do gestor de aplicações Azure IoT Central. Selecione **Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador Retail.**

    > [!div class="mx-imgBorder"]
    > ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. **Selecione separador Retail** e selecione **Criar app** sob **aplicação de centro** de distribuição digital

3. **A create app** abrirá novo formulário de aplicação e preencherá os detalhes solicitados como mostra abaixo.
   Nome da **aplicação**: pode usar o nome sugerido por defeito ou inserir o seu nome de aplicação amigável.
   **URL:** pode utilizar URL padrão sugerido ou introduzir o seu URL memorável único amigável. Em seguida, a definição predefinida é recomendada se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se em um plano de preços padrão a qualquer momento antes que o trilho livre expire.
   **Faturação Info**: Os detalhes do Diretório, Da Subscrição Azure e da Região são necessários para fornecer os recursos.
   **Criar**: Selecione criar na parte inferior da página para implementar a sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![informação de faturação de distribuição digital](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Caminhe pelo painel de aplicações 

Depois de implementar com sucesso o modelo de aplicação, o seu dashboard padrão é um portal focado no operador do centro de distribuição. Northwind Trader é um fornecedor fictício de soluções de centros de distribuição que gere sistemas transportadores. 

Neste painel, verá um portal e uma câmara a funcionar como um dispositivo IoT. Gateway está fornecendo telemetria sobre pacotes como válido, inválido, não identificado e tamanho, juntamente com propriedades gémeas de dispositivo associados. Todos os comandos a jusante são executados em dispositivos IoT, como uma câmara. Este painel de instrumentos é pré-configurado para mostrar a atividade de operações do dispositivo do centro de distribuição crítica.

O dashboard é logicamente organizado para mostrar as capacidades de gestão do dispositivo de gestão do dispositivo Azure IoT e IoT.  
   * Pode executar tarefas de comando e controlo de gateway
   * Gerencie todas as câmaras que fazem parte da solução. 

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Modelo de dispositivo

Clique no separador de modelos do Dispositivo e verá o modelo de capacidade de gateway. Um modelo de capacidade é estruturado em torno de duas interfaces diferentes **Camera** e **Digital Distribution Gateway**

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Camera** - Esta interface organiza todas as capacidades de comando específicas da câmara 

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Gateway** de distribuição digital - Esta interface representa toda a telemetria proveniente da câmara, propriedades gémeas definidas em nuvem e informações de gateway.

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Comandos Gateway
Esta interface organiza todas as capacidades de comando gateway

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regras
Selecione o separador de regras para ver duas regras diferentes que existem neste modelo de aplicação. Estas regras estão configuradas para notificações por e-mail aos operadores para mais investigações.

 **Alerta de demasiados pacotes inválidos** - Esta regra é desencadeada quando a câmara deteta um elevado número de embalagens inválidas que fluem através do sistema transportador.
 
**Pacote grande** - Esta regra será desencadeada se a câmara detetar uma embalagem enorme que não pode ser inspecionada pela qualidade. 

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Tarefas
Selecione o separador de empregos para ver cinco empregos diferentes que existem como parte deste modelo de aplicação: Pode aproveitar a funcionalidade de empregos para realizar operações a nível de solução. Aqui os trabalhos do centro de distribuição digital estão a usar os comandos do dispositivo e a capacidade de gémeos para executar tarefas como, tais como,
   * calibrando a câmara antes de começar a deteção do pacote 
   * atualizando periodicamente firmware de câmara
   * modificando o intervalo de telemetria para gerir o upload de dados

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Limpar recursos
Se não vai continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando **as definições** de > Aplicação **da Administração** e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![Centro de Distribuição Digital](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [conceito](./architecture-digital-distribution-center-pnp.md) de centro de distribuição digital de centro de distribuição digital de arquitetura digital
* Saiba mais sobre outros [modelos de retalho IoT Central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../preview/overview-iot-central.md)
