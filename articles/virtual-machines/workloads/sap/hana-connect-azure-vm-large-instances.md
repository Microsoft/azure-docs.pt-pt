---
title: Configuração de conectividade de máquinas virtuais para SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Configuração de conectividade de máquinas virtuais para utilização DE SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8f69f94c53227dc86fbbf9e9936a2fc3c97ec55
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967878"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ligar VMs do Azure para Instâncias Grandes do HANA

O artigo [O que é SAP HANA em Azure (Grandes Instâncias)?](./hana-overview-architecture.md) refere que a implantação mínima de HANA Large Instances com a camada de aplicação SAP em Azure se parece com o seguinte:

![Azure VNet ligado ao SAP HANA em Azure (Grandes Instâncias) e no local](./media/hana-overview-architecture/image1-architecture.png)

Olhando mais de perto para o lado da rede virtual Azure, há necessidade de:

- A definição de uma rede virtual Azure na qual vai implantar os VMs da camada de aplicação SAP.
- A definição de sub-rede predefinida na rede virtual Azure que é realmente a que os VMs são implantados.
- A rede virtual Azure que é criada precisa de ter pelo menos uma sub-rede VM e uma sub-rede virtual Azure ExpressRoute. Estas sub-redes devem ser atribuídas aos intervalos de endereços IP, conforme especificado e discutido nas seguintes secções.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Criar a rede virtual Azure para HANA Grandes Instâncias

>[!Note]
>A rede virtual Azure para HANA Large Instances deve ser criada utilizando o modelo de implementação do Gestor de Recursos Azure. O modelo de implantação Azure mais antigo, vulgarmente conhecido como o modelo clássico de implementação, não é suportado pela solução HANA Large Instance.

Pode utilizar o portal Azure, o PowerShell, um modelo Azure ou o CLI Azure para criar a rede virtual. (Para obter mais informações, consulte [Criar uma rede virtual utilizando o portal Azure).](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) No exemplo seguinte, olhamos para uma rede virtual que é criada usando o portal Azure.

Ao referir-se ao espaço de **endereço** nesta documentação, ao espaço de endereço que a rede virtual Azure está autorizada a utilizar. Este espaço de endereço é também a gama de endereços que a rede virtual utiliza para propagação de rotas BGP. Este espaço de **endereço** pode ser visto aqui:

![Espaço de endereço de uma rede virtual Azure exibida no portal Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No exemplo anterior, com 10.16.0.0/16, a rede virtual Azure recebeu uma gama de endereços IP bastante grande e ampla para utilizar. Portanto, todos os intervalos de endereços IP de sub-redes subsequentes dentro desta rede virtual podem ter as suas gamas dentro desse espaço de endereço. Normalmente não recomendamos uma gama de endereços tão grande para uma única rede virtual em Azure. Mas vamos ver as sub-redes que são definidas na rede virtual Azure:

![Sub-redes de rede virtual Azure e os seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Olhamos para uma rede virtual com uma primeira sub-rede VM (aqui chamada "padrão") e uma sub-rede chamada "GatewaySubnet".

Nos dois gráficos anteriores, o **espaço de endereço de rede virtual** cobre tanto a gama de **endereços IP da sub-rede como** a do gateway de rede virtual.

Pode restringir o **espaço de endereço de rede virtual** às gamas específicas utilizadas por cada sub-rede. Também pode definir o **espaço de endereço de rede virtual** de uma rede virtual como múltiplas gamas específicas, como mostrado aqui:

![Espaço de endereço de rede virtual Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Neste caso, o **espaço de endereço de rede virtual** tem dois espaços definidos. São os mesmos que os intervalos de endereços IP que são definidos para a gama de endereços IP sub-rede do Azure VM e do gateway de rede virtual. 

Você pode usar qualquer padrão de nome que você gosta para estas sub-redes de inquilinos (sub-redes VM). No entanto, **deve haver sempre uma sub-rede de gateway, e apenas uma, para cada rede virtual** que se conecta ao circuito SAP HANA em Azure (Grandes Instâncias) Circuito ExpressRoute. **Esta sub-rede gateway tem de ser denominada "GatewaySubnet"** para se certificar de que o gateway ExpressRoute está devidamente colocado.

> [!WARNING] 
> É fundamental que a sub-rede gateway seja sempre chamada de "GatewaySubnet".

Pode utilizar várias sub-redes VM e intervalos de endereços não contíguos. Estes intervalos de endereços devem ser cobertos pelo **espaço de endereço de rede virtual** da rede virtual. Podem estar numa forma agregada. Também podem estar numa lista das gamas exatas das sub-redes VM e da sub-rede gateway.

Segue-se um resumo dos factos importantes sobre uma rede virtual Azure que se conecta à HANA Large Instances:

- Tem de enviar o **espaço de endereço de rede virtual** para a Microsoft quando estiver a realizar uma implementação inicial de HANA Large Instances. 
- O **espaço de endereço de rede virtual** pode ser uma gama maior que cobre as gamas tanto para a gama de endereços IP da sub-rede como para o gateway de rede virtual.
- Ou pode submeter várias gamas que cobrem as diferentes gamas de endereços IP do (s) intervalo de endereços IP da sub-rede VM e do intervalo de endereços IP do gateway de rede virtual.
- O **espaço de endereço de rede virtual** definido é utilizado para a propagação do encaminhamento BGP.
- O nome da sub-rede gateway deve ser : **"GatewaySubnet".**
- O espaço do endereço é utilizado como um filtro no lado HANA Large Instance para permitir ou não permitir o tráfego para as unidades HANA Large Instance de Azure. As informações de encaminhamento BGP da rede virtual Azure e dos intervalos de endereço IP configurados para filtragem no lado HANA Large Instance devem coincidir. Caso contrário, podem ocorrer problemas de conectividade.
- Existem alguns detalhes sobre a sub-rede gateway que são discutidos mais tarde, na secção **Ligando uma rede virtual à HANA Large Instance ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a definir 

Algumas das gamas de endereços IP que são necessárias para a implantação de Grandes Instâncias HANA já foram introduzidas. Mas há mais intervalos de endereços IP que também são importantes. Nem todas as seguintes gamas de endereços IP precisam de ser submetidas à Microsoft. No entanto, é necessário defini-los antes de enviar um pedido de implantação inicial:

- **Espaço de endereço de rede virtual**: O espaço de endereço de rede **virtual** são os intervalos de endereço IP que atribui ao parâmetro do espaço de endereço nas redes virtuais Azure. Estas redes ligam-se ao ambiente SAP HANA Large Instance. Recomendamos que este parâmetro de espaço de endereço seja um valor multi-linha. Deve consistir na gama de sub-redes do Azure VM e na gama de sub-redes do gateway Azure. Esta gama de sub-redes foi mostrada nos gráficos anteriores. NÃO deve sobrepor-se às suas gamas IP no local ou servidor ou intervalos de endereços ER-P2P. Como obtém estes intervalos de endereços IP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer um ou vários intervalos de endereços IP que não sejam utilizados dentro da sua rede. Por exemplo, a sub-rede do seu Azure VM é de 10.0.1.0/24 e a sub-rede da sua sub-rede de gateway Azure é de 10.0.2.0/28.  Recomendamos que o seu espaço de endereço de rede virtual Azure seja definido como: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores do espaço do endereço possam ser agregados, recomendamos que os corresponda às gamas de sub-redes. Desta forma, pode acidentalmente evitar reutilizar intervalos de endereços IP não uusados em espaços de endereço maiores em outros lugares da sua rede. **O espaço de endereço de rede virtual é um intervalo de endereços IP. Tem de ser submetido à Microsoft quando se pede uma primeira implementação.**
- **Intervalo de endereço IP da sub-rede Azure VM:** Esta gama de endereços IP é a que atribui ao parâmetro de sub-rede de rede virtual Azure. Este parâmetro está na sua rede virtual Azure e conecta-se ao ambiente SAP HANA Large Instance. Este intervalo de endereços IP é utilizado para atribuir endereços IP aos seus VMs Azure. Os endereços IP fora desta gama são permitidos ligar-se ao seu servidor SAP HANA Large Instance. Se necessário, pode utilizar várias sub-redes Azure VM. Recomendamos um bloco CIDR /24 para cada sub-rede Azure VM. Esta gama de endereços deve fazer parte dos valores que são utilizados no espaço de endereços de rede virtual Azure. Como obtém este intervalo de endereços IP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está a ser utilizada dentro da sua rede.
- **Gama de endereços IP de porta de rede virtual:** Dependendo das funcionalidades que pretende utilizar, o tamanho recomendado é:
   - Gateway ExpressRoute ultra-desempenho: /26 bloco de endereços -- necessário para a classe tipo II de SKUs.
   - Coexistência com VPN e ExpressRoute utilizando um gateway de rede virtual ExpressRoute de alto desempenho (ou menor): /27 bloco de endereços.
   - Todas as outras situações: /28 bloco de endereços. Esta gama de endereços deve ser uma parte dos valores utilizados nos valores "espaço de endereço VNet". Esta gama de endereços deve ser uma parte dos valores que são utilizados nos valores de espaço de endereço de rede virtual Azure que submete à Microsoft. Como obtém este intervalo de endereços IP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está atualmente a ser utilizada dentro da sua rede. 
- **Intervalo de endereços para conectividade ER-P2P:** Esta gama é a gama IP para a sua ligação P2P SAP HANA Large Instance ExpressRoute (ER). Esta gama de endereços IP deve ser um intervalo de endereços IP /29. Este intervalo não deve sobrepor-se às suas gamas de endereços IP no local ou a outros intervalos de endereços Azure IP. Esta gama de endereços IP é utilizada para configurar a conectividade ER a partir do seu gateway virtual ExpressRoute para os servidores SAP HANA Large Instance. Como obtém este intervalo de endereços IP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está atualmente a ser utilizada dentro da sua rede. **Este alcance é um intervalo de endereços IP. Tem de ser submetido à Microsoft quando se pede uma primeira implementação.**  
- **Gama de endereços ip pool do servidor:** Este intervalo de endereços IP é utilizado para atribuir o endereço IP individual aos servidores de grande instância HANA. O tamanho da sub-rede recomendado é um bloco CIDR /24. Se necessário, pode ser menor, com apenas 64 endereços IP. A partir desta gama, os primeiros 30 endereços IP são reservados para utilização pela Microsoft. Certifique-se de que explica este facto quando escolher o tamanho do intervalo. Este intervalo NÃO deve sobrepor-se aos seus endereços IP no local ou outros endereços IP Azure. Como obtém este intervalo de endereços IP? A sua equipa de rede corporativa ou prestador de serviços deve fornecer uma gama de endereços IP que não está atualmente a ser utilizada dentro da sua rede.  **Esta gama é uma gama de endereços IP, que precisa de ser submetida à Microsoft quando se pede uma implementação inicial**.

Intervalos de endereço IP opcionais que eventualmente precisam de ser submetidos à Microsoft:

- Se optar por utilizar o [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) para ativar o encaminhamento direto das unidades HANA Large Instance, tem de reservar outro intervalo de endereços IP /29. Este intervalo pode não se sobrepor a nenhum dos outros intervalos de endereço IP definidos anteriormente.
- Se optar por utilizar o [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) para permitir o encaminhamento direto de um inquilino HANA Large Instance numa região de Azure para outro inquilino da HANA Large Instance noutra região de Azure, precisa de reservar outra gama de endereços IP /29. Este intervalo pode não se sobrepor a nenhum dos outros intervalos de endereço IP definidos anteriormente.

Para obter mais informações sobre o ExpressRoute Global Reach e utilização em torno de grandes instâncias HANA, verifique os documentos:

- [SAP HANA (Grandes Instâncias) arquitetura de rede](./hana-network-architecture.md)
- [Ligue uma rede virtual a grandes instâncias HANA](./hana-connect-vnet-express-route.md)
 
É necessário definir e planear os intervalos de endereços IP que foram descritos anteriormente. No entanto, não é necessário transmiti-los todos à Microsoft. Os intervalos de endereço IP que é obrigado a nomear para a Microsoft são:

- Espaço de endereço de rede virtual Azure
- Intervalo de endereços para conectividade ER-P2P
- Gama de endereços IP do servidor

Se adicionar redes virtuais adicionais que precisam de se ligar a HANA Large Instances, tem de enviar o novo espaço de endereço de rede virtual Azure que está a adicionar à Microsoft. 

Segue-se um exemplo das diferentes gamas e algumas gamas de exemplos que é necessário configurar e eventualmente fornecer à Microsoft. O valor para o espaço de endereço de rede virtual Azure não é agregado no primeiro exemplo. No entanto, é definido a partir das gamas da primeira gama de endereços IP da sub-rede Azure VM e da gama de endereços IP da rede virtual gateway. 

Pode utilizar várias sub-redes VM dentro da rede virtual Azure quando configurar e enviar as gamas de endereços IP adicionais da sub-rede VM adicional como parte do espaço de endereço virtual Azure.

![Intervalos de endereços IP exigidos no SAP HANA em Azure (Grandes Instâncias) implantação mínima](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

O gráfico não mostra a gama adicional de endereços IP que são necessárias para a utilização opcional do ExpressRoute Global Reach.

Também pode agregar os dados que submete à Microsoft. Nesse caso, o espaço de endereço da rede virtual Azure inclui apenas um espaço. Utilizando o endereço IP varia do exemplo anterior, o espaço de endereço de rede virtual agregado pode parecer a seguinte imagem:

![Segunda possibilidade de intervalos de endereços IP exigidos no SAP HANA em Azure (Grandes Instâncias) implantação mínima](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

No exemplo, em vez de duas gamas menores que definiram o espaço de endereço da rede virtual Azure, temos uma gama maior que cobre 4096 endereços IP. Uma definição tão grande do espaço de endereço deixa algumas gamas bastante grandes sem ser reutilizada. Uma vez que o(s) valor de espaço de endereço de rede virtual é utilizado para a propagação da rota BGP, a utilização das gamas não utilizadas no local ou em qualquer outro lugar da sua rede pode causar problemas de encaminhamento. O gráfico não mostra a gama adicional de endereços IP que são necessárias para a utilização opcional do ExpressRoute Global Reach.

Recomendamos que mantenha o espaço do endereço bem alinhado com o espaço real do endereço da sub-rede que utiliza. Se necessário, sem incorrer em tempo de inatividade na rede virtual, pode sempre adicionar novos valores de espaço de endereço mais tarde.
 
> [!IMPORTANT] 
> Cada gama de endereços IP no ER-P2P, no pool IP do servidor e no espaço de endereço virtual Azure **NÃO** deve sobrepor-se uns aos outros ou com qualquer outro intervalo que seja utilizado na sua rede. Cada um deve ser discreto. Como os dois gráficos anteriores mostram, também não podem ser uma sub-rede de qualquer outra gama. Se ocorrerem sobreposições entre intervalos, a rede virtual Azure pode não se ligar ao circuito ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Os próximos passos após as gamas de endereços foram definidos
Depois de definidos os intervalos de endereços IP, é necessário que aconteçam as seguintes coisas:

1. Envie os intervalos de endereços IP para o espaço de endereço de rede virtual Azure, a conectividade ER-P2P e a gama de endereços IP do servidor, juntamente com outros dados que foram listados no início do documento. Neste ponto, também pode começar a criar a rede virtual e as sub-redes VM. 
2. Um circuito ExpressRoute é criado pela Microsoft entre a sua subscrição Azure e o selo HANA Large Instance.
3. Uma rede de inquilinos é criada no carimbo de Grande Instância pela Microsoft.
4. A Microsoft configura a rede na infraestrutura SAP HANA em Azure (Grandes Instâncias) para aceitar endereços IP do seu espaço de endereço de rede virtual Azure que comunica com HANA Large Instances.
5. Dependendo do SKU SAP HANA específico em Azure (Grandes Instâncias) que comprou, a Microsoft atribui uma unidade de computação numa rede de inquilinos. Também atribui e monta armazenamento, e instala o sistema operativo (SUSE ou Red Hat Linux). Os endereços IP para estas unidades são retirados do intervalo de endereços IP Pool do Servidor que submeteu à Microsoft.

No final do processo de implementação, a Microsoft fornece-lhe os seguintes dados:
- Informação necessária para ligar a sua rede virtual Azure ao circuito ExpressRoute que liga as redes virtuais Azure à HANA Large Instances:
     - Chave de autorização
     - ExpressRoute PeerID
- Dados para aceder a HANA Large Instances depois de estabelecer o circuito ExpressRoute e a rede virtual Azure.

Também pode encontrar a sequência de ligação de grandes instâncias HANA no documento [SAP HANA na configuração de Azure (Grandes Instâncias).](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) Muitos dos seguintes passos são mostrados num exemplo de implantação nesse documento. 

## <a name="next-steps"></a>Passos seguintes

- Consulte a [Ligação de uma rede virtual ao HANA Large Instance ExpressRoute](hana-connect-vnet-express-route.md).
