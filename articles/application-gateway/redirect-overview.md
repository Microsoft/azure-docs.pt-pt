---
title: Redirecione a visão geral para O Gateway de Aplicações Azure
description: Saiba mais sobre a capacidade de redirecionamento no Azure Application Gateway para redirecionar o tráfego recebido num ouvinte para outro ouvinte ou para um site externo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 8c2bac1bee5a2301a29589f15d72d0f69b4a05b4
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253856"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicação

Pode utilizar o gateway de aplicações para redirecionar o tráfego.  Este tem um mecanismo de redirecionamento genérico que permite redirecionar o tráfego recebido num serviço de escuta para outro serviço de escuta ou para um site externo. Isto simplifica a configuração da aplicação, otimiza o uso do recurso e suporta novos cenários de reorientação, incluindo a reorientação global e baseada no caminho.

Um cenário comum de redirecionamento para muitas aplicações web é suportar a reorientação automática https para HTTPS para garantir que toda a comunicação entre a aplicação e os seus utilizadores ocorre através de um caminho encriptado. No passado, os clientes utilizaram técnicas como a criação de um pool dedicado de backend cujo único objetivo é redirecionar os pedidos que recebe em HTTPS. Com o suporte de reorientação no Application Gateway, pode fazê-lo simplesmente adicionando uma nova configuração de redirecionamento a uma regra de encaminhamento e especificando outro ouvinte com o protocolo HTTPS como o ouvinte-alvo.

São suportados os seguintes tipos de reorientação:

- 301 Redirecionamento Permanente
- 302 Encontrado
- 303 Ver Outros
- 307 Redirecionamento Temporário

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

-  **Reorientação global**

   Redirecionamentos de um ouvinte para outro ouvinte no portal. Isto permite o redirecionamento de HTTP para HTTPS num site.
- **Redirecionamento baseado no caminho**

   Este tipo de reorientação permite que https redirecionamento apenas em uma área específica do site, por exemplo, uma área de carrinho de compras denotada por /carrinho/*.
- **Redirecionar para o site externo**

![redirecionamento](./media/redirect-overview/redirect.png)

Com esta alteração, os clientes precisam de criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte-alvo ou o site externo para o qual é desejada a reorientação. O elemento de configuração também suporta opções para permitir a pendência do caminho URI e a cadeia de consulta para o URL redirecionado. Também pode escolher o tipo de reorientação. Uma vez criado, esta configuração de redirecionamento é anexada ao ouvinte de origem através de uma nova regra. Ao utilizar uma regra básica, a configuração do redirecionamento está associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra baseada em caminhos é usada, a configuração de redirecionamento é definida no mapa do caminho do URL. Assim, só se aplica à área específica do caminho de um site.

### <a name="next-steps"></a>Passos seguintes

[Configurar a reorientação do URL num gateway de aplicações](tutorial-url-redirect-powershell.md)
