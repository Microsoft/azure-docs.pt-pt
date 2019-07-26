---
title: Atributos de segurança para o Azure Service Fabric
description: Uma lista de verificação de atributos de segurança para avaliar o Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443860"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atributos de segurança para o Azure Service Fabric

Este artigo documenta os atributos de segurança internos do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim |  |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Chamadas de API criptografadas| Sim | Service Fabric chamadas à API são feitas por meio de Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Usando NSG (grupos de segurança de rede). |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o suporte ao monitoramento do Azure e o suporte de terceiros. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | IAM (gerenciamento de acesso e identidade) para chamadas via SFRP. As chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer função. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações do plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Log e auditoria do plano de dados| N/A | O cliente possui o cluster.  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |