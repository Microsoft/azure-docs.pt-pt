---
title: Usando pontos de extremidade privados com o armazenamento do Azure | Microsoft Docs
description: Visão geral de pontos de extremidade privados para acesso seguro a contas de armazenamento de redes virtuais.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949472"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Usando pontos de extremidade privados para o armazenamento do Azure (visualização)

O armazenamento do Azure permite o uso de [pontos de extremidade privados](../../private-link/private-endpoint-overview.md) para clientes em uma rede virtual (VNet) para acessar com segurança dados em uma conta de armazenamento por um [link privado](../../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu serviço de conta de armazenamento. O tráfego de rede entre os clientes na VNet e a conta de armazenamento atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

O uso de pontos de extremidade privados para sua conta de armazenamento permite que você:
- Proteja sua conta de armazenamento Configurando o firewall de armazenamento para bloquear todas as conexões no ponto de extremidade público para o serviço de armazenamento.
- Aumenta a segurança para a VNet (rede virtual), permitindo que você bloqueie vazamento de dados da VNet.
- Conecte-se com segurança a contas de armazenamento de redes locais que se conectam à VNet usando [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../../expressroute/expressroute-locations.md) com emparelhamento privado.

## <a name="conceptual-overview"></a>Visão geral conceitual
![Visão geral dos pontos de extremidade privados para o armazenamento do Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Um ponto de extremidade privado é uma interface de rede especial em sua VNet ( [rede virtual](../../virtual-network/virtual-networks-overview.md) ) para um serviço do Azure. Ele fornece conectividade segura entre clientes em sua VNet e sua conta de armazenamento. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o serviço de armazenamento usa um link privado seguro.

Os aplicativos na VNet podem se conectar ao serviço de armazenamento por meio do ponto de extremidade privado diretamente, usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma. Pontos de extremidade privados podem ser usados com todos os protocolos compatíveis com a conta de armazenamento, incluindo REST e SMB.

Quando você cria um ponto de extremidade privado para um serviço de armazenamento em sua VNet, uma solicitação de consentimento é enviada para aprovação para o proprietário da conta de armazenamento. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário da conta de armazenamento, essa solicitação de consentimento será aprovada automaticamente.

Os proprietários da conta de armazenamento podem aprovar ou rejeitar as solicitações de consentimento, bem como exibir ou gerenciar os pontos de extremidade privados, por meio da guia ' pontos de extremidade particulares ' para a conta de armazenamento no [portal do Azure](https://portal.azure.com).

Você pode proteger sua conta de armazenamento para aceitar somente conexões de sua VNet, [Configurando o firewall de armazenamento](storage-network-security.md#change-the-default-network-access-rule) para negar o acesso por meio de seu ponto de extremidade público por padrão. Você não precisa de uma regra de firewall de armazenamento para permitir o tráfego de uma VNet que tem um ponto de extremidade privado, já que as regras de firewall de armazenamento se aplicam somente ao seu ponto de extremidade público. Em vez disso, pontos de extremidade privados dependem do fluxo de autorização para conceder acesso de sub-redes ao serviço de armazenamento.

### <a name="private-endpoints-for-storage-service"></a>Pontos de extremidade privados para o serviço de armazenamento

Ao criar o ponto de extremidade privado, você deve especificar a conta de armazenamento e o serviço de armazenamento ao qual ele se conecta. Você precisa de um ponto de extremidade privado para cada serviço de armazenamento em uma conta de armazenamento para a qual o acesso é necessário, ou seja, [BLOBs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [arquivos](../files/storage-files-introduction.md), [filas](../queues/storage-queues-introduction.md), [tabelas](../tables/table-storage-overview.md)ou [sites estáticos](../blobs/storage-blob-static-website.md).

Para garantir a disponibilidade de leitura para uma [conta de armazenamento com redundância geográfica com acesso de leitura](storage-redundancy-grs.md#read-access-geo-redundant-storage), você precisa de pontos de extremidade privados separados para as instâncias primária e secundária do serviço.

#### <a name="resources"></a>Recursos

Para obter informações mais detalhadas sobre como criar um ponto de extremidade privado para sua conta de armazenamento, consulte os seguintes artigos:

- [Conecte-se de forma privada a uma conta de armazenamento da experiência de conta de armazenamento no portal do Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Criar um ponto de extremidade privado usando o centro de links privado no portal do Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando CLI do Azure](../../private-link/create-private-endpoint-cli.md)
- [Criar um ponto de extremidade privado usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares

Quando você cria um ponto de extremidade privado para um serviço de armazenamento, atualizamos o registro de recurso DNS CNAME para esse ponto de extremidade de armazenamento para um alias em um subdomínio com o prefixo '*privatelink*'. Por padrão, também criamos uma [zona DNS privada](../../dns/private-dns-overview.md) anexada à VNet. Essa zona DNS privada corresponde ao subdomínio com o prefixo '*privatelink*' e contém os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve a URL do ponto de extremidade de armazenamento de fora da VNet na qual o ponto de extremidade privado é criado, ele ainda é resolvido para o ponto de extremidade público do serviço de armazenamento. Quando resolvido da VNet que hospeda o ponto de extremidade privado, a URL do ponto de extremidade de armazenamento é resolvida para o endereço IP do ponto de extremidade privado.

Para o exemplo ilustrado acima, os registros de recurso de DNS para a conta de armazenamento ' StorageAccountA ', quando resolvidos de fora da VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<ponto de extremidade público\>                                   |
| \<ponto de extremidade público\>                                   | A     | \<endereço IP público do serviço de armazenamento\>                 |

Como mencionado anteriormente, você pode negar todo o acesso por meio do ponto de extremidade público usando o firewall de armazenamento.

Os registros de recurso DNS para StorageAccountA, quando resolvido por um cliente na VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Essa abordagem habilita o acesso à conta de armazenamento usando a mesma cadeia de conexão da VNet que hospeda os pontos de extremidade privados, bem como clientes fora da VNet. Você pode usar o firewall de armazenamento para negar o acesso a todos os clientes fora da VNet.

## <a name="pricing"></a>Preços

Para obter detalhes de preços, consulte [preço do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="copy-blob-failures"></a>Falhas de cópia de BLOB

Atualmente, os comandos [copiar blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) emitidos para as contas de armazenamento acessadas por meio de pontos de extremidade privados falham quando a conta de armazenamento de origem é protegida por um firewall.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso de armazenamento para clientes no VNets com pontos de extremidade privados

Clientes em VNets que têm um ponto de extremidade particular de armazenamento existente restrições de face ao acessar outras contas de armazenamento com pontos de extremidades privados. Por exemplo, suponha que uma VNet N1 tenha um ponto de extremidade privado para uma conta de armazenamento a1 para, digamos, o serviço BLOB. Se a conta de armazenamento a2 tiver um ponto de extremidade privado em um N2 de VNet para o serviço BLOB, os clientes na VNet N1 também deverão acessar o serviço blob na conta a2 usando um ponto de extremidade privado. Se a conta de armazenamento a2 não tiver nenhum ponto de extremidade privado para o serviço BLOB, os clientes na VNet N1 poderão acessar seu serviço blob sem um ponto de extremidade privado.

Essa restrição é um resultado das alterações de DNS feitas quando a conta a2 cria um ponto de extremidade privado.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>Regras de NSG em sub-redes com pontos de extremidade privados

As regras do NSG ( [grupo de segurança de rede](../../virtual-network/security-overview.md) ) não podem ser configuradas para sub-redes com pontos de extremidade privados, neste momento. Uma solução alternativa limitada para esse problema é implementar suas regras de acesso para pontos de extremidade privados nas sub-redes de origem, embora essa abordagem possa exigir uma sobrecarga de gerenciamento maior.
