---
title: Investigar os utilizadores com análise de utilizadores em pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba mais sobre como investigar os utilizadores com análise de utilizadores no Azure sentinela.
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b9944764345f97c561f3f82f9cce9d37e204f134
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992979"
---
# <a name="investigate-users-with-user-analytics"></a>Investigar os utilizadores com análise de utilizadores

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os utilizadores são entidades que quer monitorizar de perto. Para ajudar a obter informações sobre os seus utilizadores, Azure sentinela integram-se perfeitamente com a proteção de ameaças avançada do Azure. Esta integração permite-lhe analisar o comportamento do utilizador e priorizar os utilizadores que deve investigar em primeiro lugar, com base nos seus alertas e padrões de atividade suspeita em sentinela do Azure e o Microsoft 365.

Azure Sentinel otimiza os dados dos utilizadores com análises do Microsoft 365 para ativar a análise de análise e o risco de utilizador abrangente para todos os utilizadores no Azure Active Directory. 

## <a name="how-it-works"></a>Como funciona

1. Com base na sua segurança regras de análise, quando uma correspondência for detectada, Azure sentinela envia os alertas para ATP do Azure.
1. ATP do Azure verifica quais entidades de utilizador estão relacionadas com os alertas e calcula a prioridade de investigação para os utilizadores.
3. Azure ATP recalcula, em seguida, a pontuação dos utilizadores depois que ele é enriquecido com dados a partir de suas regras de análise para sentinela do Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Uma licença de proteção de ameaças avançada do Azure 
- Para ativar a funcionalidade, terá de permissões de administrador global no inquilino no qual instalou o sentinela do Azure

> [!NOTE]
> - Para cada inquilino da ATP do Azure, pode ligar-se uma instância de sentinela do Azure.
> - Análise de utilizadores está atualmente disponível apenas para utilizadores do Active Directory do Azure. 

## <a name="enable-user-analytics"></a>Ativar a análise de utilizador

1. Para ativar a análise de utilizadores no Azure sentinela, no portal aceda ao **análise de utilizadores** página e clique em **ativar**. Esta ação envia informações sobre a área de trabalho a ATP do Azure.

1. Em seguida, ele leva-o a ATP do Azure. Sob **extensões de segurança** no **sentinela do Microsoft Azure** separador, clique nas **+ plus** para adicionar e, em seguida, selecione a área de trabalho. 
1. Clique em **Ligar**.

## <a name="investigate-a-user"></a>Investigar um utilizador

1. No menu do Azure sentinela sob **análise de utilizadores**, reveja a lista de utilizadores de acordo com a prioridade de investigação. A pontuação baseia-se no Azure sentinela alertas e alertas do Microsoft 365.

2. Procurar por um utilizador que pretende investigar. Clique no utilizador para chegar à página de utilizador. Rever atividades e alertas, o utilizador ao longo do tempo, na linha do tempo. Pode ver todas as atividades do Microsoft 365 e Azure sentinela. Também pode chegar à página de utilizador por desagregar os utilizadores de dentro de um caso.
      
    ![Utilizadores](./media/user-analytics/user-investigation.png)

 
3. Para isto funcionar bem, precisa configurar corretamente o [regra de alerta personalizada](tutorial-detect-threats.md) mapear os identificadores de utilizador certo para o **conta** entidade.

    - Para eventos do Windows, mapear **conta** para o **SID**
    - Os dados do Azure AD (que podem ser encontrados nos registos muitos, incluindo a atividade do Azure) ou dados do Office 365, mapear os **conta** propriedade para o **GUID**, ou o **Nome Principal de utilizador**. 

   ![Consulta](./media/user-analytics/query-window.png)



Por exemplo: 
> [!NOTE]
> Nos registos de atividades de atividade do Azure, a entidade de chamador inclui o UPN.

1. Esta consulta pesquisa para a criação de um número anómala de recursos ou atividades de implementação no registo de atividades do Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Na regra de alerta personalizada, mapear os **conta** propriedade **chamador**.
   
   ![Consulta](./media/user-analytics/query-window.png)

3. Investigar o usuário na janela de investigação do utilizador. Para orientações sobre como investigar os utilizadores, consulte [Tutorial: Investigar um utilizador](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a ligar o seu fornecedor de informações sobre ameaças a sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes.

- Para começar a utilizar com o Azure sentinela, terá de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [carregar os dados para o Azure sentinela](quickstart-onboard.md), e [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
