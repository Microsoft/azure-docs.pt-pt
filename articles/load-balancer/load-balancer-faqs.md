---
title: Perguntas frequentes - Azure Load Balancer
description: Respostas a perguntas frequentes sobre o Balançador de Carga Azure.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: e9f46b11d9c0b5251ee4d52f64d657926f6f9c5e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222994"
---
# <a name="load-balancer-frequently-asked-questions"></a>Balanceador de Carga frequentemente fez perguntas

## <a name="what-types-of-load-balancer-exist"></a>Que tipos de Balanceador de Carga existem?
Os balançadores de carga internos que equilibram o tráfego dentro de um VNET e de balançadores de carga externos que equilibram o tráfego de e para um ponto final ligado à Internet. Para obter mais informações, consulte [os tipos de balançadores de carga.](components.md#frontend-ip-configurations) 

Para ambos os tipos, o Azure oferece um SKU Básico e SKU Standard que têm diferentes capacidades funcionais, de desempenho, segurança e rastreio de saúde. Estas diferenças são explicadas no nosso artigo [de Comparação SKU.](skus.md)

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Como posso fazer upgrade de um Básico para um Balanceador de Carga Padrão?
Consulte a [atualização do](upgrade-basic-standard.md) artigo Basic para Standard para obter um script automatizado e orientação sobre a atualização de um SKU do Balancer de Carga.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quais são as diferentes opções de equilíbrio de carga em Azure?
Consulte o [guia de tecnologia do balanceador](/azure/architecture/guide/technology-choices/load-balancing-overview)  de carga para os serviços de equilíbrio de carga disponíveis e utilizações recomendadas para cada um.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Onde posso encontrar modelos arm balanceador de carga?
Consulte a [lista de modelos de arranque rápido do Azure Load Balancer](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) para modelos ARM de implementações comuns.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Como as regras de entrada da NAT são diferentes das regras de equilíbrio de carga?
As regras da NAT são usadas para especificar um recurso de backend para encaminhar o tráfego para. Por exemplo, configurar uma porta de balanço de carga específica para enviar o tráfego RDP para um VM específico. As regras de equilíbrio de carga são utilizadas para especificar um conjunto de recursos de backend para encaminhar o tráfego para, equilibrando a carga em cada instância. Por exemplo, uma regra do balançador de carga pode encaminhar os pacotes TCP na porta 80 do equilibrador de carga através de um conjunto de servidores web.

## <a name="what-is-ip-1686312916"></a>O que é IP 168.63.129.16?
O endereço IP virtual para o anfitrião marcado como o Balanceador de Carga da infraestrutura Azure, onde as Sondas de Saúde Azure são originárias. Ao configurar casos de backend, devem permitir que o tráfego deste endereço IP responda com sucesso às sondas de saúde. Esta regra não interage com o acesso ao frontend do balanceador de carga. Se não estiver a utilizar o Balançador de Carga Azure, pode anular esta regra. Pode saber mais sobre etiquetas de serviço [aqui.](../virtual-network/service-tags-overview.md#available-service-tags)

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Posso usar o VNet Global a espreitar com o Balanceador de Carga Básica?
N.º O Balancer de Carga Básica não suporta o olhar global do VNET. Em vez disso, pode utilizar um Balanceador de Carga Padrão. Consulte a [atualização do](upgrade-basic-standard.md) artigo Basic para Standard para uma atualização sem emenda.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Como posso descobrir o IP público que um Azure VM utiliza?

Existem muitas formas de determinar o endereço IP de fonte pública de uma ligação de saída. O OpenDNS fornece um serviço que lhe pode mostrar o endereço IP público do seu VM.
Ao utilizar o comando nslookup, pode enviar uma consulta DNS para o nome myip.opendns.com para o openDns resolver. O serviço devolve o endereço IP de origem que foi utilizado para enviar a consulta. Quando executou a seguinte consulta a partir do seu VM, a resposta é o IP público utilizado para esse VM:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Posso adicionar um VM da mesma disponibilidade definida para diferentes piscinas de backend de um Balancer de Carga?
Não, isto não é possível.

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Como funcionam as ligações com o Azure Storage na mesma região?
Ter conectividade de saída através dos cenários acima não é necessário ligar ao Armazenamento na mesma região que o VM. Se não quiser, utilize grupos de segurança de rede (NSGs) como explicado acima. Para a conectividade com o Armazenamento noutras regiões, é necessária conectividade de saída. Por favor, note que ao ligar ao Armazenamento a partir de um VM na mesma região, o endereço IP de origem nos registos de diagnóstico de Armazenamento será um endereço interno do fornecedor, e não o endereço IP público do seu VM. Se pretender restringir o acesso à sua conta de Armazenamento a VMs numa ou mais sub-redes de Rede Virtual na mesma região, utilize [os pontos finais do serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) e não o endereço IP público ao configurar a firewall da sua conta de armazenamento. Uma vez configurados os pontos finais do serviço, verá o seu endereço IP privado da Rede Virtual nos seus registos de diagnóstico de Armazenamento e não no endereço do fornecedor interno.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Quais são as melhores práticas no que diz respeito à conectividade de saída?
O Balancer de Carga Padrão e o IP Público Standard introduzem habilidades e comportamentos diferentes para a conectividade de saída. Não são os mesmos que os SKUs básicos. Se quiser conectividade de saída ao trabalhar com SKUs padrão, deve defini-lo explicitamente com endereços IP públicos padrão ou Balancer de Carga pública Padrão. Isto inclui a criação de conectividade de saída quando se utiliza um Balancer de Carga Padrão interno. Recomendamos que utilize sempre regras de saída num Balanceador de Carga público Standard. Isto significa que quando um Balancer de Carga Padrão interno é utilizado, você precisa tomar medidas para criar conectividade de saída para os VMs na piscina de backend se a conectividade de saída for desejada. No contexto da conectividade de saída, um VM autónomo único, todos os VM's num Conjunto de Disponibilidade, todas as instâncias de um VMSS comportam-se em grupo. Isto significa que, se um único VM num Conjunto de Disponibilidade estiver associado a um SKU Standard, todos os casos de VM dentro deste Conjunto de Disponibilidades comportam-se agora pelas mesmas regras que se estão associados ao SKU padrão, mesmo que um caso individual não esteja diretamente associado a ele. Este comportamento também é observado no caso de um VM autónomo com vários cartões de interface de rede ligados a um equilibrador de carga. Se um NIC for adicionado como um autónomo, terá o mesmo comportamento. Reveja cuidadosamente todo este documento para compreender os conceitos globais, reveja o [Balanceador de Carga Padrão](./load-balancer-overview.md) para as diferenças entre SKUs e reveja [as regras de saída](load-balancer-outbound-connections.md#outboundrules).
A utilização de regras de saída permite-lhe um controlo fino sobre todos os aspetos da conectividade de saída.
 
## <a name="next-steps"></a>Passos Seguintes
Se a sua pergunta não estiver listada acima, por favor envie feedback sobre esta página com a sua pergunta. Isto criará um problema gitHub para a equipa de produtos para garantir que todas as nossas valiosas perguntas de clientes são respondidas.
