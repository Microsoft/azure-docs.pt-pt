---
title: Azure Files considerações de rede | Microsoft Docs
description: Uma visão geral das opções de networking para ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759692"
---
# <a name="azure-files-networking-considerations"></a>Considerações de networking de ficheiros Azure 
Pode ligar-se a uma partilha de ficheiros Azure de duas formas:

- Aceder diretamente à partilha através do Bloco de Mensagens do Servidor (SMB), do Sistema de Ficheiros de Rede (NFS) (pré-visualização) ou dos protocolos FileREST. Este padrão de acesso é principalmente utilizado quando para eliminar o maior número possível de servidores no local.
- Criar uma cache da partilha de ficheiros Azure num servidor no local (ou num Azure VM) com o Azure File Sync, e aceder aos dados da partilha de ficheiros a partir do servidor no local com o seu protocolo de escolha (SMB, NFS, FTPS, etc.) para o seu caso de utilização. Este padrão de acesso é útil porque combina o melhor de desempenho no local e escala de nuvem e serviços anexáveis sem servidor, como O Azure Backup.

Este artigo centra-se em como configurar a rede para quando o seu caso de utilização requer o acesso diretamente à partilha de ficheiros Azure em vez de utilizar o Azure File Sync. Para obter mais informações sobre considerações de networking para uma implementação de Azure File Sync, consulte [considerações de networking de ficheiros Azure](../file-sync/file-sync-networking-overview.md)File Sync .

A configuração de rede para ações de ficheiros Azure é feita na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias partilhas de arquivos, bem como outros recursos de armazenamento, tais como recipientes blob ou filas. As contas de armazenamento expõem várias configurações que o ajudam a garantir o acesso da rede às suas partilhas de ficheiros: pontos finais de rede, definições de firewall de conta de armazenamento e encriptação em trânsito. 

Recomendamos a leitura [do Planeamento de uma implementação de Ficheiros Azure](storage-files-planning.md) antes de ler este guia conceptual.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Este vídeo é um guia e demonstração para como expor de forma segura as partilhas de ficheiros Azure diretamente a trabalhadores de informação e apps em cinco passos simples. As secções abaixo fornecem links e contexto adicional à documentação referenciada no vídeo.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Aceder às suas ações de ficheiros Azure
Quando implementa uma parte do ficheiro Azure numa conta de armazenamento, a sua parte do ficheiro é imediatamente acessível através do ponto final público da conta de armazenamento. Isto significa que os pedidos autenticados, tais como pedidos autorizados pela identidade de início de súmã de um utilizador, podem ter origem seguramente dentro ou fora de Azure. 

Em muitos ambientes de clientes, uma montagem inicial da partilha de ficheiros Azure na sua estação de trabalho no local falhará, mesmo que os suportes dos VMs Azure tenham sucesso. A razão para isso é que muitas organizações e fornecedores de serviços de internet (ISPs) bloqueiam a porta que o SMB usa para comunicar, porta 445. As ações da NFS não têm esta questão. Esta prática tem origem em orientações de segurança sobre versões antigas e preprecadas do protocolo SMB. Embora o SMB 3.0 seja um protocolo seguro para a Internet, as versões mais antigas do SMB, especialmente sMB 1.0 não são. As ações de ficheiros Azure só podem ser acedidas externamente através do SMB 3.0 e do protocolo FileREST (que também é um protocolo de segurança na Internet) através do ponto final público.

Uma vez que a forma mais fácil de aceder à sua partilha de ficheiros Azure SMB a partir do local é abrir a sua rede no local para a porta 445, a Microsoft recomenda os seguintes passos para remover o SMB 1.0 do seu ambiente:

1. Certifique-se de que o SMB 1.0 é removido ou desativado nos dispositivos da sua organização. Todas as versões suportadas atualmente do Suporte ao Windows e Do Windows Server, removendo ou desativando o SMB 1.0, e a partir do Windows 10, a versão 1709, SMB 1.0 não está instalada no Windows por padrão. Para saber mais sobre como desativar o SMB 1.0, consulte as nossas páginas específicas do OS:
    - [Proteger o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Segurança Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Certifique-se de que nenhum produto dentro da sua organização requer SMB 1.0 e remova os que o fazem. Mantemos um [SMB1 Product Clearinghouse,](https://aka.ms/stillneedssmb1)que contém todos os produtos de primeira e terceira parte conhecidos pela Microsoft para exigir SMB 1.0. 
1. (Opcional) Use uma firewall de terceiros com a rede de instalações da sua organização para evitar que o tráfego SMB 1.0 saia do seu limite organizacional.

Se a sua organização exigir que a porta 445 seja bloqueada por política ou regulamento, ou se a sua organização exigir que o tráfego para Azure siga um caminho determinístico, pode utilizar o Azure VPN Gateway ou o ExpressRoute para fazer um túnel de tráfego para as suas ações de ficheiros Azure. As ações da NFS não requerem nada disto, uma vez que não necessitam do porto 445.

> [!Important]  
> Mesmo que decida utilizar um método alternativo para aceder às suas ações de ficheiros Azure, a Microsoft ainda recomenda remover o SMB 1.0 do seu ambiente.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Fazer túneis sobre uma rede privada virtual ou ExpressRoute
Quando estabelece um túnel de rede entre a sua rede no local e o Azure, está a espreitar a sua rede no local com uma ou mais redes virtuais em Azure. Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que você operaria no local. Como uma conta de armazenamento Azure ou um VM Azure, um VNet é um recurso Azure que é implantado num grupo de recursos. 

A Azure Files suporta os seguintes mecanismos para fazer um túnel de tráfego entre as suas estações de trabalho e servidores no local e as ações de ficheiros Azure SMB/NFS:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Um gateway VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego encriptado entre uma rede virtual Azure e uma localização alternativa (como no local) através da internet. Um Azure VPN Gateway é um recurso Azure que pode ser implantado num grupo de recursos juntamente com uma conta de armazenamento ou outros recursos Azure. As portas VPN expõem dois tipos diferentes de ligações:
    - [Ligações de gateway VPN ponto-a-local (P2S),](../../vpn-gateway/point-to-site-about.md) que são ligações VPN entre Azure e um cliente individual. Esta solução é principalmente útil para dispositivos que não fazem parte da rede de instalações da sua organização, como telecomuters que querem ser capazes de montar a sua parte de arquivo Azure a partir de casa, um café ou hotel enquanto estão na estrada. Para utilizar uma ligação P2S VPN com ficheiros Azure, uma ligação P2S VPN terá de ser configurada para cada cliente que queira ligar. Para simplificar a implantação de uma ligação P2S VPN, consulte [Configure uma VPN ponto-a-local (P2S) no Windows para utilização com Ficheiros Azure](storage-files-configure-p2s-vpn-windows.md) e [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com Ficheiros Azure](storage-files-configure-p2s-vpn-linux.md).
    - [VPN site-to-site (S2S),](../../vpn-gateway/design.md#s2smulti)que são ligações VPN entre a Azure e a rede da sua organização. Uma ligação S2S VPN permite-lhe configurar uma ligação VPN uma vez, para um servidor VPN ou dispositivo alojado na rede da sua organização, em vez de fazer para cada dispositivo cliente que precisa aceder à sua partilha de ficheiros Azure. Para simplificar a implantação de uma ligação S2S VPN, consulte [configurar uma VPN site-to-site (S2S) para utilização com Ficheiros Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute,](../../expressroute/expressroute-introduction.md)que lhe permite criar uma rota definida entre o Azure e a sua rede no local que não atravessa a internet. Uma vez que o ExpressRoute fornece um caminho dedicado entre o datacenter no local e o Azure, o ExpressRoute pode ser útil quando o desempenho da rede é uma consideração. O ExpressRoute também é uma boa opção quando a política ou os requisitos regulamentares da sua organização requerem um caminho determinístico para os seus recursos na nuvem.

Independentemente do método de túneis que utilize para aceder às suas partilhas de ficheiros Azure, precisa de um mecanismo para garantir que o tráfego para a sua conta de armazenamento passa pelo túnel em vez da sua ligação regular à Internet. É tecnicamente possível encaminhar para o ponto final público da conta de armazenamento, no entanto, isto requer a codificação dura de todos os endereços IP para os clusters de armazenamento Azure numa região, uma vez que as contas de armazenamento podem ser movidas entre clusters de armazenamento a qualquer momento. Isto também requer uma atualização constante dos mapeamentos de endereços IP, uma vez que novos clusters são adicionados a toda a hora.

Em vez de codificar duramente o endereço IP das suas contas de armazenamento nas suas regras de encaminhamento VPN, recomendamos a utilização de pontos finais privados, que dão à sua conta de armazenamento um endereço IP a partir do espaço de endereço de uma rede virtual Azure. Uma vez que a criação de um túnel para Azure estabelece o espreitamento entre a sua rede no local e uma ou mais redes virtuais, isto permite o encaminhamento correto de forma duradoura.

### <a name="private-endpoints"></a>Pontos finais privados
Além do ponto final público padrão para uma conta de armazenamento, a Azure Files oferece a opção de ter um ou mais pontos finais privados. Um ponto final privado é um ponto final que só é acessível dentro de uma rede virtual Azure. Quando cria um ponto final privado para a sua conta de armazenamento, a sua conta de armazenamento obtém um endereço IP privado dentro do espaço de endereço da sua rede virtual, tal como um servidor de ficheiros no local ou dispositivo NAS recebe um endereço IP dentro do espaço de endereço dedicado da sua rede no local. 

Um ponto final privado individual está associado a uma sub-rede de rede virtual Azure específica. Uma conta de armazenamento pode ter pontos finais privados em mais de uma rede virtual.

A utilização de pontos finais privados com ficheiros Azure permite-lhe:
- Ligue-se de forma segura às suas ações de ficheiros Azure a partir de redes no local utilizando uma ligação VPN ou ExpressRoute com o seu peering privado.
- Proteja as suas ações de ficheiroS Azure configurando a firewall da conta de armazenamento para bloquear todas as ligações no ponto final público. Por predefinição, a criação de um ponto final privado não bloqueia as ligações ao ponto final público.
- Aumentar a segurança da rede virtual, permitindo-lhe bloquear a exfiltração de dados a partir da rede virtual (e os limites de espreitar).

Para criar um ponto final privado, consulte [configurar pontos finais privados para ficheiros Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Pontos finais privados e DNS
Quando cria um ponto final privado, por padrão também criamos uma (ou atualização) de uma zona de DNS privada existente correspondente ao `privatelink` subdomínio. Em rigor, a criação de uma zona privada de DNS não é necessária para utilizar um ponto final privado para a sua conta de armazenamento, mas é altamente recomendado em geral e explicitamente necessário ao montar a sua partilha de ficheiros Azure com um diretor de itado ativo ou aceder a partir da API filerest.

> [!Note]  
> Este artigo utiliza o sufixo DNS da conta de armazenamento para as regiões públicas do Azure, `core.windows.net` . Este comentário também se aplica às nuvens soberanas de Azure, como a nuvem do Governo dos EUA Azure e a nuvem Azure China - basta substituir os sufixos apropriados para o seu ambiente. 

Na sua zona privada de DNS, criamos um registo A `storageaccount.privatelink.file.core.windows.net` e um registo CNAME para o nome regular da conta de armazenamento, que segue o `storageaccount.file.core.windows.net` padrão. Uma vez que a sua zona de DNS privada Azure está ligada à rede virtual que contém o ponto final privado, pode observar a configuração de DNS quando ligando para o `Resolve-DnsName` cmdlet do PowerShell num VM Azure (alternadamente `nslookup` no Windows e Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Por exemplo, a conta de armazenamento `storageaccount.file.core.windows.net` resolve-se com o endereço IP privado do ponto final privado, que por acaso é `192.168.0.4` .

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

Se executar o mesmo comando a partir do local, verá que o mesmo nome da conta de armazenamento se resolve para o endereço IP público da conta de armazenamento; `storageaccount.file.core.windows.net` é um registo CNAME para `storageaccount.privatelink.file.core.windows.net` , que por sua vez é um registo CNAME para o cluster de armazenamento Azure que alberga a conta de armazenamento:

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

Isto reflete o facto de a conta de armazenamento poder expor tanto o ponto final público como um ou mais pontos finais privados. Para garantir que o nome da conta de armazenamento se resolva para o endereço IP privado do ponto final privado, tem de alterar a configuração nos servidores DNS no local. Isto pode ser realizado de várias maneiras:

- Modificar o ficheiro de anfitriões nos seus clientes para resolver `storageaccount.file.core.windows.net` o endereço IP privado do ponto final pretendido. Isto é fortemente desencorajado para ambientes de produção, uma vez que você precisará fazer estas alterações a cada cliente que queira montar as suas ações de arquivo Azure e as alterações na conta de armazenamento ou ponto final privado não serão automaticamente manuseadas.
- Criar um registo A para `storageaccount.file.core.windows.net` os seus servidores DNS no local. Isto tem a vantagem de que os clientes no seu ambiente no local serão capazes de resolver automaticamente a conta de armazenamento sem necessidade de configurar cada cliente, no entanto esta solução é igualmente frágil para modificar o ficheiro dos anfitriões porque as alterações não são refletidas. Embora esta solução seja frágil, pode ser a melhor escolha para alguns ambientes.
- Encaminhe a `core.windows.net` zona dos servidores DNS no local para a sua zona de DNS privada Azure. O anfitrião DNS privado Azure pode ser alcançado através de um endereço IP especial ( `168.63.129.16` ) que só é acessível dentro de redes virtuais que estão ligadas à zona privada de DNS Azure. Para contornar esta limitação, pode executar servidores DNS adicionais dentro da sua rede virtual que irá `core.windows.net` encaminhar-se para a zona privada de DNS do Azure. Para simplificar esta configuração, fornecemos cmdlets PowerShell que irão implantar automaticamente servidores DNS na sua rede virtual Azure e configurá-los conforme desejado. Para aprender a configurar o reencaminhamento de DNS, consulte [configurar DNS com Ficheiros Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Definições de firewall de conta de armazenamento
Uma firewall é uma política de rede que controla quais os pedidos que podem aceder ao ponto final público para uma conta de armazenamento. Utilizando a firewall da conta de armazenamento, pode restringir o acesso ao ponto final público da conta de armazenamento a certos endereços ou intervalos IP ou a uma rede virtual. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso em rede a uma ou mais redes virtuais. 

Existem duas abordagens para restringir o acesso a uma conta de armazenamento a uma rede virtual:
- Crie um ou mais pontos finais privados para a conta de armazenamento e restrinja todo o acesso ao ponto final público. Isto garante que apenas o tráfego originário das redes virtuais pretendidas pode aceder às ações de ficheiros Azure dentro da conta de armazenamento.
- Restringir o ponto final público a uma ou mais redes virtuais. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais de serviço*. Quando restringe o tráfego a uma conta de armazenamento através de um ponto final de serviço, ainda está a aceder à conta de armazenamento através do endereço IP público.

> [!NOTE]
> As ações da NFS não podem aceder ao ponto final público da conta de armazenamento através do endereço IP público, só podem aceder ao ponto final público da conta de armazenamento utilizando redes virtuais. As ações da NFS também podem aceder à conta de armazenamento utilizando pontos finais privados.

Para saber mais sobre como configurar a firewall da conta de armazenamento, consulte [as firewalls de armazenamento Azure e redes virtuais.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="encryption-in-transit"></a>Encriptação de dados em circulação

> [!IMPORTANT]
> Esta secção cobre a encriptação em detalhes de trânsito para ações SMB. Para obter detalhes sobre encriptação em trânsito com ações NFS, consulte [Segurança](storage-files-compare-protocols.md#security).

Por padrão, todas as contas de armazenamento Azure têm encriptação em trânsito ativada. Isto significa que quando monta uma partilha de ficheiros sobre o SMB ou acede-lo através do protocolo FileREST (por exemplo através do portal Azure, PowerShell/CLI ou Azure SDKs), os Ficheiros Azure só permitirão a ligação se for feita com SMB 3.0+ com encriptação ou HTTPS. Os clientes que não suportam SMB 3.0 ou clientes que suportem encriptação SMB 3.0 mas não SMB não poderão montar a partilha de ficheiros Azure se a encriptação em trânsito estiver ativada. Para obter mais informações sobre quais os sistemas operativos que suportam SMB 3.0 com encriptação, consulte a nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux.](storage-how-to-use-files-linux.md) Todas as versões atuais do suporte de PowerShell, CLI e SDKs HTTPS.  

Pode desativar a encriptação em trânsito para uma conta de armazenamento Azure. Quando a encriptação é desativada, os Ficheiros Azure também permitirão chamadas SMB 2.1, SMB 3.0 sem encriptação e chamadas de API de Ficheiros não encriptadas em HTTP. A principal razão para desativar a encriptação em trânsito é suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga. A Azure Files só permite ligações SMB 2.1 na mesma região de Azure que a partilha de ficheiros Azure; um cliente SMB 2.1 fora da região de Azure da partilha de ficheiros Azure, como no local ou numa região de Azure diferente, não poderá aceder à partilha de ficheiros.

Para obter mais informações sobre encriptação em trânsito, consulte [a necessidade de transferência segura no armazenamento Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Ver também
- [Descrição geral dos Ficheiros do Azure](storage-files-introduction.md)
- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)