---
title: Descrição geral de redirecionamento para o Gateway de aplicação do Azure | Documentos da Microsoft
description: Saiba mais sobre a capacidade de redirecionamento no Gateway de aplicação do Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831764"
---
# <a name="application-gateway-redirect-overview"></a>Descrição geral de redirecionamento do Gateway de aplicação

É um cenário comum para muitos aplicativos web suportar automática HTTP para redirecionamento a HTTPS para garantir que toda a comunicação entre aplicativos e para os utilizadores ocorre através de um caminho encriptado. No passado, os clientes usaram técnicas como a criação de um conjunto de back-end dedicado cujo único objetivo é redirecionar os pedidos recebidos de HTTP para HTTPS.  Gateway de aplicação suporta agora a capacidade de redirecionar o tráfego no Gateway de aplicação. Isso simplifica a configuração da aplicação, otimiza a utilização de recursos e oferece suporte a novos cenários de redirecionamento, incluindo global e com base no caminho de redirecionamento. Suporte de redirecionamento do Gateway de aplicação não está limitado a HTTP -> redirecionamento a HTTPS sozinho. Esse é um mecanismo de redirecionamento genérico, que permite o redirecionamento de tráfego recebido num serviço de escuta para outro serviço de escuta no Gateway de aplicação. Também suporta o redirecionamento para um site externo. O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

1. Redirecionamento global de um serviço de escuta para outro serviço de escuta no Gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
2. Redirecionamento com base no caminho. Este tipo de redirecionamento de permite HTTP para redirecionamento a HTTPS apenas numa área de site específico, por exemplo uma área de carrinho de compras indicado pelos/carrinho / *.
3. Redirecionar para o site externo.

![redirect](./media/application-gateway-redirect-overview/redirect.png)

Com esta alteração, os clientes têm de criar um novo objeto de configuração de redirecionamento, que especifica o serviço de escuta de destino ou um site externo ao qual o redirecionamento é desejado. O elemento de configuração também oferece suporte a opções para ativar a acrescentar a cadeia de caminho e a consulta do URI para o URL redirecionado. Os clientes também podem escolher se o redirecionamento é uma temporária (código de estado HTTP 302) ou um redirecionamento permanente (código de estado HTTP 301). Depois de criar esta configuração de redirecionamento está ligado ao serviço de escuta de origem através de uma nova regra. Quando utilizar uma regra básica, a configuração de redirecionamento está associada um serviço de escuta de origem e é um redirecionamento global. Quando uma regra baseada em caminho é utilizada, a configuração de redirecionamento é definida no mapa do caminho de URL e, por conseguinte, só se aplica à área de caminho específico de um site.

### <a name="next-steps"></a>Passos Seguintes

[Configurar o HTTP para redirecionamento a HTTPS num gateway de aplicação](redirect-http-to-https-portal.md)
