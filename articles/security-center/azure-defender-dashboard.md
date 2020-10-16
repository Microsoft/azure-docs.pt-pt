---
title: Painel de instrumentos do Azure Defender e as suas características
description: Conheça as características do painel Azure Defender.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 35469c7a11d47e586187b55bde1e8ad8a0c94f5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448405"
---
# <a name="the-azure-defender-dashboard"></a>O painel Azure Defender

O painel Azure Defender fornece:

- Visibilidade na sua cobertura do Azure Defender através dos seus diferentes tipos de recursos
- Links para configurar capacidades avançadas de proteção de ameaças
- Instalação do estado de embarque e do agente
- Alertas de deteção de ameaças do Azure Defender 

Para aceder ao painel Azure Defender, selecione **O Defender Azure** a partir da secção de Segurança da Nuvem do menu do Security Center.

## <a name="whats-shown-on-the-dashboard"></a>O que é mostrado no painel?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Um exemplo do painel do Azure Defender" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

O painel inclui as seguintes secções:

1. **Cobertura do Azure Defender** - Aqui pode ver os tipos de recursos que estão na sua subscrição e elegíveis para proteção pelo Azure Defender. Sempre que relevante, também terá a opção de fazer upgrade. Se pretender atualizar todos os recursos elegíveis possíveis, **selecione Upgrade all**.

1. **Área de alertas de segurança** - Quando o Azure Defender deteta uma ameaça em qualquer área do seu ambiente, gera um alerta. Estes alertas descrevem detalhes dos recursos afetados, sugeriram medidas de reparação e, em alguns casos, uma opção para desencadear uma aplicação lógica em resposta. Selecionar em qualquer lugar deste gráfico abre a **página alertas de segurança**.

1. **Proteção avançada** - O Azure Defender inclui muitas capacidades avançadas de proteção de ameaças para máquinas virtuais, bases de dados SQL, contentores, aplicações web, sua rede, e muito mais. Nesta secção de proteção avançada, pode ver o estado dos recursos nas suas subscrições selecionadas para cada uma destas proteções. Selecione qualquer um deles para ir diretamente para a área de configuração para esse tipo de proteção.

1. **Insights** - Este painel de notícias, leitura sugerida e alertas de alta prioridade dão informações do Centro de Segurança sobre questões de segurança urgentes que são relevantes para si e para a sua subscrição. Quer se trate de uma lista de CVEs de alta gravidade descobertos nos seus VMs por uma ferramenta de análise de vulnerabilidade, ou um novo post de blog por um membro da equipa do Security Center, você vai encontrá-lo aqui no painel insights do seu **painel de instrumentos Azure Defender**.




## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o painel Azure Defender. 

Para mais informações sobre o Azure Defender, consulte [Introdução ao Azure Defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Ativar o Azure Defender](security-center-pricing.md)