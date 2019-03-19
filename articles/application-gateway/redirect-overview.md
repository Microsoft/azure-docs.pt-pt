---
title: Descrição geral do redirecionamento para o Gateway de aplicação do Azure
description: Saiba mais sobre a capacidade de redirecionamento no Gateway de aplicação do Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170059"
---
# <a name="application-gateway-redirect-overview"></a>Descrição geral de redirecionamento do Gateway de aplicação

Pode utilizar o gateway de aplicação para redirecionar o tráfego.  Ele tem um mecanismo de redirecionamento genérico que permite redirecionar o tráfego recebido num serviço de escuta para outro serviço de escuta ou a um site externo. Isso simplifica a configuração da aplicação, otimiza a utilização de recursos e oferece suporte a novos cenários de redirecionamento, incluindo global e com base no caminho de redirecionamento.

É um cenário comum de redirecionamento para muitos aplicativos web suportar automática HTTP para redirecionamento a HTTPS para garantir que toda a comunicação entre aplicativos e para os utilizadores ocorre através de um caminho encriptado. No passado, os clientes usaram técnicas como a criação de um conjunto de back-end dedicado cujo único objetivo é redirecionar os pedidos recebidos de HTTP para HTTPS. Com o suporte de redirecionamento no Gateway de aplicação, é possível fazer isso simplesmente ao adicionar uma nova configuração de redirecionamento para uma regra de roteamento e especificar o outro serviço de escuta com o protocolo HTTPS como o serviço de escuta de destino.

São suportados os seguintes tipos de redirecionamento de:

- 301 Permanent Redirect
- 302 Encontrado
- 303 ver outros
- 307 Temporary Redirect

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

-  **Redirecionamento global**

   Redirecionamentos de um serviço de escuta para outro serviço de escuta no gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
- **Com base no caminho de redirecionamento**

   Este tipo de redirecionamento de permite HTTP para redirecionamento a HTTPS apenas numa área de site específico, por exemplo uma área de carrinho de compras indicado pelos/carrinho / *.
- **Redirecionar para o site externo**

![redirect](./media/redirect-overview/redirect.png)

Com esta alteração, os clientes têm de criar um novo objeto de configuração de redirecionamento, que especifica o serviço de escuta de destino ou um site externo ao qual o redirecionamento é desejado. O elemento de configuração também oferece suporte a opções para ativar a acrescentar a cadeia de caminho e a consulta do URI para o URL redirecionado. Também pode escolher o tipo de redirecionamento. Depois de criado, esta configuração de redirecionamento está ligada ao serviço de escuta de origem através de uma nova regra. Quando utilizar uma regra básica, a configuração de redirecionamento está associada um serviço de escuta de origem e é um redirecionamento global. Quando uma regra baseada em caminho é utilizada, a configuração de redirecionamento é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site.

### <a name="next-steps"></a>Passos Seguintes

[Configurar o redirecionamento de URL num gateway de aplicação](tutorial-url-redirect-powershell.md)
