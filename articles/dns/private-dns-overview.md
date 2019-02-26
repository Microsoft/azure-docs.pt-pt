---
title: Utilizar o DNS do Azure para domínios privados
description: Uma visão geral do DNS privado que aloja o serviço no Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: f1e92c8581f8528b3622ad88f086d3f66619b996
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823485"
---
# <a name="use-azure-dns-for-private-domains"></a>Utilizar o DNS do Azure para domínios privados

O sistema de nomes de domínio ou DNS, é responsável pela tradução (ou resolver) um nome de serviço para o endereço IP. Um serviço de alojamento para domínios DNS, o DNS do Azure oferece resolução de nomes através da infraestrutura do Microsoft Azure. Além de oferecer suporte a domínios DNS de acesso à internet, DNS do Azure também suporta agora domínios DNS privados como uma funcionalidade de pré-visualização.

O DNS do Azure fornece um serviço DNS fiável e seguro para gerir e resolver os nomes de domínio numa rede virtual sem ser necessária adicionar uma solução DNS personalizada. Ao utilizar zonas privadas do DNS, pode utilizar seus próprios nomes de domínio personalizado em vez dos nomes fornecida pelo Azure atualmente disponíveis. Utilizar nomes de domínio personalizado ajuda-o a criar a sua arquitetura de rede virtual para se adequar melhor às necessidades da sua organização. Ele fornece resolução de nomes para máquinas virtuais (VMs) numa rede virtual assim como entre redes virtuais. Além disso, pode configurar nomes de zonas com uma vista dividida horizontalmente, que permite que uma zona DNS pública e privada para o nome da partilha.

Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona. São denominadas *redes virtuais de resolução*. Também pode especificar redes virtuais para as quais o DNS do Azure mantém registos de nomes de anfitrião sempre que uma VM for criada, mudar de IP ou for eliminada. Chamamos a isto uma *rede virtual de registo*.

Se especificar uma rede virtual de registo, os registos DNS para as VMs da rede virtual que estão registados para a zona privada não são visíveis ou recuperáveis a partir do Azure Powershell e APIs de CLI do Azure, mas os registos VM, de fato, estão registados e será Resolva com êxito.

![Descrição geral do DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Benefícios

O DNS do Azure fornece as seguintes vantagens:

* **Remove a necessidade de soluções DNS personalizadas**. Anteriormente, muitos clientes criar soluções personalizadas de DNS para gerir zonas DNS na sua rede virtual. Agora pode efetuar gestão de zonas DNS ao utilizar a infraestrutura do Azure nativa, o que remove a sobrecarga de criar e gerir soluções DNS personalizadas.

* **Utilizar todos os tipos de registos DNS comuns**. O DNS do Azure suporta registos A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gerenciamento de registros de nome de anfitrião automática**. Juntamente com os registos DNS personalizados de alojamento, o Azure mantém automaticamente registos de nome de anfitrião para as VMs nas redes virtuais especificadas. Neste cenário, pode otimizar os nomes de domínio que sem precisar de utilizar para criar soluções personalizadas de DNS ou modificação das aplicações.

* **Resolução de nome de anfitrião entre redes virtuais**. Ao contrário dos nomes de host fornecido com o Azure, as zonas DNS privadas podem ser partilhadas entre redes virtuais. Esta capacidade simplifica a deteção de serviços e de rede em vários cenários, como o peering de rede virtual.

* **Ferramentas familiares e a experiência do usuário**. Para reduzir a curva de aprendizado, esta nova oferta utiliza bem estabelecidas ferramentas do DNS do Azure (PowerShell, modelos do Azure Resource Manager e a API REST).

* **Suporte a DNS dividida horizontalmente**. Com o DNS do Azure, pode criar zonas com o mesmo nome que resolva para respostas diferentes de dentro de uma rede virtual e a internet pública. Um cenário típico para o DNS dividida horizontalmente é fornecer uma versão dedicada de um serviço para utilização no interior da rede virtual.

* **Disponível em todas as regiões do Azure**. A funcionalidade de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na cloud pública do Azure.

## <a name="capabilities"></a>Capacidades

O DNS do Azure fornece as seguintes capacidades:

* **Registo automático de máquinas virtuais a partir de uma única rede virtual que está ligada a uma zona privada como uma rede virtual de registo**. As máquinas virtuais são registados (adicionado) da zona privada como registros que aponta para os IPs privados. Quando uma máquina virtual no registo de uma rede virtual é eliminada, o Azure também automaticamente remove o DNS correspondente registos da zona privada ligada. 

  Por predefinição, o registo de redes virtuais também agir como redes virtuais de resolução, no sentido de que a resolução de DNS em relação a zona funciona em qualquer uma das máquinas virtuais dentro da rede virtual de registo.

  > [!NOTE]
  > Se especificar uma rede virtual de registo, os registos DNS para as VMs da rede virtual que estão registados para a zona privada não são visíveis ou recuperáveis a partir do Azure Powershell e APIs de CLI do Azure. Os registos VM, de fato, estão registados e serão resolvido com êxito.

* **Resolução direta de DNS é suportada por várias redes virtuais que estejam ligadas à zona privada como redes virtuais de resolução**. Para a rede virtual entre a resolução de DNS, não existe nenhuma dependência explícita, de modo a que as redes virtuais em modo de peering entre si. No entanto, os clientes podem querer configurar o peering entre redes virtuais para outros cenários (por exemplo, o tráfego HTTP).

* **Pesquisa reversa de DNS é suportada pelo âmbito da rede virtual**. Pesquisa reversa de DNS para um IP privado na rede virtual atribuído a uma zona privada devolve o FQDN que inclui o nome do anfitrião/registo e o nome da zona como o sufixo.

## <a name="limitations"></a>Limitações

O DNS do Azure tem as seguintes limitações:

* Rede virtual de apenas um registo é permitido por zona privada.
* Até 10 resolução redes virtuais são permitidas por zona privada. Este limite será removido quando esta funcionalidade está disponível em geral.
* Uma rede virtual específica pode ser ligada a apenas uma zona privada como uma rede virtual de registo.
* Uma rede virtual específica pode ser ligada para zonas privadas do até 10 como uma rede virtual de resolução. Este limite será removido quando esta funcionalidade está disponível em geral.
* Se especificar uma rede virtual de registo, os registos DNS para as VMs da rede virtual que estão registados para a zona privada não são visíveis ou recuperáveis a partir do Azure Powershell e APIs de CLI do Azure. Os registos VM, de fato, estão registados e serão resolvido com êxito.
* Inversa de DNS de funciona apenas para o espaço IP privados na rede virtual de registo.
* DNS inverso para um IP privado que não está registado na zona privada (por exemplo, um IP privado para uma máquina virtual numa rede virtual que está ligada como uma rede virtual de resolução a privada zona) devolve *internal.cloudapp.net* como o sufixo DNS. No entanto, este sufixo não é possível resolver.
* A rede virtual tem de ser totalmente vazia na primeira vez que o liga a uma zona privada como uma rede virtual de registo ou resolução. No entanto, a rede virtual, em seguida, pode estar vazio para a ligação futuras como um registo ou resolução de rede virtual para outras zonas privadas.
* Atualmente, o reencaminhamento condicional não é suportado (por exemplo, para ativar a resolução entre redes do Azure e local). Para obter informações sobre como os clientes podem obter este cenário através de outros mecanismos, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Para perguntas e respostas sobre zonas privadas no DNS do Azure comuns, incluindo o comportamento específico de resolução e registo DNS pode esperar para determinados tipos de operações, consulte [FAQ](./dns-faq.md#private-dns).  

## <a name="pricing"></a>Preços

A funcionalidade de zonas DNS privada de é gratuita durante a pré-visualização pública. Durante a disponibilidade geral, o recurso oferece um baseada na utilização modelo de preços semelhante ao que o DNS do Azure existentes da oferta. 

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar uma zona privada no DNS do Azure, utilizando [do Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

Leia sobre alguns comum [cenários de zona privada](./private-dns-scenarios.md) que podem ser conseguidos com as zonas privadas no DNS do Azure.

Para perguntas e respostas sobre zonas privadas no DNS do Azure comuns, incluindo o comportamento específico pode esperar para determinados tipos de operações, consulte [FAQ](./dns-faq.md#private-dns). 

Saiba mais sobre zonas e registos DNS ao visitar [zonas e registos de descrição geral do DNS](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
