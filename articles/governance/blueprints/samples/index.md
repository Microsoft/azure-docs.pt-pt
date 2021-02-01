---
title: Índice de exemplos de esquema
description: Índice de exemplos de conformidade e normas para a implementação de ambientes, políticas e fundações do Framework de Adoção da Cloud com o Azure Blueprints.
ms.date: 02/01/2021
ms.topic: sample
ms.openlocfilehash: 17bacecf8fcf7c0f1969a489561a3a3ac976872e
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225146"
---
# <a name="azure-blueprints-samples"></a>Exemplos do Azure Blueprints

A tabela seguinte inclui ligações para exemplos do Azure Blueprints. Cada exemplo é de qualidade de produção e está pronto para implementar hoje mesmo para o ajudar a atender as várias necessidades de conformidade.

## <a name="standards-based-blueprint-samples"></a>Exemplos de esquema baseado em normas

| Sample | Descrição |
|---------|---------|
| [ISM PROTECTED da Administração Australiana](./ism-protected/index.md) | Fornece guarda-costas para o cumprimento do ISM PROTECTED do Governo Australiano. |
| [Referência de referência de segurança Azure v1](./azure-security-benchmark.md) | Fornece guarda-costas para o cumprimento do [Benchmark de Segurança Azure v1](../../../security/benchmarks/overview.md). |
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Fornece guarda-costas para o cumprimento do Canadá Federal Protected B, Integridade Média, Disponibilidade Média (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1-1-0.md)| Fornece um conjunto de políticas para ajudar a cumprir as recomendações do CIS Microsoft Azure Foundations Benchmark. |
| [Impacto DoD Nível 4](./dod-impact-level-4/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o Impacto DoD Nível 4. |
| [Impacto DoD Nível 5](./dod-impact-level-5/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o Impacto DoD Nível 5. |
| [FedRAMP Moderado](./fedramp-m/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o FedRAMP Moderado. |
| [FedRAMP Alto](./fedramp-h/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o FedRAMP Alto. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | Fornece um conjunto de políticas para ajudar a cumprir o HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Fornece guarda-costas para o cumprimento do IRS 1075.|
| [ISO 27001](./iso-27001-2013.md) | Fornece proteções para conformidade com a ISO 27001. |
| [Serviços Partilhados ISO 27001](./iso27001-shared/index.md) | Fornece um conjunto de grades de proteção de políticas e padrões de infraestrutura conformes que contribuem para o atestado da norma ISO 27001. |
| [Carga de trabalho da Base de Dados SQL/Ambiente do Serviço de Aplicações ISO 27001](./iso27001-ase-sql-workload/index.md) | Fornece mais infraestruturas à amostra de projeto [iso 27001 dos Serviços Partilhados.](./iso27001-shared/index.md) |
| [Media](./media/index.md) | Fornece um conjunto de políticas para ajudar a cumprir o Media MPAA. |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | Fornece guarda-costas para o cumprimento do NIST SP 800-53 R4. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | Fornece guarda-costas para o cumprimento do NIST SP 800-171 R2. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornece um conjunto de políticas para ajudar na conformidade do PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Auxilia na conformidade com SWIFT CSP-CSCF v2020. |
| [OFICIAL DO REINO UNIDO e NHS do Reino Unido](./ukofficial/index.md) | Fornece um conjunto de princípios de proteção de políticas e padrões de infraestrutura conformes que contribuem para o atestado OFICIAL DO REINO UNIDO e NHS do Reino Unido. |
| [Fundação do CAF](./caf-foundation/index.md) | Fornece um conjunto de controlos que o ajudam a gerir o seu património de cloud em linha com o [Microsoft Cloud Adoption Framework para o Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona de destino de migração do CAF](./caf-migrate-landing-zone/index.md) | Fornece um conjunto de controlos que o ajudam a preparar-se para migrar a sua primeira carga de trabalho e a gerir o seu património de cloud em linha com o [Microsoft Cloud Adoption Framework para o Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Estratégia de Exemplos

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="Diagrama de onde os exemplos de Esquema se enquadram na complexidade da arquitetura vs requisitos de conformidade." border="false":::
   Descreve um sistema de coordenadas em que a complexidade da arquitetura está no eixo X e os requisitos de conformidade estão no eixo Y.  À medida que a complexidade arquitetónica e os requisitos de conformidade aumentam, adote amostras padrão do Plano do portal designado na região E.  Para os clientes que estão a começar com o Azure use a Base de Adoção da Nuvem (C A F) com base em plantas da Fundação e zona de aterragem designadas pelas regiões A e B.  O espaço restante é atribuído a plantas personalizadas criadas pelos clientes são parceiros para as regiões C, D e F. :::image-end:::

Os esquemas Fundação do CAF e Zona de destino de migração do CAF pressupõem que o cliente está a preparar uma subscrição individual limpa já existente para migrar ativos e cargas de trabalho no local para o Azure.
(Regiões A e B na figura).  

Há a oportunidade de iterar sobre os exemplos de esquema e de procurar personalizações que um cliente esteja a aplicar. Existe também a oportunidade de abordar proativamente esquemas específicos de uma indústria como, por exemplo, serviços financeiros e comércio eletrónico (parte superior da Região B). Da mesma forma, queremos criar esquemas que sirvam considerações arquiteturais complexas, tais como várias subscrições, elevada disponibilidade, recursos entre regiões e clientes que estejam a implementar controlos em subscrições e recursos existentes (Regiões C e D).

Existem exemplos de esquema adequados ao cenário de cliente em que os requisitos de conformidade são exigentes e as complexidades de arquitetura são elevadas (Região E na Figura). A Região F na figura é uma que é abordada por clientes e parceiros que estão a aplicar as plantas da amostra e a personalizar cada uma para as suas necessidades únicas.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
