---
title: Configuração de conectividade de máquinas virtuais para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Configuração de conectividade de máquinas virtuais para utilizar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2628cafada47b2602b195c44d4b6f2e6b16012ef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092774"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ligar VMs do Azure para instâncias grandes do HANA

O artigo [o que é o SAP HANA no Azure (instâncias grandes)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) menciona que a implementação mínima de instâncias grandes do HANA com a camada de aplicação SAP no Azure é semelhante ao seguinte:

![VNet do Azure ligado ao SAP HANA no Azure (instâncias grandes) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Análise mais cuidadosa do lado da rede virtual do Azure, percebemos que a necessidade de:

- A definição de uma rede virtual do Azure na qual pretende implantar as VMs da camada de aplicação SAP.
- A definição de uma sub-rede de predefinição na rede virtual do Azure que é uma na qual as VMs são implementadas.
- A rede virtual do Azure que é criada tem de ter pelo menos uma sub-rede VM e uma sub-rede de gateway de rede virtual do Azure ExpressRoute. Estas sub-redes devem ser atribuídas os intervalos de endereços IP como discutido e especificados nas seções a seguir.

Vamos ver um pouco mais de perto a criação de rede virtual do Azure para instâncias grandes do HANA.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Criar a rede virtual do Azure para instâncias grandes do HANA

>[!Note]
>A rede virtual do Azure para instâncias grandes do HANA tem de ser criada utilizando o modelo de implementação Azure Resource Manager. O modelo de implementação do Azure mais antigo, comumente conhecido como modelo de implementação clássica, não é suportado pela solução de instância grande do HANA.

Pode utilizar o portal do Azure, PowerShell, um modelo do Azure ou a CLI do Azure para criar a rede virtual. (Para obter mais informações, consulte [criar uma rede virtual com o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). No exemplo a seguir, vamos ver uma rede virtual que é criada utilizando o portal do Azure.

Vamos ver como as definições das redes virtuais se relaciona com o que podemos listar como intervalos de endereços IP diferentes. Quando estamos falando sobre o **espaço de endereços**, queremos dizer que o espaço de endereços da rede virtual do Azure está autorizada a utilizar. Este espaço de endereço também é o intervalo de endereços da rede virtual utiliza para a propagação de rotas do BGP. Isso **espaço de endereços** pode ser visto aqui:

![Espaço de endereços de uma rede virtual do Azure, apresentado no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No exemplo anterior, com 10.16.0.0/16, a rede virtual do Azure foi indicada um intervalo de endereços IP em vez disso, grande e amplo para utilizar. Por conseguinte, todos os intervalos de endereços IP da sub-redes subsequentes dentro desta rede virtual podem ter seus intervalos dentro desse espaço de endereços. Normalmente, não é recomendada a tal um intervalo de endereços grandes para uma única rede virtual no Azure. Mas vamos dar uma olhada em sub-redes que estão definidas na rede virtual do Azure:

![Intervalos de endereços do seu IP e sub-redes da rede virtual do Azure](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Vamos examinar uma rede virtual com uma sub-rede VM primeiro (denominada "predefinido") e uma sub-rede denominada "GatewaySubnet".

Nos dois gráficos anteriores, o **espaço de endereços de rede virtual** abrange ambos os **o intervalo de endereços IP de sub-rede da VM do Azure** e que o gateway de rede virtual.

Pode restringir os **espaço de endereços de rede virtual** para os intervalos específicos utilizados por cada sub-rede. Também pode definir o **espaço de endereços de rede virtual** de virtual de rede como vários intervalos específicos, como mostrado aqui:

![Espaço de endereços de rede virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Neste caso, o **espaço de endereços de rede virtual** tem dois espaços definidos. Eles são os mesmos que os intervalos de endereços IP que estão definidos para o intervalo de endereços IP de sub-rede de VM do Azure e o gateway de rede virtual. 

Pode usar qualquer padrão de nomenclatura que quiser para estas sub-redes de inquilino (sub-redes VM). No entanto, **deve sempre haver um e apenas uma sub-rede do gateway para cada rede virtual** que liga-se para o SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes). E **esta sub-rede de gateway tem de ter o nome "GatewaySubnet"** para se certificar de que o gateway do ExpressRoute corretamente é colocado.

> [!WARNING] 
> É essencial que a sub-rede do gateway sempre ter o nome "GatewaySubnet".

Pode usar várias sub-redes VM e intervalos de endereços não contínuos. Estes intervalos de endereço devem ser cobertos pela **espaço de endereços de rede virtual** da rede virtual. Podem estar em forma agregada. Também podem estar numa lista dos intervalos exatos de sub-redes VM e a sub-rede do gateway.

Segue-se um resumo dos fatos importantes sobre uma rede virtual do Azure que se liga a instâncias grandes do HANA:

- Tem de submeter o **espaço de endereços de rede virtual** à Microsoft quando está fazendo uma implementação inicial de instâncias grandes do HANA. 
- O **espaço de endereços de rede virtual** pode ser um intervalo maior que abrange os intervalos para ambas as sub-rede intervalo de endereços IP da VM do Azure e o gateway de rede virtual.
- Ou pode enviar a intervalos de intervalos de endereços IP de sub-rede VM e o intervalo de endereços IP de gateway de rede virtual de endereços de vários intervalos que abrangem o IP diferente.
- O definido **espaço de endereços de rede virtual** é utilizado para a propagação de encaminhamento de BGP.
- O nome da sub-rede do gateway tem de ser: **"GatewaySubnet"**.
- O espaço de endereço é utilizado como um filtro no lado do instância grande do HANA para permitir ou não o tráfego para as unidades de instância grande do HANA a partir do Azure. As informações de encaminhamento de BGP de rede virtual do Azure e os intervalos de endereços IP que estão configurados para filtragem do lado de instância grande do HANA devem corresponder. Caso contrário, podem ocorrer problemas de conectividade.
- Existem alguns detalhes sobre a sub-rede do gateway que são abordadas mais tarde, na secção **ligar uma rede virtual ao ExpressRoute de instância grande do HANA.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Intervalos de endereços IP diferentes a definir 

Introduzimos já alguns dos intervalos de endereços IP que são necessários para implementar instâncias grandes do HANA. Mas há mais intervalos de endereços IP que também são importantes. Vamos percorrer algumas mais detalhes. Os seguintes endereços IP não precisam ser submetidas à Microsoft. No entanto, tem de defini-los antes de enviar um pedido para a implementação inicial:

- **Espaço de endereços de rede virtual**: O **espaço de endereços de rede virtual** é os intervalos de endereços IP que é atribuída ao seu parâmetro de espaço de endereço nas redes virtuais do Azure. Ligar estas redes para o ambiente de instância grande do SAP HANA.

  Recomendamos que este parâmetro de espaço de endereço é um valor de com várias linha. Ele deve consistir o intervalo de sub-rede da VM do Azure e os intervalos de sub-rede do gateway do Azure. Este intervalo de sub-rede foi mostra os elementos de gráficos anteriores. Ele não pode sobrepor com a sua no local ou o conjunto IP do servidor ou a intervalos de endereços de ER P2P. 
 
Como obtém esses intervalos de endereços IP? 

O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um ou vários endereços IP, intervalos que não são utilizadas no interior da rede. Por exemplo, vamos supor que a sub-rede da sua VM do Azure é 10.0.1.0/24, e a sub-rede da sua sub-rede de gateway do Azure é 10.0.2.0/28.  Recomendamos que o seu espaço de endereços de rede virtual do Azure é as duas linhas seguintes: 10.0.1.0/24 e 10.0.2.0/28. 

Embora os valores de espaço de endereço podem ser agregados, recomendamos correspondentes-los para os intervalos de sub-rede. Desta forma pode acidentalmente evitar reutilizar os intervalos de endereços IP não utilizados dentro de espaços de endereços maiores em outro lugar na sua rede. **O espaço de endereços de rede virtual é um intervalo de endereços IP. Ele precisa ser submetidas à Microsoft quando procura uma implementação inicial**.

- **Azure VM sub-rede intervalo de endereços IP:** Este intervalo de endereços IP é a que atribuir para o parâmetro de sub-rede de rede virtual do Azure. Este parâmetro é na sua rede virtual do Azure e liga-se para o ambiente de instância grande do SAP HANA. Este intervalo de endereços IP é utilizado para atribuir endereços IP às VMs do Azure. Os endereços IP fora deste intervalo têm permissão para ligar ao seu servidor de instância grande do SAP HANA (es). Se for necessário, pode utilizar várias sub-redes VM do Azure. Recomendamos um /24 bloco CIDR para cada sub-rede VM do Azure. Este intervalo de endereços tem de ser uma parte dos valores que são utilizados no espaço de endereços de rede virtual do Azure. 

Como obtém este intervalo de endereços IP? 

O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP que não está a ser utilizado no interior da rede.

- **Intervalo de endereços do rede virtual gateway sub-rede IP:** Dependendo dos recursos que planeia utilizar, o tamanho recomendado é:
   - Gateway ExpressRoute de ultra desempenho: / 26 bloco de endereços – necessários para a classe de tipo II dos SKUs.
   - Coexistência com o ExpressRoute através de um gateway de rede virtual do ExpressRoute de elevado desempenho e de VPN (ou menor): / 27 bloco de endereços.
   - Todas as outras situações: / 28 bloco de endereços. Este intervalo de endereços tem de ser uma parte dos valores utilizados os valores de "Espaço de endereços da VNet". Este intervalo de endereços tem de ser uma parte dos valores que são utilizados os valores de espaço de endereço de rede virtual do Azure que submeter para a Microsoft. Como obtém este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP que atualmente não está a ser utilizado no interior da rede. 

- **Intervalo de endereços para conectividade de ER P2P:** Esse intervalo é o intervalo IP para a sua ligação de SAP HANA grandes instância ExpressRoute (ER) P2P. Este intervalo de endereços IP tem de ser/29 intervalo de endereços IP de CIDR. Este intervalo não pode sobrepor com no local ou outro IP Azure intervalos de endereços. Este intervalo de endereços IP é utilizado para configurar a conectividade de ER do seu gateway de virtual do ExpressRoute para os servidores de instância grande do SAP HANA. Como obtém este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP que atualmente não está a ser utilizado no interior da rede. **Esse intervalo é um intervalo de endereços IP. Ele precisa ser submetidas à Microsoft quando procura uma implementação inicial**.
  
- **Intervalo de endereços do conjunto IP do servidor:** Este intervalo de endereços IP é utilizado para atribuir o endereço IP individual para servidores de instâncias grandes do HANA. O tamanho da sub-rede recomendada é um /24 bloco CIDR. Se for necessário, pode ser menor, com um mínimo de 64 endereços IP. Deste intervalo, os 30 primeiros endereços IP estão reservados para utilização pela Microsoft. Certifique-se de que tem em conta este facto ao escolher o tamanho do intervalo. Este intervalo não pode sobrepor com no local ou outro IP Azure endereços. Como obtém este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP que atualmente não está a ser utilizado no interior da rede. 

  **Esse intervalo é um intervalo de endereços IP, que tem de ser submetidas à Microsoft quando pedir para uma implementação inicial**.
 
Tem de definir e planear os intervalos de endereços IP que foram descritos anteriormente. No entanto, não precisa de transmitir todos eles para a Microsoft. Os intervalos de endereços IP que é necessário o nome para a Microsoft são:

- Espaços de endereços de rede virtual do Azure
- Intervalo de endereços para conectividade de ER P2P
- Intervalo de endereços do conjunto IP do servidor

Se adicionar as redes virtuais adicionais que precisam de ligar a instâncias grandes do HANA, tem de submeter o novo espaço de endereços da rede virtual do Azure que estiver a adicionar à Microsoft. 

Segue-se um exemplo de alguns intervalos de exemplo e os intervalos diferentes conforme necessário configurar e, eventualmente, fornecer à Microsoft. O valor para o espaço de endereços de rede virtual do Azure não é agregado no primeiro exemplo. No entanto, ela é definida de intervalos de intervalo de endereços IP de sub-rede a primeira VM do Azure e o intervalo de endereços IP de sub-rede de gateway de rede virtual. 

Pode usar várias sub-redes VM na rede virtual do Azure ao configurar e enviar os intervalos de endereços IP adicionais de sub-redes VM adicionais como parte do espaço de endereços de rede virtual do Azure.

![Intervalos de endereços IP necessários no SAP HANA na implementação básica do Azure (instâncias grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Também pode agregar os dados que enviar à Microsoft. Nesse caso, o espaço de endereços da rede virtual do Azure inclui apenas um espaço. Com os intervalos de endereços IP do exemplo anterior, o espaço de endereços de rede virtual agregados pode ter um aspeto semelhante à imagem seguinte:

![Segundo possibilidade de intervalos de endereços IP necessários no SAP HANA na implementação básica do Azure (instâncias grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

No exemplo, em vez de dois intervalos menores que definiu o espaço de endereços da rede virtual do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do espaço de endereços deixa alguns bastante grandes intervalos não utilizados. Uma vez que os valores de espaço de endereço de rede virtual são utilizados para a propagação de rotas do BGP, a utilização de intervalos não utilizados no local ou em outro lugar na sua rede pode causar problemas de encaminhamento. 

Assim, recomendamos que mantenha o espaço de endereços estreitamente alinhado com o espaço de endereços de sub-rede real que utilizar. Se for necessário, sem incorrer em períodos de indisponibilidade na rede virtual, pode sempre adicionar novos valores de espaço de endereço posteriormente.
 
> [!IMPORTANT] 
> Cada intervalo de endereços IP em ER P2P, o conjunto IP do servidor e o espaço de endereços de rede virtual do Azure tem **não** se sobrepõem entre si, ou com qualquer outro intervalo que é utilizado na sua rede. Cada um tem de ser discreto. Como mostram os dois gráficos anteriores, eles também não podem ser uma sub-rede de qualquer outro intervalo. Se ocorrerem sobreposições entre intervalos de, a rede virtual do Azure não poderá ligar ao circuito do ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Passos seguintes depois de intervalos de endereços foram definidos
Depois dos intervalos de endereços IP foram definidos, têm de ocorrer os seguintes procedimentos:

1. Submeta os intervalos de endereços IP para o espaço de endereços de rede virtual do Azure, a conectividade de ER P2P e servidor conjunto intervalo de endereços IP, juntamente com outros dados que tenham sido listados no início do documento. Neste ponto, também pode começar a criar a rede virtual e as sub-redes VM. 
2. Um circuito do ExpressRoute é criado pela Microsoft, entre a sua subscrição do Azure e o carimbo de data / instância grande do HANA.
3. É criada uma rede de inquilino no carimbo a instância grande pela Microsoft.
4. Microsoft configura o funcionamento em rede no SAP HANA na infraestrutura do Azure (instâncias grandes) para aceitar endereços IP do seu espaço de endereços de rede virtual do Azure que se comunique com instâncias grandes do HANA.
5. Consoante o específico SAP HANA no Azure SKU (instâncias grandes) que comprou, a Microsoft atribui-lhe uma unidade de computação numa rede de inquilino. Ele também aloca e monta o armazenamento e instala o sistema operativo (SUSE ou Red Hat Linux). Endereços IP para estas unidades ficam fora do intervalo de endereços do conjunto de IP do servidor enviada à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para si:
- Informações necessárias para ligar a sua rede ou redes virtuais do Azure para o circuito do ExpressRoute que se liga a redes virtuais do Azure para instâncias grandes do HANA:
     - Chaves de autorização
     - PeerID do ExpressRoute
- Dados para aceder ao HANA nas instâncias grandes, depois de estabelecer o circuito do ExpressRoute e de rede virtual do Azure.

Também pode encontrar a seqüência de conexão instâncias grandes do HANA no documento [SAP HANA no Azure (instâncias grandes) configuração](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitos dos passos seguintes são exibidos num exemplo de implementação contidas nesse documento. 

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [ligar uma rede virtual ao ExpressRoute de instância grande do HANA](hana-connect-vnet-express-route.md).
