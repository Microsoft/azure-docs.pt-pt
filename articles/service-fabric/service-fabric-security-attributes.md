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
ms.openlocfilehash: b25400f21e24b18a0a21b9d849bdd505f6d47aac
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765754"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Atributos comuns de segurança para o Azure Service Fabric

A segurança integra todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comuns incorporados no Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | O cliente possui o cluster e o conjunto no qual o cluster é criado de dimensionamento de máquina virtual. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim |  |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto no qual o cluster é criado de dimensionamento de máquina virtual. Encriptação de disco do Azure pode ser ativada no conjunto de dimensionamento de máquina virtual. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Chamadas de API do Service Fabric são feitas através do Gestor de recursos do Azure. Um válido JSON web tokens (JWT) é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de injeção de vNET| Sim |  |
| Isolamento de rede e o suporte de firewall| Sim | Utilizar grupos de segurança de rede (NSG). |
| Suporte de encapsulamento de forçado| Sim | Redes do Azure fornecem um túnel forçado. |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Usando o suporte de terceiros e de suporte de monitorização do Azure. |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | Gestão identidades e acessos (IAM) para chamadas via SFRP. Chamadas diretamente para o ponto de final de cluster suporta duas funções: Utilizador e administrador. O cliente pode mapear as APIs para cada função. |


## <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Todas as operações do painel de controle executadas através de processos de auditoria e aprovações. |
| Auditoria e registo de plano de dados| N/A | Cliente é o proprietário do cluster.  |

## <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | |