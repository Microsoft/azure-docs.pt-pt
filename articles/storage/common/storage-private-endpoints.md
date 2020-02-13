---
title: Utilização de pontos finais privados com armazenamento Azure  Microsoft Docs
description: Visão geral dos pontos finais privados para acesso seguro a contas de armazenamento de redes virtuais.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 85b59c6549a62f7d9945f5739d1d0fde8c0fa3b8
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158915"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Utilização de pontos finais privados para armazenamento azure (pré-visualização)

Pode utilizar [pontos finais privados](../../private-link/private-endpoint-overview.md) para as suas contas de Armazenamento Azure para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura a dados num [Link Privado](../../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP do espaço de endereço VNet para o seu serviço de conta de armazenamento. O tráfego de rede entre os clientes no VNet e a conta de armazenamento atravessa o VNet e um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

A utilização de pontos finais privados para a sua conta de armazenamento permite::
- Proteja a sua conta de armazenamento configurando a firewall de armazenamento para bloquear todas as ligações no ponto final público para o serviço de armazenamento.
- Aumente a segurança da rede virtual (VNet), permitindo bloquear a exfiltração de dados do VNet.
- Ligue-se de forma segura a contas de armazenamento a partir de redes no local que se ligam à VNet utilizando [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../../expressroute/expressroute-locations.md) com private-peering.

## <a name="conceptual-overview"></a>Visão geral conceptual
![Pontos finais privados para visão geral do armazenamento azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Um Private Endpoint é uma interface de rede especial para um serviço Azure na sua [Rede Virtual](../../virtual-network/virtual-networks-overview.md) (VNet). Ao criar um ponto final privado para a sua conta de armazenamento, proporciona conectividade segura entre clientes no seu VNet e seu armazenamento. O ponto final privado é atribuído um endereço IP da gama de endereços IP do seu VNet. A ligação entre o ponto final privado e o serviço de armazenamento utiliza um link privado seguro.

As aplicações no VNet podem ligar-se ao serviço de armazenamento sobre o ponto final privado sem **problemas, utilizando as mesmas cordas de ligação e mecanismos**de autorização que utilizariam de outra forma . Os pontos finais privados podem ser utilizados com todos os protocolos suportados pela conta de armazenamento, incluindo REST e SMB.

Os pontos finais privados podem ser criados em subredes que utilizam [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md)de serviço . Os clientes numa subnet podem assim ligar-se a uma conta de armazenamento utilizando pontos finais privados, utilizando pontos finais de serviço para aceder a outros.

Quando cria um ponto final privado para um serviço de armazenamento na VNet, é enviado um pedido de consentimento para aprovação ao proprietário da conta de armazenamento. Se o utilizador que solicita a criação do ponto final privado for também proprietário da conta de armazenamento, este pedido de consentimento é automaticamente aprovado.

Os proprietários de contas de armazenamento podem gerir os pedidos de consentimento e os pontos finais privados, através do separador '*Private Endpoints*' para a conta de armazenamento no [portal Azure](https://portal.azure.com).

> [!TIP]
> Se pretender restringir o acesso à sua conta de armazenamento apenas através do ponto final privado, configure a firewall de armazenamento para negar ou controlar o acesso através do ponto final público.

Pode garantir a sua conta de armazenamento apenas para aceitar ligações do seu VNet, [configurando a firewall de armazenamento](storage-network-security.md#change-the-default-network-access-rule) para negar o acesso através do seu ponto final público por padrão. Você não precisa de uma regra de firewall para permitir o tráfego de um VNet que tem um ponto final privado, uma vez que a firewall de armazenamento apenas controla o acesso através do ponto final público. Em vez disso, os pontos finais privados baseiam-se no fluxo de consentimento para conceder às subredes o acesso ao serviço de armazenamento.

### <a name="private-endpoints-for-storage-service"></a>Pontos finais privados para o serviço de armazenamento

Ao criar o ponto final privado, deve especificar a conta de armazenamento e o serviço de armazenamento ao qual se liga. Você precisa de um ponto final privado separado para cada serviço de armazenamento em uma conta de armazenamento que você precisa aceder, nomeadamente [Blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2,](../blobs/data-lake-storage-introduction.md) [Files,](../files/storage-files-introduction.md) [Queues,](../queues/storage-queues-introduction.md) [Tables,](../tables/table-storage-overview.md)ou [Site Estática](../blobs/storage-blob-static-website.md).

> [!TIP]
> Crie um ponto final privado separado para a instância secundária do serviço de armazenamento para uma melhor leitura do desempenho nas contas RA-GRS.

Para ler o acesso à região secundária com uma conta de armazenamento configurada para armazenamento geo-redundante, precisa de pontos finais privados separados para as instâncias primárias e secundárias do serviço. Não é preciso criar um ponto final privado para a instância secundária para **o failover**. O ponto final privado ligar-se-á automaticamente à nova instância primária após a falha. Para obter mais informações sobre as opções de redundância de armazenamento, consulte a [redundância do Armazenamento Azure.](storage-redundancy.md)

#### <a name="resources"></a>Recursos

Para obter informações mais detalhadas sobre a criação de um ponto final privado para a sua conta de armazenamento, consulte os seguintes artigos:

- [Ligue-se em privado a uma conta de armazenamento da experiência da Conta de Armazenamento no portal Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Criar um ponto final privado usando o Private Link Center no portal Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado usando o Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Criar um ponto final privado usando o Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Ligação a pontos finais privados

Os clientes de um VNet que utilizem o ponto final privado devem usar a mesma cadeia de ligação para a conta de armazenamento, uma vez que os clientes que se ligam ao ponto final público. Contamos com a resolução do DNS para encaminhar automaticamente as ligações da VNet para a conta de armazenamento sobre um link privado.

> [!IMPORTANT]
> Utilize a mesma cadeia de ligação para ligar à conta de armazenamento utilizando pontos finais privados, uma vez que utilizaria de outra forma. Por favor, não se conectem à conta de armazenamento utilizando o seu URL de subdomínio*privatelink.*

Criamos uma [zona Privada de DNS](../../dns/private-dns-overview.md) anexada ao VNet com as atualizações necessárias para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de fazer alterações adicionais na configuração do DNS. A secção de [alterações do DNS](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para pontos finais privados.

## <a name="dns-changes-for-private-endpoints"></a>Alterações do DNS para pontos finais privados

Quando se cria um ponto final privado, o registo de recursos DNS CNAME para a conta de armazenamento é atualizado para um pseudónimo num subdomínio com o link privado prefixo '*privatelink*'. Por padrão, também criamos uma [zona privada de DNS,](../../dns/private-dns-overview.md)correspondente ao subdomínio de*privatelink*, com os registos de recursos DNS A para os pontos finais privados.

Ao resolver o URL final de armazenamento de fora da VNet com o ponto final privado, resolve-se até ao ponto final público do serviço de armazenamento. Quando resolvido a partir do VNet que acolhe o ponto final privado, o URL final de ponto de armazenamento resolve-se para o endereço IP do ponto final privado.

Para o exemplo ilustrado acima, os registos de recursos DNS para a conta de armazenamento 'StorageAccountA', quando resolvidos fora do VNet que acolhe o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | serviço de armazenamento \<\> final do serviço de armazenamento                   |
| serviço de armazenamento \<\> final do serviço de armazenamento                   | A     | serviço de armazenamento \<endereço IP público\>                 |

Como mencionado anteriormente, pode negar ou controlar o acesso a clientes fora do VNet através do ponto final público utilizando a firewall de armazenamento.

Os registos de recursos dNS para StorageAccountA, quando resolvidos por um cliente na VNet que acolhe o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Esta abordagem permite o acesso à conta de armazenamento utilizando a mesma cadeia de **ligação** para clientes no VNet que acolhe os pontos finais privados, bem como clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o FQDN para o ponto final da conta de armazenamento para o endereço IP do ponto final privado. Deve configurar o seu servidor DNS para delegar o seu subdomínio de ligação privada na zona privada de DNS para o VNet, ou configurar os registos A para '*StorageAccountA.privatelink.blob.core.windows.net*' com o endereço IP final do ponto final privado.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deve configurar o seu servidor DNS para resolver o nome da conta de armazenamento no subdomínio 'privatelink' para o endereço IP do ponto final privado. Pode fazê-lo delegando o subdomínio 'privatelink' na zona Privada DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

Os nomes de zona DNS recomendados para pontos finais privados para serviços de armazenamento são:

| Serviço de armazenamento        | Nome da zona                            |
| :--------------------- | :----------------------------------- |
| Serviço Blob           | `privatelink.blob.core.windows.net`  |
| Armazenamento do Data Lake Ger2 | `privatelink.dfs.core.windows.net`   |
| Serviço de ficheiros           | `privatelink.file.core.windows.net`  |
| Serviço de fila          | `privatelink.queue.core.windows.net` |
| Serviço de mesa          | `privatelink.table.core.windows.net` |
| Sites estáticos        | `privatelink.web.core.windows.net`   |

#### <a name="resources"></a>Recursos

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:

- [Name resolution for resources in Azure virtual networks](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DNS para pontos finais privados](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="copy-blob-support"></a>Suporte de Cópia Blob

Durante a pré-visualização, não suportamos os comandos [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) emitidos para contas de armazenamento acedidas através de pontos finais privados quando a conta de armazenamento de origem é protegida por uma firewall.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso ao armazenamento para clientes em VNets com Pontos Finais Privados

Os clientes em VNets com pontos finais privados existentes enfrentam constrangimentos ao aceder a outras contas de armazenamento que têm pontos finais privados. Por exemplo, suponha que um VNet N1 tem um ponto final privado para uma conta de armazenamento A1 para, digamos, o serviço de blob. Se a conta de armazenamento A2 tem um ponto final privado num VNet N2 para o serviço blob, então os clientes da VNet N1 também devem aceder ao serviço blob da conta A2 usando um ponto final privado. Se a conta de armazenamento A2 não tiver quaisquer pontos finais privados para o serviço blob, então os clientes da VNet N1 podem aceder ao seu serviço de blob sem um ponto final privado.

Esta restrição é o resultado das alterações do DNS feitas quando a conta A2 cria um ponto final privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regras do Grupo de Segurança da Rede para subredes com pontos finais privados

Atualmente, não é possível configurar as regras do [Network Security Group](../../virtual-network/security-overview.md) (NSG) para subredes com pontos finais privados. Uma suposição limitada para esta questão é implementar as suas regras de acesso a pontos finais privados nas subredes de origem, embora esta abordagem possa exigir uma gestão superior.
