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
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473090"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Definições de conectividade Azure Synapse Analytics

Este artigo explicará as definições de conectividade no Azure Synapse Analytics e como configurá-las sempre que aplicável.


## <a name="connection-policy"></a>Política de conexão
A política de ligação para o Sinapse SQL em Azure Synapse Analytics está definida como *Padrão*. Não pode alterar isto no Azure Synapse Analytics. Pode saber mais sobre como isso afeta as ligações ao Synapse SQL em Azure Synapse Analytics [aqui.](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) 

## <a name="minimal-tls-version"></a>Versão mínima do TLS
O Synapse SQL em Azure Synapse Analytics permite ligações utilizando todas as versões TLS. Não é possível definir a versão TLS mínima para Synapse SQL em Azure Synapse Analytics.

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](./synapse-workspace-ip-firewall.md)
