---
title: Atributos comuns de segurança para gestão de API do Azure
description: Uma lista de verificação de atributos comuns de segurança para avaliar a gestão de API
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508143"
---
# <a name="common-security-attributes-for-api-management"></a>Atributos comuns de segurança para gestão de API

A segurança integra todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comuns, criados para gestão de API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim (apenas para encriptação do lado do serviço) | Os dados confidenciais, tais como certificados, chaves e valores com o segredo nome são encriptados com gerida pelo serviço, por chaves de instância de serviço. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | [Express Route](../expressroute/index.yml) e a VNet encriptação é fornecida pela [funcionamento em rede do Azure](../virtual-network/index.yml). |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não | Todas as chaves de encriptação são por instância de serviço e serviço gerido. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de plano de gestão são realizadas através do [do Azure Resource Manager](../azure-resource-manager/index.yml) por TLS. Um válido JSON web tokens (JWT) é necessário.  Chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação suportados (por exemplo, o certificado de cliente ou o JWT).
 |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não | |
| Suporte de injeção de VNet| Sim | |
| Isolamento de rede e o suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG) e o Gateway de aplicação do Azure (ou outra aplicação de software), respectivamente. |
| Suporte de encapsulamento de forçado| Sim | Redes do Azure fornecem um túnel forçado. |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | |
| Autorização| Sim | |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | [Registos de atividade do Monitor do Azure](../azure-monitor/platform/activity-logs-overview.md) |
| Auditoria e registo de plano de dados| Sim | [Registos de diagnóstico do Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) e (opcionalmente) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Gestão da configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Usando o [Kit de recursos de DevOps de gestão de API do Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade analisa os falsos positivos

Esta secção documenta as vulnerabilidades mais comuns, não afetam a gestão de API do Azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016 9244) | Ticketbleed é uma vulnerabilidade na implementação da extensão do TLS SessionTicket encontrado em alguns produtos de F5. Ele permite que o vazamento ("sangramento") de até 31 de bytes de dados da memória não inicializada. Esta situação é provocada pela pilha de TLS preenchimento de um ID de sessão, passado do cliente, com os dados para torná-lo 32-bits longo. |