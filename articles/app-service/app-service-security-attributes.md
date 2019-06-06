---
title: Atributos de segurança para o serviço de aplicações do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o serviço de aplicações do Azure
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475090"
---
# <a name="security-attributes-for-azure-app-service"></a>Atributos de segurança para o serviço de aplicações do Azure

Este artigo documenta os atributos comuns de segurança incorporados no serviço de aplicações do Azure.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | Sim | Conteúdo do ficheiro de Web site é armazenado no armazenamento do Azure, que encripta automaticamente o conteúdo em repouso. Ver [encriptação do armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md).<br><br>Segredos do cliente fornecido são encriptados em inatividade. Os segredos são encriptados em inatividade enquanto armazenados em bases de dados de configuração de serviço de aplicações.<br><br>Discos ligados localmente, opcionalmente, podem ser utilizados como armazenamento temporário por Web sites (D:\local e % TMP %). Discos ligados localmente não são encriptados em inatividade. |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de VNet e a encriptação de VNet a VNet)| Sim | Os clientes podem configurar sites para exigir e utilizar o HTTPS para o tráfego de entrada. Consulte a mensagem de blogue [como criar um Azure App Service apenas HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Os clientes podem optar por armazenar segredos da aplicação no Key Vault e recuperá-los em tempo de execução. Ver [faz referência a utilizar o Cofre de chave para o serviço de aplicações e as funções do Azure (pré-visualização)](app-service-key-vault-references.md).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de gestão para configurar o serviço de aplicações ocorrem através de [do Azure Resource Manager](../azure-resource-manager/index.yml) chamadas através de HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Atualmente disponível em pré-visualização para o serviço de aplicações. Ver [restrições de acesso de serviço de aplicações do Azure](app-service-ip-restrictions.md). |
| Suporte de injeção de VNet| Sim | Ambientes de serviço de aplicações são implementações privadas do serviço de aplicações dedicado de um único cliente injetado na rede virtual de um cliente. Ver [introdução aos ambientes de serviço de aplicações](environment/intro.md). |
| Suporte de isolamento de rede e Firewalling| Sim | Para a variação de multi-inquilino pública do serviço de aplicações, os clientes podem configurar as ACLs (restrições de IP) para bloquear o tráfego de entrada permitido de rede.  Ver [restrições de acesso de serviço de aplicações do Azure](app-service-ip-restrictions.md).  Os ambientes de serviço de aplicações são implementados diretamente em redes virtuais e, por conseguinte, podem ser protegidos com NSGs. |
| Suporte de encapsulamento de forçado| Sim | Ambientes de serviço de aplicações podem ser implementados numa rede virtual do cliente em que o protocolo de túnel forçado está configurado. Os clientes têm de seguir as instruções [configurar o ambiente de serviço de aplicações com túnel forçado](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Serviço de aplicações se integra com o Application Insights para linguagens com suporte para o Application Insights (.NET Framework completo, .NET Core, Java e node. js).  Ver [desempenho do serviço de aplicações do Azure Monitor](../azure-monitor/app/azure-web-apps.md). Serviço de aplicações também envia métricas da aplicação no Azure Monitor. Ver [monitorizar aplicações no serviço de aplicações do Azure](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Os clientes podem criar aplicações no serviço de aplicações que se integram automaticamente [do Azure Active Directory (Azure AD)](../active-directory/index.yml) , bem como outros fornecedores de identidade compatível de OAuth; consulte [autenticação e autorização nas Serviço de aplicações do Azure](overview-authentication-authorization.md). Para obter acesso de gestão a recursos de serviço de aplicações, todo o acesso é controlado por uma combinação de principal do Azure AD autenticado e funções do RBAC do Azure Resource Manager. |
| Autorização| Sim | Para obter acesso de gestão a recursos de serviço de aplicações, todo o acesso é controlado por uma combinação de principal do Azure AD autenticado e funções do RBAC do Azure Resource Manager.  |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as operações de gestão efetuadas em objetos do serviço de aplicações ocorrem através de [do Azure Resource Manager](../azure-resource-manager/index.yml). Os registos históricos essas operações estão disponíveis no portal e através da CLI; ver [operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [registo de atividade do monitor az](/cli/azure/monitor/activity-log). |
| Auditoria e registo de plano de dados | Não | O plano de dados para o serviço de aplicações é uma partilha de ficheiros remoto que contêm conteúdo de site da web implantado de um cliente.  Não existe nenhum auditoria de partilha de ficheiros remota. |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração de serviço de aplicações pode ser exportado como um modelo Azure Resource Manager e com a versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões diferentes de em direto de um aplicativo usando a funcionalidade de ranhuras de implementação do serviço de aplicações. | 

