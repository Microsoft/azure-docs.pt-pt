---
title: Considerações de rede de arquivos do Azure | Microsoft Docs
description: Uma visão geral das opções de rede para arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141795"
---
# <a name="azure-files-networking-considerations"></a>Considerações de rede de arquivos do Azure 
Você pode se conectar a um compartilhamento de arquivos do Azure de duas maneiras:

- Acessando o compartilhamento diretamente por meio dos protocolos SMB ou filerest. Esse padrão de acesso é empregado principalmente quando é possível eliminar o máximo possível de servidores locais.
- Criar um cache do compartilhamento de arquivos do Azure em um servidor local com Sincronização de Arquivos do Azure e acessar os dados do compartilhamento de arquivos do servidor local com o protocolo de sua escolha (SMB, NFS, FTPS, etc.) para seu caso de uso. Esse padrão de acesso é útil porque ele combina o melhor de desempenho local e escala de nuvem e serviços anexáveis sem servidor, como o backup do Azure.

Este artigo se concentra em como configurar a rede para quando seu caso de uso chama o acesso ao compartilhamento de arquivos do Azure diretamente, em vez de usar Sincronização de Arquivos do Azure. Para obter mais informações sobre considerações de rede para uma implantação de Sincronização de Arquivos do Azure, consulte [definindo configurações de proxy e firewall de sincronização de arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Configurações da conta de armazenamento
Uma conta de armazenamento é uma construção de gerenciamento que representa um pool compartilhado de armazenamento no qual é possível implantar vários compartilhamentos de arquivos, bem como outros recursos de armazenamento, como contêineres ou filas de BLOBs. As contas de armazenamento do Azure expõem dois conjuntos básicos de configurações para proteger a rede: criptografia em trânsito e firewalls e redes virtuais (VNets).

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. Isso significa que quando você monta um compartilhamento de arquivos via SMB ou o acessa por meio do protocolo filerest (por exemplo, por meio do portal do Azure, PowerShell/CLI ou SDKs do Azure), os arquivos do Azure só permitirão a conexão se for feita com o SMB 3.0 + com criptografia ou HTTPS. Clientes que não dão suporte a SMB 3,0 ou clientes que dão suporte a SMB 3,0, mas não a criptografia SMB não poderão montar o compartilhamento de arquivos do Azure se a criptografia em trânsito estiver habilitada. Para obter mais informações sobre quais sistemas operacionais dão suporte ao SMB 3,0 com criptografia, consulte nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, da CLI e dos SDKs dão suporte a HTTPS.  

Você pode desabilitar a criptografia em trânsito para uma conta de armazenamento do Azure. Quando a criptografia estiver desabilitada, os arquivos do Azure também permitirão SMB 2,1, SMB 3,0 sem criptografia e chamadas da API filerest não criptografadas sobre HTTP. O principal motivo para desabilitar a criptografia em trânsito é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux. Os arquivos do Azure só permitem conexões SMB 2,1 na mesma região do Azure que o compartilhamento de arquivos do Azure; um cliente SMB 2,1 fora da região do Azure do compartilhamento de arquivos do Azure, como local ou em uma região diferente do Azure, não será capaz de acessar o compartilhamento de arquivos.

Para obter mais informações sobre criptografia em trânsito, consulte [exigindo transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais 
Um firewall é uma política de rede que solicita permissão para acessar os compartilhamentos de arquivos do Azure e outros recursos de armazenamento em sua conta de armazenamento. Quando uma conta de armazenamento é criada com as configurações de rede padrão, ela não é restrita a uma rede específica e, portanto, é acessível pela Internet. Isso não significa que qualquer pessoa na Internet possa acessar os dados armazenados nos compartilhamentos de arquivos do Azure hospedados em sua conta de armazenamento, mas, em vez disso, a conta de armazenamento aceitará solicitações autorizadas de qualquer rede. As solicitações podem ser autorizadas com uma chave de conta de armazenamento, um token de SAS (assinatura de acesso compartilhado) (somente filerest) ou com uma entidade de usuário Active Directory. 

A política de firewall para uma conta de armazenamento pode ser usada para restringir o acesso a determinados endereços IP ou intervalos, ou a uma rede virtual. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso de rede a uma rede virtual. 

Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que você operaria em seu próprio datacenter. Ele permite que você crie um canal de comunicação seguro para seus recursos do Azure, como compartilhamentos de arquivos do Azure, VMs, bancos de dados SQL, etc. para se comunicar entre si. Como uma conta de armazenamento do Azure ou uma VM do Azure, uma VNet é um recurso do Azure que é implantado em um grupo de recursos. Com a configuração de rede adicional, o Azure VNets também pode estar conectado às suas redes locais.

Quando recursos como uma VM do Azure são adicionados a uma rede virtual, uma NIC (interface de rede virtual) conectada à máquina virtual é restrita especificamente a essa VNet. Isso é possível porque as VMs do Azure são computadores virtualizados, o que é claro que têm NICs. As máquinas virtuais são oferecidas como parte da lista de produtos de infraestrutura como serviço do Azure ou IaaS. Como os compartilhamentos de arquivos do Azure são compartilhamentos de arquivos sem servidor, eles não têm uma NIC para você adicionar a uma VNet. Dito uma maneira diferente, os arquivos do Azure são oferecidos como parte da linha de produtos da plataforma como serviço do Azure ou PaaS. Para permitir que uma conta de armazenamento seja capaz de fazer parte de uma VNet, o Azure dá suporte a um conceito de serviços PaaS chamados de pontos de extremidade de serviço. Um ponto de extremidade de serviço permite que os serviços de PaaS façam parte de uma rede virtual. Para saber mais sobre pontos de extremidade de serviço, consulte [pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md).

Uma conta de armazenamento pode ser adicionada a uma ou mais redes virtuais. Para saber mais sobre como adicionar sua conta de armazenamento a uma rede virtual ou definir outras configurações de firewall, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Rede do Azure
Por padrão, os serviços do Azure, incluindo os arquivos do Azure, podem ser acessados pela Internet. Como o tráfego padrão para sua conta de armazenamento é criptografado (e montagens SMB 2,1 nunca são permitidas fora de uma região do Azure), não há nada inerentemente inseguro sobre o acesso de seus compartilhamentos de arquivos do Azure pela Internet. Com base na política da sua organização ou em requisitos regulatórios exclusivos, você pode exigir comunicação mais restritiva com o Azure e, portanto, o Azure fornece várias maneiras de restringir como o tráfego de fora do Azure chega aos arquivos do Azure. Você pode proteger ainda mais sua rede ao acessar o compartilhamento de arquivos do Azure usando as seguintes ofertas de serviço:

- [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e um local alternativo (como no local) pela Internet. Um gateway de VPN do Azure é um recurso do Azure que pode ser implantado em um grupo de recursos junto com uma conta de armazenamento ou outros recursos do Azure. Gateways de VPN expõem dois tipos diferentes de conexões:
    - Conexões de gateway de [VPN ponto a site (P2S)](../../vpn-gateway/point-to-site-about.md) , que são conexões VPN entre o Azure e um cliente individual. Essa solução é útil principalmente para dispositivos que não fazem parte da rede local da sua organização, como telecomutadores que desejam poder montar o compartilhamento de arquivos do Azure de casa, de uma cafeteria ou de um hotel em trânsito. Para usar uma conexão VPN P2S com os arquivos do Azure, uma conexão VPN P2S precisará ser configurada para cada cliente que deseja se conectar. 
    - [VPN site a site (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), que são conexões VPN entre o Azure e a rede da sua organização. Uma conexão VPN S2S permite que você configure uma conexão VPN uma vez, para um servidor VPN ou dispositivo hospedado na rede da sua organização, em vez de fazer para cada dispositivo cliente que precisa acessar o compartilhamento de arquivos do Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), que permite que você crie uma rota definida entre o Azure e sua rede local que não atravessa a Internet. Como o ExpressRoute fornece um caminho dedicado entre o datacenter local e o Azure, o ExpressRoute pode ser útil quando o desempenho da rede é uma consideração. O ExpressRoute também é uma boa opção quando os requisitos regulatórios ou de política da sua organização exigem um caminho determinístico para seus recursos na nuvem.

## <a name="securing-access-from-on-premises"></a>Protegendo o acesso do local 
Ao migrar compartilhamentos de arquivos de uso geral (para itens como documentos do Office, PDFs, documentos CAD etc.) para arquivos do Azure, os usuários normalmente continuarão precisando acessar seus arquivos de dispositivos locais, como estações de trabalho, laptops e tablets. A principal consideração para um compartilhamento de arquivos de uso geral é como os usuários locais podem acessar com segurança seus compartilhamentos de arquivos por meio da Internet ou WAN.

A maneira mais fácil de acessar o compartilhamento de arquivos do Azure local é abrir sua rede local para a porta 445, a porta usada pelo SMB e montar o caminho UNC fornecido pelo portal do Azure. Isso não requer nenhuma rede especial necessária. Muitos clientes são relutantes para abrir a porta 445 devido a uma orientação de segurança desatualizada sobre o SMB 1,0, que a Microsoft não considera um protocolo seguro para a Internet. Os arquivos do Azure não implementam SMB 1,0. 

O SMB 3,0 foi projetado com o requisito explícito de ser protocolos de compartilhamento de arquivos seguros da Internet. Portanto, ao usar o SMB 3.0 +, da perspectiva da rede de computadores, a abertura da porta 445 não é diferente de abrir a porta 443, a porta usada para conexões HTTPS. Em vez de bloquear a porta 445 para evitar tráfego SMB 1,0 inseguro, a Microsoft recomenda as seguintes etapas:

> [!Important]  
> Mesmo que você decida deixar a porta 445 fechada ao tráfego de saída, a Microsoft ainda recomenda seguir estas etapas para remover o SMB 1,0 do seu ambiente.

1. Verifique se o SMB 1,0 foi removido ou desabilitado nos dispositivos da sua organização. Todas as versões atualmente com suporte do Windows e do Windows Server dão suporte à remoção ou desabilitação do SMB 1,0 e a partir do Windows 10, versão 1709, o SMB 1,0 não é instalado no Windows por padrão. Para saber mais sobre como desabilitar o SMB 1,0, consulte nossas páginas específicas do sistema operacional:
    - [Protegendo o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Protegendo o Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Certifique-se de que nenhum produto em sua organização exija o SMB 1,0 e remova aqueles que o fazem. Mantemos uma [câmara de compensação de produtos de SMB1](https://aka.ms/stillneedssmb1), que contém todos os produtos da primeira e de terceiros conhecidos pela Microsoft para exigir o SMB 1,0. 
1. Adicional Use um firewall de terceiros com a rede local da sua organização para evitar o tráfego SMB 1,0.

Se sua organização exigir que a porta 445 seja bloqueada por política ou regulamento, você poderá usar o gateway de VPN do Azure ou o ExpressRoute para encapsular o tráfego pela porta 443. Para saber mais sobre as etapas específicas para implantá-las, consulte nossas páginas de instruções específicas:
- [Configurar uma VPN S2S (site a site) para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md)
- [Configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md)

Sua organização pode ter o requisito adicional de que o tráfego de saída do site local deve seguir um caminho determinístico para seus recursos na nuvem. Nesse caso, o ExpressRoute é capaz de atender a esse requisito.

## <a name="securing-access-from-cloud-resources"></a>Protegendo o acesso de recursos de nuvem
Geralmente, quando um aplicativo local é levantado e deslocado para a nuvem, o aplicativo e os dados do aplicativo são movidos ao mesmo tempo. Isso significa que a principal consideração para uma migração de comparação de precisão e de deslocamento está bloqueando o acesso ao compartilhamento de arquivos do Azure para máquinas virtuais específicas ou serviços do Azure que exigem acesso ao compartilhamento de arquivos para operar. 

Talvez você queira usar o VNets para limitar quais VMs ou outros recursos do Azure têm permissão para fazer conexões de rede (montagens SMB ou chamadas à API REST para o compartilhamento de arquivos do Azure). É sempre recomendável colocar o compartilhamento de arquivos do Azure em uma VNet se você permitir tráfego não criptografado para sua conta de armazenamento. Caso contrário, se você usar o VNets é uma decisão que deve ser orientada pelos seus requisitos de negócios e pela política organizacional.

O principal motivo para permitir o tráfego não criptografado para o compartilhamento de arquivos do Azure é dar suporte ao Windows Server 2008 R2, ao Windows 7 ou a outro sistema operacional mais antigo, acessando o compartilhamento de arquivos do Azure com SMB 2,1 (ou SMB 3,0 sem criptografia para algumas distribuições do Linux). Não recomendamos o uso de SMB 2,1 ou SMB 3,0 sem criptografia em sistemas operacionais que dão suporte a SMB 3.0 + com criptografia.

## <a name="see-also"></a>Ver também
- [Descrição geral dos Ficheiros do Azure](storage-files-introduction.md)
- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)