---
title: Atributos de segurança para o Azure Cosmos DB
description: Uma lista de verificação de atributos de segurança para avaliar o Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480475"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Atributos de segurança para o Azure Cosmos DB

Este artigo documenta os atributos comuns de segurança integrados do Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | Sim | Todas as bases de dados do Cosmos DB e as cópias de segurança são criptografadas por padrão. ver [encriptação de dados no Azure Cosmos DB](database-encryption-at-rest.md). Encriptação do lado do servidor com chaves geridas pelo cliente não é suportada. |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de Vnet e a encriptação de VNet a VNet)| Sim | Todos os dados do Azure Cosmos DB são encriptados em trânsito. |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Sim | Apenas no Premium de API de tabelas. Nem todas as APIs suportam esta funcionalidade. Consulte [introdução ao Azure Cosmos DB: API de tabela](table-introduction.md). |
| Chamadas de API encriptadas| Sim | Todas as ligações ao Azure Cosmos DB suportam HTTPS. O Azure Cosmos DB também suporta ligações de TLS 1.2, mas isso não é imposto ainda. Se os clientes desativar de nível inferior TLS no respetivo fim, pode certificar-se para ligar ao Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de Injeção de vNET| Sim | Com o ponto final de serviço de VNet, pode configurar uma conta do Azure Cosmos DB para permitir o acesso apenas a partir de uma sub-rede específica de uma rede virtual (VNet). Também pode combinar o acesso de VNet com regras de firewall.  Ver [acesso do Azure Cosmos DB a partir de redes virtuais](vnet-service-endpoint.md). |
| Suporte de isolamento de rede e Firewalling| Sim | Com o suporte de firewall, pode configurar a sua conta do Cosmos do Azure para permitir o acesso apenas a partir de um conjunto aprovado de endereços IP, um intervalo de endereços IP e/ou serviços em nuvem. Ver [firewall do IP de configurar no Azure Cosmos DB](how-to-configure-firewall.md).|
| Suporte para protocolo de túnel forçado | Sim | Pode ser configurado no lado do cliente na VNET onde estão localizadas as máquinas virtuais.   |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Todos os pedidos que são enviados para o Azure Cosmos DB são registados. [Monitorização do Azure](../azure-monitor/overview.md), métricas do Azure, o registo de auditoria do Azure são suportados.  Pode iniciar sessão informações correspondentes para pedidos de plano de dados, as estatísticas de tempo de execução de consulta, texto da consulta, o MongoDB pedidos. Também pode configurar alertas. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Autenticação| Sim | Sim ao nível da conta de base de dados; no nível de plano de dados, o Cosmos DB utiliza tokens de recurso e o acesso à chave. |
| Autorização| Sim | Suportada na conta do Cosmos do Azure com chaves mestras (primário e secundário) e tokens de recursos. Pode obter de leitura/escrita ou apenas o acesso aos dados com o mestre de chaves de leitura. Os tokens de recursos permitem acesso de tempo limitado a recursos como documentos e contentores. |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo| Sim | Registo de atividades do Azure para operações de nível de conta, tais como Firewalls, VNets, acesso a chaves e IAM. |
| Registo e auditoria do plano de dados | Sim | Como o registo para operações de nível de contêiner de monitoramento de diagnósticos criar contentor, débito de aprovisionamento, a indexação de políticas e as operações de CRUD em documentos. |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Não  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Atributos de segurança adicional para o Cosmos DB

| Atributo de segurança | Sim/não | Notas|
|---|---|--|
| Cruzada (CORS) de partilha de recursos de origem | Sim | Ver [configurar recursos de várias origens (CORS) de partilha](how-to-configure-cross-origin-resource-sharing.md). |
