---
title: Considerações de networking da Azure Files Microsoft Docs
description: Uma visão geral das opções de networking para Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067156"
---
# <a name="azure-files-networking-considerations"></a>Considerações de networking de Ficheiros Azure 
Pode ligar-se a uma participação de ficheiros Azure de duas formas:

- Aceder diretamente à partilha através dos protocolos SMB ou FileREST. Este padrão de acesso é usado principalmente quando eliminar o maior número possível de servidores no local.
- Criar uma cache da partilha de ficheiros Azure num servidor no local com o Azure File Sync e aceder aos dados da partilha de ficheiros a partir do servidor no local com o seu protocolo de eleição (SMB, NFS, FTPS, etc.) para o seu caso de utilização. Este padrão de acesso é útil porque combina o melhor de ambos os desempenhos no local e escala de nuvem e serviços acopníveis sem servidor, como o Azure Backup.

Este artigo centra-se em como configurar a rede para quando o seu caso de utilização pede acesso diretamente à partilha de ficheiros Azure em vez de utilizar o Azure File Sync. Para obter mais informações sobre considerações de networking para uma implementação de Sincronização de Ficheiros Azure, consulte [configurar as definições](storage-sync-files-firewall-and-proxy.md)de proxy e firewall do Ficheiro Azure .

A configuração de rede para as ações de ficheiros Azure é feita na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes de bolhas ou filas. As contas de armazenamento expõem várias configurações que o ajudam a garantir o acesso à rede às suas partilhas de ficheiros: pontos finais de rede, definições de firewall de conta de armazenamento e encriptação em trânsito. 

Recomendamos a leitura de Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md) antes de ler este guia conceptual.

## <a name="accessing-your-azure-file-shares"></a>Aceder às suas ações de ficheiros Azure
Quando implementa uma parte de ficheiro Azure dentro de uma conta de armazenamento, a sua parte de ficheiro é imediatamente acessível através do ponto final da conta de armazenamento. Isto significa que os pedidos autenticados, tais como pedidos autorizados pela identidade de logon de um utilizador, podem originar-se de forma segura dentro ou fora do Azure. 

Em muitos ambientes de clientes, uma primeira parte do ficheiro Azure na sua estação de trabalho no local falhará, mesmo que os montes de VMs Azure tenham sucesso. A razão para isso é que muitas organizações e fornecedores de serviços de internet (ISPs) bloqueiam a porta que a SMB usa para comunicar, porta 445. Esta prática tem origem em orientações de segurança sobre legados e versões depreciadas do protocolo SMB. Embora o SMB 3.0 seja um protocolo seguro para a Internet, as versões mais antigas de SMB, especialmente SMB 1.0 não são. As ações de ficheiros Azure só podem ser acedidas externamente através do SMB 3.0 e do protocolo FileREST (que também é um protocolo de segurança na Internet) através do ponto final público.

Uma vez que a forma mais fácil de aceder à sua quota de ficheiros Azure a partir do local é abrir a sua rede no local para a porta 445, a Microsoft recomenda os seguintes passos para remover o SMB 1.0 do seu ambiente:

1. Certifique-se de que o SMB 1.0 é removido ou desativado nos dispositivos da sua organização. Todas as versões suportadas atualmente do Windows e do Windows Server suportam a remoção ou desativação do SMB 1.0 e, a começar pelo Windows 10, a versão 1709, o SMB 1.0 não está instalado no Windows por padrão. Para saber mais sobre como desativar o SMB 1.0, consulte as nossas páginas específicas do OS:
    - [Proteger o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Assegurar o Linux](storage-how-to-use-files-linux.md#securing-linux)
2. Certifique-se de que nenhum produto dentro da sua organização requer SMB 1.0 e remova os que o fazem. Mantemos um [SMB1 Product Clearinghouse,](https://aka.ms/stillneedssmb1)que contém todos os produtos de primeira e terceira suposição conhecidas pela Microsoft para exigir SMB 1.0. 
3. (Opcional) Utilize uma firewall de terceiros com a rede no local da sua organização para evitar que o tráfego SMB 1.0 saia do limite organizacional.

Se a sua organização necessitar que o porto 445 seja bloqueado por política ou regulação, ou se a sua organização exigir que o tráfego para o Azure siga um caminho determinístico, pode utilizar o Azure VPN Gateway ou o ExpressRoute para bloquear o tráfego para as suas ações de ficheiros Azure.

> [!Important]  
> Mesmo que decida utilizar um método alternativo para aceder às suas partilhas de ficheiros Azure, a Microsoft ainda recomenda a remoção do SMB 1.0 do seu ambiente.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Túneis de tráfego sobre uma rede privada virtual ou ExpressRoute
Ao estabelecer um túnel de rede entre a sua rede no local e o Azure, está a espreitar a sua rede no local com uma ou mais redes virtuais em Azure. Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que operaria no local. Como uma conta de armazenamento Azure ou um Azure VM, um VNet é um recurso Azure que é implantado num grupo de recursos. 

O Azure Files suporta os seguintes mecanismos para fazer um túnel entre as estações de trabalho e servidores no local e o Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Um gateway VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego encriptado entre uma rede virtual Azure e uma localização alternativa (como no local) através da internet. Um Azure VPN Gateway é um recurso Azure que pode ser implantado num grupo de recursos juntamente com uma conta de armazenamento ou outros recursos Azure. Os gateways VPN expõem dois tipos diferentes de ligações:
    - Ligações de gateway [VPN ponto-a-local (P2S),](../../vpn-gateway/point-to-site-about.md) que são ligações VPN entre o Azure e um cliente individual. Esta solução é principalmente útil para dispositivos que não fazem parte da rede de instalações da sua organização, como os telecommuters que querem ser capazes de montar a sua partilha de ficheiros Azure a partir de casa, um café ou hotel enquanto estão na estrada. Para utilizar uma ligação P2S VPN com Ficheiros Azure, uma ligação P2S VPN terá de ser configurada para cada cliente que queira ligar. Para simplificar a implementação de uma ligação VPN P2S, consulte [Configurar uma VPN ponto-a-local (P2S) no Windows para utilização com Ficheiros Azure](storage-files-configure-p2s-vpn-windows.md) e [configurar uma VPN ponto-a-site (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), que são ligações VPN entre o Azure e a rede da sua organização. Uma ligação VPN S2S permite-lhe configurar uma ligação VPN uma vez, para um servidor ou dispositivo VPN hospedado na rede da sua organização, em vez de fazer para cada dispositivo cliente que precisa aceder à sua partilha de ficheiros Azure. Para simplificar a implementação de uma ligação VPN S2S, consulte [Configurar uma VPN site-to-site (S2S) para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), que lhe permite criar uma rota definida entre o Azure e a sua rede no local que não atravessa a internet. Uma vez que o ExpressRoute fornece um caminho dedicado entre o seu datacenter no local e o Azure, o ExpressRoute pode ser útil quando o desempenho da rede é uma consideração. O ExpressRoute também é uma boa opção quando a política ou os requisitos regulamentares da sua organização requerem um caminho determinístico para os seus recursos na nuvem.

Independentemente do método de túnel que utilize para aceder às suas partilhas de ficheiros Azure, precisa de um mecanismo para garantir que o tráfego para a sua conta de armazenamento passa pelo túnel e não pela sua ligação regular à Internet. Tecnicamente, é possível encaminhar para o ponto final público da conta de armazenamento, no entanto, isso requer codificação dura de todos os endereços IP para os clusters de armazenamento Azure numa região, uma vez que as contas de armazenamento podem ser movidas entre clusters de armazenamento a qualquer momento. Isto também requer atualizar constantemente os mapeamentos de endereçoIP, uma vez que novos clusters são adicionados a toda a hora.

Em vez de codificar duramente o endereço IP das suas contas de armazenamento nas suas regras de encaminhamento VPN, recomendamos a utilização de pontos finais privados, que dão à sua conta de armazenamento um endereço IP a partir do espaço de endereço de uma rede virtual Azure. Uma vez que a criação de um túnel para o Azure estabelece o olhar entre a sua rede no local e uma ou mais redes virtuais, isto permite o encaminhamento correto de uma forma durável.

### <a name="private-endpoints"></a>Pontos finais privados
Além do ponto final do público por defeito para uma conta de armazenamento, o Azure Files oferece a opção de ter um ou mais pontos finais privados. Um ponto final privado é um ponto final que só é acessível dentro de uma rede virtual Azure. Quando cria um ponto final privado para a sua conta de armazenamento, a sua conta de armazenamento obtém um endereço IP privado a partir do espaço de endereço da sua rede virtual, tal como um servidor de ficheiros no local ou um dispositivo NAS recebe um endereço IP dentro do espaço de endereço dedicado da sua rede no local. 

Um ponto final privado individual está associado a uma subnet de rede virtual Azure específica. Uma conta de armazenamento pode ter pontos finais privados em mais de uma rede virtual.

A utilização de pontos finais privados com Ficheiros Azure permite-lhe:
- Ligue-se de forma segura às partilhas de ficheiros Azure das redes no local utilizando uma ligação VPN ou ExpressRoute com o private-peering.
- Proteja as suas ações de ficheiro Azure configurando a firewall da conta de armazenamento para bloquear todas as ligações no ponto final público. Por predefinição, a criação de um ponto final privado não bloqueia as ligações ao ponto final público.
- Aumente a segurança da rede virtual, permitindo bloquear a exfiltração de dados da rede virtual (e peering limites).

Para criar um ponto final privado, consulte [configurar pontos finais privados para Ficheiros Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Pontos finais privados e DNS
Quando cria um ponto final privado, por padrão também criamos uma (ou atualizar `privatelink` uma zona privada de DNS existente) correspondente ao subdomínio. Em rigor, a criação de uma zona Privada de DNS não é necessária para utilizar um ponto final privado para a sua conta de armazenamento, mas é altamente recomendada em geral e explicitamente necessária ao montar a sua partilha de ficheiros Azure com um diretor de diretório ativo ou aceder a partir da API FileREST.

> [!Note]  
> Este artigo utiliza a conta de armazenamento DNS sufixo para as regiões públicas de Azure, `core.windows.net`. Este comentário também se aplica às nuvens soberanas do Azure, como a nuvem do Governo dos EUA azure e a nuvem azure china - apenas substitua os sufixos apropriados para o seu ambiente. 

Na sua zona privada de DNS, `storageaccount.privatelink.file.core.windows.net` criamos um registo A e um registo CNAME `storageaccount.file.core.windows.net`para o nome regular da conta de armazenamento, que segue o padrão . Uma vez que a sua zona Privada DeNs Azure está ligada à rede virtual que `Resolve-DnsName` contém o ponto final privado, pode observar `nslookup` a configuração DNS quando ligar para o cmdlet da PowerShell num Azure VM (alternadamente no Windows e Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Por este exemplo, `storageaccount.file.core.windows.net` a conta de armazenamento resolve-se com o endereço `192.168.0.4`IP privado do ponto final privado, que por acaso é .

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Se executar o mesmo comando a partir do local, verá que o mesmo nome da conta de armazenamento se resolve para o endereço IP público da conta de armazenamento; `storageaccount.file.core.windows.net` é um registo `storageaccount.privatelink.file.core.windows.net`CNAME para, que por sua vez é um registo CNAME para o cluster de armazenamento Azure que acolhe a conta de armazenamento:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Isto reflete o facto de a conta de armazenamento poder expor tanto o ponto final público como um ou mais pontos finais privados. Para garantir que o nome da conta de armazenamento se resolve no endereço IP privado do ponto final privado, deve alterar a configuração dos seus servidores DNS no local. Isto pode ser realizado de várias maneiras:

- Modificar o ficheiro dos anfitriões `storageaccount.file.core.windows.net` nos seus clientes para resolver o endereço IP privado do ponto final pretendido. Isto é fortemente desencorajado para ambientes de produção, uma vez que você precisará fazer estas alterações a todos os clientes que queiram montar as suas ações de ficheiro Saque e alterações na conta de armazenamento ou ponto final privado não serão tratados automaticamente.
- Criar um registo `storageaccount.file.core.windows.net` para os seus servidores DNS no local. Isto tem a vantagem de que os clientes no seu ambiente no local serão capazes de resolver automaticamente a conta de armazenamento sem precisar de configurar cada cliente, no entanto esta solução é igualmente frágil para modificar o ficheiro dos anfitriões porque as alterações não são refletidas. Embora esta solução seja frágil, pode ser a melhor escolha para alguns ambientes.
- Encaminhar `core.windows.net` a zona dos seus servidores DNS no local para a sua zona DNS privada Azure. O anfitrião Privado DNS azure pode ser`168.63.129.16`alcançado através de um endereço IP especial ( ) que só é acessível dentro de redes virtuais que estão ligadas à zona Privada DeNs do Azure. Para contornar esta limitação, pode executar servidores DNS adicionais dentro da sua rede virtual que irão encaminhar-se `core.windows.net` para a zona Privada DNS do Azure. Para simplificar esta configuração, fornecemos cmdlets PowerShell que irão implantar automaticamente servidores DNS na sua rede virtual Azure e configurá-los como desejado. Para aprender a configurar o encaminhamento de DNS, consulte [Configurar DNS com Ficheiros Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Definições de firewall de conta de armazenamento
Uma firewall é uma política de rede que controla quais os pedidos que são autorizados a aceder ao ponto final público para uma conta de armazenamento. Utilizando a firewall da conta de armazenamento, pode restringir o acesso ao ponto final da conta de armazenamento a determinados endereços ou gamas IP ou a uma rede virtual. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso em rede a uma ou mais redes virtuais. 

Existem duas abordagens para restringir o acesso a uma conta de armazenamento a uma rede virtual:
- Crie um ou mais pontos finais privados para a conta de armazenamento e restrinja todo o acesso ao ponto final público. Isto garante que apenas o tráfego originário das redes virtuais desejadas pode aceder às ações de ficheiro sintetizadores do Azure dentro da conta de armazenamento.
- Restringir o ponto final público a uma ou mais redes virtuais. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais*de serviço . Quando restringe o tráfego a uma conta de armazenamento através de um ponto final de serviço, ainda está a aceder à conta de armazenamento através do endereço IP público.

Para saber mais sobre como configurar a firewall da conta de armazenamento, consulte as firewalls de [armazenamento do Azure e as redes virtuais.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Por padrão, todas as contas de armazenamento do Azure têm encriptação ativada em trânsito. Isto significa que quando montar uma partilha de ficheiros sobre SMB ou acedê-la através do protocolo FileREST (como por exemplo através do portal Azure, PowerShell/CLI ou Azure SDKs), o Azure Files só permitirá a ligação se for feita com SMB 3.0+ com encriptação ou HTTPS. Os clientes que não suportam SMB 3.0 ou clientes que suportem sMB 3.0 mas não encriptação SMB não serão capazes de montar a partilha de ficheiros Azure se a encriptação em trânsito estiver ativada. Para obter mais informações sobre quais os sistemas operativos suportam SMB 3.0 com encriptação, consulte a nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, CLI e SDKs suportam HTTPS.  

Pode desativar a encriptação em trânsito para uma conta de armazenamento Azure. Quando a encriptação é desativada, o Azure Files também permitirá chamadas SMB 2.1, SMB 3.0 sem encriptação e chamadas API De FileREST não encriptadas sobre HTTP. A principal razão para desativar a encriptação em trânsito é para suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga. O Azure Files apenas permite ligações SMB 2.1 dentro da mesma região azure que a quota de ficheiros Azure; um cliente SMB 2.1 fora da região Azure da quota de ficheiros Azure, como no local ou numa região do Azure diferente, não poderá aceder à partilha de ficheiros.

Para obter mais informações sobre encriptação em trânsito, consulte a necessidade de [transferência segura no armazenamento do Azure.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Consulte também
- [Descrição geral dos Ficheiros do Azure](storage-files-introduction.md)
- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)