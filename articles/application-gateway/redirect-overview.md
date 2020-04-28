---
title: Redirecione a visão geral do Gateway de Aplicação Azure
description: Saiba mais sobre a capacidade de redirecionamento no Portal de Aplicações Azure para redirecionar o tráfego recebido num ouvinte para outro ouvinte ou para um site externo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74129879"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral de redirecionamento do Gateway de aplicação

Pode utilizar o gateway da aplicação para redirecionar o tráfego.  Este tem um mecanismo de redirecionamento genérico que permite redirecionar o tráfego recebido num serviço de escuta para outro serviço de escuta ou para um site externo. Isto simplifica a configuração da aplicação, otimiza o uso de recursos e suporta novos cenários de reorientação, incluindo a reorientação global e baseada em caminhos.

Um cenário comum de reorientação para muitas aplicações web é suportar a reorientação automática HTTP a HTTPS para garantir que toda a comunicação entre a aplicação e os seus utilizadores ocorra através de um caminho encriptado. No passado, os clientes usaram técnicas como a criação de um backend dedicado cujo único objetivo é redirecionar os pedidos que recebe em HTTP para HTTPS. Com suporte de reorientação no Gateway de Aplicação, pode fazê-lo simplesmente adicionando uma nova configuração de redirecionamento a uma regra de encaminhamento, e especificando outro ouvinte com protocolo HTTPS como ouvinte-alvo.

São apoiados os seguintes tipos de reorientação:

- 301 Redirecionamento Permanente 301
- 302 Encontrado
- 303 Ver Outro
- 307 Redirecionamento Temporário 307

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

-  **Reorientação global**

   Redireciona de um ouvinte para outro ouvinte na porta de entrada. Isto permite o redirecionamento de HTTP para HTTPS num site.
- **Reorientação baseada no caminho**

   Este tipo de reorientação permite a reorientação http a HTTPS apenas numa área específica do local, por exemplo, uma área de carrinho de compras denotada por /carrinho/*.
- **Redirecionamento para site externo**

![redirecionar](./media/redirect-overview/redirect.png)

Com esta alteração, os clientes precisam de criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte-alvo ou o site externo para o qual é desejada a reorientação. O elemento de configuração também suporta opções para permitir a adesão do caminho URI e a corda de consulta ao URL redirecionado. Também pode escolher o tipo de reorientação. Uma vez criada, esta configuração de redirecionamento é anexada ao ouvinte de origem através de uma nova regra. Ao utilizar uma regra básica, a configuração de redirecionamento está associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra baseada no caminho é utilizada, a configuração de redirecionamento é definida no mapa do caminho url. Portanto, só se aplica à área específica do caminho de um local.

### <a name="next-steps"></a>Passos seguintes

[Configure a reorientação do URL em um gateway de aplicação](tutorial-url-redirect-powershell.md)
