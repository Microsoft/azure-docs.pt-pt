---
title: Controlo de Segurança Azure - Proteção de Dados
description: Proteção de Dados de Controlo de Segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408547"
---
# <a name="security-control-data-protection"></a>Controlo de Segurança: Proteção de Dados

As recomendações de proteção de dados focam-se na abordagem de questões relacionadas com encriptação, listas de controlo de acesso, controlo de acesso baseado na identidade e registo de auditoria para acesso a dados.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.1 | 13.1 | Cliente |

Utilize tags para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.2 | 13.2, 2.10 | Cliente |

Implementar o isolamento utilizando assinaturas separadas e grupos de gestão para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade de dados. Pode restringir o nível de acesso aos seus recursos Azure que as suas aplicações e ambientes empresariais exigem. Pode controlar o acesso aos recursos azure através do controlo de acesso baseado no diretório Ativo do Azure. 

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.3 | 13.3 | Partilhado |

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie essas transferências, alertando os profissionais de segurança da informação.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e atentos à perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.4 | 14.4 | Partilhado |

Criptografe todas as informações confidenciais em trânsito. Certifique-se de que todos os clientes que se ligam aos seus recursos Azure são capazes de negociar TLS 1.2 ou superior.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, quando aplicável.

- [Compreender a encriptação em trânsito com o Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.5 | 14.5 | Partilhado |

Quando não houver nenhuma funcionalidade disponível para o seu serviço específico no Azure, utilize uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo os localizados no local, ou num fornecedor de serviços remotos, e atualizar o inventário de informação sensível da organização.

Utilize a Proteção de Informação Azure para identificar informações sensíveis dentro dos documentos do Office 365.

Utilize a Proteção de Informação Azure SQL para ajudar na classificação e rotulagem das informações armazenadas nas bases de dados Azure SQL.

- [Como implementar a Descoberta de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Como implementar a Proteção de Informação do Azure](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.6 | 14.6 | Cliente |

Utilize o Azure AD RBAC para controlar o acesso a dados e recursos, caso contrário utilize métodos específicos de controlo de acesso do serviço.

- [Como configurar o RBAC em Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.7 | 14.7 | Partilhado |

Se necessário para o cumprimento dos recursos computacionais, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perdas de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.8 | 14.8 | Cliente |

Use encriptação em repouso em todos os recursos do Azure. A Microsoft recomenda que o Azure gere as suas chaves de encriptação, no entanto existe a opção de gerir as suas próprias chaves em alguns casos. 

- [Compreenda a encriptação em repouso em Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Como configurar as chaves de encriptação geridas pelo cliente](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.9 | 14.9 | Cliente |

Utilize o Monitor Azure com o Registo de Atividades do Azure para criar alertas para quando as alterações ocorrerem aos recursos azure críticos.

- [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo Controlo de Segurança: [Gestão de Vulnerabilidades](security-control-vulnerability-management.md)