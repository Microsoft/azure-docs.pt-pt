---
title: Ligar ao Azure Data Lake Storage Gen1 de VNETs | Documentos da Microsoft
description: Ligar ao Azure Data Lake Storage Gen1 de VNETs do Azure
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878065"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Acesso do Azure Data Lake Storage Gen1 de VMs dentro de uma VNET do Azure
Geração de armazenamento 1 do Azure Data Lake é um serviço de PaaS que é executado em endereços de IP de Internet públicos. Qualquer servidor que pode ligar-se para a Internet pública, normalmente, pode ligar a pontos finais de geração 1 de armazenamento do Azure Data Lake também. Por predefinição, todas as VMs em VNETs do Azure podem aceder à Internet e, por conseguinte, podem aceder a geração 1 de armazenamento do Azure Data Lake. No entanto, é possível configurar as VMs numa VNET não ter acesso à Internet. Para essas VMs, acesso a geração 1 de armazenamento do Azure Data Lake é restrito também. Bloquear o acesso público à Internet para VMs em VNETs do Azure pode ser feito através de qualquer uma das seguintes abordagens:

* Ao configurar grupos de segurança de rede (NSG)
* Ao configurar rotas definidas pelo utilizador (UDR)
* Ao trocar rotas através do BGP (setor padrão protocolo de encaminhamento dinâmico), quando é utilizado o ExpressRoute, que bloquear o acesso à Internet

Neste artigo, aprenderá a ativar o acesso ao Azure Data Lake Storage Gen1 VMS do Azure, que foram restritos para aceder aos recursos através de um dos três métodos listados anteriormente.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Ativar a conectividade para geração 1 de armazenamento do Azure Data Lake das VMs com conectividade restrita
Para acessar Gen1 de armazenamento do Azure Data Lake nessas VMs, deve configurá-las para acessar o endereço IP para a região onde a conta de geração 1 de armazenamento do Azure Data Lake está disponível. Pode identificar os endereços IP para as regiões de conta do Data Lake Storage Gen1 resolvendo os nomes DNS das suas contas (`<account>.azuredatalakestore.net`). Para resolver nomes DNS das suas contas, pode usar ferramentas como **nslookup**. Abra um prompt de comando no seu computador e execute o seguinte comando:

    nslookup mydatastore.azuredatalakestore.net

O resultado é semelhante ao seguinte. O valor contra **endereço** propriedade é o endereço IP associado à conta de geração 1 de armazenamento do Data Lake.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Ativar a conectividade de VMs restringida ao utilizar o NSG
Quando uma regra de NSG é utilizada para bloquear o acesso à Internet, em seguida, pode criar outro NSG que permita o acesso para o endereço de IP de geração 1 do Data Lake Storage. Para obter mais informações sobre as regras do NSG, consulte [descrição geral dos grupos de segurança de rede](../virtual-network/security-overview.md). Para obter instruções sobre como criar NSGs, consulte [como criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Ativar a conectividade de VMs restringida ao utilizar o UDR ou o ExpressRoute
Quando as rotas, UDRs ou rotas BGP-trocados, são utilizadas para bloquear o acesso à Internet, uma rota especial tem de ser configurado para que as VMs em tais sub-redes podem aceder a pontos finais de geração 1 de armazenamento do Data Lake. Para obter mais informações, consulte [descrição geral das rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md). Para obter instruções sobre como criar as UDRs, consulte [criar as UDRs no Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Ativar a conectividade de VMs restringida ao utilizar o ExpressRoute
Quando um circuito do ExpressRoute estiver configurado, os servidores no local podem aceder a geração 1 de armazenamento do Data Lake através do peering público. Obter mais detalhes sobre como configurar o ExpressRoute para peering público está disponível no [FAQs do ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados armazenados na geração 1 de armazenamento do Azure Data Lake](data-lake-store-security-overview.md)

