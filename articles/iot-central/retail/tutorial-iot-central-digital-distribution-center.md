---
title: Tutorial do IoT Digital Distribution Center | Microsoft Docs
description: Um tutorial do modelo de aplicação do centro de distribuição digital para ioT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 9d7c4d2afa8eaf197ec05bcb0aed654153fb8f0d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831302"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação de centro de distribuição digital

Este tutorial mostra-lhe como começar com a implementação de um modelo de aplicação **do centro de distribuição digital** IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, aprende-se a, 

> [!div class="checklist"]
> Criar aplicação de centro de distribuição digital Caminhe pela aplicação 

## <a name="prerequisites"></a>Pré-requisitos
* Não são necessários pré-requisitos específicos para implementar esta aplicação
* Recomendado para ter subscrição Azure, mas pode até tentar sem ele

## <a name="create-digital-distribution-center-application-template"></a>Crie o modelo de aplicação do centro de distribuição digital

Pode criar uma aplicação utilizando os seguintes passos

1. Navegue para o site do gestor de aplicações Azure IoT Central. **Selecione Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador 'Varejo'.**

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Modelo de aplicação de centro de distribuição digital":::
1. Selecione o separador **de varejo** e selecione **Criar app** sob **aplicação digital do centro de distribuição**

1. **Criar aplicação** abrirá novo formulário de aplicação e preencherá os detalhes solicitados como mostra abaixo.
   **Nome da aplicação**: pode utilizar o nome sugerido por defeito ou introduzir o nome da sua aplicação amigável.
   **URL**: pode utilizar URL padrão sugerido ou inserir o seu AMIGÁVEL URL memorável único. Em seguida, recomenda-se a definição predefinida se já tiver uma Subscrição Azure. Você pode começar com um plano de preços de teste gratuito de 7 dias e optar por converter-se a um plano de preços padrão a qualquer momento antes que o trilho gratuito expire.
   **Informações de Faturação**: Os dados do Diretório, da Subscrição Azure e da Região são necessários para a disponibilização dos recursos.
   **Criar:** Selecione criar na parte inferior da página para implementar a sua aplicação.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Screenshot mostrando como criar uma aplicação a partir do modelo de aplicação do centro de distribuição digital":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Screenshot mostrando as opções de faturação quando cria a aplicação":::

## <a name="walk-through-the-application-dashboard"></a>Caminhe pelo painel de aplicações 

Depois de implementar com sucesso o modelo de aplicação, o seu painel de instrumentos padrão é um portal focado no operador do centro de distribuição. A Northwind Trader é uma empresa fictícia de soluções de centros de distribuição que gere sistemas transportadores. 

Neste painel de instrumentos, verá um gateway e uma câmara atuando como um dispositivo IoT. Gateway está fornecendo telemetria sobre pacotes como válido, inválido, não identificado e tamanho, juntamente com propriedades gémeas do dispositivo associado. Todos os comandos a jusante são executados em dispositivos IoT, como uma câmara. Este painel está pré-configurado para mostrar a atividade crítica do dispositivo do centro de distribuição.

O dashboard está logicamente organizado para mostrar as capacidades de gestão do dispositivo do gateway IoT Azure e do dispositivo IoT.  
   * Pode executar tarefas de controlo de comando de gateway &
   * Gerencie todas as câmaras que fazem parte da solução. 

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando o painel de distribuição digital do centro de distribuição](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Modelo de dispositivo

Clique no separador modelos do dispositivo e verá o modelo de capacidade do gateway. Um modelo de capacidade é estruturado em torno de duas interfaces diferentes **Câmara** e **Gateway de Distribuição Digital**

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando o modelo de dispositivo de gateway de distribuição digital na aplicação](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Câmara** - Esta interface organiza todas as capacidades de comando específicas da câmara 

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a interface da câmara no modelo de dispositivo de gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Gateway de Distribuição Digital** - Esta interface representa toda a telemetria proveniente da câmara, propriedades gémeas do dispositivo definido na nuvem e informações de gateway.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a interface de gateway de distribuição digital no modelo de dispositivo de gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Comandos Gateway
Esta interface organiza todas as capacidades de comando gateway

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a interface de comandos gateway no modelo de dispositivo de gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regras
Selecione o separador regras para ver duas regras diferentes que existem neste modelo de aplicação. Estas regras estão configuradas para notificações por e-mail aos operadores para mais investigações.

 **Alerta de pacotes inválidos -** Esta regra é ativada quando a câmara deteta um elevado número de pacotes inválidos que fluem através do sistema transportador.
 
**Pacote grande** - Esta regra será ativada se a câmara detetar um pacote enorme que não pode ser inspecionado para obter a qualidade. 

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a lista de regras na aplicação do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Tarefas
Selecione o separador de empregos para ver cinco empregos diferentes que existem como parte deste modelo de aplicação: Você pode aproveitar o recurso de empregos para executar operações em toda a solução. Aqui, os trabalhos dos centros de distribuição digital estão a usar os comandos do dispositivo & capacidade gémea para executar tarefas como,
   * calibração da câmara antes de iniciar a deteção de pacotes 
   * atualizar periodicamente firmware de câmara
   * modificando o intervalo de telemetria para gerir o upload de dados

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando a lista de empregos na aplicação do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Limpar os recursos
Se não continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando  >  **as definições de Aplicação da** Administração e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando como apagar a aplicação quando terminar com ela](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a arquitetura de soluções de centro de distribuição digital:

> [!div class="nextstepaction"]
> [conceito de centro de distribuição digital](./architecture-digital-distribution-center.md)
