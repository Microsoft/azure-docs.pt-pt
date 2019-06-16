---
title: Recomendações de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento explica-lhe como recomendações no Centro de segurança do Azure ajudar a proteger os seus recursos do Azure e mantenha-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064243"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança no Centro de Segurança do Azure 
Este tópico explica como visualizar e compreender as recomendações no Centro de segurança do Azure para o ajudar a proteger os seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>Quais são as recomendações de segurança?
O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários.

## <a name="implementing-security-recommendations"></a>Implementar recomendações de segurança
### <a name="set-recommendations"></a>Recomendações de conjunto
Na [definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md), vai aprender a:

* Configure políticas de segurança.
* Ative a recolha de dados.
* Escolha quais as recomendações para ver como parte da sua política de segurança.

Centro de recomendações de política atual em torno de atualizações do sistema, regras de linha de base, programas de antimalware [grupos de segurança de rede](../virtual-network/security-overview.md) em sub-redes e interfaces de rede, a auditoria de base de dados SQL, encriptação de dados transparente de banco de dados SQL, e firewalls de aplicações web.  [Definir políticas de segurança](tutorial-security-policy.md) fornece uma descrição de cada opção de recomendação.

### <a name="monitor-recommendations"></a>Recomendações de monitor
Após definir uma política de segurança, o Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O **recomendações** mosaico sob **descrição geral** mostra o número total de recomendações identificado pelo centro de segurança.

![Descrição geral do Centro de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **mosaico de recomendações** sob **descrição geral**. O **recomendações** é aberta a lista.
    
      ![Ver recomendações](./media/security-center-recommendations/view-recommendations.png)

    Pode filtrar recomendações. Para filtrar as recomendações, selecione **filtro** sobre o **recomendações** painel. O **filtro** é aberto o painel e selecionar os valores de gravidade e o estado que pretende ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **PROTEGER O IMPACTO DE PONTUAÇÃO**: Uma pontuação gerada pelo centro de segurança utilizando as suas recomendações de segurança e a aplicação de algoritmos avançados para determinar como crucial é de cada recomendação. Para obter mais informações, consulte [proteger o cálculo de pontuação](security-center-secure-score.md#secure-score-calculation).
   * **RECURSO**: Lista os recursos aos quais se aplica esta recomendação.
   * **BARRAS DE ESTADO**:  Descreve a gravidade dessa recomendação específica:
       * **Alto (vermelho)** : Uma vulnerabilidade existe com um recurso significativo (por exemplo, um aplicativo, uma VM ou um grupo de segurança de rede) e necessita de atenção.
       * **Médio (laranja)** : Existe uma vulnerabilidade e passos não críticos ou adicionais são necessários para eliminá-la ou para concluir um processo.
       * **Baixa (azul)** : Uma vulnerabilidade existe que deve ser resolvido, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender vê-los.) 
       * **Bom estado de funcionamento (verde)** :
       * **Não está disponível (cinzento)** :

1. Para ver os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Ver [clássico e modelos de implementação do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
  
 ### <a name="apply-recommendations"></a>Aplicar recomendações
> Depois de rever todas as recomendações, decida qual delas para aplicar primeiro. Recomendamos que utilize a segura Pontuar o impacto para avaliar quais recomendações devem ser aplicadas primeiro.

1. Na lista, clique na recomendação.
1. Siga as instruções no *passos de remediação* secção.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, foram introduzidas para recomendações de segurança no Centro de segurança. Para saber mais sobre o Centro de segurança, consulte os seguintes tópicos:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
