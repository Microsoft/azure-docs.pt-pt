---
title: Atributos de segurança para serviços do Azure
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007624"
---
# <a name="common-security-attributes-for-azure-services"></a>Atributos comuns de segurança para serviços do Azure

A segurança integra todos os aspectos de um serviço do Azure. Este artigo recolhe os atributos de segurança comuns para serviços do Azure selecionados. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## [<a name="azure-backup"></a>Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | A utilizar a encriptação do serviço de armazenamento para as contas de armazenamento. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Não | Através de HTTPS. |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Não |  |
| Chamadas de API encriptadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não |  |
| suporte de Injeção de vNET| Não |  |
| Isolamento de rede / suporte de firewall| Sim | O túnel forçado é suportado para cópia de segurança VM. O túnel forçado não é suportado para cargas de trabalho em execução em VMs. |
| Suporte para protocolo de túnel forçado | Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | O log Analytics é suportado através de registos de diagnóstico. Consulte a cópia de segurança do Monitor do Azure protegidos cargas de trabalho utilizar o Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Gestão de acesso - autorização| Sim | Cliente criada e funções RBAC incorporadas são usadas. Consulte Use Role-Based o controlo de acesso para gerir pontos de recuperação de cópia de segurança do Azure (/ / / cópia de segurança-rbac-rs-cofre do azure backup) para obter mais informações. |


### <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo| Sim | Todas as ações de cliente acionada no portal do Azure são registadas nos registos de atividade. |
| Registo e auditoria do plano de dados| Não | Não é possível aceder diretamente ao plano de dados de cópia de segurança do Azure.  |

### <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim|  |

## [<a name="azure-key-vault"></a>Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | Todos os objetos são encriptados. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim | Toda a comunicação é por meio de chamadas de API encriptadas |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Cliente controla todas as chaves no seu Cofre de chaves. Quando são especificadas chaves de módulo (HSM) cópia de segurança de hardware, um HSM de 2 de nível de FIPS protege a chave, o certificado ou o segredo. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Através de HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Através de pontos finais de serviço de rede Virtual (Vnet). |
| suporte de Injeção de vNET| Não |  |
| Isolamento de rede / suporte de firewall| Sim | Usando as regras de firewall da Vnet. |
| Suporte para protocolo de túnel forçado | Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Utilizar o Log Analytics. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Gestão de acesso - autorização| Sim | A utilizar a política de acesso do Cofre de chaves. |


### <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão/controlo do plano de auditoria e Registro em log| Sim | Utilizar o Log Analytics. |
| Registo e auditoria do plano de dados| Sim | Utilizar o Log Analytics. |

### <a name="access-controls"></a>Controlos de acesso

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Controlos de acesso do plano de gestão/controlo | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controlos de acesso do plano de dados (em todos os níveis de serviço) | Sim | Política de acesso do Cofre de chaves |

## [<a name="azure-service-fabric"></a>Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquina virtual (VM) do cluster é criado no. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim |  |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquina virtual (VM) do cluster é criado no. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Chamadas de API do Service Fabric são feitas através do Gestor de recursos do Azure. Um válido JSON web tokens (JWT) é necessário. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de Injeção de vNET| Sim |  |
| Isolamento de rede / suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG). |
| Suporte para protocolo de túnel forçado | Sim | Redes do Azure fornecem um túnel forçado. |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Usando o suporte de terceiros e de suporte de monitorização do Azure. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Gestão de acesso - autorização| Sim | Gestão identidades e acessos (IAM) para chamadas via SFRP. Chamadas diretamente para o ponto de final de cluster suporta duas funções: Utilizador e administrador. O cliente pode mapear as APIs para cada função. |


### <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo| Sim | Todas as operações do painel de controle executadas através de processos de auditoria e aprovações. |
| Registo e auditoria do plano de dados| N/A | Cliente é o proprietário do cluster.  |

### <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | A configuração do serviço é uma versão e implementados com o Azure implementar. O código (aplicação ou tempo de execução) tem a mesma versão de através da compilação do Azure.
 |

## <a name="azure-storage"></a>Storage do Azure

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim | Suporte a mecanismos de HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Ver [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de Injeção de vNET| N/A |  |
| Isolamento de rede / suporte de firewall| Sim | |
| Suporte para protocolo de túnel forçado | N/A |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Métricas do Azure Monitor disponíveis agora, registos de partida da pré-visualização |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | O Azure Active Directory, chave partilhada, o token de acesso partilhado. |
| Gestão de acesso - autorização| Sim | Autorização de suporte através do RBAC, POSIX ACLs e os Tokens SAS |


### <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo | Sim | Registo de atividades do Gestor de recursos do Azure |
| Registo e auditoria do plano de dados| Sim | Os registos de diagnóstico do serviço e o registo do Azure Monitor partida pré-visualização  |

### <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Suporte de controle de versão do fornecedor de recursos através de APIs do Azure Resource Manager |