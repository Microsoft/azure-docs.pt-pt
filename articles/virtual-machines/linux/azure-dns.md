---
title: Opções de resolução de nome snS para VMs Linux
description: Cenários de resolução de nomes para máquinas virtuais Linux em Azure IaaS, incluindo serviços DNS fornecidos, DNS externos híbridos e trazer o seu próprio servidor DNS.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 3d5ecaf67dcff182c7dace474b7bda45cdfd5c58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969324"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para máquinas virtuais Linux em Azure
O Azure fornece a resolução de nome snS dNS por padrão para todas as máquinas virtuais que se encontram numa única rede virtual. Pode implementar a sua própria solução de resolução de nomes DNS configurando os seus próprios serviços DNS nas suas máquinas virtuais que o Azure acolhe. Os seguintes cenários devem ajudá-lo a escolher aquele que funciona para a sua situação.

* [Resolução de nomes que Azure fornece](#name-resolution-that-azure-provides)
* [Resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que usa depende de como as suas máquinas virtuais e instâncias de papéis precisam de comunicar entre si.

O quadro que se segue ilustra cenários e soluções correspondentes de resolução de nomes:

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nomes entre instâncias de papéis ou máquinas virtuais na mesma rede virtual |Resolução de nomes que Azure fornece |nome de anfitrião ou nome de domínio totalmente qualificado (FQDN) |
| Resolução de nomes entre exemplos de papéis ou máquinas virtuais em diferentes redes virtuais |Servidores DNS geridos pelo cliente que reencaminha consultas entre redes virtuais para resolução por Azure (procuração DNS). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de computadores no local e nomes de serviços de instâncias de papéis ou máquinas virtuais em Azure |Servidores DNS geridos pelo cliente (por exemplo, controlador de domínio no local, controlador de domínio local apenas de leitura ou um DNS secundário sincronizado utilizando transferências de zona). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de anfitriões azure de computadores no local |Encaminhar consultas para um servidor de procuração DNS gerido pelo cliente na rede virtual correspondente. O servidor proxy reencaminha consultas ao Azure para resolução. Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| DNS invertidos para IPs internos |[Resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nomes que Azure fornece
Juntamente com a resolução de nomes públicos do DNS, o Azure fornece resolução interna de nomes para máquinas virtuais e instâncias de papéis que se encontram na mesma rede virtual. Em redes virtuais baseadas no Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual; o FQDN não é necessário. Os nomes DNS podem ser atribuídos tanto aos cartões de interface de rede (NICs) como às máquinas virtuais. Embora a resolução de nomes que o Azure fornece não exija qualquer configuração, não é a escolha adequada para todos os cenários de implementação, como se pode ver na tabela anterior.

### <a name="features-and-considerations"></a>Características e considerações
**Características:**

* Não é necessária qualquer configuração para usar a resolução de nomes que o Azure fornece.
* O serviço de resolução de nomes que o Azure fornece está altamente disponível. Não precisa de criar e gerir clusters dos seus próprios servidores DNS.
* O serviço de resolução de nomes que o Azure fornece pode ser utilizado juntamente com os seus próprios servidores DNS para resolver tanto no local como no nome de anfitriões Azure.
* A resolução de nomes é fornecida entre máquinas virtuais em redes virtuais sem necessidade para o FQDN.
* Pode utilizar nomes de anfitriões que melhor descrevam as suas implementações em vez de trabalhar com nomes gerados automaticamente.

**Considerações:**

* O sufixo DNS que o Azure cria não pode ser modificado.
* Não pode registar manualmente os seus próprios registos.
* AS WINS e a NetBIOS não são suportadas.
* Os nomes de anfitriões devem ser compatíveis com dNS.
    Os nomes devem usar apenas 0-9, a-z e '-', e não podem começar ou terminar com um '-'. Consulte a Secção 2 do RFC 3696.
* O tráfego de consulta dNS é estrangulado para cada máquina virtual. O estrangulamento não deve afetar a maioria das aplicações.  Se o pedido for respeitado, certifique-se de que o cache do lado do cliente está ativado.  Para mais informações, consulte [Tirar o máximo partido da resolução de nomes que o Azure fornece.](#getting-the-most-from-name-resolution-that-azure-provides)

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Tirar o máximo partido da resolução de nomes que o Azure fornece
**Cache do lado do cliente:**

Algumas consultas de DNS não são enviadas através da rede. O cache do lado do cliente ajuda a reduzir a latência e a melhorar a resiliência às inconsistências da rede, resolvendo consultas de DNS recorrentes a partir de uma cache local. Os registos DNS contêm um Time-To-Live (TTL), que permite à cache armazenar o registo o máximo de tempo possível sem afetar a frescura recorde. Como resultado, o cache do lado do cliente é adequado para a maioria das situações.

Algumas distribuições linux não incluem o cache por defeito. Recomendamos que adicione uma cache a cada máquina virtual Linux depois de verificar se já não existe uma cache local.

Vários pacotes de cache DNS diferentes, como dnsmasq, estão disponíveis. Aqui estão os passos para instalar dnsmasq nas distribuições mais comuns:

**Ubuntu (usa resolveconf)**
  * Instale a embalagem dnsmasq ("sudo apt-get instalar dnsmasq").

**SUSE (utiliza netconf)**:
1. Instale a embalagem dnsmasq ("sudo zypper instale dnsmasq").
2. Ativar o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Editar "/etc/sysconfig/network/config", e alterar NETCONFIG_DNS_FORWARDER="" para "dnsmasq".
5. Atualizar resolve.conf ("atualização netconfig") para definir a cache como o dNS local resolver.

**CentOS by Rogue Wave Software (anteriormente OpenLogic; usa NetworkManager)**
1. Instale a embalagem dnsmasq ("sudo yum instalar dnsmasq").
2. Ativar o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Adicione "prepend domain-name-servers 127.0.0.1;" a "/etc/dhclient-eth0.conf".
5. Reiniciar o serviço de rede ("reinício da rede de serviços") para definir a cache como o dNS local resolver

> [!NOTE]
> : O pacote 'dnsmasq' é apenas um dos muitos caches DNS disponíveis para o Linux. Antes de usá-lo, verifique a sua adequação às suas necessidades e não esteja instalado nenhum outro cache.
>
>

**Repetições do lado do cliente**

DNS é principalmente um protocolo UDP. Como o protocolo da UDP não garante a entrega de mensagens, o protocolo DNS em si trata da lógica de retry. Cada cliente DNS (sistema operativo) pode apresentar diferentelógica de retry dependendo da preferência do criador:

* Os sistemas operativos windows voltam a tentar após um segundo e depois de mais dois, quatro e mais quatro segundos.
* A configuração padrão linux se retenta após cinco segundos.  Deve mudar isto para voltar a tentar cinco vezes em intervalos de um segundo.  

Para verificar as definições atuais numa máquina virtual Linux, 'cat /etc/resolve.conf', e olhar para a linha 'opções', por exemplo:

    options timeout:1 attempts:5

O ficheiro resolve.conf é gerado automaticamente e não deve ser editado. Os passos específicos que adicionam a linha 'opções' variam por distribuição:

**Ubuntu** (usa resolveconf)
1. Adicione a linha de opções para '/etc/resolveconf/resolve.conf.d/head'.
2. Executar 'resolveconf-u' para atualizar.

**SUSE** (usa netconf)
1. Adicione o 'timeout:1 tentativas:5' ao parâmetro NETCONFIG_DNS_RESOLVER_OPTIONS="" em '/etc/sysconfig/network/config'.
2. Executar 'netconfig update' para atualizar.

**CentOS by Rogue Wave Software (anteriormente OpenLogic)** (usa NetworkManager)
1. Adicione 'RES_OPTIONS="timeout:1 tentativas:5"" ao '/etc/sysconfig/rede'.
2. Executar 'reinício da rede de serviços' para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nome utilizando o seu próprio servidor DNS
As necessidades de resolução de nomes podem ir além das funcionalidades que o Azure fornece. Por exemplo, pode necessitar de resolução dNS entre redes virtuais. Para cobrir este cenário, pode utilizar os seus próprios servidores DNS.  

Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para resolver resoluções recursivas do Azure para resolver nomes de anfitriões que estão na mesma rede virtual. Por exemplo, um servidor DNS que funciona em Azure pode responder a consultas dNS para os seus próprios ficheiros de zona DNS e encaminhar todas as outras consultas para o Azure. Esta funcionalidade permite que as máquinas virtuais vejam ambas as suas entradas nos seus ficheiros de zona e nomes de anfitriões que o Azure fornece (através do forwardr). O acesso às resolvers recursivas do Azure é fornecido através do IP virtual 168.63.129.16.

O encaminhamento dNS também permite a resolução de DNS entre redes virtuais e permite que as suas máquinas no local resolvam nomes de anfitriões que o Azure fornece. Para resolver o nome de anfitrião de uma máquina virtual, a máquina virtual do servidor DNS deve residir na mesma rede virtual e ser configurada para encaminhar consultas de nome de anfitrião para O Azure. Como o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de encaminhamento condicional para enviar consultas de DNS para a rede virtual correta para resolução. A imagem seguinte mostra duas redes virtuais e uma rede no local que faz a resolução dNS entre redes virtuais utilizando este método:

![Resolução dNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Quando utiliza a resolução de nomes que o Azure fornece, o sufixo interno do DNS é fornecido a cada máquina virtual utilizando o DHCP. Quando utiliza a sua própria solução de resolução de nome, este sufixo não é fornecido a máquinas virtuais porque o sufixo interfere com outras arquiteturas DNS. Para se referir a máquinas da FQDN ou para configurar o sufixo nas suas máquinas virtuais, pode utilizar o PowerShell ou a API para determinar o sufixo:

* Para redes virtuais geridas pelo Gestor de Recursos Azure, o sufixo está disponível através do recurso do cartão de interface de [rede.](https://msdn.microsoft.com/library/azure/mt163668.aspx) Também pode executar `azure network public-ip show <resource group> <pip name>` o comando para exibir os detalhes do seu IP público, que inclui o FQDN do NIC.

Se encaminhar consultas para o Azure não se adequa às suas necessidades, precisa de fornecer a sua própria solução DNS.  A sua solução DNS tem de:

* Fornecer uma resolução de nome de anfitrião apropriada, por exemplo através [de DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se utilizar o DDNS, poderá ter de desativar a limpeza de registos DNS. Os arrendamentos DHCP de Azure são muito longos e a limpeza pode remover os registos de DNS prematuramente.
* Fornecer uma resolução recursiva adequada para permitir a resolução de nomes de domínioexternos externos.
* Seja acessível (TCP e UDP na porta 53) a partir dos clientes que serve e possa aceder à Internet.
* Seja protegido contra o acesso da Internet para mitigar as ameaças colocadas por agentes externos.

> [!NOTE]
> Para um melhor desempenho, quando utilizar máquinas virtuais em servidores DNS Azure, desative o IPv6 e atribua um [IP público de nível de instância](../../virtual-network/virtual-networks-instance-level-public-ip.md) a cada máquina virtual do servidor DNS.  
>
>
