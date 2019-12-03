---
title: Controlos de segurança
description: Encontre uma lista de verificação de controles de segurança para avaliar Azure App serviço para sua organização.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671440"
---
# <a name="security-controls-for-azure-app-service"></a>Controles de segurança para o serviço Azure App

Este artigo documenta os controles de segurança incorporados ao serviço Azure App.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Disponível para o serviço de aplicativo.| [Azure App restrições de acesso de serviço](app-service-ip-restrictions.md)
| Suporte à injeção de VNet| Sim | Os ambientes de serviço de aplicativo são implementações privadas do serviço de aplicativo dedicado a um único cliente injetado na rede virtual de um cliente. | [Introdução aos ambientes do serviço de aplicativo](environment/intro.md)
| Isolamento de rede e suporte de firewall| Sim | Para a variação pública de vários locatários do serviço de aplicativo, os clientes podem configurar ACLs de rede (restrições de IP) para bloquear o tráfego de entrada permitido.  Os ambientes do serviço de aplicativo são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com o NSGs. | [Azure App restrições de acesso de serviço](app-service-ip-restrictions.md)
| Suporte a túnel forçado| Sim | Os ambientes do serviço de aplicativo podem ser implantados na rede virtual do cliente em que o túnel forçado está configurado. | [Configurar o Ambiente de Serviço de Aplicações com túnel forçado](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | O serviço de aplicativo integra-se com Application Insights para idiomas que dão suporte a Application Insights (completo .NET Framework, .NET Core, Java e node. JS).  Consulte [monitorar o desempenho do serviço Azure app](../azure-monitor/app/azure-web-apps.md). O serviço de aplicativo também envia métricas de aplicativo para Azure Monitor. | [Monitorar aplicativos no serviço Azure App](web-sites-monitor.md)
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações de gerenciamento executadas nos objetos do serviço de aplicativo ocorrem por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). Os logs históricos dessas operações estão disponíveis no portal e por meio da CLI. | [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-log](/cli/azure/monitor/activity-log)
| Log e auditoria do plano de dados | Não | O plano de dados para o serviço de aplicativo é um compartilhamento de arquivo remoto que contém o conteúdo do site implantado de um cliente.  Não há auditoria do compartilhamento de arquivos remoto. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Notas |  Documentação
|---|---|--|
| Autenticação| Sim | Os clientes podem criar aplicativos no serviço de aplicativo que se integram automaticamente com o [Azure Active Directory (AD do Azure)](../active-directory/index.yml) , bem como outros provedores de identidade compatíveis com OAuth para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC. | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)
| Autorização| Sim | Para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC.  | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O conteúdo do arquivo do site é armazenado no armazenamento do Azure, que criptografa automaticamente o conteúdo em repouso. <br><br>Os segredos fornecidos pelo cliente são criptografados em repouso. Os segredos são criptografados em repouso enquanto estão armazenados nos bancos de dados de configuração do serviço de aplicativo.<br><br>Os discos anexados localmente podem, opcionalmente, ser usados como armazenamento temporário por sites (D:\Local e% TMP%). Os discos anexados localmente não são criptografados em repouso. | [Criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Os clientes podem optar por armazenar os segredos do aplicativo no Key Vault e recuperá-los em tempo de execução. | [Usar referências de Key Vault para o serviço de aplicativo e Azure Functions (visualização)](app-service-key-vault-references.md)
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Os clientes podem configurar sites para exigir e usar HTTPS para tráfego de entrada.  | [Como tornar um serviço Azure app somente HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (postagem de blog)
| Chamadas de API criptografadas| Sim | As chamadas de gerenciamento para configurar o serviço de aplicativo ocorrem por meio de chamadas [Azure Resource Manager](../azure-resource-manager/index.yml) por HTTPS. |

## <a name="configuration-management"></a>Gestão de configurações

| Controle de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de serviço de aplicativo pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões dinâmicas diferentes de um aplicativo usando o recurso de Slots de implantação do serviço de aplicativo. | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
