---
title: Resolução de nomes para recursos em redes virtuais do Azure
titlesuffix: Azure Virtual Network
description: Cenários de resolução de nomes para Azure IaaS, soluções híbridas, entre diferentes serviços de nuvem, Active Directory e usando seu próprio servidor DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: rohink
ms.openlocfilehash: ebacd386221ed12e1171034eb5d23236bd234849
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176049"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Resolução de nomes para recursos em redes virtuais do Azure

Dependendo de como você usa o Azure para hospedar as soluções de IaaS, PaaS e híbrido, talvez seja necessário permitir que as máquinas virtuais (VMs) e outros recursos implantados em uma rede virtual se comuniquem entre si. Embora você possa habilitar a comunicação usando endereços IP, é muito mais simples usar nomes que possam ser facilmente lembrados e não alterar. 

Quando os recursos implementados nas redes virtuais têm de resolver os nomes de domínio para endereços IP internos, só podem utilizar um método de dois:

* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução de nomes que usa seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server) (que pode encaminhar consultas para os servidores DNS fornecidos pelo Azure)

O tipo de resolução de nomes que utiliza depende do modo como os recursos têm de comunicar entre si. A tabela a seguir ilustra os cenários e as soluções de resolução de nomes correspondentes:

> [!NOTE]
> Dependendo do seu cenário, talvez você queira usar o recurso Zonas Privadas do DNS do Azure, que está atualmente em visualização pública. Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](../dns/private-dns-overview.md).
>

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nomes entre as VMs localizadas na mesma rede virtual ou instâncias de função dos serviços de nuvem do Azure no mesmo serviço de nuvem. | Resolução de nomes [zonas privadas do DNS do Azure](../dns/private-dns-overview.md) ou [fornecida pelo Azure](#azure-provided-name-resolution) |Nome do host ou FQDN |
| Resolução de nomes entre VMs em diferentes redes virtuais ou instâncias de função em diferentes serviços de nuvem. |Os servidores DNS [zonas privadas do DNS do Azure](../dns/private-dns-overview.md) ou gerenciados pelo cliente encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de um serviço de Azure App (aplicativo Web, função ou bot) usando a integração de rede virtual para instâncias de função ou VMs na mesma rede virtual. |Os servidores DNS gerenciados pelo cliente encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de aplicativos Web do serviço de aplicativo para VMs na mesma rede virtual. |Os servidores DNS gerenciados pelo cliente encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de aplicativos Web do serviço de aplicativo em uma rede virtual para VMs em uma rede virtual diferente. |Os servidores DNS gerenciados pelo cliente encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de computadores e serviços locais de VMs ou instâncias de função no Azure. |Servidores DNS gerenciados pelo cliente (controlador de domínio local, controlador de domínio somente leitura ou um DNS secundário sincronizado usando transferências de zona, por exemplo). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de host do Azure de computadores locais. |Encaminhe consultas para um servidor de proxy DNS gerenciado pelo cliente na rede virtual correspondente, o servidor proxy encaminha as consultas para a resolução do Azure. Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| DNS reverso para IPs internos. |[Resolução de nomes usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Não aplicável |
| Resolução de nomes entre VMs ou instâncias de função localizadas em diferentes serviços de nuvem, não em uma rede virtual. |Não aplicável. Não há suporte para a conectividade entre VMs e instâncias de função em serviços de nuvem diferentes fora de uma rede virtual. |Não aplicável|

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure

Juntamente com a resolução de nomes DNS públicos, o Azure fornece a resolução de nomes interna para VMs e instâncias de função que residem na mesma rede virtual ou serviço de nuvem. As VMs e instâncias em um serviço de nuvem compartilham o mesmo sufixo DNS, portanto, o nome do host sozinho é suficiente. Mas, em redes virtuais implantadas usando o modelo de implantação clássico, diferentes serviços de nuvem têm diferentes sufixos DNS. Nessa situação, você precisa do FQDN para resolver nomes entre diferentes serviços de nuvem. Em redes virtuais implantadas usando o modelo de implantação Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual e, portanto, o FQDN não é necessário. Os nomes DNS podem ser atribuídos às VMs e às interfaces de rede. Embora a resolução de nomes fornecida pelo Azure não exija nenhuma configuração, ela não é a opção apropriada para todos os cenários de implantação, conforme detalhado na tabela anterior.

> [!NOTE]
> Ao usar as funções Web e de trabalho dos serviços de nuvem, você também pode acessar os endereços IP internos das instâncias de função usando a API REST de gerenciamento de serviços do Azure. Para obter mais informações, consulte a [referência da API REST do gerenciamento de serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx). O endereço é baseado no nome da função e no número da instância. 
>
>

### <a name="features"></a>Funcionalidades

A resolução de nomes fornecida pelo Azure inclui os seguintes recursos:
* Facilidade de uso. Nenhuma configuração é necessária.
* Elevada disponibilidade. Você não precisa criar e gerenciar clusters de seus próprios servidores DNS.
* Você pode usar o serviço em conjunto com seus próprios servidores DNS, para resolver nomes de host locais e do Azure.
* Você pode usar a resolução de nomes entre VMs e instâncias de função dentro do mesmo serviço de nuvem, sem a necessidade de um FQDN.
* Você pode usar a resolução de nomes entre VMs em redes virtuais que usam o modelo de implantação Azure Resource Manager, sem a necessidade de um FQDN. As redes virtuais no modelo de implantação clássico exigem um FQDN quando você está resolvendo nomes em diferentes serviços de nuvem. 
* Você pode usar nomes de host que melhor descrevem suas implantações, em vez de trabalhar com nomes gerados automaticamente.

### <a name="considerations"></a>Considerações

Pontos a serem considerados quando você estiver usando a resolução de nomes fornecida pelo Azure:
* O sufixo DNS criado pelo Azure não pode ser modificado.
* Você não pode registrar seus próprios registros manualmente.
* Não há suporte para WINS e NetBIOS. Você não pode ver suas VMs no Windows Explorer.
* Os nomes de host devem ser compatíveis com DNS. Os nomes devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'.
* O tráfego de consulta DNS é limitado para cada VM. A limitação não deve afetar a maioria dos aplicativos. Se a limitação da solicitação for observada, verifique se o Caching do lado do cliente está habilitado. Para obter mais informações, consulte [configuração do cliente DNS](#dns-client-configuration).
* Somente as VMs nos primeiros serviços de nuvem 180 são registradas para cada rede virtual em um modelo de implantação clássico. Esse limite não se aplica a redes virtuais no Azure Resource Manager.
* O endereço IP do DNS do Azure é 168.63.129.16. Esse é um endereço IP estático e não será alterado.

## <a name="dns-client-configuration"></a>Configuração do cliente DNS

Esta seção aborda o cache do lado do cliente e as tentativas do lado do cliente.

### <a name="client-side-caching"></a>Cache do lado do cliente

Nem todas as consultas DNS precisam ser enviadas pela rede. O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resiliência para a rede blips, resolvendo consultas de DNS recorrentes de um cache local. Os registros DNS contêm um mecanismo de vida útil (TTL), que permite que o cache armazene o registro pelo máximo de tempo possível sem afetar a atualização do registro. Portanto, o armazenamento em cache do lado do cliente é adequado para a maioria das situações.

O cliente DNS padrão do Windows tem um cache DNS interno. Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. Se você achar que ainda não existe um cache local, adicione um cache DNS a cada VM do Linux.

Há vários pacotes de cache DNS diferentes disponíveis (como dnsmasq). Veja como instalar o dnsmasq nas distribuições mais comuns:

* **Ubuntu (usa resolvconf)** :
  * Instale o pacote dnsmasq com `sudo apt-get install dnsmasq`.
* **SuSE (usa netconf)** :
  * Instale o pacote dnsmasq com `sudo zypper install dnsmasq`.
  * Habilite o serviço dnsmasq com `systemctl enable dnsmasq.service`. 
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`. 
  * Edite **/etc/sysconfig/network/config**e altere *NETCONFIG_DNS_FORWARDER = ""* para *dnsmasq*.
  * Atualize a resolução. conf com `netconfig update`para definir o cache como o resolvedor DNS local.
* **CentOS (usa NetworkManager)** :
  * Instale o pacote dnsmasq com `sudo yum install dnsmasq`.
  * Habilite o serviço dnsmasq com `systemctl enable dnsmasq.service`.
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`.
  * Adicione *domínio-nome-servidores 127.0.0.1;* a **/etc/dhclient-eth0.conf**.
  * Reinicie o serviço de rede com `service network restart`para definir o cache como o resolvedor de DNS local.

> [!NOTE]
> O pacote dnsmasq é apenas um dos muitos caches DNS disponíveis para Linux. Antes de usá-lo, verifique sua adequação para suas necessidades específicas e verifique se nenhum outro cache está instalado.
>
>
    
### <a name="client-side-retries"></a>Tentativas do lado do cliente

O DNS é principalmente um protocolo UDP. Como o protocolo UDP não garante a entrega de mensagens, a lógica de repetição é manipulada no próprio protocolo DNS. Cada cliente DNS (sistema operacional) pode exibir uma lógica de repetição diferente, dependendo da preferência do Criador:

* Os sistemas operacionais Windows tentam novamente após um segundo e, depois, novamente após outros dois segundos, quatro segundos e outros quatro segundos. 
* A instalação padrão do Linux tenta novamente após cinco segundos. É recomendável alterar as especificações de repetição para cinco vezes, em intervalos de um segundo.

Verifique as configurações atuais em uma VM Linux com `cat /etc/resolv.conf`. Examine a linha de *Opções* , por exemplo:

```bash
options timeout:1 attempts:5
```

O arquivo de resolução. conf geralmente é gerado automaticamente e não deve ser editado. As etapas específicas para adicionar a linha de *Opções* variam de acordo com a distribuição:

* **Ubuntu** (usa resolvconf):
  1. Adicione a linha de *Opções* a **/etc/resolvconf/resolv.conf.d/tail**.
  2. Execute `resolvconf -u` para atualizar.
* **SuSE** (usa netconf):
  1. Adicionar *tempo limite: 1 tentativas: 5* ao parâmetro **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** em **/etc/sysconfig/network/config**.
  2. Execute `netconfig update` para atualizar.
* **CentOS** (usa NetworkManager):
  1. Adicione *echo "opções timeout: 1 tentativas: 5"* a **/etc/NetworkManager/Dispatcher.d/11-dhclient**.
  2. Atualize com `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolução de nomes que usa seu próprio servidor DNS

Esta seção aborda as VMs, as instâncias de função e os aplicativos Web.

### <a name="vms-and-role-instances"></a>VMs e instâncias de função

Suas necessidades de resolução de nomes podem ir além dos recursos fornecidos pelo Azure. Por exemplo, talvez seja necessário usar domínios Active Directory do Microsoft Windows Server, resolver nomes DNS entre redes virtuais. Para abordar esses cenários, o Azure fornece a capacidade de usar seus próprios servidores DNS.

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos no Azure. Isso permite que você resolva os nomes de host dentro dessa rede virtual. Por exemplo, um controlador de domínio (DC) em execução no Azure pode responder a consultas DNS para seus domínios e encaminhar todas as outras consultas para o Azure. O encaminhamento de consultas permite que as VMs vejam os recursos locais (por meio do DC) e os nomes de host fornecidos pelo Azure (por meio do encaminhador). O acesso aos resolvedores recursivos no Azure é fornecido por meio do 168.63.129.16 de IP virtual.

O encaminhamento de DNS também permite a resolução DNS entre redes virtuais e permite que seus computadores locais resolvam nomes de host fornecidos pelo Azure. Para resolver o nome de host de uma VM, a VM do servidor DNS deve residir na mesma rede virtual e ser configurada para encaminhar consultas de nome de host para o Azure. Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicional para enviar consultas DNS para a rede virtual correta para resolução. A imagem a seguir mostra duas redes virtuais e uma rede local fazendo a resolução DNS entre redes virtuais, usando esse método. Um encaminhador DNS de exemplo está disponível na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Uma instância de função pode executar A resolução de nomes de VMs na mesma rede virtual. Ele faz isso usando o FQDN, que consiste no nome do host da VM e no sufixo DNS **Internal.cloudapp.net** . No entanto, nesse caso, a resolução de nome só será bem-sucedida se a instância de função tiver o nome da VM definido no [esquema de função (arquivo. cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> As instâncias de função que precisam executar a resolução de nomes de VMs em outra rede virtual (FQDN usando o sufixo **Internal.cloudapp.net** ) precisam fazer isso usando o método descrito nesta seção (os servidores DNS personalizados encaminham entre os dois virtuais redes).
>

![Diagrama de DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando você estiver usando a resolução de nomes fornecida pelo Azure, o protocolo DHCP fornece um sufixo DNS interno ( **. Internal.cloudapp.net**) para cada VM. Esse sufixo habilita a resolução de nomes de host porque os registros de nome de host estão na zona **Internal.cloudapp.net** . Quando você está usando sua própria solução de resolução de nomes, esse sufixo não é fornecido para VMs porque ele interfere em outras arquiteturas de DNS (como cenários ingressados no domínio). Em vez disso, o Azure fornece um espaço reservado não funcional (*reddog.Microsoft.com*).

Se necessário, você pode determinar o sufixo DNS interno usando o PowerShell ou a API:

* Para redes virtuais em modelos de implantação Azure Resource Manager, o sufixo está disponível por meio da [API REST da interface de rede](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), do cmdlet [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) do PowerShell e do comando [AZ Network NIC show](/cli/azure/network/nic#az-network-nic-show) CLI do Azure.
* Em modelos de implantação clássicas, o sufixo está disponível por meio da chamada à [API obter implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou do cmdlet [Get-AzureVM-Debug](/powershell/module/servicemanagement/azure/get-azurevm) .

Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você deverá fornecer sua própria solução de DNS. Sua solução DNS precisa:

* Forneça a resolução de nome de host apropriada, por exemplo, por meio de [DDNS](virtual-networks-name-resolution-ddns.md). Se você estiver usando DDNS, talvez seja necessário desabilitar a limpeza de registro DNS. As concessões de DHCP do Azure são longas e a eliminação pode remover os registros DNS prematuramente. 
* Forneça a resolução recursiva adequada para permitir a resolução de nomes de domínio externos.
* Ser acessível (TCP e UDP na porta 53) dos clientes que ele atende e ser capaz de acessar a Internet.
* Seja protegido contra acesso da Internet, para reduzir as ameaças representadas por agentes externos.

> [!NOTE]
> Para obter o melhor desempenho, quando você estiver usando VMs do Azure como servidores DNS, o IPv6 deverá ser desabilitado. Um [endereço IP público](virtual-network-public-ip-address.md) deve ser atribuído a cada VM do servidor DNS. Para análise de desempenho adicional e otimizações quando você estiver usando o Windows Server como seu servidor DNS, consulte [desempenho de resolução de nomes de um servidor DNS do Windows 2012 R2 recursivo](https://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Aplicações Web
Suponha que você precise executar a resolução de nomes de seu aplicativo Web criado usando o serviço de aplicativo, vinculado a uma rede virtual, às VMs na mesma rede virtual. Além de configurar um servidor DNS personalizado que tem um encaminhador DNS que encaminha consultas para o Azure (168.63.129.16 de IP virtual), execute as seguintes etapas:
1. Habilite a integração de rede virtual para seu aplicativo Web, se ainda não tiver feito, conforme descrito em [integrar seu aplicativo a uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. No portal do Azure, para o plano do serviço de aplicativo que hospeda o aplicativo Web, selecione **sincronizar rede** em **rede**, **integração de rede virtual**.

    ![Captura de tela da resolução de nomes de rede virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se você precisar executar a resolução de nomes de seu aplicativo Web criado usando o serviço de aplicativo, vinculado a uma rede virtual, a VMs em uma rede virtual diferente, será necessário usar servidores DNS personalizados em ambas as redes virtuais, da seguinte maneira:

* Configure um servidor DNS em sua rede virtual de destino, em uma VM que também pode encaminhar consultas para o resolvedor recursivo no Azure (168.63.129.16 de IP virtual). Um encaminhador DNS de exemplo está disponível na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Configure um encaminhador DNS na rede virtual de origem em uma VM. Configure esse encaminhador DNS para encaminhar consultas para o servidor DNS em sua rede virtual de destino.
* Configure o servidor DNS de origem nas configurações da sua rede virtual de origem.
* Habilite a integração de rede virtual para seu aplicativo Web para vincular à rede virtual de origem, seguindo as instruções em [integrar seu aplicativo a uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* No portal do Azure, para o plano do serviço de aplicativo que hospeda o aplicativo Web, selecione **sincronizar rede** em **rede**, **integração de rede virtual**.

## <a name="specify-dns-servers"></a>Especificar servidores DNS
Quando você estiver usando seus próprios servidores DNS, o Azure fornecerá a capacidade de especificar vários servidores DNS por rede virtual. Também pode especificar vários servidores DNS por interface de rede (para o Azure Resource Manager) ou por serviço cloud (para o modelo de implementação clássico). Os servidores DNS especificados para uma interface de rede ou serviço de nuvem obtêm precedência sobre os servidores DNS especificados para a rede virtual.

> [!NOTE]
> As propriedades de conexão de rede, como IPs do servidor DNS, não devem ser editadas diretamente nas VMs. Isso ocorre porque eles podem ser apagados durante a reparo do serviço quando o adaptador de rede virtual é substituído. Isso se aplica a VMs Windows e Linux.
>
>

Ao usar o modelo de implantação Azure Resource Manager, você pode especificar servidores DNS para uma rede virtual e uma interface de rede. Para obter detalhes, consulte [gerenciar uma rede virtual](manage-virtual-network.md) e [gerenciar uma interface de rede](virtual-network-network-interface.md).

> [!NOTE]
> Se você optar pelo servidor DNS personalizado para sua rede virtual, deverá especificar pelo menos um endereço IP do servidor DNS; caso contrário, a rede virtual ignorará a configuração e usará o DNS fornecido pelo Azure.
>
>

Ao usar o modelo de implantação clássico, você pode especificar servidores DNS para a rede virtual no portal do Azure ou no arquivo de [configuração de rede](https://msdn.microsoft.com/library/azure/jj157100). Para serviços de nuvem, você pode especificar servidores DNS por meio do [arquivo de configuração de serviço](https://msdn.microsoft.com/library/azure/ee758710) ou usando o PowerShell, com [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Se você alterar as configurações de DNS para uma rede virtual ou máquina virtual que já está implantada, será necessário reiniciar cada VM afetada para que as alterações entrem em vigor.
>
>

## <a name="next-steps"></a>Passos seguintes

Modelo de implantação de Azure Resource Manager:

* [Gerenciar uma rede virtual](manage-virtual-network.md)
* [Gerenciar uma interface de rede](virtual-network-network-interface.md)

Modelo de implantação clássico:

* [Esquema de configuração do serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md)
