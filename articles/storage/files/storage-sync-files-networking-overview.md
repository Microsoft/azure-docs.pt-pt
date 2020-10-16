---
title: Considerações de networking de ArquivoS Azure Microsoft Docs
description: Aprenda a configurar a rede para utilizar o Azure File Sync para cache ficheiros no local.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b291bd45b4003dd2241f40c810ed9d78af9f8bc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267793"
---
# <a name="azure-file-sync-networking-considerations"></a>Considerações de networking de arquivo azure
Pode ligar-se a uma partilha de ficheiros Azure de duas formas:

- Aceder diretamente à partilha através dos protocolos SMB ou FileREST. Este padrão de acesso é principalmente utilizado quando para eliminar o maior número possível de servidores no local.
- Criar uma cache da partilha de ficheiros Azure num servidor no local (ou num Azure VM) com o Azure File Sync, e aceder aos dados da partilha de ficheiros a partir do servidor no local com o seu protocolo de escolha (SMB, NFS, FTPS, etc.) para o seu caso de utilização. Este padrão de acesso é útil porque combina o melhor de desempenho no local e escala de nuvem e serviços anexáveis sem servidor, como O Azure Backup.

Este artigo centra-se em como configurar a rede para quando o seu caso de utilização requer a utilização do Azure File Sync para cache ficheiros no local, em vez de montar diretamente a partilha de ficheiros Azure sobre SMB. Para obter mais informações sobre considerações de networking para uma implementação de [Ficheiros Azure, consulte considerações de networking de ficheiros Azure](storage-files-networking-overview.md).

A configuração de rede para Azure File Sync abrange dois objetos Azure diferentes: um Serviço de Sincronização de Armazenamento e uma conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias partilhas de arquivos, bem como outros recursos de armazenamento, tais como recipientes blob ou filas. Um Serviço de Sincronização de Armazenamento é uma construção de gestão que representa servidores registados, que são servidores de ficheiros Windows com uma relação de confiança estabelecida com a Azure File Sync, e grupos de sincronização, que definem a topologia da relação de sincronização. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Ligando o servidor de ficheiros do Windows ao Azure com o Azure File Sync 
Para configurar e utilizar ficheiros Azure e Azure File Sync com um servidor de ficheiros Windows no local, não é necessária nenhuma rede especial para o Azure para além de uma ligação básica à Internet. Para implementar o Azure File Sync, instale o agente Azure File Sync no servidor de ficheiros Windows que gostaria de sincronizar com o Azure. O agente Azure File Sync consegue sincronização com uma partilha de ficheiros Azure através de dois canais:

- O protocolo FileREST, que é um protocolo baseado em HTTPS para aceder à sua partilha de ficheiros Azure. Como o protocolo FileREST utiliza https padrão para transferência de dados, apenas a porta 443 deve estar acessível de saída. O Azure File Sync não utiliza o protocolo SMB para transferir dados dos seus Servidores Windows no local para a sua partilha de ficheiros Azure.
- O protocolo de sincronização Azure File Sync, que é um protocolo baseado em HTTPS para a troca de conhecimentos de sincronização, ou seja, a informação da versão sobre os ficheiros e pastas no seu ambiente, entre pontos finais no seu ambiente. Este protocolo também é utilizado para a troca de metadados sobre os ficheiros e pastas no seu ambiente, tais como os timetamps e as listas de controlo de acesso (ACLs). 

Uma vez que a Azure Files oferece acesso direto ao protocolo SMB em ações de ficheiros Azure, os clientes muitas vezes perguntam-se se precisam de configurar uma rede especial para montar as ações de ficheiros Azure com a SMB para o agente Azure File Sync aceder. Isto não só não é necessário, como também é desencorajado, exceto para cenários de administrador, devido à falta de deteção de alterações rápidas nas alterações feitas diretamente à partilha de ficheiros Azure (as alterações podem não ser descobertas por mais de 24 horas, dependendo do tamanho e número de itens na partilha de ficheiros Azure). Se desejar utilizar diretamente a partilha de ficheiros Azure, ou seja, não utilizar o Azure File Sync para cache no local, pode saber mais sobre as considerações de networking para acesso direto [consultando a visão geral da rede Azure Files](storage-files-networking-overview.md).

Embora o Azure File Sync não exija nenhuma configuração especial de rede, alguns clientes podem desejar configurar definições avançadas de rede para permitir os seguintes cenários:

- Interoperando com a configuração do servidor proxy da sua organização.
- Abra a firewall da sua organização no local para os serviços Azure Files e Azure File Sync.
- Ficheiros Azure do Túnel e Sincronização de Ficheiros Azure sobre ExpressRoute ou uma ligação VPN.

### <a name="configuring-proxy-servers"></a>Configurar servidores de procuração
Muitas organizações usam um servidor proxy como intermediário entre recursos dentro da sua rede no local e recursos fora da sua rede, como em Azure. Os servidores proxy são úteis para muitas aplicações, tais como isolamento e segurança de rede, e monitorização e registo. O Azure File Sync pode interoperar totalmente com um servidor proxy, no entanto deve configurar manualmente as definições de ponto final de procuração para o seu ambiente com Azure File Sync. Isto deve ser feito através do PowerShell utilizando as cmdlets do servidor Azure File Sync. 

Para obter mais informações sobre como configurar o Azure File Sync com um servidor proxy, consulte [Configurar o Azure File Sync com um servidor de procuração](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Configurar firewalls e tags de serviço
Pode isolar os seus servidores de ficheiros da maioria da localização da Internet para fins de segurança. Para utilizar o Azure File Sync no seu ambiente, precisa de ajustar a sua firewall para abri-la para selecionar os serviços Azure. Pode fazê-lo recuperando as gamas de endereços IP para os serviços que necessitava através de um mecanismo chamado [tags de serviço](../../virtual-network/service-tags-overview.md).

O Azure File Sync requer os intervalos de endereços IP para os seguintes serviços, identificados pelas suas etiquetas de serviço:

| Serviço | Description | Etiqueta de serviço |
|---------|-------------|-------------|
| Azure File Sync | O serviço Azure File Sync, representado pelo objeto Storage Sync Service, é responsável pela atividade principal de sincronização de dados entre uma partilha de ficheiros Azure e um servidor de ficheiros Windows. | `StorageSyncService` |
| Ficheiros do Azure | Todos os dados sincronizados via Azure File Sync são armazenados na partilha de ficheiros Azure. Os ficheiros alterados nos servidores de ficheiros do Windows são replicados na sua partilha de ficheiros Azure e os ficheiros incluídos no servidor de ficheiros no local são descarregados sem problemas quando um utilizador os solicita. | `Storage` |
| Azure Resource Manager | O Azure Resource Manager é a interface de gestão do Azure. Todas as chamadas de gestão, incluindo o registo do servidor Azure File Sync e as tarefas do servidor de sincronização em curso, são es feitas através do Gestor de Recursos Azure. | `AzureResourceManager` |
| Azure Active Directory | O Azure Ative Directory, ou Azure AD, contém os principais utilizadores necessários para autorizar o registo do servidor contra um Serviço de Sincronização de Armazenamento, e os principais serviços necessários para que o Azure File Sync seja autorizado a aceder aos seus recursos na nuvem. | `AzureActiveDirectory` |

Se estiver a utilizar o Azure File Sync dentro do Azure, mesmo que seja uma região diferente, pode utilizar o nome da etiqueta de serviço diretamente no seu grupo de segurança de rede para permitir o tráfego para esse serviço. Para saber mais sobre como fazê-lo, consulte [os grupos de segurança da Rede.](../../virtual-network/security-overview.md) 

Se estiver a utilizar o Azure File Sync no local, pode utilizar a marca de serviço API para obter intervalos de endereços IP específicos para a lista de autorizações da sua firewall. Existem dois métodos para obter esta informação:

- A lista atual de intervalos de endereços IP para todos os serviços Azure que suportam tags de serviço são publicadas semanalmente no Microsoft Download Center sob a forma de um documento JSON. Cada nuvem Azure tem o seu próprio documento JSON com os intervalos de endereço IP relevantes para essa nuvem:
    - [Público de Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
- A advimento da marca de serviço API (pré-visualização) permite a recuperação programática da lista atual de tags de serviço. Na pré-visualização, a a API de descoberta da marca de serviço pode devolver informações menos atuais do que as informações devolvidas dos documentos JSON publicados no Microsoft Download Center. Pode utilizar a superfície API com base na sua preferência de automatização:
    - [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [CLI do Azure](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Para saber mais sobre como utilizar a marca de serviço API para recuperar os endereços dos seus serviços, consulte [a lista de permitir endereços IP do Azure File Sync](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Fazer túneis sobre uma rede privada virtual ou ExpressRoute
Algumas organizações exigem a comunicação com a Azure para passar por um túnel de rede, como uma rede privada virtual privada (VPN) ou ExpressRoute, para uma camada adicional de segurança ou para garantir a comunicação com a Azure segue uma rota determinística. 

Quando estabelece um túnel de rede entre a sua rede no local e o Azure, está a espreitar a sua rede no local com uma ou mais redes virtuais em Azure. Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que você operaria no local. Como uma conta de armazenamento Azure ou um VM Azure, um VNet é um recurso Azure que é implantado num grupo de recursos. 

Os Ficheiros Azure e o File Sync suportam os seguintes mecanismos para fazer um túnel de tráfego entre os servidores do local e o Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Um gateway VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego encriptado entre uma rede virtual Azure e uma localização alternativa (como no local) através da internet. Um Azure VPN Gateway é um recurso Azure que pode ser implantado num grupo de recursos juntamente com uma conta de armazenamento ou outros recursos Azure. Uma vez que o Azure File Sync deve ser utilizado com um servidor de ficheiros Windows no local, normalmente utilizaria uma [VPN site-to-site (S2S), embora](../../vpn-gateway/design.md#s2smulti)seja tecnicamente possível utilizar uma [VPN ponto-a-local (P2S).](../../vpn-gateway/point-to-site-about.md) 

    As ligações VPN site-to-site (S2S) ligam a sua rede virtual Azure e a rede de instalações da sua organização. Uma ligação S2S VPN permite-lhe configurar uma ligação VPN uma vez, para um servidor VPN ou dispositivo alojado na rede da sua organização, em vez de fazer para cada dispositivo cliente que precisa aceder à sua partilha de ficheiros Azure. Para simplificar a implantação de uma ligação S2S VPN, consulte [configurar uma VPN site-to-site (S2S) para utilização com Ficheiros Azure](storage-files-configure-s2s-vpn.md).

- [ExpressRoute,](../../expressroute/expressroute-introduction.md)que lhe permite criar uma rota definida entre o Azure e a sua rede no local que não atravessa a internet. Uma vez que o ExpressRoute fornece um caminho dedicado entre o datacenter no local e o Azure, o ExpressRoute pode ser útil quando o desempenho da rede é uma consideração. O ExpressRoute também é uma boa opção quando a política ou os requisitos regulamentares da sua organização requerem um caminho determinístico para os seus recursos na nuvem.

### <a name="private-endpoints"></a>Pontos finais privados
Para além dos pontos finais públicos predefinidos, os Ficheiros Azure e o File Sync fornecem através da conta de armazenamento e do Serviço de Sincronização de Armazenamento, os Ficheiros Azure e o File Sync oferecem a opção de ter um ou mais pontos finais privados por recurso. Quando cria um ponto final privado para um recurso Azure, obtém um endereço IP privado a partir do espaço de endereço da sua rede virtual, tal como o servidor de ficheiros Windows no local tem um endereço IP dentro do espaço de endereço dedicado da sua rede de instalações. 

> [!Important]  
> Para utilizar pontos finais privados no recurso Storage Sync Service, tem de utilizar a versão 10.1 ou superior do agente Azure File Sync. As versões do agente antes do 10.1 não suportam pontos finais privados no Serviço de Sincronização de Armazenamento. Todas as versões anteriores do agente suportam pontos finais privados no recurso da conta de armazenamento.

Um ponto final privado individual está associado a uma sub-rede de rede virtual Azure específica. As contas de armazenamento e os Serviços de Sincronização de Armazenamento podem ter pontos finais privados em mais de uma rede virtual.

A utilização de pontos finais privados permite-lhe:
- Ligue-se seguramente aos seus recursos Azure a partir de redes no local utilizando uma ligação VPN ou ExpressRoute com o seu olhar privado.
- Proteja os seus recursos Azure desativando os pontos finais públicos para ficheiros Azure e File Sync. Por predefinição, a criação de um ponto final privado não bloqueia as ligações ao ponto final público.
- Aumentar a segurança da rede virtual, permitindo-lhe bloquear a exfiltração de dados a partir da rede virtual (e os limites de espreitar).

Para criar um ponto final privado, consulte [configurar pontos finais privados para Azure File Sync](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Pontos finais privados e DNS
Quando cria um ponto final privado, por padrão também criamos uma (ou atualização) de uma zona de DNS privada existente correspondente ao `privatelink` subdomínio. Para as regiões de nuvem pública, estas zonas de DNS são `privatelink.file.core.windows.net` para Ficheiros Azure e `privatelink.afs.azure.net` para Azure File Sync.

> [!Note]  
> Este artigo utiliza o sufixo DNS da conta de armazenamento para as regiões públicas do Azure, `core.windows.net` . Este comentário também se aplica às nuvens soberanas de Azure, como a nuvem do Governo dos EUA Azure e a nuvem Azure China - basta substituir os sufixos apropriados para o seu ambiente. 

Quando cria pontos finais privados para uma conta de armazenamento e um Serviço de Sincronização de Armazenamento, criamos registos A para eles nas respetivas zonas privadas de DNS. Também atualizamos a entrada pública de DNS de modo que os nomes de domínio regulares totalmente qualificados são CNAMEs para o nome de privatelink relevante. Isto permite que os nomes de domínio totalmente qualificados apontem para o endereço IP do ponto final privado quando o solicitador está dentro da rede virtual e apontar para o endereço IP do ponto final público quando o solicitador estiver fora da rede virtual. 

Para ficheiros Azure, cada ponto final privado tem um único nome de domínio totalmente qualificado, seguindo o padrão `storageaccount.privatelink.file.core.windows.net` , mapeado para um endereço IP privado para o ponto final privado. Para o Azure File Sync, cada ponto final privado tem quatro nomes de domínio totalmente qualificados, para os quatro pontos finais diferentes que o Azure File Sync expõe: gestão, sincronização (primária), sincronização (secundária) e monitorização. Os nomes de domínio totalmente qualificados para estes pontos finais seguirão normalmente o nome do Serviço de Sincronização de Armazenamento, a menos que o nome contenha caracteres não ASCII. Por exemplo, se o seu nome de Serviço de Sincronização de Armazenamento estiver `mysyncservice` na região oeste dos EUA 2, os pontos finais equivalentes seriam `mysyncservicemanagement.westus2.afs.azure.net` , e `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` `mysyncservicemonitoring.westus2.afs.azure.net` . Cada ponto final privado de um Serviço de Sincronização de Armazenamento conterá 4 endereços IP distintos. 

Uma vez que a sua zona de DNS privada Azure está ligada à rede virtual que contém o ponto final privado, pode observar a configuração de DNS quando ligando para o `Resolve-DnsName` cmdlet do PowerShell num VM Azure (alternadamente `nslookup` no Windows e Linux):

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

Isto reflete o facto de que os Ficheiros Azure e o Azure File Sync podem expor tanto os seus pontos finais públicos como um ou mais pontos finais privados por recurso. Para garantir que os nomes de domínio totalmente qualificados para os seus recursos se resolvam para os endereços IP privados de pontos finais privados, tem de alterar a configuração nos servidores DNS no local. Isto pode ser realizado de várias maneiras:

- Modificar o ficheiro de anfitriões nos seus clientes para que os nomes de domínio totalmente qualificados para as suas contas de armazenamento e Serviços de Sincronização de Armazenamento resolvam os endereços IP privados pretendidos. Isto é fortemente desencorajado para ambientes de produção, uma vez que você precisará fazer estas alterações a cada cliente que precisa de aceder aos seus pontos finais privados. As alterações nos seus pontos finais/recursos privados (supressões, modificações, etc.) não serão manuseadas automaticamente.
- Criar zonas DNS nos seus servidores no local `privatelink.file.core.windows.net` para e `privatelink.afs.azure.net` com registos A para os seus recursos Azure. Isto tem a vantagem de que os clientes no seu ambiente no local serão capazes de resolver automaticamente os recursos da Azure sem precisarem de configurar cada cliente, no entanto esta solução é igualmente frágil para modificar o ficheiro dos anfitriões porque as alterações não se refletem. Embora esta solução seja frágil, pode ser a melhor escolha para alguns ambientes.
- Encaminhe as `core.windows.net` zonas e `afs.azure.net` zonas dos seus servidores DNS no local para a sua zona de DNS privada Azure. O anfitrião DNS privado Azure pode ser alcançado através de um endereço IP especial ( `168.63.129.16` ) que só é acessível dentro de redes virtuais que estão ligadas à zona privada de DNS Azure. Para contornar esta limitação, pode executar servidores DNS adicionais dentro da sua rede virtual que irão encaminhar `core.windows.net` e passar para as `afs.azure.net` zonas de DNS privadas equivalentes do Azure. Para simplificar esta configuração, fornecemos cmdlets PowerShell que irão implantar automaticamente servidores DNS na sua rede virtual Azure e configurá-los conforme desejado. Para aprender a configurar o reencaminhamento de DNS, consulte [configurar DNS com Ficheiros Azure](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Encriptação em trânsito
As ligações efetuadas do agente Azure File Sync à sua partilha de ficheiros Azure ou ao Serviço de Sincronização de Armazenamento estão sempre encriptadas. Embora as contas de armazenamento do Azure tenham uma definição para desativar a encriptação necessária em trânsito para comunicações para ficheiros Azure (e os outros serviços de armazenamento Azure que são geridos fora da conta de armazenamento), a desativação desta definição não afetará a encriptação do Azure File Sync ao comunicar com os Ficheiros Azure. Por padrão, todas as contas de armazenamento Azure têm encriptação em trânsito ativada. 

Para obter mais informações sobre encriptação em trânsito, consulte [a necessidade de transferência segura no armazenamento Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Consulte também
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)