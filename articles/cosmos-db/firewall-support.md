---
title: Firewall do IP para contas do Cosmos do Azure
description: Saiba como proteger dados do Azure Cosmos DB ao utilizar políticas de controlo de acesso IP para o suporte de firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 84e56610cb0f9090d8cacc461a73bf41d05f2251
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033490"
---
# <a name="ip-firewall-for-azure-cosmos-accounts"></a>Firewall do IP para contas do Cosmos do Azure

Para proteger os dados armazenados na sua conta, do Azure Cosmos DB suporta um modelo de autorização baseada em segredo que utiliza um forte baseada em Hash Message Authentication Code (HMAC). Além disso, o Azure Cosmos DB suporta controlos de acesso baseado em IP para o suporte de firewall de entrada. Esse modelo é semelhante para as regras de firewall de um sistema de base de dados tradicionais e fornece um nível adicional de segurança à sua conta. Com firewalls, pode configurar a sua conta do Cosmos do Azure para ser acessível apenas a partir de um conjunto aprovado de computadores e/ou serviços em nuvem. Acesso a dados armazenados na sua base de dados do Cosmos do Azure desses aprovados conjuntos de máquinas e serviços ainda exige o autor da chamada apresentar um token de autorização válida.

## <a id="ip-access-control-overview"></a>Descrição geral de controlo de acesso IP

Por predefinição, sua conta do Cosmos do Azure é acessível a partir da internet, desde que o pedido seja acompanhado por um token de autorização válida. Para configurar o controlo de acesso baseado em política IP, o utilizador tem de fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR (Classless Inter-Domain Routing) a serem incluídos como a lista de permitidos do cliente IPs para aceder a uma conta do Cosmos do Azure especificada. Depois desta configuração é aplicada, quaisquer pedidos provenientes de máquinas fora desta lista de permissões recebem um 404 resposta (não encontrado). Ao usar o firewall do IP, recomenda-se para permitir que o portal do Azure aceder à sua conta. O acesso é necessário para permitir a utilização do data explorer, bem como para obter métricas para a sua conta, que aparecem no portal do Azure.

Pode combinar o firewall baseado em IP com a sub-rede e o controlo de acesso VNET. Ao combiná-los, pode limitar o acesso a qualquer origem de que tem um IP público e/ou de uma sub-rede específica na VNET. Para saber mais sobre como utilizar a sub-rede e o controlo de acesso com base na VNET, veja [recursos de acesso do Azure Cosmos DB a partir de redes virtuais](vnet-service-endpoint.md).

Para resumir, o token de autorização sempre é necessário para aceder a uma conta do Cosmos do Azure. Se a firewall do IP e a lista de controlo de acesso (ACLs) de VNET não estão definidas, a conta do Cosmos do Azure pode ser acessada com o token de autorização. Depois do firewall do IP, ACLs de VNET ou ambas são configuradas na conta do Cosmos do Azure, apenas pedidos com origem a partir de origens que especificou (e com o token de autorização) obtém respostas válidas. 

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode configurar firewall do IP ou o ponto final de serviço VNET para a sua conta ao utilizar os seguintes documentos:

* [Como configurar a firewall do IP para a sua conta do Cosmos do Azure](how-to-configure-firewall.md)
* [Aceder a recursos do Azure Cosmos DB a partir de redes virtuais](vnet-service-endpoint.md)
* [Como configurar o ponto final de serviço de rede virtual para a sua conta do Cosmos do Azure](how-to-configure-vnet-service-endpoint.md)




