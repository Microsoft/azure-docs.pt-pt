---
title: Atributos comuns de segurança para o Azure Service Fabric
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003064"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atributos de segurança para o Azure Service Fabric

Este artigo documenta os atributos de segurança integrados do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | O cliente possui o cluster e o conjunto no qual o cluster é criado de dimensionamento de máquina virtual. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim |  |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto no qual o cluster é criado de dimensionamento de máquina virtual. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Chamadas de API do Service Fabric são feitas através do Gestor de recursos do Azure. Um válido JSON web tokens (JWT) é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| Suporte de injeção de VNet| Sim |  |
| Isolamento de rede e o suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG). |
| Suporte de encapsulamento de forçado| Sim | Redes do Azure fornecem um túnel forçado. |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Usando o suporte de terceiros e de suporte de monitorização do Azure. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | Gestão identidades e acessos (IAM) para chamadas via SFRP. Chamadas diretamente para o ponto de final de cluster suporta duas funções: Utilizador e administrador. O cliente pode mapear as APIs para cada função. |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as operações do painel de controle executadas através de processos de auditoria e aprovações. |
| Auditoria e registo de plano de dados| N/A | Cliente é o proprietário do cluster.  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | |