---
title: Integração do Microsoft 365 Defender com a | do Azure Sentinel Microsoft Docs
description: Saiba como a integração do Microsoft 365 Defender com o Azure Sentinel lhe dá a capacidade de usar o Azure Sentinel como a sua fila de incidentes universais, preservando os pontos fortes do M365D para ajudar na investigação de incidentes de segurança M365, e também como ingerir os dados avançados de caça dos componentes do Defender em Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746999"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integração do Microsoft 365 Defender com Azure Sentinel

> [!IMPORTANT]
> O conector Microsoft 365 Defender encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

> [!IMPORTANT]
>
> **O Microsoft 365 Defender** era anteriormente conhecido como **Microsoft Threat Protection** ou **MTP**.
>
> **O Microsoft Defender for Endpoint** era anteriormente conhecido como **Microsoft Defender Advanced Threat Protection** ou **MDATP**.
>
> Pode ver os nomes antigos ainda em uso por um período de tempo.

## <a name="incident-integration"></a>Integração de incidentes

A integração de incidentes microsoft [365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) do Azure Sentinel permite transmitir todos os incidentes M365D em Azure Sentinel e mantê-los sincronizados entre ambos os portais. Incidentes de M365D (anteriormente conhecido como Microsoft Threat Protection ou MTP) incluem todos os alertas, entidades e informações relevantes associados, fornecendo-lhe contexto suficiente para realizar triagem e investigação preliminar em Azure Sentinel. Uma vez em Sentinel, os Incidentes permanecerão bi-direccionalmente sincronizados com m365D, permitindo-lhe tirar partido dos benefícios de ambos os portais na sua investigação de incidentes.

Esta integração dá à Microsoft 365 incidentes de segurança a visibilidade a ser gerida a partir de dentro do Azure Sentinel, como parte da fila principal de incidentes em toda a organização, para que possa ver – e correlacionar – incidentes M365 juntamente com os de todos os seus outros sistemas de nuvem e instalações. Ao mesmo tempo, permite-lhe tirar partido dos pontos fortes e capacidades únicos do M365D para investigações aprofundadas e uma experiência específica M365 em todo o ecossistema M365. O M365 Defender enriquece e agruta alertas de vários produtos M365, reduzindo o tamanho da fila de incidentes do SOC e encurtando o tempo para resolver. Os serviços de componentes que fazem parte da stack M365 Defender são:

- **Microsoft Defender para Endpoint** (MDE, anteriormente MDATP)
- **Microsoft Defender para a Identidade** (MDI, anteriormente AATP)
- **Microsoft Defender para o Office 365** (MDO, anteriormente O365ATP)
- **Segurança da Aplicação Microsoft Cloud** (MCAS)

Além de recolher alertas destes componentes, o M365 Defender gera alertas próprios. Cria incidentes a partir de todos estes alertas e envia-os para Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Casos e cenários de uso comum

- Ligação de um clique dos incidentes do M365 Defender, incluindo todos os alertas e entidades dos componentes M365 Defender, para o Azure Sentinel.

- Sincronização bidisal entre os incidentes de Sentinel e M365D no estado, proprietário e motivo de fecho.

- Alavancar as capacidades de agrupamento e enriquecimento do M365 Defender em Azure Sentinel, reduzindo assim o tempo de resolução.

- No contexto, uma ligação profunda entre um incidente de Azure Sentinel e o seu incidente paralelo M365 Defender, para facilitar as investigações em ambos os portais.

### <a name="connecting-to-microsoft-365-defender"></a>Ligação ao Microsoft 365 Defender

Depois de ter ativado o conector de dados do Microsoft 365 Defender para [recolher incidentes e alertas,](connect-microsoft-365-defender.md)os incidentes M365D aparecerão na fila de incidentes do Azure Sentinel, com o **Microsoft 365 Defender** no campo nome do **Produto,** pouco depois de gerados no M365 Defender.
- Pode levar até 10 minutos a partir do momento em que um incidente é gerado no M365 Defender até ao momento em que aparece em Azure Sentinel.

- Os incidentes serão ingeridos e sincronizados sem custos adicionais.

Uma vez ligada a integração do M365 Defender, todos os conectores de alerta de componente (MDE, MDI, MDO, MCAS) serão automaticamente ligados em segundo plano se já não o estivessem. Se alguma licença de componente foi comprada após a ligação do M365 Defender, os alertas e incidentes do novo produto continuarão a fluir para o Azure Sentinel sem nenhuma configuração ou carga adicional.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidentes do M365 Defender e regras de criação de incidentes da Microsoft

- Os incidentes gerados pelo M365 Defender, com base em alertas provenientes de produtos de segurança M365, são criados com base na lógica M365 personalizada.

- As regras de criação de incidentes da Microsoft no Azure Sentinel também criam incidentes a partir dos mesmos alertas, utilizando (uma lógica diferente) personalizada do Azure Sentinel.

- A utilização de ambos os mecanismos em conjunto é completamente suportada, e esta configuração pode ser usada para facilitar a transição para a nova lógica de criação de incidentes M365 Defender. Isto irá, no entanto, criar **incidentes duplicados** para os mesmos alertas.

- Para evitar a criação de incidentes duplicados para os mesmos alertas, recomendamos que os clientes desliguem todas **as regras de criação de incidentes da Microsoft** para produtos M365 (MDE, MDI e MDO - ver MCAS abaixo) ao ligar o M365 Defender. Isto pode ser feito marcando a caixa de verificação relevante na página do conector. Tenha em mente que, se o fizer, quaisquer filtros que foram aplicados pelas regras de criação de incidentes não serão aplicados à integração de incidentes M365 Defender.

- Para os alertas de Segurança da Aplicação cloud da Microsoft (MCAS), nem todos os tipos de alerta estão a bordo do M365 Defender. Para se certificar de que ainda está a receber incidentes para todos os alertas mcas, deve manter ou criar regras de **criação de incidentes** da Microsoft para os tipos de alerta *não a bordo* do M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Trabalhando com incidentes do M365 Defender em Azure Sentinel e sincronização bidisal

Os incidentes do M365 Defender aparecerão na fila de incidentes do Azure Sentinel com o nome de produto **Microsoft 365 Defender**, e com detalhes e funcionalidades semelhantes a quaisquer outros incidentes do Sentinel. Cada incidente contém uma ligação de volta ao incidente paralelo no portal M365 Defender.

À medida que o incidente evolui no M365 Defender, e mais alertas ou entidades lhe são adicionados, o incidente do Azure Sentinel será atualizado em conformidade.

As alterações efetuadas ao estado, ao motivo de encerramento ou à atribuição de um incidente M365, tanto no M365D como no Azure Sentinel, também atualizarão em conformidade na fila de incidentes do outro. A sincronização ocorrerá em ambos os portais imediatamente após a alteração do incidente, sem demora. Poderá ser necessária uma atualização para ver as últimas alterações.

No M365 Defender, todos os alertas de um incidente podem ser transferidos para outro, resultando na fusão dos incidentes. Quando isso acontecer, os incidentes do Azure Sentinel refletirão as mudanças. Um incidente conterá todos os alertas de ambos os incidentes originais, e o outro incidente será automaticamente encerrado, com uma etiqueta de "redirecionado" adicionado.

> [!NOTE]
> Incidentes em Azure Sentinel podem conter um máximo de 150 alertas. Os incidentes da M365D podem ter mais do que isto. Se um incidente M365D com mais de 150 alertas for sincronizado com Azure Sentinel, o incidente do Sentinel mostrará como tendo alertas "150+" e fornecerá uma ligação ao incidente paralelo em M365D onde você verá o conjunto completo de alertas.

## <a name="advanced-hunting-event-collection"></a>Coleção avançada de eventos de caça

O conector Microsoft 365 Defender também permite transmitir eventos **de caça avançados** - um tipo de dados de eventos brutos - do Microsoft 365 Defender e seus serviços de componentes para o Azure Sentinel. Atualmente, você pode recolher eventos [de caça avançados](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) microsoft Defender para [Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) e transmiti-los diretamente em mesas construídas de propósito no seu espaço de trabalho Azure Sentinel. Estas tabelas são construídas sobre o mesmo esquema que é usado no portal Microsoft 365 Defender, dando-lhe acesso completo a todo o conjunto de eventos de caça avançados, e permitindo-lhe fazer o seguinte:

- Copie facilmente o microsoft defender existente para consultas avançadas de caça Endpoint em Azure Sentinel.

- Use os registos de eventos brutos para fornecer informações adicionais para os seus alertas, caça e investigação, e correlacionar estes eventos com outros de fontes de dados adicionais em Azure Sentinel.

- Guarde os registos com uma retenção aumentada, para além do Microsoft Defender para o Endpoint ou para a retenção padrão do Microsoft 365 Defender de 30 dias. Pode fazê-lo configurando a retenção do seu espaço de trabalho ou configurando a retenção por mesa no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a beneficiar com a utilização do Microsoft 365 Defender juntamente com o Azure Sentinel, utilizando o conector Microsoft 365 Defender.

- Obtenha instruções para [ativar o conector Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Crie [alertas personalizados](tutorial-detect-threats-custom.md) e [investigue incidentes.](tutorial-investigate-cases.md)
