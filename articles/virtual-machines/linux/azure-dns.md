---
title: Opções de resolução de nome DNS para máquinas virtuais do Linux no Azure
description: Serviços de DNS de cenários para máquinas virtuais do Linux no Azure IaaS, incluindo fornecidos de resolução de nome, o servidor DNS e traga seu próprio DNS externo no híbrida.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: ae8315b2a484cddc500b5c2dd02a019cb4f46d8e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127094"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para máquinas virtuais do Linux no Azure
O Azure fornece resolução de nomes DNS por predefinição, todas as máquinas virtuais que estão numa única rede virtual. Pode implementar a sua própria solução de resolução de nome DNS ao configurar os seus próprios serviços DNS nas suas máquinas virtuais que aloja o Azure. Os seguintes cenários devem ajudar a escolher uma que funcione para a sua situação.

* [Resolução de nomes fornecidos pelo Azure](#name-resolution-that-azure-provides)
* [Resolução de nomes através de seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que utiliza depende de como as máquinas virtuais e as instâncias de função precisam se comunicar entre si.

A tabela seguinte ilustra os cenários e soluções de resolução de nome correspondente:

| **Cenário** | **Solução** | **Suffix** |
| --- | --- | --- |
| Resolução de nomes entre instâncias de função ou máquinas virtuais na mesma rede virtual |Resolução de nomes fornecidos pelo Azure |nome de anfitrião ou nome de domínio completamente qualificado (FQDN) |
| Resolução de nomes entre instâncias de função ou máquinas virtuais em diferentes redes virtuais |Gerida pelo cliente servidores DNS que reencaminhar consultas entre redes virtuais para a resolução pelo Azure (proxy do DNS). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de serviço de instâncias de função ou máquinas virtuais no Azure e de computadores no local |Gerida pelo cliente servidores DNS (por exemplo, o controlador de domínio no local, controlador de domínio só de leitura local ou um DNS secundário sincronizados através de transferências de zona). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de anfitrião do Azure a partir de computadores no local |Reencaminhar consultas para um servidor de proxy gerida pelo cliente DNS na rede virtual correspondente. O servidor proxy encaminha consultas para o Azure para a resolução. Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| DNS inverso para os IPs interno |[Resolução de nomes através de seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nomes fornecidos pelo Azure
Juntamente com a resolução de nomes DNS públicos, o Azure oferece resolução de nomes internos para máquinas virtuais e instâncias de função que estão na mesma rede virtual. Em redes virtuais baseadas no Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual; o FQDN não é necessária. Nomes DNS podem ser atribuídos a máquinas virtuais e placas de interface de rede (NICs). Embora a resolução de nome fornecidos pelo Azure não necessita de qualquer configuração, não é a opção adequada para todos os cenários de implementação, conforme mostrado na tabela anterior.

### <a name="features-and-considerations"></a>Recursos e considerações
**Funcionalidades:**

* É necessária nenhuma configuração para utilizar a resolução de nome fornecidos pelo Azure.
* O serviço de resolução de nome fornecidos pelo Azure é de elevada disponibilidade. Não precisa de criar e gerir clusters de seus próprios servidores DNS.
* O serviço de resolução de nome fornecidos pelo Azure pode ser utilizado juntamente com seus próprios servidores DNS para resolver os nomes de anfitrião do Azure e no local.
* Resolução de nomes é fornecida entre máquinas virtuais nas redes virtuais sem necessidade do FQDN.
* Pode utilizar nomes de anfitriões que melhor descrevem as suas implementações em vez de trabalhar com nomes gerado automaticamente.

**Considerações:**

* Não é possível modificar o sufixo DNS que o Azure cria.
* Não é possível registar manualmente os seus próprios registos.
* WINS e NetBIOS não são suportadas.
* Os nomes de anfitrião tem de ser compatível com o DNS.
    Os nomes têm de utilizar apenas 0-9, a-z, e "-", e eles não podem começar nem terminar com um "-". Consulte o RFC 3696 secção 2.
* O tráfego de consulta DNS está limitado para cada máquina virtual. A limitação não deve afetar a maioria dos aplicativos.  Se a limitação de pedidos é observada, certifique-se de que a colocação em cache do lado do cliente está ativada.  Para obter mais informações, consulte [obtendo o máximo partido da resolução de nomes fornecidos pelo Azure](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Obtendo o máximo de resolução de nomes de benefícios do Azure fornece
**Cache do cliente:**

Algumas consultas DNS não são enviadas através da rede. Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar a resiliência a inconsistências de rede resolvendo recorrentes consultas DNS de um cache local. Registos de DNS contêm um Time-To-Live (TTL), que permite que o cache para armazenar o registo por quanto tempo for possível sem afetar a atualização de registo. Como resultado, a colocação em cache do lado do cliente é adequada para a maioria das situações.

Algumas distribuições de Linux não incluem a colocação em cache por predefinição. Recomendamos que adicione uma cache a cada máquina virtual do Linux, depois de verificar o que há não é um cache local já.

Vários DNS diferentes, colocação em cache, como pacotes dnsmasq, estão disponíveis. Eis os passos para instalar dnsmasq sobre as distribuições mais comuns:

**Ubuntu (utiliza resolvconf)**
  * Instale o pacote de dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (utiliza netconf)**:
1. Instale o pacote de dnsmasq ("sudo zypper instalação dnsmasq").
2. Ative o serviço de dnsmasq ("systemctl ativar dnsmasq.service").
3. Inicie o serviço de dnsmasq ("systemctl início dnsmasq.service").
4. Editar "/ etc/sysconfig/rede/configuração" e altere NETCONFIG_DNS_FORWARDER = "" para "dnsmasq".
5. Atualize resolv ("atualização netconfig") para definir a cache como o resolvedor DNS local.

**CentOS pelo Software de onda não autorizado (anteriormente conhecido como OpenLogic; utiliza NetworkManager)**
1. Instale o pacote de dnsmasq ("sudo yum install dnsmasq").
2. Ative o serviço de dnsmasq ("systemctl ativar dnsmasq.service").
3. Inicie o serviço de dnsmasq ("systemctl início dnsmasq.service").
4. Adicionar "preceder o nome-domínio-servidores 127.0.0.1;" para "/etc/dhclient-eth0.conf".
5. Reinicie o serviço de rede ("rede reiniciar o serviço do") para definir a cache como o resolvedor DNS local

> [!NOTE]
> : O pacote de 'dnsmasq' é apenas uma das muitas caches DNS que estão disponíveis para o Linux. Antes de usá-lo, verificar a respetiva adequação para as suas necessidades e sem outro cache está instalado.
>
>

**Repetições do lado do cliente**

O DNS é principalmente um protocolo UDP. Uma vez que o protocolo UDP não garante a entrega de mensagens, o próprio protocolo DNS processa a lógica de repetição. Cada cliente DNS (sistema operativo) pode usar a lógica de repetição diferentes consoante a preferência do criador:

* Sistemas de operativos Windows novamente após um segundo e, em seguida, novamente após a outra dois, quatro e outro quatro segundos.
* As repetições a configuração do Linux predefinida que se depois de cinco segundos.  Deve alterar isso para repetir cinco vezes em intervalos de um segundo.  

Para verificar as definições atuais numa máquina virtual do Linux, o "/etc/resolv.conf gato" e as olhada na linha de 'options', por exemplo:

    options timeout:1 attempts:5

O ficheiro resolv é gerado automaticamente e não deve ser editado. Os passos específicos que adicione a linha 'options' variam de acordo com a distribuição:

**Ubuntu** (utiliza resolvconf)
1. Adicione a linha de opções para ' / etc/resolveconf/resolv.conf.d/head'.
2. Execute 'resolvconf -u' para atualizar.

**SUSE** (utiliza netconf)
1. Adicionar 'timeout:1 tentativas: 5' para o NETCONFIG_DNS_RESOLVER_OPTIONS = "" em "/ etc/sysconfig/rede/configuração" de parâmetro.
2. Execute "netconfig update" para atualizar.

**CentOS pelo Software de onda não autorizado (anteriormente conhecido como OpenLogic)** (utiliza NetworkManager)
1. Adicionar "RES_OPTIONS ="timeout:1 tentativas: 5"' para '/ etc/sysconfig/rede'.
2. Execute o reinício de rede de serviço para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes através de seu próprio servidor DNS
Suas necessidades de resolução de nome podem ir para além dos recursos fornecidos pelo Azure. Por exemplo, poderá necessitar de resolução de DNS entre redes virtuais. Para abordar neste cenário, pode usar seus próprios servidores DNS.  

Servidores DNS dentro de uma rede virtual podem encaminhar consultas DNS para resoluções recursivas do Azure para resolver os nomes de anfitrião que estão na mesma rede virtual. Por exemplo, um servidor DNS que é executada no Azure pode responder a consultas de DNS para seus próprios ficheiros de zona DNS e reencaminhar todas as outras consultas para o Azure. Esta funcionalidade permite que as máquinas virtuais ver as suas entradas nos seus ficheiros de zona e os nomes de anfitrião fornecidos pelo Azure (por meio de reencaminhador). Acesso para as resoluções recursivas do Azure é fornecido através do IP virtual 168.63.129.16.

Reencaminhamento do DNS também permite a resolução DNS entre redes virtuais e permite que as máquinas no local resolver os nomes de anfitrião fornecidos pelo Azure. Para resolver o nome de anfitrião de uma máquina virtual, a máquina de virtual de servidor DNS tem de residir na mesma rede virtual e de ser configurada para consultas de nome de anfitrião de encaminhamento para o Azure. Porque o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de reencaminhamento condicional para enviar consultas DNS para a rede virtual correta para a resolução. A imagem seguinte mostra duas redes virtuais e uma rede no local, fazendo a resolução de DNS entre redes virtuais através deste método:

![Resolução de DNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Ao utilizar a resolução de nomes fornecidos pelo Azure, o sufixo DNS interno é fornecido para cada máquina virtual através de DHCP. Quando usa sua própria solução de resolução de nome, este sufixo não é fornecido para as máquinas virtuais, porque o sufixo interfere com outras arquiteturas DNS. Para fazer referência às máquinas através do FQDN, ou para configurar o sufixo nas suas máquinas virtuais, pode utilizar o PowerShell ou a API para determinar o sufixo:

* Para as redes virtuais que são geridas pelo Azure Resource Manager, o sufixo está disponível através da [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) recursos. Também pode executar o `azure network public-ip show <resource group> <pip name>` comando para apresentar os detalhes de seu IP público, que inclui o FQDN do NIC.

Se o reencaminhamento de consultas para o Azure não se adequar às suas necessidades, terá de fornecer sua própria solução DNS.  Sua solução DNS tem de:

* Fornecer a resolução de nome de anfitrião apropriado, por exemplo [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se usar DDNS, poderá ter de desativar a eliminação do registo DNS. Concessões DHCP do Azure são muito longas e eliminação poderá remover registos DNS prematuramente.
* Fornece resolução recursiva adequadas para permitir a resolução de nomes de domínio externo.
* Ser acessível (TCP e UDP na porta 53) dos clientes funciona e ser capaz de aceder à Internet.
* Estar protegida contra acesso a partir da Internet para atenuar as ameaças impostas por agentes externos.

> [!NOTE]
> Para obter melhor desempenho, ao usar máquinas virtuais em servidores de DNS do Azure, desabilitar o IPv6 e atribuir um [IP público de nível de instância](../../virtual-network/virtual-networks-instance-level-public-ip.md) a cada máquina de virtual de servidor DNS.  
>
>
