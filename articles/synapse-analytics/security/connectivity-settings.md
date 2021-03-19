---
title: Definições de conectividade Azure Synapse
description: Um artigo que o ensina a configurar configurações de conectividade no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587938"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Definições de conectividade Azure Synapse Analytics

Este artigo explicará as definições de conectividade no Azure Synapse Analytics e como configurá-las sempre que aplicável.


## <a name="connection-policy"></a>Política de conexão
A política de ligação para o Sinapse SQL em Azure Synapse Analytics está definida como *Padrão*. Não pode alterar isto no Azure Synapse Analytics. Pode saber mais sobre como isso afeta as ligações ao Synapse SQL em Azure Synapse Analytics [aqui.](../../azure-sql/database/connectivity-architecture.md#connection-policy) 

## <a name="minimal-tls-version"></a>Versão mínima do TLS
O Synapse SQL em Azure Synapse Analytics permite ligações utilizando todas as versões TLS. Não é possível definir a versão TLS mínima para Synapse SQL em Azure Synapse Analytics.

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](./synapse-workspace-ip-firewall.md)