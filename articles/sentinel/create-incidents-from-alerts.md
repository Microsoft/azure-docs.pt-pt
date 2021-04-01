---
title: Criar incidentes a partir de alertas em Azure Sentinel | Microsoft Docs
description: Saiba como criar incidentes a partir de alertas em Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 5c7c3d69bb26773171e9e0afc9f79ff25909a12a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99807297"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Criar automaticamente incidentes a partir de alertas de segurança da Microsoft

Os alertas desencadeados nas soluções de segurança da Microsoft que estão ligados ao Azure Sentinel, como o Microsoft Cloud App Security e o Microsoft Defender for Identity (ex-Azure ATP), não criam automaticamente incidentes no Azure Sentinel. Por padrão, quando ligar uma solução Microsoft ao Azure Sentinel, qualquer alerta gerado nesse serviço será armazenado como dados brutos em Azure Sentinel, na tabela Alerta de Segurança no seu espaço de trabalho Azure Sentinel. Em seguida, pode utilizar esses dados como qualquer outro dado bruto que se conecta ao Azure Sentinel.

Pode configurar facilmente o Azure Sentinel para criar automaticamente incidentes sempre que um alerta é acionado numa solução de segurança da Microsoft conectada, seguindo as instruções deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Tem de [ligar as soluções de segurança](connect-data-sources.md#data-connection-methods) da Microsoft para permitir a criação de incidentes a partir de alertas de serviços de segurança.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Usando as regras de análise de criação de incidentes da Microsoft Security

Utilize as regras incorporadas disponíveis no Azure Sentinel para escolher quais as soluções de segurança conectadas da Microsoft que devem criar incidentes Azure Sentinel automaticamente em tempo real. Também pode editar as regras para definir opções mais específicas para filtrar quais dos alertas gerados pela solução de segurança da Microsoft devem criar incidentes no Azure Sentinel. Por exemplo, pode optar por criar incidentes Azure Sentinel automaticamente apenas a partir de alertas de Alta Severidade Azure Defender (antigo Centro de Segurança Azure).

1. No portal Azure sob O Azure Sentinel, **selecione Analytics**.

1. Selecione o **separador 'Modelos de Regra'** para ver todas as regras de análise incorporadas.

    ![Modelos de regras](media/incidents-from-alerts/rule-templates.png)

1. Escolha o modelo de regra de análise de segurança da **Microsoft** que pretende utilizar e clique na **regra Criar**.

    ![Regra de análise de segurança](media/incidents-from-alerts/security-analytics-rule.png)

1. Pode modificar os detalhes da regra e optar por filtrar os alertas que irão criar incidentes por gravidade de alerta ou por texto contido no nome do alerta.  
      
    Por exemplo, se escolher **o Azure Defender** (ainda pode ser chamado *de Azure Security Center*) no campo de serviço de segurança da **Microsoft** e escolher **High** in the Filter por campo **de gravidade,** apenas os alertas de Alta Gravidade do Azure Defender criarão automaticamente incidentes em Azure Sentinel.  

    ![Criar assistente de regras](media/incidents-from-alerts/create-rule-wizard.png)

1. Também pode criar uma nova regra de segurança da **Microsoft** que filtra alertas de diferentes serviços de segurança da Microsoft clicando em **+Criar** e selecionando a regra de **criação de incidentes da Microsoft.**

    ![Regra de criação de incidentes](media/incidents-from-alerts/incident-creation-rule.png)

  Pode criar mais do que uma regra de análise **do Microsoft Security** por tipo de serviço de segurança da **Microsoft.** Isto não cria incidentes duplicados, uma vez que cada regra é usada como filtro. Mesmo que um alerta corresponda a mais do que uma regra de análise **da Microsoft Security,** cria apenas um incidente do Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Ativar a geração de incidentes automaticamente durante a ligação
 Quando ligar uma solução de segurança da Microsoft, pode selecionar se deseja que os alertas da solução de segurança gerem automaticamente incidentes no Azure Sentinel.

1. Ligue uma fonte de dados de solução de segurança da Microsoft. 

   ![Gerar incidentes de segurança](media/incidents-from-alerts/generate-security-incidents.png)

1. Em **Configurar incidentes** selecione Ativar para **ativar** a regra de análise padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra sob **a análise** e, em seguida, **as regras Ative**.

## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [embarcar os seus dados ao Azure Sentinel](quickstart-onboard.md)e obtenha [visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
