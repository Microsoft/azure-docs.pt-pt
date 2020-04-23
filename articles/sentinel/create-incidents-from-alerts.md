---
title: Criar incidentes a partir de alertas no Azure Sentinel Microsoft Docs
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025123"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Criar automaticamente incidentes a partir de alertas de segurança da Microsoft

Os alertas desencadeados nas soluções de segurança da Microsoft que estão ligadas ao Azure Sentinel, como o Microsoft Cloud App Security e o Azure Advanced Threat Protection, não criam automaticamente incidentes no Azure Sentinel. Por predefinição, quando ligar uma solução Microsoft ao Azure Sentinel, qualquer alerta gerado nesse serviço será armazenado como dados brutos no Azure Sentinel, na tabela de Alerta de Segurança no seu espaço de trabalho Azure Sentinel. Em seguida, pode utilizar esses dados como quaisquer outros dados brutos que se conecta ao Sentinel.

Pode configurar facilmente o Azure Sentinel para criar automaticamente incidentes sempre que um alerta é desencadeado numa solução de segurança da Microsoft conectada, seguindo as instruções deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Deve [ligar as soluções](connect-data-sources.md#data-connection-methods) de segurança da Microsoft para permitir a criação de incidentes a partir de alertas de serviçode segurança.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Usando regras analíticas de criação de incidentes da Microsoft Security

Utilize as regras incorporadas disponíveis no Azure Sentinel para escolher quais as soluções de segurança conectadas da Microsoft que devem criar incidentes Azure Sentinel automaticamente em tempo real. Também pode editar as regras para definir opções mais específicas para filtrar quais dos alertas gerados pela solução de segurança da Microsoft devem criar incidentes no Azure Sentinel. Por exemplo, pode optar por criar incidentes Azure Sentinel automaticamente apenas a partir de alertas do Centro de Segurança Azure de alta gravidade.

1. No portal Azure sob o Azure Sentinel, selecione **Analytics**.

1. Selecione o separador de **modelos de regra** para ver todas as regras analíticas incorporadas.

    ![Modelos de regras](media/incidents-from-alerts/rule-templates.png)

1. Escolha o modelo de regra de análise de segurança da **Microsoft** que pretende utilizar e clique na **regra Criar**.

    ![Regra de análise de segurança](media/incidents-from-alerts/security-analytics-rule.png)

1. Pode modificar os detalhes da regra e optar por filtrar os alertas que criarão incidentes por gravidade de alerta ou por texto contido no nome do alerta.  
      
    Por exemplo, se escolher o **Azure Security Center** no campo de serviço de segurança da **Microsoft** e escolher **o High** in the Filter por campo de **gravidade,** apenas os alertas do Centro de Segurança Azure de alta gravidade criarão automaticamente incidentes no Azure Sentinel.  

    ![Criar o assistente de regras](media/incidents-from-alerts/create-rule-wizard.png)

1. Também pode criar uma nova regra de segurança da **Microsoft** que filtra alertas de diferentes serviços de segurança da Microsoft clicando em **+Create** e selecionando a regra de criação de **incidentes**da Microsoft .

    ![Regra de criação de incidentes](media/incidents-from-alerts/incident-creation-rule.png)

  Pode criar mais do que uma regra analítica microsoft **Security** por tipo de serviço de **segurança da Microsoft.** Isto não cria incidentes duplicados, uma vez que cada regra é usada como filtro. Mesmo que um alerta corresponda a mais do que uma regra analítica da **Microsoft Security,** cria apenas um incidente do Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Ativar a geração de incidentes automaticamente durante a ligação
 Quando ligar uma solução de segurança da Microsoft, pode selecionar se pretende que os alertas da solução de segurança gerem automaticamente incidentes no Azure Sentinel.

1. Ligue uma fonte de dados da solução de segurança da Microsoft. 

   ![Gerar incidentes de segurança](media/incidents-from-alerts/generate-security-incidents.png)

1. Em caso **de criar incidentes,** selecione **Ativar** para ativar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra de acordo com **as regras** **Analytics** e, em seguida, Ative .

## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [bordo dos seus dados para o Azure Sentinel](quickstart-onboard.md)e obtenha visibilidade nos seus dados e ameaças [potenciais.](quickstart-get-visibility.md)
