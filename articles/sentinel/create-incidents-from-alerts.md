---
title: Criar incidentes de alertas no Azure Sentinel | Microsoft Docs
description: Saiba como criar incidentes de alertas no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 1acef92ed5de86d4526d8b5c4bcf338b341c50f7
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241271"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Crie incidentes automaticamente com os alertas de segurança da Microsoft

Alertas disparados nas soluções de segurança da Microsoft que estão conectadas ao Azure Sentinel, como Microsoft Cloud App Security e proteção avançada contra ameaças do Azure, não criam automaticamente incidentes no Azure Sentinel. Por padrão, quando você conecta uma solução da Microsoft ao Azure Sentinel, qualquer alerta gerado nesse serviço será armazenado como dados brutos no Azure Sentinel, na tabela alerta de segurança em seu espaço de trabalho do Azure Sentinel. Em seguida, você pode usar esses dados como qualquer outro dado bruto que você conecte ao sentinela.

Você pode configurar facilmente o Sentinela do Azure para criar incidentes automaticamente toda vez que um alerta for disparado em uma solução de segurança da Microsoft conectada, seguindo as instruções neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve [conectar as soluções de segurança da Microsoft](connect-data-sources.md#data-connection-methods) para habilitar a criação de incidentes de alertas do serviço de segurança.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Usando regras analíticas de criação de incidentes de segurança da Microsoft

Use as regras internas disponíveis no Azure Sentinel para escolher quais soluções de segurança da Microsoft conectadas devem criar incidentes do Azure Sentinel automaticamente em tempo real. Você também pode editar as regras para definir opções mais específicas para filtrar quais dos alertas gerados pela solução de segurança da Microsoft devem criar incidentes no Azure Sentinel. Por exemplo, você pode optar por criar incidentes do Azure Sentinel automaticamente somente de alertas da central de segurança do Azure de alta gravidade.

1. No portal do Azure em Azure Sentinel, selecione **análise**.

1. Selecione a guia **modelos de regra** para ver todas as regras analíticas internas.

    ![Modelos de regra](media/incidents-from-alerts/rule-templates.png)

1. Escolha o modelo de regra do **Microsoft Security** Analytics que você deseja usar e clique em **criar regra**.

    ![Regra de análise de segurança](media/incidents-from-alerts/security-analytics-rule.png)

1. Você pode modificar os detalhes da regra e optar por filtrar os alertas que criarão incidentes pela severidade do alerta ou pelo texto contido no nome do alerta.  
      
    Por exemplo, se você escolher **central de segurança do Azure** no campo **serviço de segurança da Microsoft** e escolher **alto** no campo **Filtrar por severidade** , somente os alertas da central de segurança do Azure de severidade alta criarão incidentes automaticamente no Azure Sentinel.  

    ![Assistente de criação de regra](media/incidents-from-alerts/create-rule-wizard.png)

1. Você também pode criar uma nova regra de **segurança da Microsoft** que filtra alertas de diferentes serviços de segurança da Microsoft clicando em **+ criar** e selecionando **regra de criação de incidentes da Microsoft**.

    ![Regra de criação de incidentes](media/incidents-from-alerts/incident-creation-rule.png)

  Você pode criar mais de uma regra de análise de **segurança da Microsoft** por tipo de serviço de **segurança da Microsoft** . Isso não cria incidentes duplicados, pois cada regra é usada como um filtro. Mesmo que um alerta corresponda a mais de uma regra analítica de **segurança da Microsoft** , ele cria apenas um incidente do Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Habilitar a geração de incidentes automaticamente durante a conexão
 Ao conectar uma solução de segurança da Microsoft, você pode selecionar se deseja que os alertas da solução de segurança gerem automaticamente incidentes no Azure Sentinel automaticamente.

1. Conecte uma fonte de dados da solução de segurança da Microsoft. 

   ![Gerar incidentes de segurança](media/incidents-from-alerts/generate-security-incidents.png)

1. Em **criar incidentes** , selecione **habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Você pode editar essa regra em **análise** e em **regras ativas**.

## <a name="next-steps"></a>Passos seguintes

- Para começar a usar o Azure Sentinel, você precisa de uma assinatura para Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como integrar [seus dados ao Azure Sentinel](quickstart-onboard.md)e [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
