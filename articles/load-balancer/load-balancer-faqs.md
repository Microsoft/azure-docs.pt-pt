---
title: Perguntas frequentes - Azure Load Balancer
description: Respostas a perguntas frequentes sobre o Equilíbrio de Carga Azure.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005143"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

## <a name="what-types-of-load-balancer-exist"></a>Que tipos de Balancer de Carga existem?
Os equilibradores de carga internos que equilibram o tráfego dentro de um VNET e equilibram os equilibradores de carga externos que equilibram o tráfego de e para um ponto final ligado à Internet. Para mais informações, consulte Os Tipos de [Equilíbrio de Carga](components.md#frontend-ip-configurations). 

Para ambos os tipos, o Azure oferece um SKU básico e um SKU padrão que têm diferentes capacidades funcionais, de desempenho, segurança e de rastreio de saúde. Estas diferenças são explicadas no nosso artigo de [Comparação SKU.](skus.md)

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Como posso atualizar de um Basic para um Balancer de Carga Padrão?
Consulte a [atualização do](upgrade-basic-standard.md) artigo Basic to Standard para obter um script automatizado e orientação sobre a atualização de um SKU de Equilíbrio de Carga.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quais são as diferentes opções de equilíbrio de carga em Azure?
Consulte o guia de tecnologia de equilíbrio de [carga](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) para os serviços de equilíbrio de carga disponíveis e utilizações recomendadas para cada um.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Onde posso encontrar modelos ARM de Balancer de Carga?
Consulte a [lista de modelos de quickstart do Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) para modelos ARM de implementações comuns.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Como é que as regras de entrada na NAT são diferentes das regras de equilíbrio de carga?
As regras da NAT são usadas para especificar um recurso de backend para encaminhar o tráfego para. Por exemplo, configurar uma porta de equilíbrio de carga específica para enviar tráfego rdp para um VM específico. As regras de equilíbrio de carga são utilizadas para especificar um conjunto de recursos de backend para encaminhar o tráfego, equilibrando a carga em cada instância. Por exemplo, uma regra do equilibrista de carga pode encaminhar pacotes DeTCP na porta 80 do equilibrista de carga através de um conjunto de servidores web.

## <a name="next-steps"></a>Passos Seguintes
Se a sua pergunta não estiver listada acima, por favor envie feedback sobre esta página com a sua pergunta. Isto criará um problema GitHub para a equipa de produtos para garantir que todas as nossas questões de clientes valorizadas sejam respondidas.
