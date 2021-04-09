---
title: Conector de gestão de serviços de TI no Monitor Azure
description: Este artigo fornece informações sobre como ligar os seus produtos/serviços ITSM com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho do ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009322"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ligar os produtos/serviços do ITSM ao Conector de Gestão de Serviços de TI
Este artigo fornece informações sobre como configurar a ligação entre o seu produto/serviço ITSM e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho. Para obter mais informações sobre o ITSMC, consulte [a Visão Geral.](./itsmc-overview.md)

São suportados os seguintes produtos/serviços ITSM. Selecione o produto para ver informações detalhadas sobre como ligar o produto ao ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Propomos aos nossos clientes Cherwell e Provance que utilizem a [ação Webhook](./action-groups.md#webhook) para cherwell e provance como outra solução para a integração.

## <a name="ip-ranges-for-itsm-partners-connections"></a>Gamas IP para ligações de parceiros ITSM
Para enumerar os endereços IP itsm para permitir ligações ITSM de parceiros ferramentas ITSM, recomendamos que enuseça toda a gama pública de IP da região de Azure onde o seu espaço de trabalho LogAnalytics pertence. [detalhes aqui](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Para as regiões EUS/WEU/EUS2/WUS2/US South Central, o cliente só pode listar a etiqueta de rede do ActionGroup.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)
