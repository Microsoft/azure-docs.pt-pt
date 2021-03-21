---
title: Name resolution for resources in Azure virtual networks (Resolução de nomes para recursos em redes virtuais do Azure)
titlesuffix: Azure Virtual Network
description: Designar cenários de resolução de nomes para Azure IaaS, soluções híbridas, entre diferentes serviços em nuvem, Ative Directory e usar o seu próprio servidor DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222858"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Name resolution for resources in Azure virtual networks (Resolução de nomes para recursos em redes virtuais do Azure)

Dependendo da forma como utiliza o Azure para acolher soluções IaaS, PaaS e híbridas, poderá ter de permitir que as máquinas virtuais (VMs) e outros recursos implantados numa rede virtual se comuniquem entre si. Embora possa ativar a comunicação utilizando endereços IP, é muito mais simples usar nomes que podem ser facilmente lembrados e não mudar. 

Quando os recursos implantados em redes virtuais precisam de resolver os nomes de domínio para endereços IP internos, podem utilizar um de três métodos:

* [Zonas privadas Azure DNS](../dns/private-dns-overview.md)
* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução de nomes que usa o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server) (que pode encaminhar consultas para os servidores DNS fornecidos pelo Azure)

O tipo de resolução de nomes que utiliza depende do modo como os recursos têm de comunicar entre si. A tabela a seguir ilustra cenários e soluções correspondentes de resolução de nomes:

> [!NOTE]
> As zonas privadas Azure DNS são a solução preferida e dá-lhe flexibilidade na gestão das suas zonas e registos DNS. Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](../dns/private-dns-overview.md).

> [!NOTE]
> Se utilizar o Azure Desde o DNS, então o sufixo DNS apropriado será automaticamente aplicado às suas máquinas virtuais. Para todas as outras opções, deve utilizar nomes de domínio totalmente qualificados (FQDN) ou aplicar manualmente o sufixo DNS apropriado às suas máquinas virtuais.

| **Cenário** | **Solução** | **Sufixo DNS** |
| --- | --- | --- |
| Resolução de nomes entre VMs localizados na mesma rede virtual, ou instâncias de função Azure Cloud Services no mesmo serviço de nuvem. | [Zonas privadas Azure DNS](../dns/private-dns-overview.md) ou [resolução de nomes fornecidas pelo Azure](#azure-provided-name-resolution) |Nome de hospedeiro ou FQDN |
| Resolução de nomes entre VMs em diferentes redes virtuais ou instâncias de função em diferentes serviços em nuvem. |[Zonas privadas Azure DNS](../dns/private-dns-overview.md) ou, servidores DNS geridos pelo cliente reencaminham consultas entre redes virtuais para resolução por Azure (dns proxy). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| Resolução de nomes a partir de um Azure App Service (Web App, Function ou Bot) usando a integração de rede virtual para instâncias de função ou VMs na mesma rede virtual. |Servidores DNS geridos pelo cliente reencaminham consultas entre redes virtuais para resolução por Azure (proxy DNS). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| Resolução de nomes de Aplicações Web do Serviço de Aplicações para VMs na mesma rede virtual. |Servidores DNS geridos pelo cliente reencaminham consultas entre redes virtuais para resolução por Azure (proxy DNS). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| Resolução de nomes de Aplicações Web do Serviço de Aplicações numa rede virtual para VMs numa rede virtual diferente. |Servidores DNS geridos pelo cliente reencaminham consultas entre redes virtuais para resolução por Azure (proxy DNS). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| Resolução de nomes informáticos e de serviços no local a partir de VMs ou de instâncias de função em Azure. |Servidores DNS geridos pelo cliente (controlador de domínio no local, controlador de domínio local apenas de leitura ou um DNS secundário sincronizado usando transferências de zona, por exemplo). Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| Resolução de azure hostnames de computadores no local. |Consultas avançadas para um servidor de procuração DNS gerido pelo cliente na rede virtual correspondente, o servidor proxy encaminha consultas para Azure para resolução. Consulte [a resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |FQDN apenas |
| DNS invertido para IPs internos. |[Zonas privadas Azure DNS](../dns/private-dns-overview.md) ou [resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) ou [resolução de nome usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Não aplicável |
| Resolução de nomes entre VMs ou instâncias de função localizadas em diferentes serviços de nuvem, não numa rede virtual. |Não aplicável. A conectividade entre VMs e instâncias de função em diferentes serviços em nuvem não é suportada fora de uma rede virtual. |Não aplicável|

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure

A azure forneceu resolução de nomes fornece apenas capacidades básicas de DNS autoritárias. Se utilizar esta opção, os nomes e registos da zona DNS serão geridos automaticamente pelo Azure e não será capaz de controlar os nomes da zona DNS ou o ciclo de vida dos registos DNS. Se necessitar de uma solução DNS totalmente apresentada para as suas redes virtuais, deve utilizar [zonas privadas Azure DNS](../dns/private-dns-overview.md) ou [servidores DNS geridos pelo Cliente](#name-resolution-that-uses-your-own-dns-server).

Juntamente com a resolução de nomes públicos de DNS, a Azure fornece uma resolução de nome interno para VMs e instâncias de função que residem dentro da mesma rede virtual ou serviço de nuvem. VMs e instâncias em um serviço de nuvem compartilham o mesmo sufixo DNS, por isso o nome do anfitrião por si só é suficiente. Mas nas redes virtuais implementadas utilizando o modelo de implementação clássico, diferentes serviços em nuvem têm diferentes sufixos DNS. Nesta situação, você precisa do FQDN para resolver nomes entre diferentes serviços na nuvem. Nas redes virtuais implementadas utilizando o modelo de implementação do Azure Resource Manager, o sufixo DNS é consistente em todas as máquinas virtuais dentro de uma rede virtual, pelo que o FQDN não é necessário. Os nomes DNS podem ser atribuídos tanto a VMs como a interfaces de rede. Embora a resolução de nomes fornecida pelo Azure não exija qualquer configuração, não é a escolha adequada para todos os cenários de implantação, como detalhado na tabela anterior.

> [!NOTE]
> Ao utilizar as funções web e de trabalho dos serviços de nuvem, também pode aceder aos endereços IP internos de instâncias de função utilizando a API de Gestão de Serviços Azure. Para mais informações, consulte a [Referência API DO REST Gestão de Serviços.](/previous-versions/azure/ee460799(v=azure.100)) O endereço baseia-se no nome da função e no número da instância. 
>

### <a name="features"></a>Funcionalidades

A resolução de nomes fornecida pelo Azure inclui as seguintes características:
* Facilidade de uso. Não é necessária qualquer configuração.
* Elevada disponibilidade. Não precisa de criar e gerir clusters dos seus próprios servidores DNS.
* Pode utilizar o serviço em conjunto com os seus próprios servidores DNS, para resolver os nomes dos anfitriões do Azure no local e do Azure.
* Pode utilizar a resolução de nomes entre VMs e instâncias de função dentro do mesmo serviço de nuvem, sem a necessidade de um FQDN.
* Pode utilizar a resolução de nomes entre VMs em redes virtuais que utilizam o modelo de implementação do Gestor de Recursos Azure, sem necessidade de um FQDN. As redes virtuais no modelo de implementação clássico requerem um FQDN quando estiver a resolver nomes em diferentes serviços na nuvem. 
* Pode utilizar nomes de anfitriões que melhor descrevam as suas implementações, em vez de trabalhar com nomes gerados automaticamente.

### <a name="considerations"></a>Considerações

Pontos a ter em conta quando estiver a utilizar a resolução de nomes fornecida pelo Azure:
* O sufixo DNS criado pelo Azure não pode ser modificado.
* A procura de DNS é procurada por uma rede virtual. Os nomes DNS criados para uma rede virtual não podem ser resolvidos a partir de outras redes virtuais.
* Não pode registar manualmente os seus próprios registos.
* WINS e NetBIOS não são apoiados. Não é possível ver os seus VMs no Windows Explorer.
* Os nomes dos anfitriões devem ser compatíveis com DNS. Os nomes devem usar apenas 0-9, a-z e '-', e não podem começar ou terminar com um '--'.
* O tráfego de consulta de DNS é acelerado para cada VM. A aceleração não deve ter impacto na maioria das aplicações. Se for observado o estrangulamento do pedido, certifique-se de que o caching do lado do cliente está ativado. Para obter mais informações, consulte a [configuração do cliente DNS](#dns-client-configuration).
* Apenas VMs nos primeiros 180 serviços em nuvem estão registados para cada rede virtual num modelo de implementação clássico. Este limite não se aplica às redes virtuais no Azure Resource Manager.
* O endereço IP Azure DNS é 168.63.129.16. Este é um endereço IP estático e não vai mudar.

### <a name="reverse-dns-considerations"></a>Considerações reversas do DNS
O DNS invertido é suportado em todas as redes virtuais baseadas na ARM. Pode emitir consultas de DNS inversas (consultas PTR) para mapear endereços IP de máquinas virtuais para FQDNs de máquinas virtuais.
* Todas as consultas de PTR para endereços IP de máquinas virtuais irão devolver FQDNs do formulário \[ vmname \] .internal.cloudapp.net
* A retrospetiva em FQDNs de formulário \[ vmname \] .internal.cloudapp.net resolverá o endereço IP atribuído à máquina virtual.
* Se a rede virtual estiver ligada a uma [zona privada Azure DNS](../dns/private-dns-overview.md) como rede virtual de registo, as consultas de DNS inversas devolverão dois registos. Um recorde será do formulário \[ vmname \] .. nome privado] e o outro será do nome \[ vmname \] .internal.cloudapp.net
* A pesquisa reversa de DNS é procurada por uma determinada rede virtual, mesmo que seja espreitada para outras redes virtuais. Consultas de DNS inversas (consultas PTR) para endereços IP de máquinas virtuais localizadas em redes virtuais espreguiçadeiras irão devolver NXDOMAIN.
* Se pretender desligar a função DNS inversa numa rede virtual, pode fazê-lo criando uma zona de pesquisa inversa utilizando [zonas privadas Azure DNS](../dns/private-dns-overview.md) e ligando esta zona à sua rede virtual. Por exemplo, se o espaço de endereço IP da sua rede virtual for 10.20.0.0/16, então pode criar uma zona privada de DNS vazia 20.10.in-addr.arpa e ligá-lo à rede virtual. Ao ligar a zona à sua rede virtual, deverá desativar o registo automático no link. Esta zona irá sobrepor-se às zonas de procura inversa padrão para a rede virtual e uma vez que esta zona está vazia, obterá NXDOMAIN para as suas consultas de DNS invertidos. Consulte o nosso [guia Quickstart](../dns/private-dns-getstarted-portal.md) para obter detalhes sobre como criar uma zona privada de DNS e ligá-la a uma rede virtual.

> [!NOTE]
> Se pretender que o dnsup invertido se estende por toda a rede virtual, pode criar uma zona de pesquisa inversa (in-addr.arpa) [as zonas privadas Azure DNS](../dns/private-dns-overview.md) e liga-a a várias redes virtuais. No entanto, terá de gerir manualmente os registos DNS invertidos para as máquinas virtuais.
>


## <a name="dns-client-configuration"></a>Configuração do cliente DNS

Esta secção abrange caching do lado do cliente e retração do lado do cliente.

### <a name="client-side-caching"></a>Caching do lado do cliente

Nem todas as consultas de DNS precisam de ser enviadas através da rede. O cache do lado do cliente ajuda a reduzir a latência e a melhorar a resiliência aos blips de rede, resolvendo consultas de DNS recorrentes a partir de uma cache local. Os registos dns contêm um mecanismo de tempo para viver (TTL), que permite que a cache armazene o registo o máximo de tempo possível sem afetar a frescura do registo. Assim, o caching do lado do cliente é adequado para a maioria das situações.

O cliente DNS do Windows padrão tem uma cache DNS incorporada. Algumas distribuições linux não incluem caching por padrão. Se descobrir que já não existe uma cache local, adicione uma cache DNS a cada LM Linux.

Existem vários pacotes de caching DNS diferentes disponíveis (como o dnsmasq). Eis como instalar o DNSmasq nas distribuições mais comuns:

* **Ubuntu (utiliza resolveconf)**:
  * Instale o pacote dnsmasq com `sudo apt-get install dnsmasq` .
* **SUSE (utiliza netconf)**:
  * Instale o pacote dnsmasq com `sudo zypper install dnsmasq` .
  * Ativar o serviço dnsmasq com `systemctl enable dnsmasq.service` . 
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service` . 
  * Editar **/etc/sysconfig/network/config,** e alterar *NETCONFIG_DNS_FORWARDER=""* para *dnsmasq*.
  * Update resolv.conf with `netconfig update` , para definir a cache como o DNS local resolver.
* **CentOS (utiliza NetworkManager)**:
  * Instale o pacote dnsmasq com `sudo yum install dnsmasq` .
  * Ativar o serviço dnsmasq com `systemctl enable dnsmasq.service` .
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service` .
  * Adicione *préend-name-servidors 127.0.0.1;* a **/etc/dhclient-eth0.conf**.
  * Reinicie o serviço de rede com `service network restart` , para definir a cache como o DNS local resolver.

> [!NOTE]
> O pacote dnsmasq é apenas um dos muitos caches DNS disponíveis para Linux. Antes de a utilizar, verifique a sua adequação às suas necessidades específicas e verifique se não está instalada outra cache.

    
### <a name="client-side-retries"></a>Retries do lado do cliente

O DNS é principalmente um protocolo da UDP. Como o protocolo da UDP não garante a entrega de mensagens, a lógica de reação é tratada no próprio protocolo DNS. Cada cliente DNS (sistema operativo) pode apresentar diferentes lógicas de retenção, dependendo da preferência do criador:

* Os sistemas operativos Windows voltam a tentar depois de um segundo, e depois de mais dois segundos, quatro segundos e mais quatro segundos. 
* As retréis linux predefinidos após cinco segundos. Recomendamos que se mude as especificações de redação para cinco vezes, em intervalos de um segundo.

Verifique as definições atuais num VM Linux com `cat /etc/resolv.conf` . Veja a linha de *opções,* por exemplo:

```bash
options timeout:1 attempts:5
```

O ficheiro resolve.conf é geralmente gerado automaticamente e não deve ser editado. Os passos específicos para adicionar a linha de *opções* variam consoante a distribuição:

* **Ubuntu** (utiliza resolveconf):
  1. Adicione a linha *de opções* a **/etc/resoluçconf/resolve.conf.d/tail**.
  2. Corra `resolvconf -u` para atualizar.
* **SUSE** (utiliza netconf):
  1. Adicione *o tempo limite:1 tentativas:5* ao parâmetro **NETCONFIG_DNS_RESOLVER_OPTIONS=""** em **/etc/sysconfig/network/config**.
  2. Corra `netconfig update` para atualizar.
* **CentOS** (utiliza NetworkManager):
  1. Adicione *o eco "options timeout:1 attempts:5"* a **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Atualização com `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolução de nomes que usa o seu próprio servidor DNS

Esta secção abrange VMs, instâncias de função e aplicações web.

### <a name="vms-and-role-instances"></a>VMs e instâncias de papel

As necessidades de resolução do seu nome podem ir além das características fornecidas pela Azure. Por exemplo, poderá ter de utilizar os domínios do Microsoft Windows Server Ative Directory, resolver os nomes DNS entre redes virtuais. Para cobrir estes cenários, o Azure fornece a capacidade de utilizar os seus próprios servidores DNS.

Os servidores DNS dentro de uma rede virtual podem encaminhar consultas DNS para as correturas recursivas em Azure. Isto permite-lhe resolver os nomes dos anfitriões dentro dessa rede virtual. Por exemplo, um controlador de domínio (DC) em execução em Azure pode responder a consultas de DNS para os seus domínios, e encaminhar todas as outras consultas para Azure. As consultas de encaminhamento permitem que os VMs vejam os seus recursos no local (através do DC) e os nomes de anfitriões fornecidos pelo Azure (através do reencaminhador). O acesso às correturas recursivas em Azure é fornecido através do IP virtual 168.63.129.16.

O reencaminhamento de DNS também permite a resolução de DNS entre redes virtuais, e permite que as suas máquinas no local resolvam os nomes de anfitriões fornecidos pelo Azure. Para resolver o nome de anfitrião de um VM, o VM do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar consultas de nome de anfitrião para Azure. Como o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de encaminhamento condicional para enviar consultas dns para a rede virtual correta para resolução. A imagem a seguir mostra duas redes virtuais e uma rede no local a fazer resolução de DNS entre redes virtuais, utilizando este método. Um remetente DNS exemplo está disponível na [galeria Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Uma instância de função pode executar a resolução de nome de VMs dentro da mesma rede virtual. Fá-lo utilizando o FQDN, que consiste no nome de anfitrião do VM e **internal.cloudapp.net** sufixo DNS. No entanto, neste caso, a resolução de nomes só é bem sucedida se a instância de função tiver o nome VM definido no esquema de [função (ficheiro.cscfg)](/previous-versions/azure/reference/jj156212(v=azure.100)).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> As instâncias de função que precisam de executar a resolução de nome de VMs noutra rede virtual (FQDN utilizando o sufixo **internal.cloudapp.net)** têm de o fazer utilizando o método descrito nesta secção (encaminhamento de servidores DNS personalizados entre as duas redes virtuais).
>

![Diagrama de DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando está a utilizar a resolução de nomes fornecida pelo Azure, o Protocolo de Configuração do Anfitrião Dinâmico Azure (DHCP) fornece um sufixo de DNS interno **(.internal.cloudapp.net)** a cada VM. Este sufixo permite a resolução do nome do anfitrião porque os registos de nome do anfitrião estão na zona **internal.cloudapp.net.** Quando está a usar a sua própria solução de resolução de nomes, este sufixo não é fornecido aos VM porque interfere com outras arquiteturas DNS (como cenários ligados ao domínio). Em vez disso, a Azure fornece um espaço reservado não funcional *(reddog.microsoft.com).*

Se necessário, pode determinar o sufixo de DNS interno utilizando o PowerShell ou a API:

* Para redes virtuais em modelos de implementação do Azure Resource Manager, o sufixo está disponível através da interface de [rede REST API,](/rest/api/virtualnetwork/networkinterfaces)o [cmdlet Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell e o comando [az network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI.
* Nos modelos clássicos de implantação, o sufixo está disponível através da chamada [Get Deployment API](/previous-versions/azure/reference/ee460804(v=azure.100)) ou do [cmdlet Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure.service/get-azurevm)

Se as consultas de encaminhamento para Azure não se adequam às suas necessidades, deverá fornecer a sua própria solução de DNS. A sua solução DNS precisa de:

* Fornecer uma resolução adequada do nome do anfitrião, via [DDNS,](virtual-networks-name-resolution-ddns.md)por exemplo. Se estiver a utilizar o DDNS, poderá ter de desativar a limpeza de registos dns. Os contratos de arrendamento Azure DHCP são longos, e a limpeza pode remover os registos de DNS prematuramente. 
* Fornecer uma resolução recursiva adequada para permitir a resolução de nomes de domínio externos.
* Esteja acessível (TCP e UDP na porta 53) a partir dos clientes que serve, e possa aceder à internet.
* Seja protegido contra o acesso da internet, para mitigar as ameaças colocadas por agentes externos.

> [!NOTE]
> Para um melhor desempenho, quando estiver a utilizar VMs Azure como servidores DNS, o IPv6 deve ser desativado.

### <a name="web-apps"></a>Web Apps
Suponha que precisa de executar a resolução de nomes a partir da sua aplicação web construída utilizando o App Service, ligado a uma rede virtual, a VMs na mesma rede virtual. Além de configurar um servidor DNS personalizado que tenha um reencaminhador DENS que encaminha consultas para Azure (IP virtual 168.63.129.16), execute os seguintes passos:
1. Ativar a integração de rede virtual para a sua aplicação web, se ainda não for feita, como descrito na [Integração da sua aplicação com uma rede virtual.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. No portal Azure, para o plano de Serviço de Aplicações que acolhe a aplicação web, selecione **Sync Network** em **Rede,** **Integração de Rede Virtual**.

    ![Screenshot da resolução de nome de rede virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se precisar de executar a resolução de nomes a partir da sua aplicação web construída utilizando o App Service, ligado a uma rede virtual, a VMs numa rede virtual diferente, tem de utilizar servidores DNS personalizados em ambas as redes virtuais, da seguinte forma:

* Configurar um servidor DNS na sua rede virtual alvo, num VM que também pode encaminhar consultas para o resolver recursivo em Azure (IP virtual 168.63.129.16). Um remetente DNS exemplo está disponível na [galeria Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Crie um reencaminhador DENS na rede virtual de origem num VM. Configure este reencaminhador DNS para encaminhar consultas para o servidor DNS na sua rede virtual alvo.
* Configure o servidor DNS de origem nas definições da sua rede virtual de origem.
* Permitir a integração de rede virtual para a sua aplicação web ligar-se à rede virtual de origem, seguindo as instruções em [Integrar a sua aplicação com uma rede virtual.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* No portal Azure, para o plano de Serviço de Aplicações que acolhe a aplicação web, selecione **Sync Network** em **Rede,** **Integração de Rede Virtual**.

## <a name="specify-dns-servers"></a>Especificar servidores DNS
Quando está a utilizar os seus próprios servidores DNS, o Azure fornece a capacidade de especificar vários servidores DNS por rede virtual. Também pode especificar vários servidores DNS por interface de rede (para o Azure Resource Manager) ou por serviço cloud (para o modelo de implementação clássico). Os servidores DNS especificados para uma interface de rede ou serviço na nuvem obtêm precedência sobre servidores DNS especificados para a rede virtual.

> [!NOTE]
> As propriedades de ligação à rede, tais como IPs do servidor DNS, não devem ser editadas diretamente dentro de VMs. Isto porque podem ser apagados durante a cicatrização do serviço quando o adaptador de rede virtual for substituído. Isto aplica-se tanto aos VMs Windows como linux.

Quando estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, pode especificar servidores DNS para uma rede virtual e uma interface de rede. Para mais detalhes, consulte [Gerir uma rede virtual](manage-virtual-network.md) e gerir uma interface de [rede.](virtual-network-network-interface.md)

> [!NOTE]
> Se optar por um servidor DNS personalizado para a sua rede virtual, tem de especificar pelo menos um endereço IP do servidor DNS; caso contrário, a rede virtual ignorará a configuração e utilizará DNS fornecidos pelo Azure.

Quando estiver a utilizar o modelo de implementação clássico, pode especificar servidores DNS para a rede virtual no portal Azure ou no [ficheiro de Configuração de Rede](/previous-versions/azure/reference/jj157100(v=azure.100)). Para serviços na nuvem, pode especificar servidores DNS através do [ficheiro de Configuração](/previous-versions/azure/reference/ee758710(v=azure.100)) de Serviço ou utilizando o PowerShell, com [o New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Se alterar as definições de DNS para uma rede virtual ou máquina virtual que já esteja implantada, para que as novas definições de DNS produzam efeitos, deve efetuar uma renovação do aluguer de DHCP em todos os VMs afetados na rede virtual. Para os VMs que executam o Sistema operativo Windows, pode fazê-lo digitando `ipconfig /renew` diretamente no VM. Os passos variam dependendo do SO. Consulte a documentação relevante para o seu tipo de SO.

## <a name="next-steps"></a>Passos seguintes

Modelo de implementação do Gestor de Recursos Azure:

* [Gerir uma rede virtual](manage-virtual-network.md)
* [Gerir uma interface de rede](virtual-network-network-interface.md)

Modelo de implantação clássico:

* [Esquema de configuração de serviço Azure](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Esquema de configuração de rede virtual](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Configure uma Rede Virtual utilizando um ficheiro de configuração de rede](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)