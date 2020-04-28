---
title: Configuração de conectividade de máquinas virtuais para SAP HANA em Azure (Grandes Instâncias) [ Microsoft Docs
description: Configuração de conectividade a partir de máquinas virtuais para a utilização de SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74224718"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ligar VMs do Azure para Instâncias Grandes do HANA

O artigo [O que é SAP HANA sobre Azure (Grandes Instâncias)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) menciona que a implantação mínima de grandes instâncias HANA com a camada de aplicação SAP em Azure parece ser a seguinte:

![Azure VNet ligado ao SAP HANA em Azure (Grandes Instâncias) e no local](./media/hana-overview-architecture/image1-architecture.png)

Olhando mais de perto para o lado da rede virtual Azure, há uma necessidade de:

- A definição de uma rede virtual Azure na qual vai implantar os VMs da camada de aplicação SAP.
- A definição de uma sub-rede padrão na rede virtual Azure que é realmente aquela em que os VMs são implantados.
- A rede virtual Azure que foi criada precisa de ter pelo menos uma subnet VM e uma subnet de gateway de rede virtual Azure ExpressRoute. Estas subredes devem ser atribuídas às gamas de endereços IP especificadas e discutidas nas seguintes secções.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Criar a rede virtual Azure para grandes instâncias HANA

>[!Note]
>A rede virtual Azure para grandes instâncias HANA deve ser criada utilizando o modelo de implementação do Gestor de Recursos Azure. O modelo de implantação mais antigo do Azure, vulgarmente conhecido como modelo de implantação clássico, não é suportado pela solução HANA Large Instance.

Pode utilizar o portal Azure, PowerShell, um modelo Azure ou o Azure CLI para criar a rede virtual. (Para obter mais informações, consulte [Criar uma rede virtual utilizando o portal Azure).](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) No exemplo seguinte, olhamos para uma rede virtual que é criada através do portal Azure.

Ao referir-se ao espaço de **endereço** nesta documentação, para o espaço de endereço que a rede virtual Azure está autorizada a utilizar. Este espaço de endereço é também a gama de endereços que a rede virtual utiliza para a propagação da rota BGP. Este **espaço de endereço** pode ser visto aqui:

![Espaço de endereço de uma rede virtual Azure exibida no portal Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No exemplo anterior, com 10.16.0.0/16, a rede virtual Azure recebeu uma gama de endereços IP bastante grande e ampla para utilizar. Portanto, todas as gamas de endereços IP de subredes subsequentes dentro desta rede virtual podem ter as suas gamas dentro desse espaço de endereço. Normalmente não recomendamos uma gama de endereços tão grande para uma única rede virtual em Azure. Mas vamos olhar para as subredes que são definidas na rede virtual Azure:

![Subnets de rede virtual Azure e as suas gamas de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Olhamos para uma rede virtual com uma primeira subnet VM (aqui chamada "padrão") e uma subnet chamada "GatewaySubnet".

Nos dois gráficos anteriores, o espaço de endereço de **rede virtual** abrange tanto a gama de **endereços IP da sub-rede do VM Azure** como a do portal de rede virtual.

Pode restringir o espaço de endereço da **rede virtual** às gamas específicas utilizadas por cada sub-rede. Também pode definir o espaço de endereço de **rede virtual** de uma rede virtual como múltiplas gamas específicas, como mostrado aqui:

![Espaço de endereço de rede virtual Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Neste caso, o espaço de endereço seleção **virtual** tem dois espaços definidos. São os mesmos que as gamas de endereços IP que são definidas para a gama de endereços IP da sub-rede do VM Azure e o gateway da rede virtual. 

Você pode usar qualquer padrão de nomeação que você quiser para estas subredes de inquilino (subnets VM). No entanto, **deve haver sempre uma, e apenas uma, sub-rede de gateway para cada rede virtual** que se conecta ao circuito SAP HANA no circuito ExpressRoute (Grandes Instâncias). **Esta sub-rede de gateway tem de ser denominada "GatewaySubnet"** para se certificar de que o gateway ExpressRoute está devidamente colocado.

> [!WARNING] 
> É fundamental que a subnet de gateway seja sempre chamada de "GatewaySubnet".

Pode utilizar várias subredes VM e intervalos de endereços não contíguos. Estas gamas de endereços devem ser abrangidas pelo espaço de endereço seleto da **rede virtual** da rede virtual. Podem estar numa forma agregada. Também podem estar numa lista das gamas exatas das subredes VM e da sub-rede gateway.

Segue-se um resumo dos factos importantes sobre uma rede virtual Azure que se conecta às grandes instâncias HANA:

- Tem de submeter o espaço de endereço de **rede virtual** à Microsoft quando estiver a realizar uma implementação inicial de Grandes Instâncias HANA. 
- O espaço de endereço de **rede virtual** pode ser uma gama maior que abrange as gamas tanto para a gama de endereços IP da sub-rede do VM Azure como para o gateway da rede virtual.
- Ou pode submeter várias gamas que cobrem as diferentes gamas de endereços IP do intervalo de endereços IP da subnet VM e a gama de endereços IP de gateway de rede virtual.
- O **espaço** de endereço de rede virtual definido é utilizado para propagação de encaminhamento de BGP.
- O nome da sub-rede gateway deve ser: **"GatewaySubnet".**
- O espaço de endereço é utilizado como filtro no lado HANA Large Instance para permitir ou proibir o tráfego para as unidades HANA Large Instance de Azure. As informações de encaminhamento de BGP da rede virtual Azure e as gamas de endereços IP que estão configuradas para filtragem no lado hana large instance devem coincidir. Caso contrário, podem ocorrer problemas de conectividade.
- Existem alguns detalhes sobre a subnet gateway que são discutidos mais tarde, na secção **Connecting a virtual network to HANA Large Instance ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes gamas de endereços IP a definir 

Algumas das gamas de endereços IP que são necessárias para a implementação de GRANDES Instâncias HANA já foram introduzidas. Mas há mais gamas de endereços IP que também são importantes. Nem todas as seguintes gamas de endereços IP têm de ser submetidas à Microsoft. No entanto, precisa de defini-los antes de enviar um pedido de implantação inicial:

- **Espaço**de endereço de rede virtual : O espaço de endereço de **rede virtual** é o endereço IP que atribui ao seu parâmetro de espaço de endereço nas redes virtuais Azure. Estas redes ligam-se ao ambiente de instância sap HANA. Recomendamos que este parâmetro de espaço de endereço seja um valor de várias linhas. Deve consistir na gama de sub-redes do VM Azure e na gama de sub-redes do gateway Azure. Esta gama de sub-redes foi mostrada nos gráficos anteriores. Não deve sobrepor-se ao seu conjunto IP no local ou ao servidor ou aos intervalos de endereços ER-P2P. Como obtém estes intervalos de endereçoip(s)? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma ou várias gamas de endereços IP que não sejam utilizadas dentro da sua rede. Por exemplo, a sub-rede do seu VM Azure é 10.0.1.0/24, e a sub-rede da sua sub-rede azure gateway é 10.0.2.0/28.  Recomendamos que o seu espaço de endereço de rede virtual Azure seja definido como: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores do espaço de endereço possam ser agregados, recomendamos que os combinem com as gamas de sub-rede. Desta forma, pode acidentalmente evitar a reutilização de gamas de endereços IP não utilizados em espaços de endereço maiores em outros lugares da sua rede. O espaço de endereço de rede virtual é um intervalo de **endereçoIP. Tem de ser submetida à Microsoft quando pede uma implementação inicial**.
- Gama de endereços IP da **sub-rede Azure VM:** Esta gama de endereços IP é a que atribui ao parâmetro de sub-rede virtual Azure. Este parâmetro encontra-se na sua rede virtual Azure e liga-se ao ambiente SAP HANA Large Instance. Esta gama de endereços IP é utilizada para atribuir endereços IP aos seus VMs Azure. Os endereços IP fora desta gama podem ligar-se ao (s) servidor(s) s da Instância Grande SAP HANA. Se necessário, pode utilizar várias subredes Azure VM. Recomendamos um bloco CIDR /24 para cada subnet Azure VM. Esta gama de endereços deve fazer parte dos valores utilizados no espaço de endereços da rede virtual Azure. Como obtém este intervalo de endereçoIP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não esteja a ser utilizada dentro da sua rede.
- Gama de endereços IP de gateway de **rede virtual:** Dependendo das funcionalidades que pretende utilizar, o tamanho recomendado é:
   - Gateway ExpressRoute ultra-performance: /26 endereço block -- necessário para classe Tipo II de SKUs.
   - Coexistência com VPN e ExpressRoute utilizando um gateway de rede virtual ExpressRoute de alto desempenho (ou menor): /27 bloco de endereços.
   - Todas as outras situações: bloco de endereços /28. Esta gama de endereços deve fazer parte dos valores utilizados nos valores "VNet address space". Esta gama de endereços deve fazer parte dos valores utilizados nos valores de espaço de endereço de rede virtual Azure que submete à Microsoft. Como obtém este intervalo de endereçoIP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está a ser utilizada dentro da sua rede. 
- **Intervalo de endereços para conectividade ER-P2P:** Esta gama é a gama IP para a sua ligação P2P sap HANA Large Instance ExpressRoute (ER). Esta gama de endereços IP deve ser uma gama de endereços IP /29 CIDR. Esta gama NÃO deve sobrepor-se às suas áreas de endereços IP ou Azure. Esta gama de endereços IP é utilizada para configurar a conectividade ER desde a sua porta de entrada virtual ExpressRoute até aos servidores SAP HANA Large Instance. Como obtém este intervalo de endereçoIP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está a ser utilizada dentro da sua rede. Esta gama é uma gama de **endereços IP. Tem de ser submetida à Microsoft quando pede uma implementação inicial**.  
- Intervalo de **endereços ip** do servidor: Esta gama de endereços IP é utilizada para atribuir o endereço IP individual a servidores de grandes instâncias HANA. O tamanho recomendado da sub-rede é um bloco CIDR /24. Se necessário, pode ser menor, com apenas 64 endereços IP. A partir desta gama, os primeiros 30 endereços IP são reservados para utilização pela Microsoft. Certifique-se de que explica este facto quando escolher o tamanho do intervalo. Esta gama NÃO deve sobrepor-se às suas instalações ou a outros endereços IP Azure. Como obtém este intervalo de endereçoIP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está a ser utilizada dentro da sua rede.  Esta gama é uma gama de **endereços IP, que precisa de ser submetida à Microsoft ao pedir uma implementação inicial**.

Gamas opcionais de endereços IP que eventualmente precisam de ser submetidas à Microsoft:

- Se optar por utilizar o [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para permitir o encaminhamento direto das instalações para as unidades HANA Large Instance, tem de reservar outra gama de endereços IP /29. Esta gama pode não se sobrepor a nenhuma das outras gamas de endereços IP que definiu anteriormente.
- Se optar por utilizar o [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para permitir o encaminhamento direto de um inquilino hana large instance numa região de Azure para outro inquilino HANA Large Instance em outra região de Azure, você precisa reservar outra gama de endereços IP /29. Esta gama pode não se sobrepor a nenhuma das outras gamas de endereços IP que definiu anteriormente.

Para obter mais informações sobre o ExpressRoute Global Reach e o uso em torno de grandes instâncias HANA, consulte os documentos:

- [Arquitetura de rede SAP HANA (Grandes Instâncias)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Ligue uma rede virtual a grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
É necessário definir e planear as gamas de endereços IP que foram descritas anteriormente. No entanto, não é necessário transmiti-los todos para a Microsoft. As gamas de endereços IP que é obrigado a nomear para a Microsoft são:

- Espaço de endereço s de rede virtual Azure
- Intervalo de endereços para conectividade ER-P2P
- Intervalo de endereços de piscina IP do servidor

Se adicionar redes virtuais adicionais que precisam de se ligar a HANA Grandes Instâncias, tem de submeter o novo espaço de endereço seletiva da rede azure que está a adicionar à Microsoft. 

Segue-se um exemplo das diferentes gamas e de algumas gamas de exemplo que precisa de configurar e eventualmente fornecer à Microsoft. O valor para o espaço de endereço seletivo da rede virtual Azure não é agregado no primeiro exemplo. No entanto, é definido a partir das gamas da primeira gama de endereços IP da sub-rede Azure VM e da gama de endereços IP da sub-rede virtual. 

Pode utilizar várias subredes VM dentro da rede virtual Azure quando configurar e submeter as gamas de endereços IP adicionais da subrede(s) VM adicional como parte do espaço de endereço s da rede virtual Azure.

![Intervalos de endereçoip exigidos em SAP HANA em Azure (Grandes Instâncias) implantação mínima](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

O gráfico não mostra as gamas de endereços IP adicionais que são necessárias para a utilização opcional do ExpressRoute Global Reach.

Também pode agregar os dados que submete à Microsoft. Nesse caso, o espaço de endereço da rede virtual Azure inclui apenas um espaço. Utilizando os endereços IP desde o exemplo anterior, o espaço de endereços de rede virtual agregado pode parecer a seguinte imagem:

![Segunda possibilidade de intervalos de endereçoip exigidos no SAP HANA em Azure (Grandes Instâncias) implantação mínima](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

No exemplo, em vez de duas gamas mais pequenas que definiram o espaço de endereço da rede virtual Azure, temos uma gama maior que abrange 4096 endereços IP. Uma definição tão grande do espaço de endereço deixa algumas gamas bastante grandes não utilizadas. Uma vez que o valor espacial de endereço s de endereço de rede virtual é utilizado para a propagação de rotas BGP, o uso das gamas não utilizadas no local ou em qualquer outro lugar da sua rede pode causar problemas de encaminhamento. O gráfico não mostra as gamas de endereços IP adicionais que são necessárias para a utilização opcional do ExpressRoute Global Reach.

Recomendamos que mantenha o espaço de endereço bem alinhado com o espaço de endereço da sub-rede real que utiliza. Se necessário, sem incorrer em tempo de inatividade na rede virtual, pode sempre adicionar novos valores de espaço de endereço mais tarde.
 
> [!IMPORTANT] 
> Cada intervalo de endereçoIP no ER-P2P, no conjunto IP do servidor e no espaço de endereço de rede virtual Azure **NÃO** deve sobrepor-se entre si ou com qualquer outra gama que seja utilizada na sua rede. Cada um deve ser discreto. Como os dois gráficos anteriores mostram, também não podem ser uma sub-rede de qualquer outra gama. Se ocorrerem sobreposições entre intervalos, a rede virtual Azure pode não se ligar ao circuito ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Os próximos passos após os intervalos de endereços foram definidos
Após a definição das gamas de endereços IP, as seguintes coisas têm de acontecer:

1. Envie as gamas de endereços IP para o espaço de endereço de rede virtual Azure, a conectividade ER-P2P e a gama de endereços IP do servidor, juntamente com outros dados que foram listados no início do documento. Neste ponto, também pode começar a criar a rede virtual e as subredes VM. 
2. Um circuito ExpressRoute é criado pela Microsoft entre a sua subscrição Azure e o carimbo HANA Large Instance.
3. Uma rede de inquilinos é criada no carimbo de Grande Instância pela Microsoft.
4. A Microsoft confunde a rede na infraestrutura SAP HANA on Azure (Grandes Instâncias) para aceitar endereços IP do seu espaço de endereços de rede virtual Azure que comunica com as Grandes Instâncias HANA.
5. Dependendo do SAP HANA específico no Azure (Grandes Instâncias) SKU que comprou, a Microsoft atribui uma unidade de computação numa rede de inquilinos. Também aloca e monta o armazenamento, e instala o sistema operativo (SUSE ou Red Hat Linux). Os endereços IP para estas unidades são retirados da gama de endereços IP Pool do Servidor que submeteu à Microsoft.

No final do processo de implementação, a Microsoft entrega-lhe os seguintes dados:
- Informações necessárias para ligar a sua rede virtual Azure ao circuito ExpressRoute que liga redes virtuais Azure a grandes instâncias HANA:
     - Chave de autorização(s)
     - ExpressRoute PeerID
- Dados para aceder a HANA Grandes Instâncias após estabelecer o circuito ExpressRoute e a rede virtual Azure.

Também pode encontrar a sequência de ligação de GRANDES Instâncias HANA no documento [SAP HANA on Azure (Grandes Instâncias) Configuração](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitos dos seguintes passos são mostrados num exemplo de implantação nesse documento. 

## <a name="next-steps"></a>Passos seguintes

- Consulte a [Ligação de uma rede virtual à HANA Large Instance ExpressRoute](hana-connect-vnet-express-route.md).
