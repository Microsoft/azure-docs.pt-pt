---
title: Resolução de nomes de recursos em redes virtuais do Azure
titlesuffix: Azure Virtual Network
description: Cenários de resolução de nome para o IaaS do Azure, soluções híbridas, entre diferentes serviços cloud, do Active Directory e utilizar o seu próprio servidor DNS.
services: virtual-network
documentationcenter: na
author: subsarma
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: subsarma
ms.openlocfilehash: 9130fef895d4f9cd31f643b20a735c0e821923b8
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193998"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Resolução de nomes de recursos em redes virtuais do Azure

Dependendo de como utilizar o Azure para alojar o IaaS, PaaS e soluções híbridas, poderá ter de permitir que as máquinas virtuais (VMs) e outros recursos implementados numa rede virtual para comunicar entre si. Embora pode permitir a comunicação utilizando endereços IP, é muito mais simples de utilizar nomes que podem ser memorizados facilmente e não são alterados. 

Quando precisam de recursos implementados em redes virtuais resolver os nomes de domínio para endereços IP internos, podem utilizar um dos dois métodos:

* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução que utiliza o seu próprio servidor DNS de nomes](#name-resolution-that-uses-your-own-dns-server) (que pode reencaminhar consultas para os servidores DNS fornecidos pelo Azure)

O tipo de resolução de nomes que utiliza depende de como os seus recursos precisam de comunicar entre si. A tabela seguinte ilustra os cenários e soluções de resolução de nome correspondente:

> [!NOTE]
> Dependendo do seu cenário, pode querer utilizar a funcionalidade zonas privadas do DNS do Azure, que está atualmente em pré-visualização pública. Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](../dns/private-dns-overview.md).
>

| **Cenário** | **Solução** | **Suffix** |
| --- | --- | --- |
| Resolução de nomes entre as VMs localizadas na mesma rede virtual ou serviços Cloud do Azure instâncias de função no mesmo serviço cloud. | [As zonas privadas do DNS do Azure](../dns/private-dns-overview.md) ou [resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) |Nome de anfitrião ou FQDN |
| Resolução de nomes entre VMs nas redes virtuais em diferentes ou instâncias de função nos serviços cloud diferentes. |[As zonas privadas do DNS do Azure](../dns/private-dns-overview.md) ou, servidores DNS geridos pelo cliente reencaminhamento consultas entre redes virtuais para a resolução pelo Azure (proxy do DNS). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de um serviço de aplicações do Azure (aplicação Web, função ou Bot) de nomes a utilizar a integração de rede virtual para VMs ou instâncias de função na mesma rede virtual. |Gerida pelo cliente servidores DNS reencaminhamento consultas entre redes virtuais para a resolução pelo Azure (proxy do DNS). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Nome resolução de aplicações de Web do serviço de aplicações para as VMs na mesma rede virtual. |Gerida pelo cliente servidores DNS reencaminhamento consultas entre redes virtuais para a resolução pelo Azure (proxy do DNS). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Nome resolução de aplicações de Web do serviço de aplicações numa rede virtual para as VMs numa rede virtual diferente. |Gerida pelo cliente servidores DNS reencaminhamento consultas entre redes virtuais para a resolução pelo Azure (proxy do DNS). Consulte a resolução de nomes através de seu próprio servidor DNS. |Apenas FQDN |
| Resolução de nomes de computador e o serviço no local de VMs ou instâncias de função no Azure. |Gerida pelo cliente servidores DNS (controlador de domínio no local, controlador de domínio só de leitura local ou um DNS secundário sincronizados com transferências de zona, por exemplo). Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de anfitrião do Azure a partir de computadores no local. |Consultas de encaminhamento para um servidor de proxy gerida pelo cliente DNS na rede virtual correspondente, o servidor proxy encaminha consultas para o Azure para a resolução. Ver [resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Apenas FQDN |
| DNS inverso para os IPs interno. |[Resolução de nomes através de seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Não aplicável |
| Resolução de nomes entre VMs ou instâncias de função localizadas nos serviços de cloud diferentes, não numa rede virtual. |Não aplicável. Conectividade entre VMs e instâncias de função nos serviços cloud diferentes não é suportada fora de uma rede virtual. |Não aplicável|

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure

Juntamente com a resolução de nomes DNS públicos, o Azure oferece resolução de nomes internos para VMs e instâncias de função que residem dentro da mesma rede virtual ou serviço em nuvem. VMs e instâncias de um serviço em nuvem partilham o mesmo sufixo DNS, pelo que o nome de anfitrião autónomo é suficiente. Mas em redes virtuais implementadas com o modelo de implementação clássica, serviços cloud diferentes têm sufixos DNS distintos. Nesta situação, terá do FQDN para resolver nomes entre diferentes serviços cloud. Em redes virtuais implementadas com o modelo de implementação Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual, pelo que o FQDN não é necessária. Nomes DNS podem ser atribuídos a VMs e interfaces de rede. Embora a resolução de nomes fornecida pelo Azure não necessita de qualquer configuração, não é a opção adequada para todos os cenários de implementação, conforme detalhado na tabela anterior.

> [!NOTE]
> Quando utilizar o cloud services funções web e de trabalho, também pode acessar os endereços IP internos de instâncias de função usando a API de REST de gestão de serviço do Azure. Para obter mais informações, consulte a [referência de API do REST de gestão do serviço](https://msdn.microsoft.com/library/azure/ee460799.aspx). O endereço baseia-se o número de nome e a instância de função. 
>
>

### <a name="features"></a>Funcionalidades

Resolução de nomes fornecida pelo Azure inclui as seguintes funcionalidades:
* Facilidade de utilização. É necessária nenhuma configuração.
* Elevada disponibilidade. Não precisa de criar e gerir clusters de seus próprios servidores DNS.
* Pode utilizar o serviço em conjunto com seus próprios servidores DNS, para resolver os nomes de anfitrião do Azure e no local.
* Pode utilizar a resolução de nomes entre VMs e instâncias de função no mesmo serviço cloud, sem a necessidade de um FQDN.
* Pode utilizar a resolução de nomes entre VMs nas redes virtuais que utilizam o modelo de implementação Azure Resource Manager, sem necessidade de um FQDN. Redes virtuais no modelo de implementação clássica requerem um FQDN, quando estiver resolvendo nomes nos serviços cloud diferentes. 
* Pode utilizar nomes de anfitriões que melhor descrevem as suas implementações, em vez de trabalhar com nomes gerado automaticamente.

### <a name="considerations"></a>Considerações

Pontos a considerar quando estiver a utilizar a resolução de nomes fornecida pelo Azure:
* Não é possível modificar o sufixo DNS do Azure criou.
* Não é possível registar manualmente os seus próprios registos.
* WINS e NetBIOS não são suportadas. Não é possível ver as suas VMs no Windows Explorer.
* Os nomes de anfitrião tem de ser compatível com o DNS. Os nomes têm de utilizar apenas 0-9, a-z, e "-" e não pode começar nem terminar com um "-".
* O tráfego de consulta DNS está limitado para cada VM. A limitação não deve afetar a maioria dos aplicativos. Se a limitação de pedidos é observada, certifique-se de que a colocação em cache do lado do cliente está ativada. Para obter mais informações, consulte [configuração do cliente DNS](#dns-client-configuration).
* Apenas as VMs nos serviços 180 cloud primeiro são registadas para cada rede virtual num modelo de implementação clássica. Este limite não é aplicável às redes virtuais no Azure Resource Manager.

## <a name="dns-client-configuration"></a>Configuração de cliente DNS

Esta secção abrange as repetições do lado do cliente e de colocação em cache do lado do cliente.

### <a name="client-side-caching"></a>Colocação em cache do lado do cliente

Nem todas as consultas DNS tem de ser enviado através da rede. Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar a resiliência de blips de rede, resolvendo recorrentes consultas DNS de um cache local. Registos DNS contém um mecanismo de (TTL) time-to-live, o que permite que o cache para armazenar o registo por quanto tempo for possível sem afetar a atualização de registo. Portanto, a colocação em cache do lado do cliente é adequada para a maioria das situações.

A predefinição do cliente DNS do Windows tem um cache DNS interno. Algumas distribuições de Linux não incluem a colocação em cache por predefinição. Se achar que existem não é um cache local já, adicione uma cache DNS para cada VM do Linux.

Há uma série de DNS diferentes pacotes disponíveis (por exemplo, dnsmasq) a colocação em cache. Eis como instalar dnsmasq sobre as distribuições mais comuns:

* **Ubuntu (utiliza resolvconf)**:
  * Instalar o pacote de dnsmasq com `sudo apt-get install dnsmasq`.
* **SUSE (utiliza netconf)**:
  * Instalar o pacote de dnsmasq com `sudo zypper install dnsmasq`.
  * Ativar o serviço de dnsmasq com `systemctl enable dnsmasq.service`. 
  * Iniciar o serviço de dnsmasq com `systemctl start dnsmasq.service`. 
  * Editar **/etc/sysconfig/network/config**e altere *NETCONFIG_DNS_FORWARDER = ""* para *dnsmasq*.
  * Atualizar resolv com `netconfig update`, para definir a cache como o resolvedor DNS local.
* **OpenLogic (utiliza NetworkManager)**:
  * Instalar o pacote de dnsmasq com `sudo yum install dnsmasq`.
  * Ativar o serviço de dnsmasq com `systemctl enable dnsmasq.service`.
  * Iniciar o serviço de dnsmasq com `systemctl start dnsmasq.service`.
  * Adicione *preceder o nome-domínio-servidores 127.0.0.1;* ao **/etc/dhclient-eth0.conf**.
  * Reinicie o serviço de rede com `service network restart`, para definir a cache como o resolvedor DNS local.

> [!NOTE]
> O pacote de dnsmasq é apenas uma das muitas caches DNS disponíveis para o Linux. Antes de o utilizar, verifique a respetiva adequação para suas necessidades particulares e verifique se nenhum outro cache está instalado.
>
>
    
### <a name="client-side-retries"></a>Repetições do lado do cliente

O DNS é principalmente um protocolo UDP. Uma vez que o protocolo UDP não garante a entrega de mensagens, a lógica de repetição é processada no próprio protocolo DNS. Cada cliente DNS (sistema operativo) pode usar a lógica de repetição diferentes, dependendo da preferência o criador:

* Sistemas operativos do Windows novamente após um segundo e, em seguida, novamente após a outra dois segundos, quatro segundos e outro quatro segundos. 
* As repetições a configuração do Linux predefinida que se depois de cinco segundos. Recomendamos que alterar as especificações de repetição para cinco vezes, em intervalos de um segundo.

Verificar as configurações atuais numa VM do Linux com `cat /etc/resolv.conf`. Examinar os *opções* linha, por exemplo:

```bash
options timeout:1 attempts:5
```

O ficheiro resolv é normalmente gerado automaticamente e não deve ser editado. Os passos específicos para adicionar o *opções* linha variam consoante a distribuição:

* **Ubuntu** (utiliza resolvconf):
  1. Adicionar a *opções* linha para **/etc/resolvconf/resolv.conf.d/tail**.
  2. Executar `resolvconf -u` para atualizar.
* **SUSE** (utiliza netconf):
  1. Adicione *timeout:1 tentativas: 5* para o **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parâmetro na **/etc/sysconfig/network/config**.
  2. Executar `netconfig update` para atualizar.
* **OpenLogic** (utiliza NetworkManager):
  1. Adicione *eco "timeout:1 tentativas: 5 opções"* ao **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Atualizar com `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolução de nomes que utiliza o seu próprio servidor DNS

Esta secção abrange as VMs, instâncias de função e as aplicações web.

### <a name="vms-and-role-instances"></a>VMs e instâncias de função

Suas necessidades de resolução de nome podem ir além das funcionalidades fornecidas pelo Azure. Por exemplo, poderá ter de utilizar domínios do Active Directory do Microsoft Windows Server, resolver nomes DNS entre redes virtuais. Para abranger esses cenários, o Azure proporciona a capacidade para utilizar os seus próprios servidores DNS.

Servidores DNS dentro de uma rede virtual podem encaminhar consultas DNS para resoluções de recursiva no Azure. Isto permite-lhe resolver os nomes de anfitrião nessa rede virtual. Por exemplo, um controlador de domínio (DC) em execução no Azure pode responder a consultas DNS para os seus domínios e reencaminhar todas as outras consultas para o Azure. Consultas de reencaminhamento, permite que as VMs ver os seus recursos no local (por meio do controlador de domínio) e nomes de host fornecido com o Azure (por meio de reencaminhador). Acesso para as resoluções recursivas no Azure é fornecido através do IP virtual 168.63.129.16.

Encaminhamento de DNS também permite a resolução DNS entre redes virtuais e permite que as máquinas no local resolver os nomes de host fornecido com o Azure. Para resolver o nome de anfitrião de uma VM, VM do servidor DNS tem de residir na mesma rede virtual e de ser configurado para consultas de nome de anfitrião de encaminhamento para o Azure. Porque o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de reencaminhamento condicional para enviar consultas DNS para a rede virtual correta para a resolução. A imagem seguinte mostra duas redes virtuais e uma rede no local, fazendo a resolução de DNS entre redes virtuais, através deste método. Um reencaminhador DNS de exemplo está disponível na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Uma instância de função pode efetuar a resolução de nomes de VMs dentro da mesma rede virtual. Ele faz isso usando o FQDN, que consiste em nome de anfitrião da VM e **internal.cloudapp.net** sufixo DNS. No entanto, neste caso, a resolução de nomes apenas é bem-sucedida se a instância de função tem o nome da VM definido no [esquema de função (ficheiro. cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> <Role name="<role-name>" vmName="<vm-name>">
>
> Instâncias de função que precisam para executar a resolução de nomes de VMs em redes virtuais (FQDN, utilizando o **internal.cloudapp.net** sufixo) tem de fazer isso usando o método descrito nesta secção (servidores DNS personalizados reencaminhamento entre as duas redes virtuais).
>

![Diagrama do DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando estiver a utilizar a resolução de nomes fornecida pelo Azure, Azure anfitrião configuração protocolo DHCP (Dynamic) fornece um sufixo DNS interno (**. internal.cloudapp.net**) para cada VM. Este sufixo permite que a resolução de nomes de anfitrião porque os registos de nome de anfitrião estão no **internal.cloudapp.net** zona. Quando estiver a utilizar sua própria solução de resolução de nome, este sufixo não é fornecido para as VMs porque ele interfere com outras arquiteturas DNS (como nos cenários associados a um domínio). Em vez disso, o Azure fornece um marcador de posição não funcional (*reddog.microsoft.com*).

Se necessário, é possível determinar o sufixo DNS interno com o PowerShell ou a API:

* Para redes virtuais em modelos de implementação Azure Resource Manager, o sufixo está disponível por meio do [interface de rede REST API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), o [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) cmdlet do PowerShell e o [ show de nic de rede de AZ](/cli/azure/network/nic#az-network-nic-show) comando da CLI do Azure.
* Em modelos de implementação clássica, o sufixo está disponível através da [obter API de implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) chamar ou o [Get-AzureVM-depurar](/powershell/module/servicemanagement/azure/get-azurevm) cmdlet.

Se o reencaminhamento de consultas para o Azure não se adequar às suas necessidades, deve fornecer sua própria solução DNS. Sua solução DNS tem de:

* Fornecer resolução de nomes de anfitrião adequado por meio [DDNS](virtual-networks-name-resolution-ddns.md), por exemplo. Se estiver a utilizar DDNS, poderá ter de desativar a eliminação do registo DNS. Concessões DHCP do Azure são longas e eliminação poderá remover registos DNS prematuramente. 
* Fornece resolução recursiva adequadas para permitir a resolução de nomes de domínio externo.
* Ser acessível (TCP e UDP na porta 53) dos clientes funciona e ser capaz de aceder à internet.
* Estar protegida contra acesso a partir da internet, para atenuar as ameaças impostas por agentes externos.

> [!NOTE]
> Para obter melhor desempenho, quando estiver a utilizar as VMs do Azure como servidores DNS, IPv6 devem ser desativados. R [endereço IP público](virtual-network-public-ip-address.md) deve ser atribuída a cada VM do servidor DNS. Para análise de desempenho adicionais e otimizações quando estiver a utilizar o Windows Server que o seu servidor DNS, consulte [nomear o desempenho de resolução de um recursiva Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Web Apps
Suponha que precisa efetuar a resolução de nome da sua aplicação web criada pelo serviço de aplicações, ligada a uma rede virtual, para as VMs na mesma rede virtual. Para além de configurar um DNS personalizado o servidor que tenha um reencaminhador DNS que encaminha consultas para o Azure (virtual IP 168.63.129.16), execute os seguintes passos:
1. Ativar a integração da rede virtual para a sua aplicação web, se não fez, conforme descrito em [integrar a sua aplicação com uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. No portal do Azure, para o plano do serviço de aplicações que aloja a aplicação web, selecione **rede de sincronização** sob **redes**, **integração da rede Virtual**.

    ![Captura de ecrã da resolução de nomes de rede virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se precisar de efetuar a resolução de nome da sua aplicação web criada pelo serviço de aplicações, ligada a uma rede virtual, para as VMs numa rede virtual diferente, terá de utilizar servidores DNS personalizados em ambas as redes virtuais, da seguinte forma:

* Configure um servidor DNS na sua rede virtual de destino, numa VM que também pode reencaminhar consultas para o resolvedor recursivo no Azure (virtual IP 168.63.129.16). Um reencaminhador DNS de exemplo está disponível na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Configure um reencaminhador DNS na rede virtual de origem, numa VM. Configure este reencaminhador DNS para encaminhar consultas para o servidor DNS na sua rede virtual de destino.
* Configure o servidor de DNS de origem nas definições de origem da sua rede virtual.
* Ativar a integração da rede virtual para a sua aplicação web ligar à rede virtual de origem, seguindo as instruções em [integrar a sua aplicação com uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* No portal do Azure, para o plano do serviço de aplicações que aloja a aplicação web, selecione **rede de sincronização** sob **redes**, **integração da rede Virtual**.

## <a name="specify-dns-servers"></a>Especifique os servidores DNS
Quando estiver a utilizar seus próprios servidores DNS, o Azure fornece a capacidade de especificar múltiplos servidores DNS por rede virtual. Também é possível especificar vários servidores DNS por interface de rede (para o Azure Resource Manager), ou por serviço cloud (para o modelo de implementação clássica). Servidores DNS especificados para um serviço de interface ou na cloud de rede tenha precedência mais servidores DNS especificados para a rede virtual.

> [!NOTE]
> Propriedades de ligação de rede, como o servidor DNS IPs, não devem ser editadas diretamente dentro de VMs do Windows. Isto acontece porque eles podem obter apagados durante o serviço de tratá-lo quando o adaptador de rede virtual será substituído.
>
>

Quando estiver a utilizar o modelo de implementação Azure Resource Manager, pode especificar servidores DNS para uma rede virtual e uma interface de rede. Para obter detalhes, consulte [gerir uma rede virtual](manage-virtual-network.md) e [gerir uma interface de rede](virtual-network-network-interface.md).

> [!NOTE]
> Se optar pelo servidor DNS personalizado para a rede virtual, tem de especificar o endereço IP do servidor DNS, pelo menos, um; caso contrário, rede virtual irá ignorar a configuração e utilização do DNS fornecida pelo Azure em vez disso.
>
>

Quando estiver a utilizar o modelo de implementação clássica, pode especificar servidores DNS para a rede virtual no portal do Azure ou o [ficheiro de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100). Serviços cloud, pode especificar servidores DNS via o [ficheiro de configuração de serviço](https://msdn.microsoft.com/library/azure/ee758710) ou com o PowerShell, com [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Se alterar as definições de DNS para uma rede virtual ou máquina virtual que já tenha sido implementada, terá de reiniciar cada VM afetada para que as alterações entrem em vigor.
>
>

## <a name="next-steps"></a>Passos Seguintes

Modelo de implementação do Gestor de recursos do Azure:

* [Gerir uma rede virtual](manage-virtual-network.md)
* [Gerir uma interface de rede](virtual-network-network-interface.md)

Modelo de implementação clássica:

* [Esquema de configuração de serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurar uma rede Virtual, utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md)