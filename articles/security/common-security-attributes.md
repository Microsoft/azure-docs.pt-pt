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
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474533"
---
# <a name="security-attributes-for-azure-services"></a>Atributos de segurança para serviços do Azure

Este artigo recolhe os atributos de segurança comuns para serviços do Azure selecionados. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Gestão de API do Azure](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim (apenas para encriptação do lado do serviço) | Os dados confidenciais, tais como certificados, chaves e valores com o segredo nome são encriptados com gerida pelo serviço, por chaves de instância de serviço. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | [Express Route](../expressroute/index.yml) e a VNet encriptação é fornecida pela [funcionamento em rede do Azure](../virtual-network/index.yml). |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não | Todas as chaves de encriptação são por instância de serviço e serviço gerido. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de plano de gestão são realizadas através do [do Azure Resource Manager](../azure-resource-manager/index.yml) por TLS. Um válido JSON web tokens (JWT) é necessário.  Chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação suportados (por exemplo, o certificado de cliente ou o JWT).
 |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não | |
| Suporte de injeção de VNet| Sim | |
| Isolamento de rede e o suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG) e o Gateway de aplicação do Azure (ou outra aplicação de software), respectivamente. |
| Suporte de encapsulamento de forçado| Sim | Redes do Azure fornecem um túnel forçado. |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | |
| Autorização| Sim | |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | [Registos de atividade do Monitor do Azure](../azure-monitor/platform/activity-logs-overview.md) |
| Auditoria e registo de plano de dados| Sim | [Registos de diagnóstico do Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) e (opcionalmente) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Usando o [Kit de recursos de DevOps de gestão de API do Azure](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Serviço de Aplicações do Azure](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | Sim | Conteúdo do ficheiro de Web site é armazenado no armazenamento do Azure, que encripta automaticamente o conteúdo em repouso. Ver [encriptação do armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md).<br><br>Segredos do cliente fornecido são encriptados em inatividade. Os segredos são encriptados em inatividade enquanto armazenados em bases de dados de configuração de serviço de aplicações.<br><br>Discos ligados localmente, opcionalmente, podem ser utilizados como armazenamento temporário por Web sites (D:\local e % TMP %). Discos ligados localmente não são encriptados em inatividade. |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de VNet e a encriptação de VNet a VNet)| Sim | Os clientes podem configurar sites para exigir e utilizar o HTTPS para o tráfego de entrada. Consulte a mensagem de blogue [como criar um Azure App Service apenas HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Os clientes podem optar por armazenar segredos da aplicação no Key Vault e recuperá-los em tempo de execução. Ver [faz referência a utilizar o Cofre de chave para o serviço de aplicações e as funções do Azure (pré-visualização)](../app-service/app-service-key-vault-references.md).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de gestão para configurar o serviço de aplicações ocorrem através de [do Azure Resource Manager](../azure-resource-manager/index.yml) chamadas através de HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Atualmente disponível em pré-visualização para o serviço de aplicações. Ver [restrições de acesso de serviço de aplicações do Azure](../app-service/app-service-ip-restrictions.md). |
| Suporte de injeção de VNet| Sim | Ambientes de serviço de aplicações são implementações privadas do serviço de aplicações dedicado de um único cliente injetado na rede virtual de um cliente. Ver [introdução aos ambientes de serviço de aplicações](../app-service/environment/intro.md). |
| Suporte de isolamento de rede e Firewalling| Sim | Para a variação de multi-inquilino pública do serviço de aplicações, os clientes podem configurar as ACLs (restrições de IP) para bloquear o tráfego de entrada permitido de rede.  Ver [restrições de acesso de serviço de aplicações do Azure](../app-service/app-service-ip-restrictions.md).  Os ambientes de serviço de aplicações são implementados diretamente em redes virtuais e, por conseguinte, podem ser protegidos com NSGs. |
| Suporte de encapsulamento de forçado| Sim | Ambientes de serviço de aplicações podem ser implementados numa rede virtual do cliente em que o protocolo de túnel forçado está configurado. Os clientes têm de seguir as instruções [configurar o ambiente de serviço de aplicações com túnel forçado](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Serviço de aplicações se integra com o Application Insights para linguagens com suporte para o Application Insights (.NET Framework completo, .NET Core, Java e node. js).  Ver [desempenho do serviço de aplicações do Azure Monitor](../azure-monitor/app/azure-web-apps.md). Serviço de aplicações também envia métricas da aplicação no Azure Monitor. Ver [monitorizar aplicações no serviço de aplicações do Azure](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Os clientes podem criar aplicações no serviço de aplicações que se integram automaticamente [do Azure Active Directory (Azure AD)](../active-directory/index.yml) , bem como outros fornecedores de identidade compatível de OAuth; consulte [autenticação e autorização nas Serviço de aplicações do Azure](../app-service/overview-authentication-authorization.md). Para obter acesso de gestão a recursos de serviço de aplicações, todo o acesso é controlado por uma combinação de principal do Azure AD autenticado e funções do RBAC do Azure Resource Manager. |
| Autorização| Sim | Para obter acesso de gestão a recursos de serviço de aplicações, todo o acesso é controlado por uma combinação de principal do Azure AD autenticado e funções do RBAC do Azure Resource Manager.  |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as operações de gestão efetuadas em objetos do serviço de aplicações ocorrem através de [do Azure Resource Manager](../azure-resource-manager/index.yml). Os registos históricos essas operações estão disponíveis no portal e através da CLI; ver [operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [registo de atividade do monitor az](/cli/azure/monitor/activity-log). |
| Auditoria e registo de plano de dados | Não | O plano de dados para o serviço de aplicações é uma partilha de ficheiros remoto que contenha um site da web cliente s implementado conteúdo.  Não existe nenhum auditoria de partilha de ficheiros remota. |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração de serviço de aplicações pode ser exportado como um modelo Azure Resource Manager e com a versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões diferentes de em direto de um aplicativo usando a funcionalidade de ranhuras de implementação do serviço de aplicações. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | HTTPS/TLS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| N/A | O Gestor de recursos do Azure não armazena nenhum conteúdo de cliente, apenas os dados de controle. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Sim | |
| Chamadas de API encriptadas| Sim | |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não | |
| Suporte de injeção de VNet| Sim | |
| Isolamento de rede e o suporte de firewall| Não |  |
| Suporte de encapsulamento de forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Não | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [O Azure Active Directory](/azure/active-directory) com base.|
| Autorização| Sim | |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Expor todas as operações (PUT, POST, DELETE) efetuadas nos seus recursos; de escrita de registos de atividades ver [ver registos de atividades para auditar as ações em recursos](../azure-resource-manager/resource-group-audit.md). |
| Auditoria e registo de plano de dados| N/A | |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | A utilizar a encriptação do serviço de armazenamento para as contas de armazenamento. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Não | Através de HTTPS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Não |  |
| Chamadas de API encriptadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não |  |
| Suporte de injeção de VNet| Não |  |
| Isolamento de rede e o suporte de firewall| Sim | O túnel forçado é suportado para cópia de segurança VM. O túnel forçado não é suportado para cargas de trabalho em execução em VMs. |
| Suporte de encapsulamento de forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | O log Analytics é suportado através de registos de diagnóstico. Consulte a cópia de segurança do Monitor do Azure protegidos cargas de trabalho utilizar o Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | Cliente criada e funções RBAC incorporadas são usadas. Consulte Use Role-Based o controlo de acesso para gerir pontos de recuperação de cópia de segurança do Azure (/ / / cópia de segurança-rbac-rs-cofre do azure backup) para obter mais informações. |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as ações de cliente acionada no portal do Azure são registadas nos registos de atividade. |
| Auditoria e registo de plano de dados| Não | Não é possível aceder diretamente ao plano de dados de cópia de segurança do Azure.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Cofre de Chaves do Azure](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | Todos os objetos são encriptados. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Toda a comunicação é por meio de chamadas de API encriptadas |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Cliente controla todas as chaves no seu Cofre de chaves. Quando são especificadas chaves de módulo (HSM) cópia de segurança de hardware, um HSM de 2 de nível de FIPS protege a chave, o certificado ou o segredo. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Através de HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Através de pontos finais de serviço de rede Virtual (VNet). |
| Suporte de injeção de VNet| Não |  |
| Isolamento de rede e o suporte de firewall| Sim | Usando as regras de firewall da VNet. |
| Suporte de encapsulamento de forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Utilizar o Log Analytics. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | A utilizar a política de acesso do Cofre de chaves. |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão/controlo do plano de auditoria e Registro em log| Sim | Utilizar o Log Analytics. |
| Auditoria e registo de plano de dados| Sim | Utilizar o Log Analytics. |

### <a name="access-controls"></a>Controlos de acesso

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Controlos de acesso do plano de gestão/controlo | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controlos de acesso do plano de dados (em todos os níveis de serviço) | Sim | Política de acesso do Cofre de chaves |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure mensagens do Service Bus](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  Sim para servidor encriptação em repouso, por predefinição. | Chaves gerida pelo cliente e a BYOK ainda não são suportadas. Encriptação do lado do cliente é responsabilidade do cliente |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Suporta o mecanismo HTTPS/TLS padrão. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |   |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de API são feitas através de [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim (apenas no escalão Premium) | Pontos finais de serviço de VNet são suportados para [escalão Premium do Service Bus](../service-bus-messaging/service-bus-premium-messaging.md) apenas. |
| Suporte de injeção de VNet| Não | |
| Isolamento de rede e o suporte de firewall| Sim (apenas no escalão Premium) |  |
| Suporte de encapsulamento de forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Suportado através de [o Azure Monitor e alertas](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Gerido através de [do Azure Active Directory identidade do serviço gerido](../service-bus-messaging/service-bus-managed-service-identity.md); consulte [barramento de serviço de autenticação e autorização](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Autorização| Sim | Suporta a autorização via [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (pré-visualização) e o token de SAS; consulte [do Service Bus autenticação e autorização](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Os registos de operações estão disponíveis; ver [registos de diagnóstico do Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Auditoria e registo de plano de dados| Não |  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Suporta o controlo de versões de fornecedor de recursos através da [API do Azure Resource Manager](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Reencaminhamento do Azure Service Bus](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  N/A | Reencaminhamento é um soquete de web e não persiste dados. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | O serviço precisa TLS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não | Utiliza certificados de Microsoft TLS apenas.  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não |  |
| Isolamento de rede e o suporte de firewall| Não |  |
| Suporte de encapsulamento de forçado| N/A | O reencaminhamento é o túnel TLS  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Através de SAS. |
| Autorização|  Sim | Através de SAS. |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml). |
| Auditoria e registo de plano de dados| Sim | Êxito de ligação / falhas e erros e registado.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquina virtual (VM) do cluster é criado no. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim |  |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquina virtual (VM) do cluster é criado no. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Chamadas de API do Service Fabric são feitas através do Gestor de recursos do Azure. Um válido JSON web tokens (JWT) é necessário. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| Suporte de injeção de VNet| Sim |  |
| Isolamento de rede e o suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG). |
| Suporte de encapsulamento de forçado| Sim | Redes do Azure fornecem um túnel forçado. |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Usando o suporte de terceiros e de suporte de monitorização do Azure. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | Gestão identidades e acessos (IAM) para chamadas via SFRP. Chamadas diretamente para o ponto de final de cluster suporta duas funções: Utilizador e administrador. O cliente pode mapear as APIs para cada função. |

### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as operações do painel de controle executadas através de processos de auditoria e aprovações. |
| Auditoria e registo de plano de dados| N/A | Cliente é o proprietário do cluster.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | A configuração do serviço é uma versão e implementados com o Azure implementar. O código (aplicação ou tempo de execução) tem a mesma versão de através da compilação do Azure.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Base de Dados SQL do Azure](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | Conhecido como "encriptação-em-use", conforme descrito no artigo [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Encriptação do lado do serviço usa [encriptação de dados transparente](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Encriptação em trânsito:<ul><li>Encriptação do ExpressRoute</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Através de HTTPS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | São oferecidos a manipulação de chave gerida pelo serviço tanto gerida pelo cliente (o último através de [do Azure Key Vault](../key-vault/index.yml). |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Sim | Por meio [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Chamadas de API encriptadas| Sim | Através de HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Aplica-se ao [base de dados individual](../sql-database/sql-database-single-index.yml) apenas. |
| Suporte de injeção de VNet| Sim | Aplica-se ao [instância gerida](../sql-database/sql-database-managed-instance.md) apenas. |
| Isolamento de rede e o suporte de firewall| Sim | Em ambas as bases de dados - e ao nível do servidor; de firewall isolamento para de rede [instância gerida](../sql-database/sql-database-managed-instance.md) apenas |
| Suporte de encapsulamento de forçado| Sim | [instância gerida](../sql-database/sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | A solução SIEM de terceiros da Imperva (SecureSphere) também é suportada, através de [os Hubs de eventos do Azure](../event-hubs/index.yml) integração via [auditoria SQL](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Active Directory. |
| Autorização| Sim |  |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Sim, em apenas alguns eventos. |
| Auditoria e registo de plano de dados | Sim | Via [auditoria SQL](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Não  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicional para a base de dados SQL

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Preventivas: avaliação de vulnerabilidade | Sim | Ver [avaliação de vulnerabilidades do SQL service ajuda a identificar vulnerabilidades de base de dados](../sql-database/sql-vulnerability-assessment.md). |
| Preventivas: deteção de dados e a classificação  | Sim | Ver [deteção de dados de base de dados do Azure SQL e SQL Data Warehouse e classificação](../sql-database/sql-database-data-discovery-and-classification.md). |
| Deteção: deteção de ameaças | Sim | Ver [proteção avançada contra ameaças para a base de dados SQL do Azure](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Armazenamento do Azure](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Suporte a mecanismos de HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Ver [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| Suporte de injeção de VNet| N/A |  |
| Isolamento de rede e o suporte de firewall| Sim | |
| Suporte de encapsulamento de forçado| N/A |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Métricas do Azure Monitor disponíveis agora, registos de partida da pré-visualização |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | O Azure Active Directory, chave partilhada, o token de acesso partilhado. |
| Autorização| Sim | Autorização de suporte através do RBAC, POSIX ACLs e os Tokens SAS |


### <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão | Sim | Registo de atividades do Gestor de recursos do Azure |
| Auditoria e registo de plano de dados| Sim | Os registos de diagnóstico do serviço e o registo do Azure Monitor partida pré-visualização  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Suporte de controle de versão do fornecedor de recursos através de APIs do Azure Resource Manager |