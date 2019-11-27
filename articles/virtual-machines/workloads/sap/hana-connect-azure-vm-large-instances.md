---
title: Configuração de conectividade de máquinas virtuais para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Configuração de conectividade de máquinas virtuais para usar SAP HANA no Azure (instâncias grandes).
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224718"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ligar VMs do Azure para Instâncias Grandes do HANA

O artigo [o que é SAP Hana no Azure (instâncias grandes)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) menciona que a implantação mínima do HANA em instâncias grandes com a camada de aplicativo SAP no Azure é semelhante ao seguinte:

![VNet do Azure conectada a SAP HANA no Azure (instâncias grandes) e locais](./media/hana-overview-architecture/image1-architecture.png)

Olhando mais de perto no lado da rede virtual do Azure, há a necessidade de:

- A definição de uma rede virtual do Azure na qual você vai implantar as VMs da camada de aplicativo do SAP.
- A definição de uma sub-rede padrão na rede virtual do Azure que é realmente aquela na qual as VMs são implantadas.
- A rede virtual do Azure que é criada precisa ter pelo menos uma sub-rede VM e uma sub-rede de gateway de rede virtual do Azure ExpressRoute. Essas sub-redes devem ser atribuídas aos intervalos de endereços IP conforme especificado e abordadas nas seções a seguir.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Criar a rede virtual do Azure para instâncias grandes do HANA

>[!Note]
>A rede virtual do Azure para instâncias grandes do HANA deve ser criada usando o modelo de implantação Azure Resource Manager. O modelo de implantação mais antigo do Azure, normalmente conhecido como modelo de implantação clássico, não é suportado pela solução de instância grande do HANA.

Você pode usar o portal do Azure, o PowerShell, um modelo do Azure ou o CLI do Azure para criar a rede virtual. (Para obter mais informações, consulte [criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). No exemplo a seguir, examinamos uma rede virtual que é criada usando o portal do Azure.

Ao fazer referência ao **espaço de endereço** nesta documentação, para o espaço de endereço que a rede virtual do Azure tem permissão para usar. Esse espaço de endereço também é o intervalo de endereços que a rede virtual usa para a propagação de rota BGP. Esse **espaço de endereço** pode ser visto aqui:

![Espaço de endereço de uma rede virtual do Azure exibida no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No exemplo anterior, com 10.16.0.0/16, a rede virtual do Azure recebeu um intervalo de endereços IP bastante grande e largo para usar. Portanto, todos os intervalos de endereços IP das sub-redes subsequentes nessa rede virtual podem ter seus intervalos dentro desse espaço de endereço. Geralmente não recomendamos um intervalo de endereços grande para uma única rede virtual no Azure. Mas vamos examinar as sub-redes que estão definidas na rede virtual do Azure:

![Sub-redes da rede virtual do Azure e seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Examinamos uma rede virtual com uma primeira sub-rede VM (aqui chamada "default") e uma sub-rede chamada "GatewaySubnet".

Nos dois gráficos anteriores, o **espaço de endereço de rede virtual** abrange **o intervalo de endereços IP de sub-rede da VM do Azure** e a do gateway de rede virtual.

Você pode restringir o **espaço de endereço de rede virtual** para os intervalos específicos usados por cada sub-rede. Você também pode definir o **espaço de endereço de rede virtual** de uma rede virtual como vários intervalos específicos, como mostrado aqui:

![Espaço de endereço de rede virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Nesse caso, o **espaço de endereço de rede virtual** tem dois espaços definidos. Eles são os mesmos que os intervalos de endereços IP que são definidos para o intervalo de endereços IP de sub-rede da VM do Azure e o gateway de rede virtual. 

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário (sub-redes VM). No entanto, **sempre deve haver uma, e apenas uma, sub-rede de gateway para cada rede virtual** que se conecta à SAP Hana no circuito de ExpressRoute do Azure (instâncias grandes). **Essa sub-rede de gateway deve ser nomeada "GatewaySubnet"** para garantir que o gateway de ExpressRoute esteja corretamente colocado.

> [!WARNING] 
> É essencial que a sub-rede de gateway sempre seja denominada "GatewaySubnet".

Você pode usar várias sub-redes VM e intervalos de endereços não contíguos. Esses intervalos de endereços devem ser cobertos pelo **espaço de endereço** da rede virtual da rede virtual. Eles podem estar em um formato agregado. Eles também podem estar em uma lista dos intervalos exatos das sub-redes VM e da sub-rede de gateway.

Veja a seguir um resumo dos fatos importantes sobre uma rede virtual do Azure que se conecta ao HANA em instâncias grandes:

- Você deve enviar o **espaço de endereço de rede virtual** à Microsoft quando estiver executando uma implantação inicial de instâncias grandes do Hana. 
- O **espaço de endereço de rede virtual** pode ser um intervalo maior que cobre os intervalos para o intervalo de endereços IP de sub-rede da VM do Azure e o gateway de rede virtual.
- Ou você pode enviar vários intervalos que abrangem os diferentes intervalos de endereços IP de intervalo (s) de endereços IP de sub-rede de VM e o intervalo de endereços IP do gateway de rede virtual.
- O **espaço de endereço de rede virtual** definido é usado para a propagação de roteamento BGP.
- O nome da sub-rede de gateway deve ser: **"GatewaySubnet"** .
- O espaço de endereço é usado como um filtro no lado do SAP HANA em instâncias grandes para permitir ou impedir o tráfego para as unidades de instância grande do HANA do Azure. As informações de roteamento BGP da rede virtual do Azure e os intervalos de endereços IP configurados para filtragem no lado da instância grande do HANA devem corresponder. Caso contrário, podem ocorrer problemas de conectividade.
- Há alguns detalhes sobre a sub-rede de gateway que são discutidas posteriormente, na seção **conectando uma rede virtual a ExpressRoute de instância grande do Hana.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a serem definidos 

Alguns dos intervalos de endereços IP necessários para a implantação de instâncias grandes do HANA já foram introduzidos. Mas há mais intervalos de endereços IP que também são importantes. Nem todos os intervalos de endereços IP a seguir precisam ser enviados à Microsoft. No entanto, você precisa defini-los antes de enviar uma solicitação de implantação inicial:

- **Espaço de endereço de rede virtual**: o **espaço de endereço de rede virtual** é o intervalo de endereços IP que você atribui ao parâmetro de espaço de endereço nas redes virtuais do Azure. Essas redes se conectam ao ambiente SAP HANA instância grande. É recomendável que esse parâmetro de espaço de endereço seja um valor de várias linhas. Ele deve consistir no intervalo de sub-rede da VM do Azure e nos intervalos de sub-rede do gateway do Azure. Esse intervalo de sub-rede foi mostrado no gráfico anterior. Ele não deve se sobrepor ao seu pool de IPS de servidor ou local ou a intervalos de endereços de ER-P2P. Como obter esses intervalos de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um ou vários intervalos de endereços IP que não são usados dentro de sua rede. Por exemplo, a sub-rede da sua VM do Azure é 10.0.1.0/24 e a sub-rede da sua sub-rede de gateway do Azure é 10.0.2.0/28.  É recomendável que seu espaço de endereço de rede virtual do Azure seja definido como: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores de espaço de endereço possam ser agregados, é recomendável correspondê-los aos intervalos de sub-rede. Dessa forma, você pode evitar acidentalmente reutilizar intervalos de endereços IP não utilizados em espaços de endereço maiores em outro lugar em sua rede. **O espaço de endereço de rede virtual é um intervalo de endereços IP. Ele precisa ser enviado à Microsoft quando você solicitar uma implantação inicial**.
- **Intervalo de endereços IP de sub-rede da VM do Azure:** Esse intervalo de endereços IP é aquele que você atribui ao parâmetro de sub-rede da rede virtual do Azure. Esse parâmetro está em sua rede virtual do Azure e se conecta ao SAP HANA ambiente de instância grande. Esse intervalo de endereços IP é usado para atribuir endereços IP às suas VMs do Azure. Os endereços IP fora desse intervalo têm permissão para se conectar ao seu SAP HANA servidor (es) de instância grande. Se necessário, você pode usar várias sub-redes de VM do Azure. Recomendamos um bloco CIDR/24 para cada sub-rede VM do Azure. Esse intervalo de endereços deve ser uma parte dos valores que são usados no espaço de endereço de rede virtual do Azure. Como obter esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não está sendo usado dentro de sua rede.
- **Intervalo de endereços IP da sub-rede do gateway de rede virtual:** Dependendo dos recursos que você planeja usar, o tamanho recomendado é:
   - Gateway de ExpressRoute de altíssimo desempenho: bloco de endereço/26 – obrigatório para a classe do tipo II de SKUs.
   - Coexistência com VPN e ExpressRoute usando um gateway de rede virtual ExpressRoute de alto desempenho (ou menor): bloco de endereço/27.
   - Todas as outras situações: bloco de endereço/28. Esse intervalo de endereços deve ser uma parte dos valores usados nos valores de "espaço de endereço de VNet". Esse intervalo de endereços deve ser uma parte dos valores que são usados nos valores de espaço de endereço de rede virtual do Azure que você envia para a Microsoft. Como obter esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado no momento dentro de sua rede. 
- **Intervalo de endereços para conectividade de er-P2P:** Esse intervalo é o intervalo de IP para sua conexão P2P de SAP HANA de instância grande (ER). Esse intervalo de endereços IP deve ser um intervalo de endereços IP CIDR/29. Esse intervalo não deve se sobrepor aos seus locais ou outros intervalos de endereços IP do Azure. Esse intervalo de endereços IP é usado para configurar a conectividade ER do gateway virtual do ExpressRoute para o SAP HANA servidores de instância grande. Como obter esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado no momento dentro de sua rede. **Esse intervalo é um intervalo de endereços IP. Ele precisa ser enviado à Microsoft quando você solicitar uma implantação inicial**.  
- **Intervalo de endereços do pool de IPS do servidor:** Esse intervalo de endereços IP é usado para atribuir o endereço IP individual a servidores do SAP HANA em instâncias grandes. O tamanho de sub-rede recomendado é um bloco CIDR/24. Se necessário, ele pode ser menor, com até 64 endereços IP. A partir desse intervalo, os 30 primeiros endereços IP são reservados para uso pela Microsoft. Certifique-se de considerar esse fato quando escolher o tamanho do intervalo. Esse intervalo não deve se sobrepor aos seus locais ou outros endereços IP do Azure. Como obter esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado no momento dentro de sua rede.  **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft ao solicitar uma implantação inicial**.

Intervalos de endereços IP opcionais que eventualmente precisam ser enviados à Microsoft:

- Se você optar por usar o [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para habilitar o roteamento direto do local para as unidades de instância grande do Hana, será necessário reservar outro/29 intervalos de endereços IP. Esse intervalo pode não se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.
- Se você optar por usar o [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para habilitar o roteamento direto de um locatário de instância grande do Hana em uma região do Azure para outro locatário de instância grande do Hana em outra região do Azure, você precisará reservar outro/29 intervalo de endereços IP. Esse intervalo pode não se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.

Para obter mais informações sobre o ExpressRoute Alcance Global e o uso em torno de instâncias grandes do HANA, verifique os documentos:

- [Arquitetura de rede SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Conectar uma rede virtual a instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Você precisa definir e planejar os intervalos de endereços IP que foram descritos anteriormente. No entanto, você não precisa transmitir todos eles para a Microsoft. Os intervalos de endereços IP que você precisa nomear para a Microsoft são:

- Espaço (s) de endereço de rede virtual do Azure
- Intervalo de endereços para conectividade de ER-P2P
- Intervalo de endereços do pool de IPS do servidor

Se você adicionar redes virtuais adicionais que precisam se conectar ao HANA em instâncias grandes, será necessário enviar o novo espaço de endereço de rede virtual do Azure que você está adicionando à Microsoft. 

Veja a seguir um exemplo dos diferentes intervalos e alguns intervalos de exemplo que você precisa configurar e, eventualmente, fornecer à Microsoft. O valor do espaço de endereço de rede virtual do Azure não é agregado no primeiro exemplo. No entanto, ele é definido dos intervalos do primeiro intervalo de endereços IP de sub-rede de VM do Azure e do intervalo de endereços IP de sub-rede de gateway de rede virtual. 

Você pode usar várias sub-redes VM na rede virtual do Azure ao configurar e enviar os intervalos de endereços IP adicionais das sub-redes VM adicionais como parte do espaço de endereço da rede virtual do Azure.

![Intervalos de endereços IP necessários no SAP HANA na implantação mínima do Azure (instâncias grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

O gráfico não mostra os intervalos de endereços IP adicionais necessários para o uso opcional do ExpressRoute Alcance Global.

Você também pode agregar os dados que você envia para a Microsoft. Nesse caso, o espaço de endereço da rede virtual do Azure inclui apenas um espaço. Usando os intervalos de endereços IP do exemplo anterior, o espaço de endereço de rede virtual agregada poderia ser semelhante à imagem a seguir:

![Segunda possibilidade de intervalos de endereços IP necessários no SAP HANA na implantação mínima do Azure (instâncias grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

No exemplo, em vez de dois intervalos menores que definiram o espaço de endereço da rede virtual do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do espaço de endereço deixa alguns intervalos muito grandes não utilizados. Como os valores de espaço de endereço de rede virtual são usados para a propagação de rota BGP, o uso dos intervalos não utilizados no local ou em outro lugar na rede pode causar problemas de roteamento. O gráfico não mostra os intervalos de endereços IP adicionais necessários para o uso opcional do ExpressRoute Alcance Global.

Recomendamos que você mantenha o espaço de endereço totalmente alinhado com o espaço de endereço de sub-rede real que você usa. Se necessário, sem incorrer em tempo de inatividade na rede virtual, você sempre poderá adicionar novos valores de espaço de endereço posteriormente.
 
> [!IMPORTANT] 
> Cada intervalo de endereços IP em ER-P2P, o pool de IPS de servidor e o espaço de endereço de rede virtual do Azure **não** deve se sobrepor um ao outro ou com qualquer outro intervalo que seja usado em sua rede. Cada um deve ser discreto. Como os dois gráficos anteriores mostram, eles também não podem ser uma sub-rede de qualquer outro intervalo. Se sobreposições ocorrerem entre intervalos, a rede virtual do Azure pode não se conectar ao circuito do ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Próximas etapas após os intervalos de endereços serem definidos
Depois que os intervalos de endereços IP tiverem sido definidos, os seguintes itens deverão acontecer:

1. Envie os intervalos de endereços IP para o espaço de endereço de rede virtual do Azure, a conectividade de ER-P2P e o intervalo de endereços do pool de IPS do servidor, junto com outros dados que foram listados no início do documento. Neste ponto, você também pode começar a criar a rede virtual e as sub-redes de VM. 
2. Um circuito do ExpressRoute é criado pela Microsoft entre sua assinatura do Azure e o carimbo de instância grande do HANA.
3. Uma rede de locatário é criada no carimbo de instância grande da Microsoft.
4. A Microsoft configura a rede no SAP HANA na infraestrutura do Azure (instâncias grandes) para aceitar endereços IP de seu espaço de endereço de rede virtual do Azure que se comunica com as instâncias grandes do HANA.
5. Dependendo do SAP HANA específico no SKU do Azure (instâncias grandes) que você comprou, a Microsoft atribui uma unidade de computação em uma rede de locatário. Ele também aloca e monta o armazenamento e instala o sistema operacional (SUSE ou Red Hat Linux). Os endereços IP para essas unidades são retirados do intervalo de endereços do pool de IPS do servidor que você enviou à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para você:
- Informações necessárias para conectar suas redes virtuais do Azure ao circuito do ExpressRoute que conecta redes virtuais do Azure a instâncias grandes do HANA:
     - Chave (s) de autorização
     - Peerid de ExpressRoute
- Dados para acessar instâncias grandes do HANA depois de estabelecer o circuito do ExpressRoute e a rede virtual do Azure.

Você também pode encontrar a sequência de conexão de instâncias grandes do HANA no documento [SAP Hana na configuração do Azure (instâncias grandes)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitas das etapas a seguir são mostradas em um exemplo de implantação nesse documento. 

## <a name="next-steps"></a>Passos seguintes

- Consulte [conectando uma rede virtual a ExpressRoute de instância grande do Hana](hana-connect-vnet-express-route.md).
