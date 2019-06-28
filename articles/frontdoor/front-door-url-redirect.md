---
title: Frente do Azure de porta de serviço - URL de redirecionamento | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como serviço de porta de entrada do Azure suporta redirecionamento de URL para suas rotas, se configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332530"
---
# <a name="url-redirect"></a>Redirecionamento de URL
Pode utilizar o serviço de porta de entrada do Azure para redirecionar o tráfego. Pode redirecionar o tráfego em vários níveis (protocolo, nome de anfitrião, caminho, cadeia de consulta) e todas as funcionalidades podem ser configuradas para microsserviços individuais como o redirecionamento é baseado no caminho. Isso simplifica a configuração da aplicação, otimiza a utilização de recursos e oferece suporte a novos cenários de redirecionamento, incluindo global e com base no caminho de redirecionamento.
</br>

![Redirecionamento de URL de porta de entrada do Azure][1]

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de estado de resposta para os clientes compreender a finalidade de redirecionamento. São suportados os seguintes tipos de redirecionamento de:

- **301 (movido permanentemente)** : Indica que o recurso de destino foi atribuído um novo URI permanente e quaisquer referências futuras para este recurso deve utilizar um dos URIs circunscritas. Utilize o código de estado 301 para HTTP para redirecionamento a HTTPS. 
- **302 (Found)** : Indica que o recurso de destino temporariamente reside num URI diferente. Uma vez que o redirecionamento pode ser alterado ocasionalmente, o cliente deve continuar a utilizar o URI do pedido em vigor para futuros pedidos.
- **307 (Redirecionamento temporário)** : Indica que o recurso de destino temporariamente reside num URI diferente e o agente de utilizador não deve alterar o método de pedido, se ele realiza um redirecionamento automático para esse URI. Uma vez que o redirecionamento pode mudar ao longo do tempo, o cliente deve continuar a utilizar o URI do pedido em vigor original para futuros pedidos.
- **308 (redirecionamento permanente)** : Indica que o recurso de destino foi atribuído um novo URI permanente e quaisquer referências futuras para este recurso deve utilizar um dos URIs circunscritas. Os clientes com a ligação de recursos, de edição deve para automaticamente relink referências a em vigor a partir da URI do pedido para uma ou mais das novas referências enviadas pelo servidor, sempre que possível.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Pode definir o protocolo que será utilizado para o redirecionamento. Isso permite que um dos casos de utilização mais comuns da funcionalidade de redirecionamento, o que é a definição de HTTP para redirecionamento a HTTPS.

- **Apenas HTTPS**: Defina o protocolo como HTTPS apenas, se estiver à procura para redirecionar o tráfego de HTTP para HTTPS. O serviço de porta de entrada do Azure recomenda que deve sempre definir o redirecionamento a HTTPS apenas.
- **Apenas HTTP**: Isso redireciona o pedido de entrada para HTTP. Utilize este valor apenas se pretender manter o tráfego HTTP, que é, não encriptada.
- **Pedido de correspondência**: Esta opção mantém o protocolo utilizado pela solicitação de entrada. Então, uma solicitação HTTP permanece HTTP e um pedido HTTPS permanece post redirecionamento a HTTPS.

## <a name="destination-host"></a>Anfitrião de destino
Como parte da configuração de um redirecionamento, encaminhamento, também pode alterar o nome de anfitrião ou o domínio para o pedido de redirecionamento. Pode definir este campo para alterar o nome de anfitrião no URL para o redirecionamento ou caso contrário, preservar o nome do anfitrião da solicitação recebida. Então, utilizar este campo pode redirecionar todos os pedidos enviados https://www.contoso.com/ * para https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Caminho de destino
Nos casos em que pretende substituir o segmento de caminho de uma URL como parte de redirecionamento, pode definir este campo com o novo valor de caminho. Caso contrário, pode optar por manter o valor do caminho como parte de redirecionamento. Então, utilizar este campo, pode redirecionar todos os pedidos enviados para o https://www.contoso.com/ * para https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Também pode substituir os parâmetros de cadeia de caracteres de consulta no URL redirecionada. Para substituir qualquer cadeia de consulta existente a partir do URL de pedido de entrada, defina este campo 'Replace' e, em seguida, defina o valor apropriado. Caso contrário, pode manter o conjunto original de cadeias de caracteres de consulta ao definir o campo 'Preserve'. Por exemplo, através deste campo, pode redirecionar todo o tráfego enviado para https://www.contoso.com/foo/bar para https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte do URL depois de "#", normalmente usado pelos navegadores para direcionado para uma secção específica numa página. Pode definir este campo para adicionar um fragmento para o URL de redirecionamento.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png