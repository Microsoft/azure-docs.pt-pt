---
title: Conector de gestão de serviços de TI no Monitor Azure
description: Este artigo fornece informações sobre como ligar os seus produtos/serviços ITSM com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho do ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804025"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ligar os produtos/serviços do ITSM ao Conector de Gestão de Serviços de TI
Este artigo fornece informações sobre como configurar a ligação entre o seu produto/serviço ITSM e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho. Para obter mais informações sobre o ITSMC, consulte [a Visão Geral.](./itsmc-overview.md)

São suportados os seguintes produtos/serviços ITSM. Selecione o produto para ver informações detalhadas sobre como ligar o produto ao ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [Gestor de serviços do Centro de Sistema](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Propomos aos nossos clientes Cherwell e Provance que utilizem a [ação Webhook](./action-groups.md#webhook) para cherwell e provance como outra solução para a integração.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)