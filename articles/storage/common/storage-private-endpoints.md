---
title: Utilizar pontos finais privados
titleSuffix: Azure Storage
description: Visão geral dos pontos finais privados para acesso seguro a contas de armazenamento a partir de redes virtuais.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 16d3d50d5ade298e2ca22f271466c70e74724381
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613566"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Use pontos finais privados para armazenamento Azure

Pode utilizar [pontos finais privados](../../private-link/private-endpoint-overview.md) para as suas contas de Armazenamento Azure para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura a dados por uma [Ligação Privada.](../../private-link/private-link-overview.md) O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para o seu serviço de conta de armazenamento. O tráfego de rede entre os clientes no VNet e a conta de armazenamento atravessa o VNet e um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

A utilização de pontos finais privados para a sua conta de armazenamento permite-lhe:

- Proteja a sua conta de armazenamento configurando a firewall de armazenamento para bloquear todas as ligações no ponto final público para o serviço de armazenamento.
- Aumentar a segurança para a rede virtual (VNet), permitindo-lhe bloquear a exfiltração de dados a partir do VNet.
- Conectar-se de forma segura às contas de armazenamento de redes no local que se conectam ao VNet [utilizando VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../../expressroute/expressroute-locations.md) com o seu olhar privado.

## <a name="conceptual-overview"></a>Descrição geral conceptual

![Visão geral dos pontos finais privados para o armazenamento Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Um ponto final privado é uma interface de rede especial para um serviço Azure na sua [Rede Virtual](../../virtual-network/virtual-networks-overview.md) (VNet). Quando cria um ponto final privado para a sua conta de armazenamento, ele fornece conectividade segura entre os clientes no seu VNet e o seu armazenamento. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereço IP do seu VNet. A ligação entre o ponto final privado e o serviço de armazenamento utiliza uma ligação privada segura.

As aplicações no VNet podem ligar-se ao serviço de armazenamento através do ponto final privado sem **problemas, utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma**. Os pontos finais privados podem ser utilizados com todos os protocolos suportados pela conta de armazenamento, incluindo REST e SMB.

Os pontos finais privados podem ser criados em sub-redes que utilizam [pontos finais de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes numa sub-rede podem assim ligar-se a uma conta de armazenamento utilizando o ponto final privado, enquanto utilizam pontos finais de serviço para aceder a outros.

Quando cria um ponto final privado para um serviço de armazenamento na VNet, é enviado um pedido de consentimento para aprovação ao proprietário da conta de armazenamento. Se o utilizador que solicita a criação do ponto final privado for também proprietário da conta de armazenamento, este pedido de consentimento é automaticamente aprovado.

Os proprietários de conta de armazenamento podem gerir os pedidos de consentimento e os pontos finais privados, através do separador '*Pontos finais Privados'* para a conta de armazenamento no [portal Azure.](https://portal.azure.com)

> [!TIP]
> Se pretender restringir o acesso à sua conta de armazenamento apenas através do ponto final privado, configunja a firewall de armazenamento para negar ou controlar o acesso através do ponto final público.

Pode proteger a sua conta de armazenamento apenas para aceitar ligações a partir do seu VNet, [configurando a firewall de armazenamento](storage-network-security.md#change-the-default-network-access-rule) para negar o acesso através do seu ponto final público por padrão. Não é necessária uma regra de firewall para permitir o tráfego a partir de um VNet que tem um ponto final privado, uma vez que a firewall de armazenamento apenas controla o acesso através do ponto final público. Em vez disso, os pontos finais privados dependem do fluxo de consentimento para a concessão de acesso às sub-redes ao serviço de armazenamento.

> [!NOTE]
> Ao copiar bolhas entre contas de armazenamento, o seu cliente deve ter acesso à rede a ambas as contas. Por isso, se optar por utilizar um link privado para apenas uma conta (seja a fonte ou o destino), certifique-se de que o seu cliente tem acesso à rede à outra conta. Para saber mais sobre outras formas de configurar o acesso à rede, consulte [firewalls de armazenamento Configure Azure e redes virtuais.](storage-network-security.md?toc=/azure/storage/blobs/toc.json) 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Criação de um ponto final privado

Quando criar um ponto final privado, deve especificar a conta de armazenamento e o serviço de armazenamento a que se conecta. 

Você precisa de um ponto final privado separado para cada recurso de armazenamento que você precisa para aceder, nomeadamente [Blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [Arquivos, Filas,](../queues/storage-queues-introduction.md) [Tabelas](../tables/table-storage-overview.md)ou [Websites Estáticos.](../blobs/storage-blob-static-website.md) [](../files/storage-files-introduction.md) No ponto final privado, estes serviços de armazenamento são definidos como o **sub-recurso-alvo** da conta de armazenamento associada. 

Se criar um ponto final privado para o recurso de armazenamento de Data Lake Gen2, então também deve criar um para o recurso de armazenamento Blob. Isso porque as operações que visam o ponto final da Data Lake Storage Gen2 podem ser redirecionadas para o ponto final blob. Ao criar um ponto final privado para ambos os recursos, você garante que as operações podem ser concluídas com sucesso.

> [!TIP]
> Crie um ponto final privado separado para a instância secundária do serviço de armazenamento para um melhor desempenho nas contas RA-GRS.
> Certifique-se de criar uma conta de armazenamento v2 (Standard ou Premium) para fins gerais.

Para ler o acesso à região secundária com uma conta de armazenamento configurada para armazenamento geo-redundante, precisa de pontos finais privados separados para as instâncias primárias e secundárias do serviço. Não é necessário criar um ponto final privado para a instância secundária para **o failover**. O ponto final privado liga-se automaticamente à nova instância primária após a falha. Para obter mais informações sobre as opções de despedimento de armazenamento, consulte [a redundância do Azure Storage](storage-redundancy.md).

Para obter informações mais detalhadas sobre a criação de um ponto final privado para a sua conta de armazenamento, consulte os seguintes artigos:

- [Conecte-se privadamente a uma conta de armazenamento a partir da experiência da Conta de Armazenamento no portal Azure](../../private-link/tutorial-private-endpoint-storage-portal.md)
- [Criar um ponto final privado utilizando o Private Link Center no portal Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado usando O Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Criar um ponto final privado usando a Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Ligação a um ponto final privado

Os clientes de um VNet que utilizem o ponto final privado devem utilizar a mesma cadeia de ligação para a conta de armazenamento, uma vez que os clientes que se ligam ao ponto final público. Contamos com a resolução do DNS para encaminhar automaticamente as ligações do VNet para a conta de armazenamento por um link privado.

> [!IMPORTANT]
> Utilize o mesmo fio de ligação para ligar à conta de armazenamento utilizando pontos finais privados, como utilizaria de outra forma. Por favor, não se conecte à conta de armazenamento usando o seu `privatelink` URL de subdomínio.

Criamos uma [zona de DNS privada](../../dns/private-dns-overview.md) anexada ao VNet com as atualizações necessárias para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de es fazer alterações adicionais na sua configuração DNS. A secção sobre [as alterações do DNS](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para os pontos finais privados.

## <a name="dns-changes-for-private-endpoints"></a>DNS alterações para pontos finais privados

Quando se cria um ponto final privado, o registo de recursos DNS CNAME para a conta de armazenamento é atualizado para um pseudónimo num subdomínio com o prefixo `privatelink` . Por padrão, também criamos uma [zona privada de DNS,](../../dns/private-dns-overview.md)correspondente ao `privatelink` subdomínio, com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o URL do ponto final de armazenamento de fora do VNet com o ponto final privado, ele resolve-se para o ponto final público do serviço de armazenamento. Quando resolvido a partir do VNet que hospeda o ponto final privado, o URL do ponto final de armazenamento resolve-se para o endereço IP do ponto final privado.

Para o exemplo ilustrado acima, os registos de recursos DNS para a conta de armazenamento 'StorageAccountA', quando resolvidos fora do VNet que alberga o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Como mencionado anteriormente, pode negar ou controlar o acesso a clientes fora do VNet através do ponto final público utilizando a firewall de armazenamento.

Os registos de recursos DNS para StorageAccountA, quando resolvidos por um cliente no VNet que hospeda o ponto final privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Esta abordagem permite o acesso à conta de armazenamento **utilizando a mesma cadeia de ligação** para clientes no VNet que hospeda os pontos finais privados, bem como clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o FQDN para o ponto final da conta de armazenamento para o endereço IP do ponto final privado. Deverá configurar o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet, ou configurar os registos A para '*StorageAccountA.privatelink.blob.core.windows.net*' com o endereço IP do ponto final privado.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deverá configurar o seu servidor DNS para resolver o nome da conta de armazenamento no `privatelink` subdomínio para o endereço IP do ponto final privado. Pode fazê-lo delegando o `privatelink` subdomínio para a zona privada de DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

Os nomes recomendados da zona DE DNS para pontos finais privados para serviços de armazenamento, bem como os sub-recursos-alvo de ponto final associados, são:

| Serviço de armazenamento        | Recurso secundário de destino | Nome da zona                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Serviço Blob           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | dfs                 | `privatelink.dfs.core.windows.net`   |
| Serviço de arquivos           | file                | `privatelink.file.core.windows.net`  |
| Serviço de fila          | fila               | `privatelink.queue.core.windows.net` |
| Serviço de mesa          | table               | `privatelink.table.core.windows.net` |
| Websites estáticos        | web                 | `privatelink.web.core.windows.net`   |

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:

- [Name resolution for resources in Azure virtual networks](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DE DNS para pontos finais privados](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas Conhecidos

Tenha em mente as seguintes questões conhecidas sobre pontos finais privados para o Azure Storage.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso ao armazenamento para clientes em VNets com pontos finais privados

Os clientes em VNets com pontos finais privados existentes enfrentam constrangimentos ao aceder a outras contas de armazenamento que tenham pontos finais privados. Por exemplo, suponha que um VNet N1 tenha um ponto final privado para uma conta de armazenamento A1 para armazenamento Blob. Se a conta de armazenamento A2 tiver um ponto final privado num VNet N2 para armazenamento Blob, então os clientes em VNet N1 também devem aceder ao armazenamento blob na conta A2 usando um ponto final privado. Se a conta de armazenamento A2 não tiver quaisquer pontos finais privados para armazenamento Blob, então os clientes no VNet N1 podem aceder ao armazenamento blob nessa conta sem um ponto final privado.

Este constrangimento resulta das alterações ao DNS escamado quando a conta A2 cria um ponto final privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Network Security Group rules for subnets with private endpoints (Regras do Grupo de Segurança de Rede para sub-redes com pontos de extremidade privados)

Atualmente, não é possível configurar as regras [do Grupo de Segurança da Rede](../../virtual-network/network-security-groups-overview.md) (NSG) e as rotas definidas pelo utilizador para pontos finais privados. As regras NSG aplicadas à sub-rede que acolhe o ponto final privado não são aplicadas ao ponto final privado. Aplicam-se apenas a outros pontos finais (por exemplo: controladores de interface de rede). Uma solução limitada para esta questão é implementar as suas regras de acesso a pontos finais privados nas sub-redes de origem, embora esta abordagem possa exigir uma maior sobrecarga de gestão.

## <a name="next-steps"></a>Passos seguintes

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
