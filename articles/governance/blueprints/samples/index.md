---
title: Índice de exemplos de Esquema
description: Índice de exemplos de conformidade e normas para a implementação de ambientes, políticas e fundações do Framework de Adoção da Cloud com o Azure Blueprints.
ms.date: 12/05/2019
ms.topic: sample
ms.openlocfilehash: a0be956037f6b1ee5229ecde7b1491f552113866
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436567"
---
# <a name="azure-blueprints-samples"></a>Exemplos do Azure Blueprints

A tabela seguinte inclui ligações para exemplos do Azure Blueprints. Cada exemplo é de qualidade de produção e está pronto para implementar hoje mesmo para o ajudar a atender as várias necessidades de conformidade.

## <a name="standards-based-blueprint-samples"></a>Exemplos de esquema baseado em normas

|  |  |
|---------|---------|
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Fornece proteções para garantir a conformidade com o Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Fornece um conjunto de políticas para ajudar a cumprir as recomendações do CIS Microsoft Azure Foundations Benchmark. |
| [FedRAMP Moderado](./fedramp-m/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o FedRAMP Moderado. |
| [HIPAA HITRUST](./HIPAA-HITRUST/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Fornece proteções para conformidade com IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Fornece proteções para conformidade com a ISO 27001. |
| [Serviços Partilhados ISO 27001](./iso27001-shared/index.md) | Fornece um conjunto de grades de proteção de políticas e padrões de infraestrutura conformes que contribuem para o atestado da norma ISO 27001. |
| [Carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001](./iso27001-ase-sql-workload/index.md) | Fornece uma infraestrutura adicional para o exemplo de esquema [Serviços Partilhados ISO 27001](./iso27001-shared/index.md). |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Fornece proteções para conformidade com NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornece um conjunto de políticas para ajudar na conformidade do PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Auxilia na conformidade com SWIFT CSP-CSCF v2020. |
| [OFICIAL DO REINO UNIDO e NHS do Reino Unido](./ukofficial/index.md) | Fornece um conjunto de princípios de proteção de políticas e padrões de infraestrutura conformes que contribuem para o atestado OFICIAL DO REINO UNIDO e NHS do Reino Unido. |
| [Fundação do CAF](./caf-foundation/index.md) | Fornece um conjunto de controlos que o ajudam a gerir o seu património de cloud em linha com o [Microsoft Cloud Adoption Framework para o Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona de destino de migração do CAF](./caf-migrate-landing-zone/index.md) | Fornece um conjunto de controlos que o ajudam a preparar-se para migrar a sua primeira carga de trabalho e a gerir o seu património de cloud em linha com o [Microsoft Cloud Adoption Framework para o Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Estratégia de Exemplos

![Estratégia de exemplos de esquema](../media/blueprint-samples-strategy.png)

Os esquemas Fundação do CAF e Zona de destino de migração do CAF pressupõem que o cliente está a preparar uma subscrição individual limpa existente para migrar recursos/cargas de trabalho no local para o Azure.
(Regiões A e B na Figura acima).  

Há a oportunidade de iterar sobre os exemplos de esquema e de procurar personalizações que um cliente esteja a aplicar. Existe também a oportunidade de abordar proativamente esquemas específicos de uma indústria como, por exemplo, serviços financeiros e comércio eletrónico (parte superior da Região B). Da mesma forma, queremos criar esquemas que sirvam considerações arquiteturais complexas, tais como várias subscrições, elevada disponibilidade, recursos entre regiões e clientes que estejam a implementar controlos em subscrições e recursos existentes (Regiões C e D).

Existem exemplos de esquema adequados ao cenário de cliente em que os requisitos de conformidade são exigentes e as complexidades de arquitetura são elevadas (Região E na Figura acima). A Região F acima corresponde aos clientes e parceiros que estejam a tirar partido de exemplos de esquema e a personalizá-los para servir as respetivas necessidades exclusivas.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).