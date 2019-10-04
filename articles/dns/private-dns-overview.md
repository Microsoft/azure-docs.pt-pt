---
title: O que é o DNS Privado do Azure?
description: Uma visão geral do serviço de Hospedagem de DNS privado no Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 152087ab3dc20dfc95cfeaa0353d961917d362d6
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959354"
---
# <a name="what-is-azure-private-dns"></a>O que é o DNS Privado do Azure?

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou resolver) um nome de serviço para seu endereço IP.  O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura de Microsoft Azure. Além de dar suporte a domínios DNS voltados para a Internet, o DNS do Azure também dá suporte a zonas DNS privadas.

O Azure DNS privado fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Usando zonas DNS privadas, você pode usar seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure disponíveis hoje. O uso de nomes de domínio personalizados ajuda você a personalizar sua arquitetura de rede virtual para melhor atender às necessidades da sua organização. Ele fornece resolução de nomes para VMs (máquinas virtuais) em uma rede virtual e entre redes virtuais. Além disso, você pode configurar os nomes de zonas com uma exibição de horizonte dividido, que permite que uma zona DNS privada e pública Compartilhe o nome.

Para resolver os registros de uma zona DNS privada de sua rede virtual, você deve vincular a rede virtual à zona. As redes virtuais vinculadas têm acesso completo e podem resolver todos os registros DNS publicados na zona privada. Além disso, você também pode habilitar o registro em um link de rede virtual. Se você habilitar o registro em um link de rede virtual, os registros DNS para as máquinas virtuais nessa rede virtual serão registrados na zona privada. Quando o Autoregistro está habilitado, o DNS do Azure também atualiza os registros de zona sempre que uma máquina virtual é criada, altera seu ' endereço IP ' ou é excluído.

![Visão geral do DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como prática recomendada, não use um domínio *. local* para sua zona DNS privada. Nem todos os sistemas operacionais dão suporte a isso.

## <a name="benefits"></a>Benefícios

O DNS privado do Azure oferece os seguintes benefícios:

* **Elimina a necessidade de soluções de DNS personalizadas**. Anteriormente, muitos clientes criaram soluções de DNS personalizadas para gerenciar zonas DNS em sua rede virtual. Agora você pode gerenciar zonas DNS usando a infraestrutura nativa do Azure, que elimina a carga de criar e gerenciar soluções de DNS personalizadas.

* **Use todos os tipos de registros DNS comuns**. O DNS do Azure dá suporte A registros A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gerenciamento de registro de nome de host automático**. Juntamente com a hospedagem de seus registros DNS personalizados, o Azure mantém automaticamente os registros de nome de host das VMs nas redes virtuais especificadas. Nesse cenário, você pode otimizar os nomes de domínio que usa sem a necessidade de criar soluções de DNS personalizadas ou modificar aplicativos.

* **Resolução de nome de host entre redes virtuais**. Ao contrário dos nomes de host fornecidos pelo Azure, as zonas DNS privadas podem ser compartilhadas entre redes virtuais. Essa funcionalidade simplifica cenários de descoberta de serviços e de rede, como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário**. Para reduzir a curva de aprendizado, esse serviço usa as ferramentas de DNS do Azure bem estabelecidas (portal do Azure, Azure PowerShell, CLI do Azure, modelos de Azure Resource Manager e a API REST).

* **Suporte a DNS de omissão de divisão**. Com o DNS do Azure, você pode criar zonas com o mesmo nome que resolvem para diferentes respostas de dentro de uma rede virtual e da Internet pública. Um cenário típico para o DNS de omissão de divisão é fornecer uma versão dedicada de um serviço para uso dentro de sua rede virtual.

* **Disponível em todas as regiões do Azure**. O recurso de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na nuvem pública do Azure.

## <a name="capabilities"></a>Capacidades

O DNS do Azure fornece os seguintes recursos:

* **Registro automático de máquinas virtuais de uma rede virtual que está vinculada a uma zona privada com o Autoregistro habilitado**. As máquinas virtuais são registradas (adicionadas) na zona privada como registros que apontam para seus endereços IP privados. Quando uma máquina virtual em um link de rede virtual com o registro automático habilitado é excluída, o DNS do Azure também remove automaticamente o registro DNS correspondente da zona privada vinculada.

* **A resolução de DNS progressivo tem suporte em redes virtuais que estão vinculadas à zona privada**. Para a resolução de DNS de rede virtual cruzada, não há dependência explícita, de modo que as redes virtuais estejam emparelhadas entre si. No entanto, talvez você queira emparelhar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **A pesquisa inversa de DNS tem suporte no escopo de rede virtual**. A pesquisa inversa de DNS para um IP privado na rede virtual atribuída a uma zona privada retorna o FQDN que inclui o nome do host/registro e o nome da zona como o sufixo.

## <a name="other-considerations"></a>Outras considerações

O DNS do Azure tem as seguintes limitações:

* Uma rede virtual específica pode ser vinculada a apenas uma zona privada se o registro automático de registros DNS da VM estiver habilitado. No entanto, você pode vincular várias redes virtuais a uma única zona DNS.
* O DNS reverso funciona apenas para o espaço IP privado na rede virtual vinculada
* O DNS reverso para um endereço IP privado para uma rede virtual vinculada retorna *Internal.cloudapp.net* como o sufixo padrão para a máquina virtual. Para redes virtuais que estão vinculadas a uma zona privada com o Autoregistro habilitado, o DNS reverso para um endereço IP privado retorna dois FQDNs: um com o padrão de sufixo *Internal.cloudapp.net* e outro com o sufixo de zona privada.
* O encaminhamento condicional não tem suporte nativo no momento. Para habilitar a resolução entre o Azure e redes locais. Consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada no DNS do Azure usando [Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns cenários comuns de [zona privada](./private-dns-scenarios.md) que podem ser percebidos com zonas privadas no DNS do Azure.

* Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo um comportamento específico que você pode esperar para determinados tipos de operações, consulte [DNS privado perguntas frequentes](./dns-faq-private.md).

* Saiba mais sobre zonas e registros DNS visitando [visão geral de zonas e registros DNS](dns-zones-records.md).

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
