---
title: Visão geral de redirecionamento para Aplicativo Azure gateway
description: Saiba mais sobre o recurso de redirecionamento no Aplicativo Azure gateway para redirecionar o tráfego recebido em um ouvinte para outro ouvinte ou para um site externo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129879"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral redirecionamento do gateway de aplicativo

Você pode usar o gateway de aplicativo para redirecionar o tráfego.  Este tem um mecanismo de redirecionamento genérico que permite redirecionar o tráfego recebido num serviço de escuta para outro serviço de escuta ou para um site externo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo o redirecionamento global e baseado em caminho.

Um cenário de redirecionamento comum para muitos aplicativos Web é dar suporte ao redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorra em um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar solicitações recebidas por HTTP para HTTPS. Com o suporte ao redirecionamento no gateway de aplicativo, você pode fazer isso simplesmente adicionando uma nova configuração de redirecionamento a uma regra de roteamento e especificando outro ouvinte com o protocolo HTTPS como o ouvinte de destino.

Há suporte para os seguintes tipos de redirecionamento:

- redirecionamento permanente 301
- 302 encontrado
- 303 ver outros
- redirecionamento temporário 307

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

-  **Redirecionamento global**

   Redireciona de um ouvinte para outro ouvinte no gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
- **Redirecionamento baseado em caminho**

   Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS somente em uma área de site específica, por exemplo, uma área de carrinho de compras denotada por/cart/*.
- **Redirecionar para o site externo**

![Redirecionamento](./media/redirect-overview/redirect.png)

Com essa alteração, os clientes precisam criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte de destino ou o site externo ao qual o redirecionamento é desejado. O elemento de configuração também dá suporte a opções para habilitar o acréscimo do caminho do URI e da cadeia de caracteres de consulta à URL redirecionada. Você também pode escolher o tipo de redirecionamento. Depois de criada, essa configuração de redirecionamento é anexada ao ouvinte de origem por meio de uma nova regra. Ao usar uma regra básica, a configuração de redirecionamento é associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra baseada em caminho é usada, a configuração de redirecionamento é definida no mapa de caminho de URL. Portanto, ele se aplica apenas à área de caminho específica de um site.

### <a name="next-steps"></a>Passos seguintes

[Configurar o redirecionamento de URL em um gateway de aplicativo](tutorial-url-redirect-powershell.md)
