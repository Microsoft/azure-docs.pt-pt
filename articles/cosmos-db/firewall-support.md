---
title: Firewall IP para contas Azure Cosmos
description: Saiba como proteger os dados DB do Azure Cosmos utilizando políticas de controlo de acesso IP para suporte a firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241084"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Firewall de IP no Azure Cosmos DB

Para proteger os dados armazenados na sua conta, a Azure Cosmos DB suporta um modelo de autorização baseado em segredo que utiliza um forte Código de Autenticação de Mensagens (HMAC) baseado em Hash. Além disso, o Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Este modelo é semelhante às regras de firewall de um sistema de base de dados tradicional e fornece um nível adicional de segurança à sua conta. Com firewalls, pode configurar a sua conta Azure Cosmos para estar acessível apenas a partir de um conjunto aprovado de máquinas e/ou serviços na nuvem. O acesso aos dados armazenados na sua base de dados Azure Cosmos a partir destes conjuntos de máquinas e serviços aprovados continuará a exigir que o chamador apresente um token de autorização válido.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Visão geral do controlo de acesso ip

Por padrão, a sua conta Azure Cosmos está acessível a partir da internet, desde que o pedido seja acompanhado de um token de autorização válido. Para configurar o controlo de acesso baseado em políticas IP, o utilizador deve fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR (Classless Inter-Domain Encaminhamento) para ser incluído como a lista permitida de IPs do cliente para aceder a uma determinada conta Azure Cosmos. Uma vez aplicada esta configuração, quaisquer pedidos originários de máquinas fora desta lista permitida recebem 403 resposta (Proibida). Ao utilizar firewall IP, é aconselhável permitir que o portal Azure aceda à sua conta. O acesso é necessário para permitir a utilização do explorador de dados, bem como para recuperar métricas para a sua conta que aparecem no portal Azure. Ao utilizar o explorador de dados, além de permitir que o portal Azure aceda à sua conta, também precisa de atualizar as definições de firewall para adicionar o seu endereço IP atual às regras de firewall. Note que as alterações na firewall podem levar até 15 min para se propagar. 

Pode combinar firewall baseada em IP com o controlo de acesso sub-rede e VNET. Ao combiná-los, pode limitar o acesso a qualquer fonte que tenha um IP público e/ou a partir de uma sub-rede específica dentro do VNET. Para saber mais sobre a utilização de controlo de acesso baseado em sub-redes e VNET consulte [os recursos DB do Access Azure Cosmos a partir de redes virtuais.](vnet-service-endpoint.md)

Resumindo, o token de autorização é sempre necessário para aceder a uma conta Azure Cosmos. Se a firewall IP e a Lista de Controlo de Acesso VNET (ACLs) não forem configuradas, a conta Azure Cosmos pode ser acedida com o token de autorização. Depois de a firewall IP ou os ACLs VNET ou ambos forem configurados na conta Azure Cosmos, apenas os pedidos originários das fontes que especificou (e com o token de autorização) obtêm respostas válidas. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode configurar firewall IP ou ponto final de serviço VNET para a sua conta, utilizando os seguintes documentos:

* [Como configurar firewall IP para a sua conta Azure Cosmos](how-to-configure-firewall.md)
* [Aceder aos recursos DB da Azure Cosmos a partir de redes virtuais](vnet-service-endpoint.md)
* [Como configurar o ponto final do serviço de rede virtual para a sua conta Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




