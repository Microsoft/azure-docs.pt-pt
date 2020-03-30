---
title: Ligue-se ao Azure Data Lake Storage Gen1 a partir de VNETs Microsoft Docs
description: Ligue-se ao Azure Data Lake Storage Gen1 a partir de VNETs Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878873"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Access Azure Data Lake Storage Gen1 a partir de VMs dentro de um Azure VNET
Azure Data Lake Storage Gen1 é um serviço PaaS que funciona em endereços IP da Internet públicos. Qualquer servidor que possa ligar-se à Internet pública pode tipicamente ligar-se aos pontos finais do Azure Data Lake Storage Gen1 também. Por padrão, todos os VMs que estão em VNETs Azure podem aceder à Internet e, portanto, podem aceder ao Azure Data Lake Storage Gen1. No entanto, é possível configurar VMs num VNET para não ter acesso à Internet. Para tais VMs, o acesso ao Azure Data Lake Storage Gen1 também é restrito. O bloqueio do acesso público à Internet para VMs em VNETs Azure pode ser feito utilizando qualquer uma das seguintes abordagens:

* Configurando grupos de segurança de rede (NSG)
* Configurando as rotas definidas pelo utilizador (UDR)
* Ao trocar rotas via BGP (protocolo de encaminhamento dinâmico padrão da indústria), quando o ExpressRoute é utilizado, que bloqueia matem o acesso à Internet

Neste artigo, você aprenderá como permitir o acesso ao Azure Data Lake Storage Gen1 a partir de VMs Azure, que foram restritos a recursos de acesso usando um dos três métodos listados anteriormente.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Permitindo a conectividade com o Azure Data Lake Storage Gen1 a partir de VMs com conectividade restrita
Para aceder ao Azure Data Lake Storage Gen1 a partir desses VMs, deve configurá-los para aceder ao endereço IP para a região onde está disponível a conta Azure Data Lake Storage Gen1. Pode identificar os endereços IP das regiões da sua conta Data Lake Storage Gen1, resolvendo os nomes dNS das suas contas (`<account>.azuredatalakestore.net`). Para resolver os nomes dNS das suas contas, pode utilizar ferramentas como **o nslookup**. Abra um pedido de comando no seu computador e execute o seguinte comando:

    nslookup mydatastore.azuredatalakestore.net

A saída assemelha-se ao seguinte. O valor da propriedade **Address** é o endereço IP associado à sua conta Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Permitir a conectividade dos VMs restringidos pela utilização de NSG
Quando uma regra NSG é usada para bloquear o acesso à Internet, então pode criar outro NSG que permite o acesso ao Endereço IP gen1 de armazenamento de data Lake. Para obter mais informações sobre as regras do NSG, consulte a visão geral dos [grupos de segurança da rede](../virtual-network/security-overview.md). Para obter instruções sobre como criar NSGs, consulte [Como criar um grupo](../virtual-network/tutorial-filter-network-traffic.md)de segurança de rede .

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Permitir a conectividade dos VMs restringidos através da utilização de UDR ou ExpressRoute
Quando as rotas, quer os UDRs ou as rotas trocadas por BGP, são utilizadas para bloquear o acesso à Internet, uma rota especial precisa de ser configurada para que os VMs nessas subredes possam aceder aos pontos finais do Data Lake Storage Gen1. Para mais informações, consulte a [visão geral das rotas definidas pelo Utilizador](../virtual-network/virtual-networks-udr-overview.md). Para obter instruções sobre a criação de UDRs, consulte [Create UDRs in Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Permitir a conectividade dos VMs restringida pela utilização do ExpressRoute
Quando um circuito ExpressRoute é configurado, os servidores no local podem aceder ao Data Lake Storage Gen1 através de um público. Mais detalhes sobre a configuração do ExpressRoute para o público estão disponíveis nas [FAQs expressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Proteger dados armazenados no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

