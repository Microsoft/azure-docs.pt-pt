---
title: O que é o DNS Privado do Azure?
description: Neste artigo, começa com uma visão geral do serviço de hospedagem privado de DNS no Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311486"
---
# <a name="what-is-azure-private-dns"></a>O que é o DNS Privado do Azure?

O Sistema de Nome de Domínio, ou DNS, é responsável por traduzir (ou resolver) um nome de serviço para um endereço IP.  O Azure DNS é um serviço de hospedagem para domínios e fornece resolução de nomeação utilizando a infraestrutura Microsoft Azure. O Azure DNS não só suporta domínios DNS virados para a Internet, como também suporta zonas privadas de DNS.

O Azure Private DNS fornece um serviço DNS fiável e seguro para a sua rede virtual. O Azure Private DNS gere e resolve nomes de domínio na rede virtual sem a necessidade de configurar uma solução de DNS personalizada. Ao utilizar zonas de DNS privadas, pode utilizar o seu próprio nome de domínio personalizado em vez dos nomes fornecidos pelo Azure durante a implementação. Usar um nome de domínio personalizado ajuda-o a adaptar a sua arquitetura de rede virtual de forma melhor adequada às necessidades da sua organização. Fornece uma resolução de nomeação para máquinas virtuais (VMs) dentro de uma rede virtual e redes virtuais conectadas. Além disso, você pode configurar nomes de zonas com uma vista de horizonte dividido, que permite que uma zona privada e pública de DNS partilhe o nome.

Para resolver os registos de uma zona privada de DNS da sua rede virtual, deve ligar a rede virtual à zona. As redes virtuais ligadas têm acesso total e podem resolver todos os registos DNS publicados na zona privada. Também pode ativar o autoregistração numa ligação de rede virtual. Quando ativa o auto-registo numa ligação de rede virtual, os registos DNS para as máquinas virtuais dessa rede virtual estão registados na zona privada. Quando o registo automático for ativado, o Azure DNS atualizará o registo da zona sempre que uma máquina virtual for criada, altera o endereço IP ou é eliminado.

![Visão geral do DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como boas práticas, não utilize um domínio *.local* para a sua zona privada de DNS. Nem todos os sistemas operativos suportam isto.

## <a name="benefits"></a>Benefícios

O Azure Private DNS proporciona os seguintes benefícios:

* **Elimina a necessidade de soluções DNS personalizadas.** Anteriormente, muitos clientes criaram soluções DNS personalizadas para gerir as zonas de DNS na sua rede virtual. Agora é possível gerir zonas DNS utilizando a infraestrutura nativa Azure, que elimina o fardo de criar e gerir soluções DE DNS personalizadas.

* **Utilize todos os tipos comuns de registos DNS**. O Azure DNS suporta os registos A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gestão automática de registos de nome de hospedeiro**. Juntamente com a hospedagem dos seus registos DNS personalizados, o Azure mantém automaticamente os registos de nome de anfitrião para os VMs nas redes virtuais especificadas. Neste cenário, pode otimizar os nomes de domínio que utiliza sem precisar de criar soluções DNS personalizadas ou modificar aplicações.

* **Resolução de nome hospedeiro entre redes virtuais.** Ao contrário dos nomes de anfitriões fornecidos pelo Azure, as zonas privadas de DNS podem ser partilhadas entre redes virtuais. Esta capacidade simplifica cenários de redes cruzadas e de descoberta de serviços, como o persimento de rede virtual.

* **Ferramentas familiares e experiência do utilizador.** Para reduzir a curva de aprendizagem, este serviço utiliza ferramentas Azure DNS bem estabelecidas (portal Azure, Azure PowerShell, Azure CLI, Azure Resource Manager e a API REST).

* **Suporte DNS de horizonte dividido**. Com o Azure DNS, pode criar zonas com o mesmo nome que resolvem diferentes respostas dentro de uma rede virtual e a partir da internet pública. Um cenário típico para DNS de horizonte dividido é fornecer uma versão dedicada de um serviço para uso dentro da sua rede virtual.

* **Disponível em todas as regiões de Azure.** A funcionalidade Azure DNS zonas privadas está disponível em todas as regiões Azure na nuvem pública de Azure.

## <a name="capabilities"></a>Capacidades

O Azure DNS fornece as seguintes capacidades:

* **Registo automático de máquinas virtuais a partir de uma rede virtual que está ligada a uma zona privada com auto-registo ativado**. As máquinas virtuais são registadas na zona privada como registos A que apontam para os seus endereços IP privados. Quando uma máquina virtual numa ligação de rede virtual com auto-registo ativada é eliminada, o Azure DNS também remove automaticamente o registo DNS correspondente da zona privada ligada.

* **A resolução de DNS a prazo é suportada através de redes virtuais que estão ligadas à zona privada.** Para a resolução de DNS de rede transversal, não há dependência explícita de tal forma que as redes virtuais sejam espreitadas umas com as outras. No entanto, é possível que queira espreitar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **A pesquisa reversa do DNS é suportada no âmbito da rede virtual**. A pesquisa reversa de DNS para um IP privado associado a uma zona privada irá devolver um FQDN que inclui o nome de anfitrião/registo e o nome da zona como sufixo.

## <a name="other-considerations"></a>Outras considerações

O Azure DNS tem as seguintes limitações:

* Uma rede virtual específica só pode ser ligada a uma zona privada se o registo automático dos registos de DNS VM estiver ativado. No entanto, pode ligar várias redes virtuais a uma única zona DE DNS.
* DNS invertido funciona apenas para espaço IP privado na rede virtual ligada
* O DNS invertido para um endereço IP privado na rede virtual ligada será retornador `internal.cloudapp.net` como sufixo padrão para a máquina virtual. Para redes virtuais que estejam ligadas a uma zona privada com auto-registo ativada, o DNS invertido para um endereço IP privado devolve dois FQDNs: um com predefinido sufixo `internal.cloudapp.net` e outro com o sufixo de zona privada.
* O reencaminhamento condicional não é suportado de forma nativa. Para permitir a resolução entre as redes Azure e as redes no local, consulte [a resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [o Preço Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [a Azure PowerShell](./private-dns-getstarted-powershell.md) ou [Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas em Azure DNS.

* Para perguntas e respostas comuns sobre zonas privadas em Azure DNS, consulte [o DNS PRIVADO FAQ](./dns-faq-private.md).

* Saiba mais sobre as zonas e registos dns, visitando [zonas de DNS e registos.](dns-zones-records.md)

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
