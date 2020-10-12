---
title: Ligue-se ao Azure Data Lake Storage Gen1 a partir de VNETs Microsoft Docs
description: Saiba como permitir o acesso ao Azure Data Lake Storage Gen1 a partir de máquinas virtuais Azure que têm acesso restrito a recursos.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 0fa836ea31793d9177ad6e838ddea1516bf51733
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88191394"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Acesso Azure Data Lake Storage Gen1 a partir de VMs dentro de um Azure VNET
Azure Data Lake Storage Gen1 é um serviço PaaS que funciona em endereços IP da Internet públicos. Qualquer servidor que possa ligar-se à Internet pública pode normalmente ligar-se aos pontos finais da Azure Data Lake Storage Gen1. Por padrão, todos os VMs que estão em VNETs Azure podem aceder à Internet e, portanto, podem aceder a Azure Data Lake Storage Gen1. No entanto, é possível configurar VMs num VNET para não ter acesso à Internet. Para estes VMs, o acesso ao Azure Data Lake Storage Gen1 também é restrito. O bloqueio do acesso público à Internet dos VMs em VNETs Azure pode ser feito utilizando qualquer uma das seguintes abordagens:

* Configurando grupos de segurança de rede (NSG)
* Configurando as rotas definidas pelo utilizador (UDR)
* Trocando rotas via BGP (protocolo de encaminhamento dinâmico padrão da indústria), quando o ExpressRoute é utilizado, que bloqueia o acesso à Internet

Neste artigo, você aprenderá como permitir o acesso ao Azure Data Lake Storage Gen1 a partir de VMs Azure, que foram restritos ao acesso a recursos usando um dos três métodos listados anteriormente.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Permitir a conectividade com a Azure Data Lake Storage Gen1 a partir de VMs com conectividade restrita
Para aceder ao Azure Data Lake Storage Gen1 a partir desses VMs, deve configurá-los para aceder ao endereço IP para a região onde está disponível a conta Azure Data Lake Storage Gen1. Pode identificar os endereços IP das regiões da sua conta de Armazenamento de Dados Lake Gen1, resolvendo os nomes DNS das suas contas `<account>.azuredatalakestore.net` (). Para resolver os nomes DNS das suas contas, pode utilizar ferramentas como **o nslookup**. Abra um pedido de comando no seu computador e execute o seguinte comando:

```console
nslookup mydatastore.azuredatalakestore.net
```

A saída assemelha-se ao seguinte. O valor contra a propriedade **Address** é o endereço IP associado à sua conta Desembrulhamento gen1.

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Permitir a conectividade dos VMs restringidos pela utilização de NSG
Quando uma regra NSG é usada para bloquear o acesso à Internet, então pode criar outro NSG que permita o acesso ao endereço IP de armazenamento de dados. Para obter mais informações sobre as regras da NSG, consulte [a visão geral dos grupos de segurança da Rede](../virtual-network/security-overview.md). Para obter instruções sobre como criar NSGs, consulte [Como criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Permitir a conectividade dos VMs restringidos através da utilização de UDR ou ExpressRoute
Quando as rotas, quer as UDRs quer as rotas trocadas pelo BGP, são usadas para bloquear o acesso à Internet, uma rota especial precisa de ser configurada para que os VMs nessas sub-redes possam aceder aos pontos finais da Data Lake Storage Gen1. Para obter mais informações, consulte [a visão geral das rotas definidas pelo Utilizador.](../virtual-network/virtual-networks-udr-overview.md) Para obter instruções sobre a criação de UDRs, consulte [criar UDRs no Gestor de Recursos](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Permitir a conectividade dos VMs restritos através da utilização do ExpressRoute
Quando um circuito ExpressRoute é configurado, os servidores no local podem aceder ao Data Lake Storage Gen1 através do espreitamento público. Mais detalhes sobre a configuração do ExpressRoute para o espreitamento público estão disponíveis nas [FAQs ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Consulte também
* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados armazenados no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

