---
title: Controlo de Segurança Azure - Proteção de Dados
description: Proteção de dados de controlo de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576829"
---
# <a name="security-control-data-protection"></a>Controlo de Segurança: Proteção de Dados

As recomendações de proteção de dados focam-se em abordar questões relacionadas com encriptação, listas de controlo de acesso, controlo de acesso baseado em identidade e registo de auditoria para acesso a dados.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.1 | 13,1 | Cliente |

Utilize Tags para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.2 | 13.2, 2.10 | Cliente |

Implementar o isolamento utilizando subscrições e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados. Pode restringir o nível de acesso aos seus recursos Azure que as suas aplicações e ambientes empresariais exigem. Você pode controlar o acesso aos recursos Azure através do controlo de acesso baseado em funções Azure (Azure RBAC). 

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.3 | 13.3 | Partilhado |

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitoriza a transferência não autorizada de informações sensíveis e bloqueia tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.4 | 14,4 | Partilhado |

Criptografe todas as informações sensíveis em trânsito. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure seja capaz de negociar TLS 1.2 ou superior.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

- [Compreender a encriptação em trânsito com Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4,5 | 14,5 | Partilhado |

Quando não houver nenhuma funcionalidade disponível para o seu serviço específico no Azure, utilize uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo as localizadas no local, ou num prestador de serviços remotos, e atualize o inventário de informação sensível da organização.

Utilize a Proteção de Informações Azure para identificar informações sensíveis dentro dos documentos da Microsoft 365.

Utilize a Azure SQL Information Protection para ajudar na classificação e rotulagem das informações armazenadas na Base de Dados Azure SQL.

- [Como implementar a Descoberta de Dados do SQL do Azure](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Como implementar a Proteção de Informação Azure](/azure/information-protection/deployment-roadmap)

- [Compreender a proteção dos dados dos clientes no Azure](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4,6 | 14.6 | Cliente |

Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso a dados e recursos, caso contrário utilize métodos específicos de controlo de acesso do serviço.

- [Como configurar o Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.7 | 14,7 | Partilhado |

Se necessário para o cumprimento dos recursos computativos, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perdas de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.8 | 14,8 | Cliente |

Use a encriptação em repouso em todos os recursos Azure. A Microsoft recomenda que o Azure gere as suas chaves de encriptação, no entanto existe a opção de gerir as suas próprias chaves em alguns casos. 

- [Compreender a encriptação de dados inativos no Azure](../fundamentals/encryption-atrest.md)

- [Como configurar chaves de encriptação geridas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4,9 | 14,9 | Cliente |

Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações para recursos críticos do Azure.

- [Como criar alertas para eventos de Registo de Atividades Azure](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança:  [Gestão de Vulnerabilidades](security-control-vulnerability-management.md)