---
title: Opções de resolução de nomes DNS para máquinas virtuais Linux no Azure
description: Cenários de resolução de nomes para máquinas virtuais do Linux no Azure IaaS, incluindo serviços DNS fornecidos, DNS externo híbrido e traga seu próprio servidor DNS.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: gwallace
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 16dc7d16b3e8f2a4c95e93f9b85c74027291ce19
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084030"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para máquinas virtuais Linux no Azure
O Azure fornece a resolução de nomes DNS por padrão para todas as máquinas virtuais que estão em uma única rede virtual. Você pode implementar sua própria solução de resolução de nomes DNS configurando seus próprios serviços DNS em suas máquinas virtuais que o Azure hospeda. Os cenários a seguir devem ajudá-lo a escolher aquele que funciona para sua situação.

* [Resolução de nomes que o Azure fornece](#name-resolution-that-azure-provides)
* [Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que você usa depende de como suas máquinas virtuais e instâncias de função precisam se comunicar entre si.

A tabela a seguir ilustra os cenários e as soluções de resolução de nomes correspondentes:

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nomes entre instâncias de função ou máquinas virtuais na mesma rede virtual |Resolução de nomes que o Azure fornece |hostname ou nome de domínio totalmente qualificado (FQDN) |
| Resolução de nomes entre instâncias de função ou máquinas virtuais em diferentes redes virtuais |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| Resolução de computadores locais e nomes de serviço de instâncias de função ou máquinas virtuais no Azure |Servidores DNS gerenciados pelo cliente (por exemplo, controladores de domínio locais, controlador de domínio somente leitura local ou um DNS secundário sincronizado usando transferências de zona). Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de host do Azure de computadores locais |Encaminhe consultas para um servidor de proxy DNS gerenciado pelo cliente na rede virtual correspondente. O servidor proxy encaminha as consultas para a resolução do Azure. Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| DNS reverso para IPs internos |[Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nomes que o Azure fornece
Juntamente com a resolução de nomes DNS públicos, o Azure fornece a resolução de nomes interna para máquinas virtuais e instâncias de função que estão na mesma rede virtual. Em redes virtuais baseadas em Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual; o FQDN não é necessário. Os nomes DNS podem ser atribuídos a NICs (placas de interface de rede) e máquinas virtuais. Embora a resolução de nomes que o Azure fornece não exija nenhuma configuração, ela não é a opção apropriada para todos os cenários de implantação, como visto na tabela anterior.

### <a name="features-and-considerations"></a>Recursos e considerações
**Features**

* Nenhuma configuração é necessária para usar a resolução de nomes fornecida pelo Azure.
* O serviço de resolução de nomes que o Azure fornece está altamente disponível. Você não precisa criar e gerenciar clusters de seus próprios servidores DNS.
* O serviço de resolução de nomes que o Azure fornece pode ser usado junto com seus próprios servidores DNS para resolver nomes de host locais e do Azure.
* A resolução de nomes é fornecida entre máquinas virtuais em redes virtuais sem a necessidade de FQDN.
* Você pode usar nomes de host que descrevam melhor suas implantações em vez de trabalhar com nome gerados automaticamente.

**Considere**

* O sufixo DNS criado pelo Azure não pode ser modificado.
* Você não pode registrar seus próprios registros manualmente.
* Não há suporte para WINS e NetBIOS.
* Os nomes de host devem ser compatíveis com DNS.
    Os nomes devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'. Consulte a RFC 3696 seção 2.
* O tráfego de consulta DNS é limitado para cada máquina virtual. A limitação não deve afetar a maioria dos aplicativos.  Se a limitação da solicitação for observada, verifique se o Caching do lado do cliente está habilitado.  Para obter mais informações, consulte [obtendo o máximo da resolução de nomes que o Azure fornece](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Obtendo o máximo da resolução de nomes que o Azure fornece
**Cache do lado do cliente:**

Algumas consultas DNS não são enviadas pela rede. O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resiliência para inconsistências de rede resolvendo consultas de DNS recorrentes de um cache local. Os registros DNS contêm uma TTL (vida útil), que permite que o cache armazene o registro pelo máximo de tempo possível sem afetar a atualização do registro. Como resultado, o armazenamento em cache do lado do cliente é adequado para a maioria das situações.

Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. É recomendável que você adicione um cache a cada máquina virtual Linux depois de verificar se ainda não há um cache local.

Vários pacotes de cache DNS diferentes, como dnsmasq, estão disponíveis. Aqui estão as etapas para instalar o dnsmasq nas distribuições mais comuns:

**Ubuntu (usa resolvconf)**
  * Instale o pacote dnsmasq ("sudo apt-get install dnsmasq").

**SuSE (usa netconf)** :
1. Instale o pacote dnsmasq ("sudo zypper install dnsmasq").
2. Habilite o serviço dnsmasq ("systemctl Enable dnsmasq. Service").
3. Inicie o serviço dnsmasq ("systemctl Start dnsmasq. Service").
4. Edite "/etc/sysconfig/network/config" e altere NETCONFIG_DNS_FORWARDER = "" para "dnsmasq".
5. Atualize a resolução. conf ("netconfig Update") para definir o cache como o resolvedor de DNS local.

**CentOS por software de Wave não autorizado (anteriormente OpenLogic; usa NetworkManager)**
1. Instale o pacote dnsmasq ("sudo yum install dnsmasq").
2. Habilite o serviço dnsmasq ("systemctl Enable dnsmasq. Service").
3. Inicie o serviço dnsmasq ("systemctl Start dnsmasq. Service").
4. Adicione "preceder nome-do-domínio-servidores 127.0.0.1;" a "/etc/dhclient-eth0.conf".
5. Reinicie o serviço de rede ("reinicialização de rede de serviço") para definir o cache como o resolvedor de DNS local

> [!NOTE]
> : O pacote ' dnsmasq ' é apenas um dos muitos caches DNS disponíveis para Linux. Antes de usá-lo, verifique sua adequação para suas necessidades e se nenhum outro cache está instalado.
>
>

**Tentativas do lado do cliente**

O DNS é principalmente um protocolo UDP. Como o protocolo UDP não garante a entrega de mensagens, o próprio protocolo DNS lida com a lógica de repetição. Cada cliente DNS (sistema operacional) pode exibir uma lógica de repetição diferente dependendo da preferência do Criador:

* Os sistemas operacionais Windows tentam novamente após um segundo e, depois, novamente após outros dois, quatro e outros quatro segundos.
* A instalação padrão do Linux tenta novamente após cinco segundos.  Você deve alterar isso para repetir cinco vezes em intervalos de um segundo.  

Para verificar as configurações atuais em uma máquina virtual do Linux, ' cat/etc/resolv.conf ' e examine a linha ' Options ', por exemplo:

    options timeout:1 attempts:5

O arquivo. conf de resolução é gerado automaticamente e não deve ser editado. As etapas específicas que adicionam a linha ' Options ' variam de acordo com a distribuição:

**Ubuntu** (usa resolvconf)
1. Adicione a linha de opções a '/etc/resolveconf/resolv.conf.d/Head '.
2. Execute ' resolvconf-u ' para atualizar.

**SuSE** (usa netconf)
1. Adicione ' timeout: 1 tentativas: 5 ' ao parâmetro NETCONFIG_DNS_RESOLVER_OPTIONS = "" em '/etc/sysconfig/network/config '.
2. Execute ' netconfig Update ' para atualizar.

**CentOS por software de Wave não autorizado (anteriormente OpenLogic)** (usa NetworkManager)
1. Adicionar ' RES_OPTIONS = "Timeout: 1 tentativas: 5" ' a '/etc/sysconfig/network '.
2. Execute ' reinicialização de rede de serviço ' para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes usando seu próprio servidor DNS
Suas necessidades de resolução de nomes podem ir além dos recursos que o Azure fornece. Por exemplo, você pode exigir a resolução de DNS entre redes virtuais. Para abordar esse cenário, você pode usar seus próprios servidores DNS.  

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para resolvedores recursivos do Azure para resolver nomes de host que estão na mesma rede virtual. Por exemplo, um servidor DNS que é executado no Azure pode responder a consultas DNS para seus próprios arquivos de zona DNS e encaminhar todas as outras consultas para o Azure. Essa funcionalidade permite que as máquinas virtuais vejam suas entradas em seus arquivos de zona e nomes de host que o Azure fornece (por meio do encaminhador). O acesso aos resolvedores recursivos do Azure é fornecido por meio do 168.63.129.16 de IP virtual.

O encaminhamento de DNS também permite a resolução DNS entre redes virtuais e permite que seus computadores locais resolvam nomes de host fornecidos pelo Azure. Para resolver o nome de host de uma máquina virtual, a máquina virtual do servidor DNS deve residir na mesma rede virtual e ser configurada para encaminhar consultas de nome de host para o Azure. Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicional para enviar consultas DNS para a rede virtual correta para resolução. A imagem a seguir mostra duas redes virtuais e uma rede local fazendo a resolução DNS entre redes virtuais usando este método:

![Resolução DNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Quando você usa a resolução de nomes que o Azure fornece, o sufixo DNS interno é fornecido para cada máquina virtual usando DHCP. Quando você usa sua própria solução de resolução de nomes, esse sufixo não é fornecido para máquinas virtuais porque o sufixo interfere com outras arquiteturas de DNS. Para fazer referência a computadores por FQDN ou configurar o sufixo em suas máquinas virtuais, você pode usar o PowerShell ou a API para determinar o sufixo:

* Para redes virtuais que são gerenciadas pelo Azure Resource Manager, o sufixo está disponível por meio do recurso de [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) . Você também pode executar o `azure network public-ip show <resource group> <pip name>` comando para exibir os detalhes do seu IP público, que inclui o FQDN da NIC.

Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS.  Sua solução DNS precisa:

* Forneça a resolução de nome de host apropriada, por exemplo, por meio de [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se você usar DDNS, talvez seja necessário desabilitar a limpeza de registro DNS. As concessões DHCP do Azure são muito longas e a eliminação pode remover os registros DNS prematuramente.
* Forneça a resolução recursiva adequada para permitir a resolução de nomes de domínio externos.
* Estar acessível (TCP e UDP na porta 53) dos clientes que ele atende e ser capaz de acessar a Internet.
* Seja protegido contra acesso da Internet para atenuar as ameaças causadas por agentes externos.

> [!NOTE]
> Para obter o melhor desempenho, ao usar máquinas virtuais em servidores DNS do Azure, desabilite o IPv6 e atribua um [IP público em nível de instância](../../virtual-network/virtual-networks-instance-level-public-ip.md) a cada máquina virtual do servidor DNS.  
>
>
