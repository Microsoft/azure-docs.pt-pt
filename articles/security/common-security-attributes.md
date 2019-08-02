---
title: Atributos de segurança para serviços do Azure
description: Uma lista de verificação de atributos de segurança para avaliar os serviços do Azure
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 0010273d41769c57144fdde63e47c528f313a228
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443353"
---
# <a name="security-attributes-for-azure-services"></a>Atributos de segurança para serviços do Azure

Este artigo coleta os atributos de segurança para os serviços do Azure selecionados. Um atributo de segurança é uma qualidade ou um recurso de um serviço do Azure. Ele contribui para a capacidade do serviço de prevenir, detectar e responder a vulnerabilidades de segurança.

Os atributos de segurança são categorizados como:
* Preventiva
* Segmentação de rede
* Deteção
* Suporte para gerenciamento de acesso e identidade
* Trilha de auditoria
* Controles de acesso (se usados)
* Gerenciamento de configuração (se usado)

Em cada categoria, mostramos "Sim" ou "não" para indicar se um atributo é usado. Para alguns serviços, mostramos "N/A" para um atributo que não é aplicável. Também poderemos fornecer uma observação ou um link para obter mais informações sobre um atributo.

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[Gestão de API](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Dados confidenciais, como certificados, chaves e valores nomeados por segredo, são criptografados com chaves gerenciadas por serviço, por chave de instância de serviço. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | A criptografia de [rota expressa](../expressroute/index.yml) e VNet é fornecida pela [rede do Azure](../virtual-network/index.yml). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | Todas as chaves de criptografia são por instância de serviço e são gerenciadas pelo serviço. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | As chamadas do plano de gerenciamento são feitas por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) sobre TLS. Um JWT (token Web JSON) válido é necessário.  As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação com suporte (por exemplo, certificado de cliente ou JWT).
 |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Sim | Usar NSG (grupos de segurança de rede) e Aplicativo Azure gateway (ou outro dispositivo de software), respectivamente. |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | |
| Autorização| Sim | |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | [Logs de atividades Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Log e auditoria do plano de dados| Sim | [Azure monitor logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) e (opcionalmente) [aplicativo Azure](../azure-monitor/app/app-insights-overview.md)insights.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Usando o [DevOps Resource Kit de gerenciamento de API do Azure](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade verifica falsos positivos

Esta seção documenta as vulnerabilidades comuns, que não afetam o gerenciamento de API do Azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed é uma vulnerabilidade na implementação da extensão SessionTicket do TLS encontrada em alguns produtos F5. Ele permite o vazamento ("sangramento") de até 31 bytes de dados de memória não inicializada. Isso é causado pelo preenchimento da pilha TLS de uma ID de sessão, passada do cliente, com dados para torná-la de 32 bits de comprimento. |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[Serviço de Aplicações](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | O conteúdo do arquivo do site é armazenado no armazenamento do Azure, que criptografa automaticamente o conteúdo em repouso. Consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).<br><br>Os segredos fornecidos pelo cliente são criptografados em repouso. Os segredos são criptografados em repouso enquanto estão armazenados nos bancos de dados de configuração do serviço de aplicativo.<br><br>Os discos anexados localmente podem, opcionalmente, ser usados como armazenamento temporário por sites (D:\Local e% TMP%). Os discos anexados localmente não são criptografados em repouso. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | Os clientes podem configurar sites para exigir e usar HTTPS para tráfego de entrada. Consulte a postagem no blog [como tornar um serviço Azure app somente HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | Os clientes podem optar por armazenar os segredos do aplicativo no Key Vault e recuperá-los em tempo de execução. Consulte [usar referências de Key Vault para o serviço de aplicativo e Azure Functions (versão prévia)](../app-service/app-service-key-vault-references.md).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | As chamadas de gerenciamento para configurar o serviço de aplicativo ocorrem por meio de chamadas [Azure Resource Manager](../azure-resource-manager/index.yml) por HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Disponível atualmente na visualização do serviço de aplicativo. Consulte [Azure app restrições de acesso de serviço](../app-service/app-service-ip-restrictions.md). |
| Suporte à injeção de VNet| Sim | Os ambientes de serviço de aplicativo são implementações privadas do serviço de aplicativo dedicado a um único cliente injetado na rede virtual de um cliente. Consulte [introdução aos ambientes do serviço de aplicativo](../app-service/environment/intro.md). |
| Isolamento de rede e suporte de firewall| Sim | Para a variação pública de vários locatários do serviço de aplicativo, os clientes podem configurar ACLs de rede (restrições de IP) para bloquear o tráfego de entrada permitido.  Consulte [Azure app restrições de acesso de serviço](../app-service/app-service-ip-restrictions.md).  Os ambientes do serviço de aplicativo são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com o NSGs. |
| Suporte a túnel forçado| Sim | Os ambientes do serviço de aplicativo podem ser implantados na rede virtual do cliente em que o túnel forçado está configurado. Os clientes precisam seguir as instruções em [configurar seu ambiente do serviço de aplicativo com túnel forçado](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | O serviço de aplicativo integra-se com Application Insights para idiomas que dão suporte a Application Insights (completo .NET Framework, .NET Core, Java e node. JS).  Consulte [monitorar o desempenho do serviço Azure app](../azure-monitor/app/azure-web-apps.md). O serviço de aplicativo também envia métricas de aplicativo para Azure Monitor. Consulte [monitorar aplicativos no serviço Azure app](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Os clientes podem criar aplicativos no serviço de aplicativo que se integram automaticamente com o [Azure Active Directory (AD do Azure)](../active-directory/index.yml) , bem como outros provedores de identidade compatíveis com OAuth; consulte [autenticação e autorização no serviço de Azure app](../app-service/overview-authentication-authorization.md). Para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC. |
| Autorização| Sim | Para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC.  |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações de gerenciamento executadas nos objetos do serviço de aplicativo ocorrem por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). Os logs históricos dessas operações estão disponíveis no portal e por meio da CLI; consulte [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Log e auditoria do plano de dados | Não | O plano de dados para o serviço de aplicativo é um compartilhamento de arquivo remoto que contém o conteúdo do site implantado de um cliente.  Não há auditoria do compartilhamento de arquivos remoto. |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de serviço de aplicativo pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões dinâmicas diferentes de um aplicativo usando o recurso de Slots de implantação do serviço de aplicativo. | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | HTTPS/TLS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| N/A | O Azure Resource Manager não armazena nenhum conteúdo do cliente, só controla os dados. |
| Criptografia em nível de coluna (serviços de dados do Azure)| Sim | |
| Chamadas de API criptografadas| Sim | |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte a túnel forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Não | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Baseado em [Azure Active Directory](/azure/active-directory) .|
| Autorização| Sim | |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Os logs de atividades expõem todas as operações de gravação (PUT, POST e DELETE) executadas em seus recursos; consulte [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/resource-group-audit.md). |
| Log e auditoria do plano de dados| N/A | |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | Usando a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Não | Usando HTTPS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Não |  |
| Chamadas de API criptografadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | O túnel forçado tem suporte para backup de VM. O túnel forçado não tem suporte para cargas de trabalho em execução dentro de VMs. |
| Suporte a túnel forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Há suporte para Log Analytics por meio dos logs de diagnóstico. Consulte [monitorar cargas de trabalho protegidas de backup do Azure usando log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | As funções RBAC criadas e internas do cliente são usadas. Consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação de backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as ações disparadas pelo cliente da portal do Azure são registradas em logs de atividades. |
| Log e auditoria do plano de dados| Não | O plano de dados do backup do Azure não pode ser acessado diretamente.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[BD do Cosmos](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Todos os bancos de dados e backups do Cosmos DB são criptografados por padrão; consulte [criptografia de dados em Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md). Não há suporte para a criptografia do lado do servidor com chaves gerenciadas pelo cliente. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Todos os dados de Azure Cosmos DB são criptografados em trânsito. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Sim | Somente na API de tabelas Premium. Nem todas as APIs dão suporte a esse recurso. Consulte [introdução ao Azure Cosmos DB: API de Tabela](../cosmos-db/table-introduction.md). |
| Chamadas de API criptografadas| Sim | Todas as conexões com Azure Cosmos DB dão suporte a HTTPS. O Azure Cosmos DB também dá suporte a conexões TLS 1,2, mas isso ainda não é imposto. Se os clientes desligarem o TLS de nível inferior em seu final, eles poderão se conectar ao Cosmos DB.  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim | Com o ponto de extremidade do serviço VNet, você pode configurar uma conta de Azure Cosmos DB para permitir o acesso somente de uma sub-rede específica de uma rede virtual (VNet). Você também pode combinar o acesso VNet com as regras de firewall.  Consulte [acessar Azure Cosmos DB de redes virtuais](../cosmos-db/VNet-service-endpoint.md). |
| Isolamento de rede e suporte de firewall| Sim | Com o suporte a firewall, você pode configurar sua conta do Azure Cosmos para permitir o acesso somente de um conjunto aprovado de endereços IP, um intervalo de endereços IP e/ou serviços de nuvem. Consulte [Configurar o firewall de IP no Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md).|
| Suporte a túnel forçado| Sim | Pode ser configurado no lado do cliente na VNet em que as máquinas virtuais estão localizadas.   |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Todas as solicitações enviadas para Azure Cosmos DB são registradas. Há suporte para o [monitoramento do Azure](../azure-monitor/overview.md), as métricas do Azure, o log de auditoria do Azure.  Você pode registrar informações correspondentes às solicitações de plano de dados, estatísticas de tempo de execução de consulta, texto de consulta, solicitações do MongoDB. Você também pode configurar alertas. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Authentication| Sim | Sim, no nível da conta do banco de dados; no nível do plano de dados, Cosmos DB usa tokens de recurso e acesso à chave. |
| Autorização| Sim | Com suporte na conta do Azure cosmos com chaves mestras (primária e secundária) e tokens de recurso. Você pode obter acesso de leitura/gravação ou somente leitura aos dados com chaves mestras. Tokens de recurso permitem acesso de tempo limitado a recursos como documentos e contêineres. |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades do Azure para operações de nível de conta, como firewalls, VNets, acesso de chaves e IAM. |
| Log e auditoria do plano de dados | Sim | Diagnóstico monitoramento de log para operações em nível de contêiner, como criar contêiner, provisionar taxa de transferência, políticas de indexação e operações CRUD em documentos. |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Não  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Atributos de segurança adicionais para Cosmos DB

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| CORS (compartilhamento de recursos entre origens) | Sim | Consulte [Configurar o CORS (compartilhamento de recursos entre origens)](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md). |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[Hubs de Eventos](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) |  Sim | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Não | |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | |
| Autorização|  Sim | |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Sim |   |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) |  N/A | O ExpressRoute não armazena dados do cliente. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Não | |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| N/A |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A |  |
| Suporte à injeção de VNet| N/A | |
| Isolamento de rede e suporte de firewall| Sim | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte a túnel forçado| N/A | Via Border Gateway Protocol (BGP). |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitoramento, métricas e alertas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md).|

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |
| Autorização|  Sim |Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas| 
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Não |   |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio do NRP (provedor de recursos de rede). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Cofre de Chaves](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Todos os objetos são criptografados. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | Toda a comunicação é por meio de chamadas de API criptografadas |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente controla todas as chaves em seu Key Vault. Quando as chaves com suporte ao módulo de segurança de hardware (HSM) são especificadas, um HSM de nível 2 do FIPS protege a chave, o certificado ou o segredo. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Chamadas de API criptografadas| Sim | Usando HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de rede virtual (VNet). |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Usando regras de firewall de VNet. |
| Suporte a túnel forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando Log Analytics. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso Key Vault. |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento/controle| Sim | Usando Log Analytics. |
| Log e auditoria do plano de dados| Sim | Usando Log Analytics. |

### <a name="access-controls"></a>Controlos de acesso

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Controles de acesso do plano de gerenciamento/controle | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso de Key Vault |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Balanceador de Carga](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | N/A | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| N/A | |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| N/A | |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Por meio do [Azure Resource Manager](../azure-resource-manager/index.yml). |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A | |
| Suporte à injeção de VNet| N/A | . |
| Isolamento de rede e suporte de firewall| N/A |  |
| Suporte a túnel forçado| N/A | |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](../load-balancer/load-balancer-monitor-log.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| N/A |  |
| Autorização| N/A |  |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](../load-balancer/load-balancer-monitor-log.md). |
| Log e auditoria do plano de dados | N/A |  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| N/A |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Mensagens do barramento de serviço](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) |  Sim, para criptografia do lado do servidor, em repouso, por padrão. | As chaves gerenciadas pelo cliente e BYOK ainda não têm suporte. A criptografia do lado do cliente é a responsabilidade do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | Dá suporte ao mecanismo HTTPS/TLS padrão. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |   |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | As chamadas à API são feitas por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim (somente camada Premium) | Os pontos de extremidade do serviço VNet têm suporte somente para a [camada Premium do barramento de serviço](../service-bus-messaging/service-bus-premium-messaging.md) . |
| Suporte à injeção de VNet| Não | |
| Isolamento de rede e suporte de firewall| Sim (somente camada Premium) |  |
| Suporte a túnel forçado| Não |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Com suporte via [Azure monitor e alertas](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Gerenciado por meio de [Azure Active Directory identidade de serviço gerenciada](../service-bus-messaging/service-bus-managed-service-identity.md); consulte [autenticação e autorização do barramento de serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Autorização| Sim | Dá suporte à autorização via [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (visualização) e token SAS; consulte [autenticação e autorização do barramento de serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md). |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Os logs de operações estão disponíveis; consulte [os logs de diagnóstico do barramento de serviço](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Log e auditoria do plano de dados| Não |  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Dá suporte ao controle de versão do provedor de recursos por meio da [API Azure Resource Manager](/rest/api/resources/).|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Reencaminhamento do Service Bus](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) |  N/A | A retransmissão é um soquete da Web e não mantém os dados. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | O serviço requer TLS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | Usa somente certificados TLS da Microsoft.  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte a túnel forçado| N/A | Retransmissão é o túnel TLS  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Via SAS. |
| Autorização|  Sim | Via SAS. |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Log e auditoria do plano de dados| Sim | Êxito na conexão/falha e erros e registrados.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Chamadas de API criptografadas| Sim | Service Fabric chamadas à API são feitas por meio de Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Usando NSG (grupos de segurança de rede). |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o suporte ao monitoramento do Azure e o suporte de terceiros. |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | IAM (gerenciamento de acesso e identidade) para chamadas via SFRP. As chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer função. |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações do plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Log e auditoria do plano de dados| N/A | O cliente possui o cluster.  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[Base de Dados SQL](../sql-database/sql-database-security-attributes.md)

O banco de dados SQL inclui [um único banco de dados e uma](../sql-database/sql-database-single-index.yml) [instância gerenciada](../sql-database/sql-database-managed-instance.md). As entradas a seguir se aplicam a ambas as ofertas, exceto onde indicado de outra forma.

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Chamado "criptografia em uso", conforme descrito no artigo [Always Encrypted](../sql-database/sql-database-always-encrypted.md). A criptografia do lado do servidor usa a Transparent [Data Encryption](../sql-database/transparent-data-encryption-azure-sql.md).|
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | Usando HTTPS. |
| Criptografia – manipulação de chaves, como CMK ou BYOK| Sim | O tratamento de chaves gerenciado pelo serviço e gerenciado pelo cliente é oferecido. O último é oferecido por meio de [Azure Key Vault](../key-vault/index.yml). |
| Criptografia em nível de coluna fornecida pelos serviços de dados do Azure| Sim | Por meio de [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Chamadas de API criptografadas| Sim | Usando HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Aplica-se somente a [um único banco de dados](../sql-database/sql-database-single-index.yml) . |
| Suporte à injeção de rede virtual do Azure| Sim | Aplica-se somente à [instância gerenciada](../sql-database/sql-database-managed-instance.md) . |
| Isolamento de rede e suporte a firewall| Sim | Firewall no nível do banco de dados e do servidor. O isolamento de rede é apenas para [instância gerenciada](../sql-database/sql-database-managed-instance.md) . |
| Suporte a túnel forçado| Sim | [Instância gerenciada](../sql-database/sql-database-managed-instance.md) por meio de uma VPN de [ExpressRoute](../expressroute/index.yml) . |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure, como Log Analytics ou Application Insights| Sim | O SecureSphere, a solução SIEM da Imperva, também tem suporte por meio da integração dos [hubs de eventos do Azure](../event-hubs/index.yml) por meio da [auditoria do SQL](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhum |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Log e auditoria do plano de controle e do plano de gerenciamento| Sim | Sim apenas para alguns eventos |
| Log e auditoria do plano de dados | Sim | Por meio da [auditoria do SQL](../sql-database/sql-database-auditing.md) |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração, como o controle de versão da configuração| Não  | Nenhum |

### <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicionais para o banco de dados SQL

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Preventivo: avaliação de vulnerabilidade | Sim | Consulte [serviço de avaliação de vulnerabilidade do SQL ajuda a identificar vulnerabilidades de banco de dados](../sql-database/sql-vulnerability-assessment.md). |
| Preventivo: descoberta e classificação de dados  | Sim | Consulte [classificação de & do banco de dados SQL do Azure e SQL data warehouse a descoberta de data](../sql-database/sql-database-data-discovery-and-classification.md). |
| Detecção: detecção de ameaças | Sim | Consulte [proteção avançada contra ameaças para o banco de dados SQL do Azure](../sql-database/sql-database-threat-detection-overview.md). |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[Armazenamento](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet) | Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de serem transmitidos para o serviço. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | Consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Chamadas de API criptografadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| N/A |  |
| Isolamento de rede e suporte de firewall| Sim | |
| Suporte a túnel forçado| N/A |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Azure Monitor métricas disponíveis agora, logs iniciando a visualização |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Azure Active Directory, chave compartilhada, token de acesso compartilhado. |
| Autorização| Sim | Suporte à autorização via RBAC, ACLs POSIX e tokens SAS |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle | Sim | Log de atividades Azure Resource Manager |
| Log e auditoria do plano de dados| Sim | Logs de diagnóstico de serviço e log de Azure Monitor iniciando a visualização  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte ao controle de versão do provedor de recursos por meio de APIs de Azure Resource Manager |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais

[](../virtual-machines/windows/virtual-machines-windows-security-attributes.md)VMs | do Linux[Windows VMs](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | conjuntos de dimensionamento de[máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)


### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Consulte [como criptografar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/encrypt-disks) e [criptografar discos virtuais em uma VM do Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | As máquinas virtuais do Azure dão suporte à criptografia de [ExpressRoute](/azure/expressroute) e VNet. Consulte [criptografia em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia do Azure com suporte; consulte [visão geral da criptografia do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Via HTTPS e SSL. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | |
| Suporte à injeção de VNet| Sim | . |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Sim | Consulte [Configurar o túnel forçado usando o modelo de implantação Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitorar e atualizar uma máquina virtual Linux no Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorar e atualizar uma máquina virtual do Windows no Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim |  |
| Autorização| Sim |  |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados | Não |  |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[Gateway de VPN](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | N/A | Dados de cliente de trânsito de gateway de VPN, não armazena dados do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O gateway de VPN criptografa os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte à criptografia de VNet para VNet. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | As chaves pré-compartilhadas especificadas pelo cliente são criptografadas em repouso; Mas não integrado com o CMK ainda. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e https  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A | |
| Suporte à injeção de VNet| N/A | . |
| Isolamento de rede e suporte de firewall| Sim | Gateways de VPN são instâncias de VM dedicadas para cada rede virtual de cliente  |
| Suporte a túnel forçado| Sim |  |

### <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [Azure monitor logs de diagnóstico/alerta](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor métricas/alerta](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerenciar o serviço e configurar o gateway de VPN do Azure. |
| Autorização| Sim | Suporte à autorização via [RBAC](../role-based-access-control/overview.md). |

### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades Azure Resource Manager. |
| Log e auditoria do plano de dados | Sim | [Azure monitor logs de diagnóstico](../azure-resource-manager/resource-group-audit.md) para auditoria e log de conectividade de VPN. |

### <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de gateway de VPN do Azure pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. | 

