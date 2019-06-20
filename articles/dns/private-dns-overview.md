---
title: O que é o DNS Privado do Azure?
description: Uma visão geral do DNS privado que aloja o serviço no Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 7012bbe98e41a3eb273b26e7e4ade705a6eaf8e1
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147570"
---
# <a name="what-is-azure-private-dns"></a>O que é o DNS Privado do Azure?

> [!IMPORTANT]
> O DNS privado do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O sistema de nomes de domínio ou DNS, é responsável pela tradução (ou resolver) um nome de serviço para o endereço IP.  O DNS do Azure é um serviço de alojamento dos domínios DNS que fornece resolução de nomes usando a infra-estrutura do Microsoft Azure. Além de oferecer suporte a domínios DNS de acesso à internet, o DNS do Azure também suporta as zonas privadas do DNS.

O DNS privado do Azure fornece um serviço DNS fiável e seguro para gerir e resolver os nomes de domínio numa rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao utilizar zonas privadas do DNS, pode utilizar seus próprios nomes de domínio personalizado em vez dos nomes fornecida pelo Azure atualmente disponíveis. Utilizar nomes de domínio personalizado ajuda-o a criar a sua arquitetura de rede virtual para se adequar melhor às necessidades da sua organização. Ele fornece resolução de nomes para máquinas virtuais (VMs) numa rede virtual assim como entre redes virtuais. Além disso, pode configurar nomes de zonas com uma vista dividida horizontalmente, que permite que uma zona DNS pública e privada para o nome da partilha.

Para resolver os registos de uma zona DNS privada da sua rede virtual, tem de associar a rede virtual com a zona. Redes virtuais ligadas a ter acesso total e podem resolver todos os registos DNS publicados na zona privada. Além disso, também pode ativar registo automático de uma ligação de rede virtual. Se ativar o registo automático de uma ligação de rede virtual, os registos DNS para as máquinas virtuais na rede virtual são registrados na zona privada. Quando o registo automático está ativado, o DNS do Azure também atualiza os registos de zona sempre que uma máquina virtual for criado, as alterações seu "endereço IP, ou é eliminada.

![Descrição geral do DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como melhor prática, não utilize um *. local* domínio para a sua zona DNS privada. Nem todos os sistemas de operativos dar suporte a isso.

## <a name="benefits"></a>Benefícios

O DNS privado do Azure fornece as seguintes vantagens:

* **Remove a necessidade de soluções DNS personalizadas**. Anteriormente, muitos clientes criar soluções personalizadas de DNS para gerir zonas DNS na sua rede virtual. Agora pode gerir zonas DNS com a infraestrutura do Azure nativa, o que remove a sobrecarga de criar e gerir soluções DNS personalizadas.

* **Utilizar todos os tipos de registos DNS comuns**. O DNS do Azure suporta registos A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gerenciamento de registros de nome de anfitrião automática**. Juntamente com os registos DNS personalizados de alojamento, o Azure mantém automaticamente registos de nome de anfitrião para as VMs nas redes virtuais especificadas. Neste cenário, pode otimizar os nomes de domínio que sem precisar de utilizar para criar soluções personalizadas de DNS ou modificação das aplicações.

* **Resolução de nome de anfitrião entre redes virtuais**. Ao contrário dos nomes de host fornecido com o Azure, as zonas DNS privadas podem ser partilhadas entre redes virtuais. Esta capacidade simplifica a deteção de serviços e de rede em vários cenários, como o peering de rede virtual.

* **Ferramentas familiares e a experiência do usuário**. Para reduzir a curva de aprendizado, esse serviço usa bem estabelecidas ferramentas do DNS do Azure (portal do Azure, Azure PowerShell, CLI do Azure, modelos Azure Resource Manager e a API REST).

* **Suporte a DNS dividida horizontalmente**. Com o DNS do Azure, pode criar zonas com o mesmo nome que resolva para respostas diferentes de dentro de uma rede virtual e a internet pública. Um cenário típico para o DNS dividida horizontalmente é fornecer uma versão dedicada de um serviço para utilização no interior da rede virtual.

* **Disponível em todas as regiões do Azure**. A funcionalidade de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na cloud pública do Azure.

## <a name="capabilities"></a>Capacidades

O DNS do Azure fornece as seguintes capacidades:

* **Registo automático de máquinas virtuais de uma rede virtual que está ligada a uma zona privada com o registo automático ativado**. As máquinas virtuais são registados (adicionado) da zona privada como registros que aponta para os respetivos endereços IP privados. Quando uma máquina virtual numa ligação de rede virtual com o registo automático ativado é eliminada, o DNS do Azure também automaticamente remove o registo DNS correspondente da zona privada ligada.

* **Resolução direta de DNS é suportada por várias redes virtuais que estejam ligadas à zona privada**. Para a rede virtual entre a resolução de DNS, não existe nenhuma dependência explícita, de modo a que as redes virtuais em modo de peering entre si. No entanto, pode querer configurar o peering entre redes virtuais para outros cenários (por exemplo, o tráfego HTTP).

* **Pesquisa reversa de DNS é suportada pelo âmbito da rede virtual**. Pesquisa reversa de DNS para um IP privado na rede virtual atribuído a uma zona privada devolve o FQDN que inclui o nome do anfitrião/registo e o nome da zona como o sufixo.

## <a name="other-considerations"></a>Outras considerações

O DNS do Azure tem as seguintes limitações:

* Uma rede virtual específica pode ser ligada a apenas uma zona privada, como se o registo automático de registos de DNS de VM está ativado. No entanto pode ligar várias redes virtuais a uma única zona DNS.
* Inversa DNS funciona apenas para o espaço IP privados na rede virtual ligado
* DNS inverso para um IP privado para uma rede virtual ligado devolve "internal.cloudapp.net" como o sufixo predefinido para a máquina virtual. Para as redes virtuais que estejam ligadas a uma zona privada com o registo automático ativado, DNS inverso para um IP privado devolve 2 FQDNs, uma com o padrão o sufixo *internal.cloudapp.net* e outra com o sufixo de zona privada.
* Reencaminhamento condicional não é suportado nativamente no momento. Para habilitar a resolução entre redes do Azure e no local. Consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passos Seguintes

* Saiba como criar uma zona privada no DNS do Azure, utilizando [do Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns comum [cenários de zona privada](./private-dns-scenarios.md) que podem ser conseguidos com as zonas privadas no DNS do Azure.

* Para perguntas e respostas sobre zonas privadas no DNS do Azure comuns, incluindo o comportamento específico pode esperar para determinados tipos de operações, consulte [FAQ do DNS privado](./dns-faq-private.md).

* Saiba mais sobre zonas e registos DNS ao visitar [zonas e registos de descrição geral do DNS](dns-zones-records.md).

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
