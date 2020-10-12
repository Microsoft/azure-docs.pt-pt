---
title: Opções de resolução de nomes DNS para VMs Linux
description: Cenários de Resolução de Nomes para máquinas virtuais Linux em Azure IaaS, incluindo serviços DNS fornecidos, DNS externos híbridos e servidor Bring Your Own DNS.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: aa007888c68df41242f937e1062a90ec1b7fc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372829"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para máquinas virtuais Linux em Azure
O Azure fornece resolução de nome DNS por padrão para todas as máquinas virtuais que se encontram numa única rede virtual. Pode implementar a sua própria solução de resolução de nomes DNS configurando os seus próprios serviços DNS nas suas máquinas virtuais que a Azure acolhe. Os seguintes cenários devem ajudá-lo a escolher o que trabalha para a sua situação.

* [Resolução de nomes que Azure fornece](#name-resolution-that-azure-provides)
* [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que usa depende de como as suas máquinas virtuais e instâncias de função precisam de comunicar entre si.

A tabela a seguir ilustra cenários e soluções correspondentes de resolução de nomes:

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nomes entre instâncias de função ou máquinas virtuais na mesma rede virtual |Resolução de nomes que Azure fornece |nome de hospedeiro ou nome de domínio totalmente qualificado (FQDN) |
| Resolução de nomes entre instâncias de função ou máquinas virtuais em diferentes redes virtuais |Servidores DNS geridos pelo cliente que encaminham consultas entre redes virtuais para resolução por Azure (dns proxy). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |FQDN apenas |
| Resolução de computadores e nomes de serviços no local a partir de instâncias de função ou máquinas virtuais em Azure |Servidores DNS geridos pelo cliente (por exemplo, controlador de domínio no local, controlador de domínio local apenas de leitura ou um DNS secundário sincronizado utilizando transferências de zona). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |FQDN apenas |
| Resolução de hostnames Azure de computadores no local |Encaminhar consultas para um servidor de procuração DNS gerido pelo cliente na rede virtual correspondente. O servidor proxy remete as consultas para Azure para resolução. Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |FQDN apenas |
| DNS invertidos para IPs internos |[Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/a |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nomes que Azure fornece
Juntamente com a resolução de nomes públicos de DNS, a Azure fornece uma resolução de nome interno para máquinas virtuais e instâncias de papel que estão na mesma rede virtual. Nas redes virtuais que são baseadas no Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual; o FQDN não é necessário. Os nomes DNS podem ser atribuídos tanto a cartões de interface de rede (NICs) como a máquinas virtuais. Embora a resolução de nomes que o Azure fornece não exija qualquer configuração, não é a escolha adequada para todos os cenários de implantação, como visto na tabela anterior.

### <a name="features-and-considerations"></a>Características e considerações
**Características:**

* Nenhuma configuração é necessária para usar a resolução de nomes que a Azure fornece.
* O serviço de resolução de nomes que a Azure fornece está altamente disponível. Não precisa de criar e gerir clusters dos seus próprios servidores DNS.
* O serviço de resolução de nomes que o Azure fornece pode ser usado juntamente com os seus próprios servidores DNS para resolver tanto os nomes de anfitriões no local como os nomes de anfitriões Azure.
* A resolução de nomes é fornecida entre máquinas virtuais em redes virtuais sem necessidade para o FQDN.
* Pode utilizar nomes de anfitriões que melhor descrevam as suas implementações em vez de trabalhar com nomes gerados automaticamente.

**Considerações:**

* O sufixo DNS que o Azure cria não pode ser modificado.
* Não pode registar manualmente os seus próprios registos.
* WINS e NetBIOS não são apoiados.
* Os anfitriões devem ser compatíveis com DNS.
    Os nomes devem usar apenas 0-9, a-z e '-', e não podem começar ou terminar com um '--.'. Consulte a secção 2 do RFC 3696.
* O tráfego de consulta de DNS é acelerado para cada máquina virtual. A aceleração não deve ter impacto na maioria das aplicações.  Se for observado o estrangulamento do pedido, certifique-se de que o caching do lado do cliente está ativado.  Para obter mais informações, consulte [obter o máximo da resolução de nomes que a Azure fornece.](#getting-the-most-from-name-resolution-that-azure-provides)

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Obter o máximo da resolução de nomes que Azure fornece
**Caching do lado do cliente:**

Algumas consultas de DNS não são enviadas através da rede. O cache do lado do cliente ajuda a reduzir a latência e a melhorar a resiliência às inconsistências da rede, resolvendo consultas de DNS recorrentes a partir de uma cache local. Os registos DNS contêm um Time-To-Live (TTL), que permite que a cache armazene o registo o máximo de tempo possível sem afetar a frescura do registo. Como resultado, o caching do lado do cliente é adequado para a maioria das situações.

Algumas distribuições linux não incluem caching por padrão. Recomendamos que adicione uma cache a cada máquina virtual Linux depois de verificar se já não existe uma cache local.

Vários pacotes de caching DNS diferentes, como o dnsmasq, estão disponíveis. Aqui estão os passos para instalar o DNSMASQ nas distribuições mais comuns:

**Ubuntu (utiliza resolveconf)**
  * Instale o pacote dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (utiliza netconf)**:
1. Instale o pacote dnsmasq ("sudo zypper install dnsmasq").
2. Ativar o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Editar "/etc/sysconfig/network/config", e alterar NETCONFIG_DNS_FORWARDER="" para "dnsmasq".
5. Atualizar resolver.conf ("netconfig update") para definir a cache como o DNS local resolver.

**CentOS by Rogue Wave Software (anteriormente OpenLogic; usa NetworkManager)**
1. Instale o pacote dnsmasq ("sudo yum instalar dnsmasq").
2. Ativar o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Adicione "prepend-name-servers 127.0.0.1;" a "/etc/dhclient-eth0.conf".
5. Reinicie o serviço de rede ("rede de serviço reiniciar") para definir a cache como o DNS local resolver

> [!NOTE]
> : O pacote 'dnsmasq' é apenas um dos muitos caches DNS que estão disponíveis para o Linux. Antes de a utilizar, verifique a sua adequação às suas necessidades e que não está instalada nenhuma outra cache.
>
>

**Retries do lado do cliente**

O DNS é principalmente um protocolo da UDP. Como o protocolo da UDP não garante a entrega de mensagens, o protocolo DNS em si lida com a lógica de retrip. Cada cliente DNS (sistema operativo) pode apresentar diferentes lógicas de retenção, dependendo da preferência do criador:

* Os sistemas operativos Windows voltam a tentar depois de um segundo e depois de mais dois, quatro e mais quatro segundos.
* As retréis linux predefinidos após cinco segundos.  Deve mudar isto para tentar cinco vezes em intervalos de um segundo.  

Para verificar as definições atuais de uma máquina virtual Linux, 'cat /etc/resolve.conf', e olhar para a linha 'opções', por exemplo:

```config-conf
options timeout:1 attempts:5
```

O ficheiro resolve.conf é gerado automaticamente e não deve ser editado. Os passos específicos que adicionam a linha de "opções" variam consoante a distribuição:

**Ubuntu** (utiliza resolveconf)
1. Adicione a linha de opções a '/etc/resoluçconf/resolve.conf.d/head'.
2. Executar 'resolverconf-u' para atualizar.

**SUSE** (utiliza netconf)
1. Adicione 'timeout:1 tentativas:5' ao parâmetro NETCONFIG_DNS_RESOLVER_OPTIONS="" em '/etc/sysconfig/network/config'.
2. Executar 'netconfig update' para atualizar.

**CentOS by Rogue Wave Software (anteriormente OpenLogic)** (usa NetworkManager)
1. Adicione 'RES_OPTIONS="timeout:1 tentativas:5" a '/etc/sysconfig/network'.
2. Executar 'rede de serviço reiniciar' para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes usando o seu próprio servidor DNS
As necessidades de resolução do seu nome podem ir além das funcionalidades que o Azure fornece. Por exemplo, pode exigir resolução de DNS entre redes virtuais. Para cobrir este cenário, pode utilizar os seus próprios servidores DNS.  

Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para resoluções recursivas do Azure para resolver os nomes de anfitriões que se encontram na mesma rede virtual. Por exemplo, um servidor DNS que funciona em Azure pode responder a consultas de DNS para os seus próprios ficheiros de zona DNS e encaminhar todas as outras consultas para a Azure. Esta funcionalidade permite que as máquinas virtuais vejam as suas entradas nos ficheiros da zona e os hostnames que o Azure fornece (através do reencaminhador). O acesso às correturas recursivas do Azure é fornecido através do IP virtual 168.63.129.16.

O reencaminhamento de DNS também permite a resolução de DNS entre redes virtuais e permite que as suas máquinas no local resolvam os hostnames que o Azure fornece. Para resolver o nome de anfitrião de uma máquina virtual, a máquina virtual do servidor DNS deve residir na mesma rede virtual e ser configurada para encaminhar consultas de nome de anfitrião para Azure. Como o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de encaminhamento condicional para enviar consultas dns para a rede virtual correta para resolução. A imagem a seguir mostra duas redes virtuais e uma rede no local a fazer resolução de DNS entre redes virtuais utilizando este método:

![Resolução DNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Quando utiliza a resolução de nomes que o Azure fornece, o sufixo de DNS interno é fornecido a cada máquina virtual utilizando o DHCP. Quando utiliza a sua própria solução de resolução de nomes, este sufixo não é fornecido a máquinas virtuais porque o sufixo interfere com outras arquiteturas DNS. Para se referir a máquinas pela FQDN ou para configurar o sufixo nas suas máquinas virtuais, pode utilizar o PowerShell ou a API para determinar o sufixo:

* Para redes virtuais geridas pelo Azure Resource Manager, o sufixo está disponível através do recurso [do cartão de interface](/rest/api/virtualnetwork/networkinterfaces) de rede. Também pode executar o `azure network public-ip show <resource group> <pip name>` comando para mostrar os detalhes do seu IP público, que inclui o FQDN do NIC.

Se as consultas de encaminhamento para Azure não se adequam às suas necessidades, precisa de fornecer a sua própria solução DNS.  A sua solução DNS precisa de:

* Fornecer uma resolução adequada do nome de hospedeiro, por exemplo através do [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se utilizar o DDNS, poderá ter de desativar a limpeza de registos dns. Os contratos de arrendamento DHCP da Azure são muito longos e a limpeza pode remover os registos dns prematuramente.
* Fornecer uma resolução recursiva adequada para permitir a resolução de nomes de domínio externos.
* Esteja acessível (TCP e UDP na porta 53) a partir dos clientes que serve e possa aceder à Internet.
* Seja protegido contra o acesso da Internet para mitigar as ameaças colocadas por agentes externos.

> [!NOTE]
> Para melhor desempenho, quando utilizar máquinas virtuais nos servidores Azure DNS, desative o IPv6 e atribua um [IP público de nível de instância](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) a cada máquina virtual de servidor DNS.  
>
>
