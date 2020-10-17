---
title: Configure o controlo de acesso à rede
titleSuffix: Azure SignalR Service
description: Configure o controlo de acesso à rede para o seu Serviço Azure SignalR.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143223"
---
# <a name="configure-network-access-control"></a>Configure o controlo de acesso à rede

O Serviço Azure SignalR permite-lhe proteger e controlar o nível de acesso ao seu ponto final de serviço, com base no tipo de pedido e subconjunto de redes utilizadas. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder ao seu Serviço Azure SignalR.

O Serviço Azure SignalR tem um ponto final público que é acessível através da internet. Também pode criar [Pontos Finais Privados para o seu Serviço Azure SignalR](howto-private-endpoints.md). O Private Endpoint atribui um endereço IP privado do seu VNet ao Serviço Azure SignalR e protege todo o tráfego entre o seu VNet e o Serviço Azure SignalR através de uma ligação privada. O controlo de acesso à rede Azure SignalR fornece controlo de acesso tanto para pontos finais públicos como para pontos finais privados.

Opcionalmente, pode optar por permitir ou negar certos tipos de pedidos para o ponto final público e cada ponto final privado. Por exemplo, pode bloquear todas as Ligações do Servidor do ponto final público e certificar-se de que elas são originárias [apenas](signalr-concept-internals.md#server-connections) de um VNet específico.

Uma aplicação que acede a um Serviço Azure SignalR quando as regras de controlo de acesso à rede estão em vigor ainda requer uma autorização adequada para o pedido.

## <a name="scenario-a---no-public-traffic"></a>Cenário A - Sem tráfego público

Para negar completamente todo o tráfego público, deve primeiro configurar a regra da rede pública para não permitir qualquer tipo de pedido. Em seguida, deve configurar regras que concedem acesso ao tráfego a partir de VNets específicos. Esta configuração permite-lhe construir um limite de rede seguro para as suas aplicações.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Cenário B - Apenas ligações de clientes da rede pública

Neste cenário, é possível configurar a regra da rede pública para permitir [apenas ligações de clientes](signalr-concept-internals.md#client-connections) a partir de redes públicas. Em seguida, pode configurar regras de rede privadas para permitir outros tipos de pedidos originários de um VNet específico. Esta configuração esconde os servidores das suas aplicações da rede pública e estabelece ligações seguras entre os servidores das suas aplicações e o Serviço Azure SignalR.

## <a name="managing-network-access-control"></a>Gerir o controlo de acessos à rede

Pode gerir o controlo de acesso à rede para o Serviço Azure SignalR através do portal Azure.

### <a name="azure-portal"></a>Portal do Azure

1. Vá ao Serviço Azure SignalR que pretende proteger.

1. Clique no menu de definições chamado **controlo de acesso à rede.**

    ![Rede ACL no portal](media/howto-network-access-control/portal.png)

1. Para editar a ação predefinitiva, alternar o botão **Permitir/Negar.**

    > [!TIP]
    > A ação predefinitiva é a ação que tomamos quando não há regras ACL. Por exemplo, se a ação por defeito for **Deny,** então os tipos de pedido que não sejam explicitamente aprovados abaixo serão negados.

1. Para editar a regra da rede pública, selecione tipos de pedidos permitidos na **rede Pública.**

    ![Editar rede pública ACL no portal ](media/howto-network-access-control/portal-public-network.png)

1. Para editar as regras de rede de pontos finais privados, selecione tipos de pedidos permitidos em cada linha em **ligações de ponto final privado .**

    ![Editar o ponto final privado ACL no portal ](media/howto-network-access-control/portal-private-endpoint.png)

1. Clique em **Guardar** para aplicar as suas alterações.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Private Link](../private-link/private-link-overview.md).