---
title: Firewall IP para contas Azure Cosmos
description: Aprenda a proteger os dados do Azure Cosmos DB utilizando políticas de controlo de acesso IP para suporte a firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241084"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Firewall de IP no Azure Cosmos DB

Para proteger os dados armazenados na sua conta, o Azure Cosmos DB suporta um modelo de autorização baseado em segredo que utiliza um forte Código de Autenticação de Mensagens (HMAC) baseado em Hash. Além disso, o Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte de firewall de entrada. Este modelo é semelhante às regras de firewall de um sistema de base de dados tradicional e fornece um nível adicional de segurança à sua conta. Com firewalls, pode configurar a sua conta Azure Cosmos para estar acessível apenas a partir de um conjunto aprovado de máquinas e/ou serviços na nuvem. O acesso aos dados armazenados na sua base de dados Azure Cosmos a partir destes conjuntos aprovados de máquinas e serviços ainda exigirá que o chamador apresente um sinal de autorização válido.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Visão geral do controlo de acesso IP

Por padrão, a sua conta Azure Cosmos é acessível a partir da internet, desde que o pedido seja acompanhado por um sinal de autorização válido. Para configurar o controlo de acesso baseado na política IP, o utilizador deve fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR (Classless Inter-Domain Routing) para ser incluído como a lista permitida de IPs de cliente para aceder a uma determinada conta Azure Cosmos. Uma vez aplicada esta configuração, quaisquer pedidos originários de máquinas fora desta lista permitida recebem 403 respostas (proibidas). Ao utilizar firewall IP, é aconselhável permitir que o portal Azure aceda à sua conta. É necessário o acesso para permitir a utilização do explorador de dados, bem como para recuperar métricas para a sua conta que aparecem no portal Azure. Ao utilizar o explorador de dados, além de permitir que o portal Azure aceda à sua conta, também precisa de atualizar as definições da firewall para adicionar o seu endereço IP atual às regras de firewall. Note que as alterações de firewall podem demorar até 15 min para se propagar. 

Pode combinar firewall baseada em IP com subnet e controlo de acesso VNET. Ao combiná-los, pode limitar o acesso a qualquer fonte que tenha um IP público e/ou a partir de uma subnet específica dentro do VNET. Para saber mais sobre a utilização da subnet e do controlo de acesso baseado em VNET, consulte [os recursos DB do Access Azure Cosmos a partir de redes virtuais](vnet-service-endpoint.md).

Resumindo, o sinal de autorização é sempre necessário para aceder a uma conta Azure Cosmos. Se a firewall IP e a Lista de Controlo de Acesso VNET (ACLs) não estiverem configuradas, a conta Azure Cosmos pode ser acedida com o sinal de autorização. Depois de a firewall IP ou Os ACLs VNET ou ambos estarem configurados na conta Azure Cosmos, apenas os pedidos originários das fontes que especificou (e com o sinal de autorização) obtêm respostas válidas. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode configurar o ponto final do serviço IP firewall ou VNET para a sua conta utilizando os seguintes docs:

* [Como configurar firewall IP para a sua conta Azure Cosmos](how-to-configure-firewall.md)
* [Acesso aos recursos DB do Access Azure Cosmos de redes virtuais](vnet-service-endpoint.md)
* [Como configurar o ponto final do serviço de rede virtual para a sua conta Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




