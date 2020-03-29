---
title: Name resolution for resources in Azure virtual networks (Resolução de nomes para recursos em redes virtuais do Azure)
titlesuffix: Azure Virtual Network
description: Cenários de resolução de nomes para O Azure IaaS, soluções híbridas, entre diferentes serviços na nuvem, Diretório Ativo e utilização do seu próprio servidor DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.openlocfilehash: 20a5c4befaa30383c54ac9536a3fd26dce3db4d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059973"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Name resolution for resources in Azure virtual networks (Resolução de nomes para recursos em redes virtuais do Azure)

Dependendo da forma como utiliza o Azure para hospedar soluções IaaS, PaaS e híbridas, poderá ser necessário permitir que as máquinas virtuais (VMs) e outros recursos implantados numa rede virtual se comuniquem entre si. Embora possa ativar a comunicação utilizando endereços IP, é muito mais simples usar nomes que podem ser facilmente lembrados e não mudam. 

Quando os recursos implantados em redes virtuais precisam de resolver nomes de domínio para endereços IP internos, podem utilizar um dos três métodos:

* [Zonas privadas Azure DNS](../dns/private-dns-overview.md)
* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução de nome que utiliza o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server) (que pode encaminhar consultas para os servidores DNS fornecidos pelo Azure)

O tipo de resolução de nomes que utiliza depende do modo como os recursos têm de comunicar entre si. O quadro que se segue ilustra cenários e soluções correspondentes de resolução de nomes:

> [!NOTE]
> As zonas privadas Azure DNS são a solução preferida e conferem-lhe flexibilidade na gestão das suas zonas e registos DNS. Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](../dns/private-dns-overview.md).

> [!NOTE]
> Se utilizar o DNS Fornecido Azure, o sufixo DNS adequado será automaticamente aplicado nas suas máquinas virtuais. Para todas as outras opções, deve utilizar nomes de domínio totalmente qualificados (FQDN) ou aplicar manualmente o sufixo DNS apropriado às suas máquinas virtuais.

| **Cenário** | **Solução** | **Sufixo DNS** |
| --- | --- | --- |
| Resolução de nomes entre VMs localizados na mesma rede virtual, ou exemplos de funções azure Cloud Services no mesmo serviço na nuvem. | [Zonas privadas Azure DNS](../dns/private-dns-overview.md) ou [resolução de nomes fornecidas pelo Azure](#azure-provided-name-resolution) |Nome de anfitrião ou FQDN |
| Resolução de nomes entre VMs em diferentes redes virtuais ou exemplos de papéis em diferentes serviços na nuvem. |[Zonas privadas Azure DNS](../dns/private-dns-overview.md) ou servidores DNS geridos pelo Cliente reencaminhando consultas entre redes virtuais para resolução por Azure (procuração DNS). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de um Serviço de Aplicações Azure (Web App, Função ou Bot) utilizando a integração de rede virtual para exemplos de papéis ou VMs na mesma rede virtual. |Servidores DNS geridos pelo cliente que reencaminha maquetes entre redes virtuais para resolução pelo Azure (procuração DNS). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de Aplicações Web do Serviço de Aplicações para VMs na mesma rede virtual. |Servidores DNS geridos pelo cliente que reencaminha maquetes entre redes virtuais para resolução pelo Azure (procuração DNS). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de Aplicações Web do Serviço de Aplicações numa rede virtual para VMs numa rede virtual diferente. |Servidores DNS geridos pelo cliente que reencaminha maquetes entre redes virtuais para resolução pelo Azure (procuração DNS). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de computador e de serviço no local de VMs ou instâncias de papéis em Azure. |Servidores DNS geridos pelo cliente (controlador de domínio no local, controlador de domínio local apenas de leitura ou um DNS sincronizado com transferências de zona, por exemplo). Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de anfitriões Azure de computadores no local. |Encaminha as consultas para um servidor de procuração DNS gerido pelo cliente na rede virtual correspondente, o servidor proxy reencaminha consultas ao Azure para resolução. Consulte [a resolução de nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| DNS invertido sinos para iPs internos. |[Zonas privadas DoDNs azure](../dns/private-dns-overview.md) ou [resolução de nome fornecida pelo Azure](#azure-provided-name-resolution) ou resolução de [nome utilizando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Não aplicável |
| Resolução de nomes entre VMs ou instâncias de papéis localizadas em diferentes serviços na nuvem, não numa rede virtual. |Não aplicável. A conectividade entre VMs e exemplos de papéis em diferentes serviços na nuvem não é suportada fora de uma rede virtual. |Não aplicável|

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure

O Azure forneceu a resolução de nomes fornece apenas capacidades básicas de DNS autoritárias. Se utilizar esta opção, os nomes e registos da zona DNS serão geridos automaticamente pelo Azure e não será capaz de controlar os nomes da zona DNS ou o ciclo de vida dos registos DNS. Se precisar de uma solução DNS totalmente em destaque para as suas redes virtuais, deve utilizar [zonas privadas DoDNs Do Azure](../dns/private-dns-overview.md) ou [servidores DNS geridos pelo Cliente](#name-resolution-that-uses-your-own-dns-server).

Juntamente com a resolução de nomes públicos do DNS, o Azure fornece resolução interna de nomes para VMs e exemplos de papéis que residem dentro da mesma rede virtual ou serviço na nuvem. VMs e instâncias num serviço na nuvem partilham o mesmo sufixo DNS, por isso o nome do anfitrião por si só é suficiente. Mas em redes virtuais implementadas usando o modelo de implantação clássico, diferentes serviços em nuvem têm diferentes sufixos DNS. Nesta situação, você precisa do FQDN para resolver nomes entre diferentes serviços na nuvem. Em redes virtuais implementadas utilizando o modelo de implementação do Gestor de Recursos Azure, o sufixo DNS é consistente em todas as máquinas virtuais dentro de uma rede virtual, pelo que o FQDN não é necessário. Os nomes DNS podem ser atribuídos tanto a VMs como a interfaces de rede. Embora a resolução de nomefornecida pelo Azure não exija qualquer configuração, não é a escolha adequada para todos os cenários de implementação, conforme detalhado na tabela anterior.

> [!NOTE]
> Ao utilizar funções web e trabalhadores de serviços na nuvem, também pode aceder aos endereços IP internos de instâncias de funções utilizando a API REST De Gestão de Serviços Azure. Para mais informações, consulte a [Referência REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)de Gestão de Serviços . O endereço baseia-se no nome do papel e no número da instância. 
>

### <a name="features"></a>Funcionalidades

A resolução de nomes fornecida pelo Azure inclui as seguintes características:
* Facilidade de utilização. Não é necessária qualquer configuração.
* Elevada disponibilidade. Não precisa de criar e gerir clusters dos seus próprios servidores DNS.
* Pode utilizar o serviço em conjunto com os seus próprios servidores DNS, para resolver os nomes dos anfitriões do Azure.
* Pode utilizar a resolução de nomes entre VMs e exemplos de papéis dentro do mesmo serviço de nuvem, sem a necessidade de um FQDN.
* Pode utilizar a resolução de nomes entre VMs em redes virtuais que utilizam o modelo de implementação do Gestor de Recursos Azure, sem necessidade de um FQDN. As redes virtuais do modelo de implementação clássica requerem um FQDN quando está a resolver nomes em diferentes serviços na nuvem. 
* Pode usar nomes de anfitriões que melhor descrevam as suas implementações, em vez de trabalhar com nomes gerados automaticamente.

### <a name="considerations"></a>Considerações

Pontos a considerar quando está a usar a resolução de nomefornecida pelo Azure:
* O sufixo DNS criado pelo Azure não pode ser modificado.
* A procura de DNS é reparada para uma rede virtual. Os nomes dNS criados para uma rede virtual não podem ser resolvidos a partir de outras redes virtuais.
* Não pode registar manualmente os seus próprios registos.
* AS WINS e a NetBIOS não são suportadas. Não é possível ver os seus VMs no Windows Explorer.
* Os nomes dos anfitriões devem ser compatíveis com dNS. Os nomes devem usar apenas 0-9, a-z e '-', e não podem começar ou terminar com um '-'.
* O tráfego de consulta dNS é estrangulado para cada VM. O estrangulamento não deve afetar a maioria das aplicações. Se o pedido for respeitado, certifique-se de que o cache do lado do cliente está ativado. Para mais informações, consulte a [configuração do cliente DNS](#dns-client-configuration).
* Apenas VMs nos primeiros 180 serviços em nuvem estão registados para cada rede virtual num modelo de implantação clássico. Este limite não se aplica às redes virtuais no Gestor de Recursos Do Azure.
* O endereço IP Azure DNS é 168.63.129.16. Este é um endereço IP estático e não vai mudar.

### <a name="reverse-dns-considerations"></a>Considerações dNS inversas
O DNS inverso é suportado em todas as redes virtuais baseadas em ARM. Pode emitir consultas de DNS inversas (consultas ptr) para mapear endereços IP de máquinas virtuais para FQDNs de máquinas virtuais.
* Todas as consultas de PTR para endereços IP de máquinas virtuais devolverão FQDNs de formulário \[vmname\].internal.cloudapp.net
* O look up para a \[frente em\]FQDNs de formulário vmname .internal.cloudapp.net resolverá o endereço IP atribuído à máquina virtual.
* Se a rede virtual estiver ligada a uma [zona privada Azure DNS](../dns/private-dns-overview.md) como uma rede virtual de registo, as consultas de DNS inversas devolverão dois registos. Um disco será o \[vmname\]de formulário. [priatednszonename] e outros seriam \[do formulário\]vmname .internal.cloudapp.net
* A procura inversa do DNS é remetido para uma determinada rede virtual, mesmo que seja observada para outras redes virtuais. As consultas de DNS inversas (consultas de PTR) para endereços IP de máquinas virtuais localizadas em redes virtuais peered devolverão o NXDOMAIN.

> [!NOTE]
> Se quiser que a procura inversa do DNS se estenda através da rede virtual, pode criar uma zona de procuração inversa (in-addr.arpa) [Zonas privadas Azure DNS](../dns/private-dns-overview.md) e liga-a a várias redes virtuais. No entanto, terá de gerir manualmente os registos dNS invertidos para as máquinas virtuais.
>


## <a name="dns-client-configuration"></a>Configuração do cliente DNS

Esta secção cobre o cache do lado do cliente e as repetições do lado do cliente.

### <a name="client-side-caching"></a>Cache do lado do cliente

Nem todas as consultas do DNS precisam de ser enviadas através da rede. O cache do lado do cliente ajuda a reduzir a latência e a melhorar a resiliência aos blips da rede, resolvendo consultas recorrentes de DNS a partir de uma cache local. Os registos DNS contêm um mecanismo de tempo para viver (TTL), que permite ao cache armazenar o registo o máximo de tempo possível sem afetar a frescura do registo. Assim, o cache do lado do cliente é adequado para a maioria das situações.

O cliente padrão do Windows DNS tem uma cache DNS incorporada. Algumas distribuições linux não incluem o cache por defeito. Se descobrir que já não existe uma cache local, adicione uma cache DNS a cada VM Linux.

Existem vários pacotes de cache DNS diferentes disponíveis (como dnsmasq). Aqui está como instalar dnsmasq nas distribuições mais comuns:

* **Ubuntu (usa resolveconf)**:
  * Instale a embalagem dnsmasq com `sudo apt-get install dnsmasq`.
* **SUSE (utiliza netconf)**:
  * Instale a embalagem dnsmasq com `sudo zypper install dnsmasq`.
  * Ativar o serviço dnsmasq com `systemctl enable dnsmasq.service`. 
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`. 
  * **Editar/etc/sysconfig/network/config,** e alterar *NETCONFIG_DNS_FORWARDER=""* para *dnsmasq*.
  * Atualizar resolve.conf `netconfig update`com, para definir a cache como o dNS local resolver.
* **CentOS (utiliza NetworkManager)**:
  * Instale a embalagem dnsmasq com `sudo yum install dnsmasq`.
  * Ativar o serviço dnsmasq com `systemctl enable dnsmasq.service`.
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`.
  * Adicione *os servidores de nome de domínio de preparação 127.0.0.1;* para **/etc/dhclient-eth0.conf**.
  * Reinicie o `service network restart`serviço de rede com, para definir a cache como o dNS local resolver.

> [!NOTE]
> O pacote dnsmasq é apenas um dos muitos caches DNS disponíveis para Linux. Antes de usá-lo, verifique a sua adequação para as suas necessidades específicas e verifique se não há outra cache instalada.

    
### <a name="client-side-retries"></a>Repetições do lado do cliente

DNS é principalmente um protocolo UDP. Como o protocolo uDP não garante a entrega de mensagens, a lógica de retry é tratada no próprio protocolo DNS. Cada cliente DNS (sistema operativo) pode apresentar diferentelógica de retry, dependendo da preferência do criador:

* Os sistemas operativos windows voltam a tentar após um segundo e, em seguida, novamente após mais dois segundos, quatro segundos e mais quatro segundos. 
* A configuração padrão linux se retenta após cinco segundos. Recomendamos que se mude as especificações de repetição para cinco vezes, em intervalos de um segundo.

Verifique as definições atuais num `cat /etc/resolv.conf`VM Linux com . Veja a linha de *opções,* por exemplo:

```bash
options timeout:1 attempts:5
```

O ficheiro resolve.conf é geralmente gerado automaticamente e não deve ser editado. Os passos específicos para a adição da linha de *opções* variam por distribuição:

* **Ubuntu** (usa resolveconf):
  1. Adicione a linha *opções* a **/etc/resolveconf/resolve.conf.d/tail**.
  2. Corra `resolvconf -u` para atualizar.
* **SUSE** (utiliza netconf):
  1. Adicione o *tempo limite:1 tentativas:5* ao parâmetro **NETCONFIG_DNS_RESOLVER_OPTIONS=""** em **/etc/sysconfig/rede/config**.
  2. Corra `netconfig update` para atualizar.
* **CentOS** (usa NetworkManager):
  1. Adicione *eco "timeout de opções:1 tentativas:5"* para **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Atualizar `service network restart`com .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolução de nome que utiliza o seu próprio servidor DNS

Esta secção abrange VMs, exemplos de papéis e aplicações web.

### <a name="vms-and-role-instances"></a>VMs e exemplos de papéis

As necessidades de resolução de nomes podem ir além das funcionalidades fornecidas pelo Azure. Por exemplo, poderá ser necessário utilizar domínios de Diretório Ativo do Microsoft Windows Server, resolver nomes de DNS entre redes virtuais. Para cobrir estes cenários, o Azure fornece a capacidade de utilizar os seus próprios servidores DNS.

Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para os resolvers recursivos em Azure. Isto permite-lhe resolver os nomes dos anfitriões dentro dessa rede virtual. Por exemplo, um controlador de domínio (DC) em funcionamento em Azure pode responder a consultas de DNS para os seus domínios, e encaminhar todas as outras consultas para O Azure. O reencaminhamento de consultas permite que os VMs vejam tanto os seus recursos no local (via DC) como os nomes de anfitriões fornecidos pelo Azure (através do avançado). O acesso aos resolvers recursivos em Azure é fornecido através do IP virtual 168.63.129.16.

O encaminhamento do DNS também permite a resolução de DNS entre redes virtuais, e permite que as suas máquinas no local resolvam os nomes de anfitriões fornecidos pelo Azure. Para resolver o nome de anfitrião de um VM, o VM do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar as consultas de nome do anfitrião para o Azure. Como o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de encaminhamento condicional para enviar consultas de DNS para a rede virtual correta para resolução. A imagem que se segue mostra duas redes virtuais e uma rede no local a fazer resolução de DNS entre redes virtuais, utilizando este método. Um exemplo de avançado dNS está disponível na [galeria De modelos De Quickstart Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Uma instância de papel pode executar a resolução de nomes de VMs dentro da mesma rede virtual. Fá-lo utilizando o FQDN, que consiste no nome de anfitrião do VM e **internal.cloudapp.net** sufixo DNS. No entanto, neste caso, a resolução de nomes só será bem sucedida se a instância de funções tiver o nome VM definido no [ficheiro Role Schema (.cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> As instâncias de função que precisam de executar a resolução de nomes de VMs noutra rede virtual (FQDN utilizando o **sufixo internal.cloudapp.net)** têm de o fazer utilizando o método descrito nesta secção (servidores DNS personalizados que se encaminha entre as duas redes virtuais).
>

![Diagrama de DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando estiver a utilizar a resolução de nomes fornecida pelo Azure, o Protocolo de Configuração do Hospedeiro Dinâmico Azure (DHCP) fornece um sufixo dNS interno **(.internal.cloudapp.net**) a cada VM. Este sufixo permite a resolução do nome do anfitrião porque os registos de nomes do anfitrião estão na zona **internal.cloudapp.net.** Quando está a usar a sua própria solução de resolução de nome, este sufixo não é fornecido a VMs porque interfere com outras arquiteturas DNS (como cenários de domínio). Em vez disso, o Azure fornece um espaço reservado que não funciona *(reddog.microsoft.com).*

Se necessário, pode determinar o sufixo interno do DNS utilizando a PowerShell ou a API:

* Para redes virtuais nos modelos de implementação do Gestor de Recursos Azure, o sufixo está disponível através da interface de [rede REST API,](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces)do [CmDLet Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell e do comando [da rede az nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI.
* Nos modelos de implantação clássicos, o sufixo está disponível através da chamada [Get Deployment API](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou do [cmdlet Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure/get-azurevm)

Se encaminhar consultas para o Azure não se adequa às suas necessidades, deverá fornecer a sua própria solução DNS. A sua solução DNS tem de:

* Forneça a resolução de nome do anfitrião apropriada, via [DDNS,](virtual-networks-name-resolution-ddns.md)por exemplo. Se estiver a utilizar DDNS, poderá ter de desativar a limpeza de registos DNS. Os contratos de arrendamento Azure DHCP são longos, e a limpeza pode remover os registos de DNS prematuramente. 
* Fornecer uma resolução recursiva adequada para permitir a resolução de nomes de domínioexternos externos.
* Seja acessível (TCP e UDP na porta 53) a partir dos clientes que serve, e possa aceder à internet.
* Seja protegido contra o acesso da internet, para mitigar as ameaças colocadas por agentes externos.

> [!NOTE]
> Para um melhor desempenho, quando estiver a utilizar Os VMs Azure como servidores DNS, o IPv6 deve ser desativado. Um [endereço IP público](virtual-network-public-ip-address.md) deve ser atribuído a cada VM do servidor DNS. 
> 

### <a name="web-apps"></a>Web Apps
Suponha que precisa de executar a resolução de nomes a partir da sua aplicação web construída utilizando o App Service, ligado a uma rede virtual, a VMs na mesma rede virtual. Além de configurar um servidor DNS personalizado que tenha um avançado DNS que reencaminha consultas para O Azure (IP virtual 168.63.129.16), execute os seguintes passos:
1. Ative a integração de rede virtual para a sua aplicação web, se ainda não for feita, conforme descrito em Integrar a [sua aplicação com uma rede virtual.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. No portal Azure, para o plano de App Service que acolhe a aplicação web, selecione **Sync Network** em **Networking**, **Integração de Rede Virtual.**

    ![Screenshot da resolução de nome de rede virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se necessitar de executar a resolução de nomes a partir da sua aplicação web construída utilizando o App Service, ligado a uma rede virtual, a VMs numa rede virtual diferente, tem de utilizar servidores DNS personalizados em ambas as redes virtuais, da seguinte forma:

* Instale um servidor DNS na sua rede virtual alvo, num VM que também pode encaminhar consultas para o resolver recursivo em Azure (IP virtual 168.63.129.16). Um exemplo de avançado dNS está disponível na [galeria De modelos De Quickstart Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Instale um avançado DNS na rede virtual de origem num VM. Configure este reencaminhador DNS para encaminhar consultas para o servidor DNS na sua rede virtual alvo.
* Configure o seu servidor DNS de origem nas definições da rede virtual de origem.
* Ative a integração de rede virtual para a sua aplicação web ligar-se à rede virtual de origem, seguindo as instruções em Integrar a [sua aplicação com uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* No portal Azure, para o plano de App Service que acolhe a aplicação web, selecione **Sync Network** em **Networking**, **Integração de Rede Virtual.**

## <a name="specify-dns-servers"></a>Especificar servidores DNS
Quando está a utilizar os seus próprios servidores DNS, o Azure fornece a capacidade de especificar vários servidores DNS por rede virtual. Também pode especificar vários servidores DNS por interface de rede (para o Azure Resource Manager) ou por serviço cloud (para o modelo de implementação clássico). Os servidores DNS especificados para uma interface de rede ou serviço de nuvem obtêm precedência sobre os servidores DNS especificados para a rede virtual.

> [!NOTE]
> As propriedades de ligação à rede, tais como IPs do servidor DNS, não devem ser editadas diretamente dentro de VMs. Isto porque podem ser apagados durante a cicatriz ação de serviço quando o adaptador de rede virtual é substituído. Isto aplica-se tanto aos VMs Windows como linux.

Quando estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, pode especificar servidores DNS para uma rede virtual e uma interface de rede. Para mais detalhes, consulte [Gerir uma rede virtual](manage-virtual-network.md) e gerir uma interface de [rede](virtual-network-network-interface.md).

> [!NOTE]
> Se optar pelo servidor DNS personalizado para a sua rede virtual, deve especificar pelo menos um endereço IP do servidor DNS; caso contrário, a rede virtual ignorará a configuração e utilizará dNS fornecidos pelo Azure.

Quando estiver a utilizar o modelo de implementação clássico, pode especificar servidores DNS para a rede virtual no portal Azure ou no [ficheiro configuração](https://msdn.microsoft.com/library/azure/jj157100)da rede . Para os serviços na nuvem, pode especificar servidores DNS através do ficheiro de [Configuração](https://msdn.microsoft.com/library/azure/ee758710) de Serviço ou utilizando o PowerShell, com [o New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Se alterar as definições de DNS para uma rede virtual ou máquina virtual que já está implantada, para que as novas definições de DNS entrem em vigor, deve realizar uma renovação de locação de DHCP em todos os VMs afetados na rede virtual. Para vMs que executam o Sistema `ipconfig /renew` operativo Windows OS, pode fazê-lo digitando diretamente no VM. Os passos variam consoante o Sistema Operativo. Consulte a documentação relevante para o seu tipo de SO.

## <a name="next-steps"></a>Passos seguintes

Modelo de implementação do Gestor de Recursos Azure:

* [Gerir uma rede virtual](manage-virtual-network.md)
* [Gerir uma interface de rede](virtual-network-network-interface.md)

Modelo de implantação clássico:

* [Esquema de Configuração de Serviço Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de Configuração de Rede Virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configure uma Rede Virtual utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md)
