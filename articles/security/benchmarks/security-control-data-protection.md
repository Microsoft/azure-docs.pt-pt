---
title: Controlo de Segurança Azure - Proteção de Dados
description: Proteção de Dados de Controlo de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934510"
---
# <a name="security-control-data-protection"></a>Controlo de Segurança: Proteção de Dados

As recomendações de proteção de dados focam-se na abordagem de questões relacionadas com encriptação, listas de controlo de acesso, controlo de acesso baseado na identidade e registo de auditoria para acesso a dados.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.1 | 13.1 | Cliente |

Utilize tags para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.2 | 13.2 | Cliente |

Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados por VNet/Subnet, marcados adequadamente e protegidos por um NSG ou Firewall Azure. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados. Para máquinas virtuais armazenando ou processando dados sensíveis, implemente a política e o procedimento(s) para desligá-los quando não estão a ser utilizados.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um Config de Segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar o Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alerta ou alerta e negar com firewall Azure:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.3 | 13.3 | Cliente |

Implemente uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie essas transferências, alertando os profissionais de segurança da informação.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.4 | 14.4 | Partilhado |

Criptografe todas as informações confidenciais em trânsito. Certifique-se de que todos os clientes que se ligam aos seus recursos Azure são capazes de negociar TLS 1.2 ou superior.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, quando aplicável.

Compreenda a encriptação em trânsito com o Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.5 | 14.5 | Cliente |

Quando não houver nenhuma funcionalidade disponível para o seu serviço específico no Azure, utilize uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo os localizados no local, ou em um prestador de serviços remotos, e atualizar o inventário de informação sensível da organização.

Utilize a Proteção de Informação Azure para identificar informações sensíveis dentro dos documentos do Office 365.

Utilize a Proteção de Informação Azure SQL para ajudar na classificação e rotulagem das informações armazenadas nas bases de dados Azure SQL.

Como implementar a Descoberta de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Como implementar a Proteção de Informação Azure:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.6 | 14.6 | Cliente |

Utilize o Azure AD RBAC para controlar o acesso a dados e recursos, caso contrário utilize métodos específicos de controlo de acesso do serviço.

Compreender o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Como configurar o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.7 | 14.7 | Cliente |

Implementar uma ferramenta de terceiros, como uma solução automatizada de prevenção de perdas de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.8 | 14.8 | Cliente |

Use encriptação em repouso em todos os recursos do Azure. A Microsoft recomenda que o Azure gere as suas chaves de encriptação, no entanto existe a opção de gerir as suas próprias chaves em alguns casos. 

Compreenda a encriptação em repouso em Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Como configurar as chaves de encriptação geridas pelo cliente:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.9 | 14.9 | Cliente |

Utilize o Monitor Azure com o Registo de Atividades do Azure para criar alertas para quando as alterações ocorrerem aos recursos azure críticos.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controlo de segurança: [Gestão de Vulnerabilidades](security-control-vulnerability-management.md)
