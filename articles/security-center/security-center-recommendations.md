---
title: Gerir recomendações de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento explica-lhe como recomendações no Centro de segurança do Azure ajudar a proteger os seus recursos do Azure e mantenha-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 8535a77c9f44a4dafca34ef24414f90741a9133e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652735"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gerir recomendações de segurança no Centro de segurança do Azure
Este documento explica como utilizar as recomendações no Centro de segurança do Azure para o ajudar a proteger os seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>
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
Após definir uma política de segurança, o Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O **recomendações** mosaico sob **descrição geral** permite-lhe determinar o número total de recomendações identificado pelo centro de segurança.

![Mosaico de recomendações][1]

Para ver os detalhes de cada recomendação, selecione o **mosaico de recomendações** sob **descrição geral**. **Recomendações** abre.

![Recomendações de filtro][2]

Pode filtrar recomendações. Para filtrar as recomendações, selecione **filtro** sobre o **recomendações** painel. O **filtro** é aberto o painel e selecionar os valores de gravidade e o estado que pretende ver.


* **RECOMENDAÇÕES**: A recomendação.
* **PROTEGER O IMPACTO DE PONTUAÇÃO**:
* **RECURSO**: Lista os recursos aos quais se aplica esta recomendação.
* **BARRAS DE ESTADO**:  Descreve a gravidade dessa recomendação específica:
   * **Alto (vermelho)**: Uma vulnerabilidade existe com um recurso significativo (por exemplo, um aplicativo, uma VM ou um grupo de segurança de rede) e necessita de atenção.
   * **Médio (laranja)**: Existe uma vulnerabilidade e passos não críticos ou adicionais são necessários para eliminá-la ou para concluir um processo.
   * **Baixa (azul)**: Uma vulnerabilidade existe que deve ser resolvido, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender vê-los.) 
   * **Bom estado de funcionamento (verde)**:
   * **Não está disponível (cinzento)**:
 <!-- * **DESCRIPTION**: Explains the recommendation and what needs to be done to address it.
**STATE**: Describes the current state of the recommendation:
   * **Open**: The recommendation hasn't been addressed yet.
    * **In Progress**: The recommendation is currently being applied to the resources, and no action is required by you.
   * **Resolved**: The recommendation has already been completed (in this case, the line is grayed out).
    -->

Utilize a tabela abaixo como referência para ajudar a compreender as recomendações de disponibilidade e o que cada um deles faz se as aplicar.
<!-- There is no table below -->
> [!NOTE]
> Vai querer compreender a [clássico e modelos de implementação do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
>
>
### <a name="apply-recommendations"></a>Aplicar recomendações
Depois de rever todas as recomendações, decida que um deve aplicar primeiro. Recomendamos que utilize a classificação de gravidade, como o parâmetro principal para avaliar quais recomendações deve ser aplicado primeiro.



## <a name="next-steps"></a>Passos Seguintes
Neste documento, foram introduzidas para recomendações de segurança no Centro de segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
