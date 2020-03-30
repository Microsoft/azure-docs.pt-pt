---
title: O que é o DNS Privado do Azure?
description: Neste artigo, inicie-se com uma visão geral do serviço privado de hospedagem dNS no Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939294"
---
# <a name="what-is-azure-private-dns"></a>O que é o DNS Privado do Azure?

O Sistema de Nome de Domínio, ou DNS, é responsável pela tradução (ou resolução) de um nome de serviço para o seu endereço IP.  O Azure DNS é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomeutilizando a infraestrutura Microsoft Azure. Além de apoiar os domínios DNS virados para a Internet, o Azure DNS também suporta zonas privadas de DNS.

O Azure Private DNS fornece um serviço DNS fiável e seguro para gerir e resolver nomes de domínio numa rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao utilizar zonas privadas de DNS, pode utilizar os seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure hoje em dia. Usar nomes de domínio personalizados ajuda-o a adaptar a sua arquitetura de rede virtual de acordo com as necessidades da sua organização. Fornece resolução de nomes para máquinas virtuais (VMs) dentro de uma rede virtual e entre redes virtuais. Além disso, pode configurar nomes de zonas com uma vista de horizonte dividido, o que permite que uma zona privada e pública de DNS partilhe o nome.

Para resolver os registos de uma zona Privada de DNS da sua rede virtual, deve ligar a rede virtual à zona. As redes virtuais ligadas têm acesso total e podem resolver todos os registos de DNS publicados na zona privada. Além disso, também pode ativar o registo automático numa ligação de rede virtual. Se ativar o registo automático numa ligação de rede virtual, os registos DNS das máquinas virtuais dessa rede virtual estão registados na zona privada. Quando o registo automático está ativado, o Azure DNS também atualiza os registos de zona sempre que uma máquina virtual é criada, altera o seu endereço IP ou é eliminado.

![Visão geral do DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como uma boa prática, não utilize um domínio *.local* para a sua zona privada de DNS. Nem todos os sistemas operativos suportam isto.

## <a name="benefits"></a>Vantagens

O Azure Private DNS proporciona os seguintes benefícios:

* **Remove a necessidade de soluções DNS personalizadas**. Anteriormente, muitos clientes criaram soluções DNS personalizadas para gerir zonas DNS na sua rede virtual. Agora pode gerir as zonas DNS utilizando a infraestrutura azure nativa, que elimina o fardo de criar e gerir soluções DNS personalizadas.

* **Utilize todos os tipos comuns de registos DNS**. O Azure DNS suporta os registos A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gestão automática de registos de nome de anfitrião.** Juntamente com a hospedagem dos seus registos DNS personalizados, o Azure mantém automaticamente os registos de nome de anfitrião para os VMs nas redes virtuais especificadas. Neste cenário, pode otimizar os nomes de domínio que utiliza sem precisar de criar soluções DNS personalizadas ou modificar aplicações.

* **Resolução de nome de anfitrião entre redes virtuais**. Ao contrário dos nomes de anfitriões fornecidos pelo Azure, as zonas privadas de DNS podem ser partilhadas entre redes virtuais. Esta capacidade simplifica cenários de inter-rede e de descoberta de serviços, como o peering virtual da rede.

* **Ferramentas familiares e experiência do utilizador.** Para reduzir a curva de aprendizagem, este serviço utiliza ferramentas DNS Azure bem estabelecidas (portal Azure, Azure PowerShell, Azure CLI, Modelos De Gestor de Recursos Azure e a API REST).

* **Suporte dNS de horizonte dividido.** Com o Azure DNS, pode criar zonas com o mesmo nome que resolvem diferentes respostas de dentro de uma rede virtual e da internet pública. Um cenário típico para dNS de horizonte dividido é fornecer uma versão dedicada de um serviço para uso dentro da sua rede virtual.

* **Disponível em todas as regiões do Azure.** A funcionalidade de zonas privadas Azure DNS está disponível em todas as regiões de Azure na nuvem pública de Azure.

## <a name="capabilities"></a>Capacidades

O Azure DNS fornece as seguintes capacidades:

* **Registo automático de máquinas virtuais de uma rede virtual ligada a uma zona privada com autoregisto habilitado**. As máquinas virtuais estão registadas (adicionadas) à zona privada como registos a apontar para os seus endereços IP privados. Quando uma máquina virtual numa ligação de rede virtual com autoregisto ativada é eliminada, o Azure DNS também remove automaticamente o registo DNS correspondente da zona privada ligada.

* **A resolução avançada do DNS é suportada através**de redes virtuais ligadas à zona privada . Para resolução de DNS de rede virtual, não há nenhuma dependência explícita tal que as redes virtuais são espreitadas umas com as outras. No entanto, é possível que queira procurar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **A procura inversa de DNS é suportada dentro do âmbito da rede virtual**. A retrospetiva do DNS invertido para um IP privado dentro da rede virtual atribuída a uma zona privada devolve o FQDN que inclui o nome anfitrião/registo e o nome de zona como sufixo.

## <a name="other-considerations"></a>Outras considerações

O Azure DNS tem as seguintes limitações:

* Uma rede virtual específica pode ser ligada apenas a uma zona privada se o registo automático dos registos DeNs VM estiver ativado. No entanto, pode ligar várias redes virtuais a uma única zona DNS.
* O DNS inverso funciona apenas para o espaço IP privado na rede virtual ligada
* Reverter dNS para um endereço IP privado para uma rede virtual ligada devolve *internal.cloudapp.net* como o sufixo padrão para a máquina virtual. Para redes virtuais ligadas a uma zona privada com autoregisto ativado, o DNS invertido para um endereço IP privado devolve duas FQDNs: uma com padrão o sufixo *internal.cloudapp.net* e outra com o sufixo de zona privada.
* O encaminhamento condicional não é atualmente apoiado de forma nativa. Para permitir a resolução entre as redes Azure e no local. Ver [resolução de nomes para VMs e exemplos de papéis](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [o Preço do DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [o Azure PowerShell](./private-dns-getstarted-powershell.md) ou [o Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários de zona privada](./private-dns-scenarios.md) comum que podem ser realizados com zonas privadas em DNS Azure.

* Para perguntas e respostas comuns sobre zonas privadas em DNS Azure, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte o [DNS faQ privado](./dns-faq-private.md).

* Conheça as zonas e registos dNS visitando [zonas de DNS e a visão geral](dns-zones-records.md)dos registos .

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
