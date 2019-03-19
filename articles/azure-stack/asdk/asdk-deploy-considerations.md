---
title: Pré-requisitos da implementação (ASDK) do Stack Development Kit do Azure | Documentos da Microsoft
description: Reveja os requisitos de hardware e de ambiente para o Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: f3fdfcc6a2fbb527582d4bf242c039a778b84ed1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877994"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Implementação de pilha do Azure, considerações de planeamento
Antes de implementar o Azure Stack Development Kit (ASDK), certifique-se de que o computador de anfitrião de kit de desenvolvimento cumpre os requisitos descritos neste artigo.


## <a name="hardware"></a>Hardware
| Componente | Mínimo | Recomendado |
| --- | --- | --- |
| Unidades de disco: Sistema Operativo |1 disco do sistema operativo com o mínimo de 200 GB disponível para a partição do sistema (SSD ou HDD) |1 disco do SO com 200 GB disponíveis, no mínimo, para a partição do sistema (SSD ou HDD) |
| Unidades de disco: Dados do kit de desenvolvimento geral<sup>*</sup>  |4 discos. Cada disco fornece um mínimo de 240 GB de capacidade (SSD ou HDD). Todos os discos disponíveis são utilizados. |4 discos. Cada disco fornece um mínimo de 400 GB de capacidade (SSD ou HDD). Todos os discos disponíveis são utilizados. |
| Computação: CPU |Dual-Socket: 16 núcleos físicos (total) |Dual-Socket: 20 núcleos físicos (total) |
| Computação: Memória |192 GB DE RAM |256 GB DE RAM |
| Computação: BIOS |Hyper-V Ativado (com suporte SLAT) |Hyper-V Ativado (com suporte SLAT) |
| Rede: NIC |Certificação do Windows Server 2012 R2. Nenhuma funcionalidade específica necessária |Certificação do Windows Server 2012 R2. Nenhuma funcionalidade específica necessária |
| Certificação de logótipo do HW |[Certificação do Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificação do Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Precisa de mais do que esta capacidade de recomendada se planear sobre como adicionar muitas da [itens do marketplace](asdk-marketplace-item.md) do Azure.

**Configuração de unidade de disco de dados:** Todas as unidades de dados tem de ser do mesmo tipo (todas SAS, SATA todos os ou todos NVMe) e capacidade. Se forem utilizadas unidades de disco SAS, as unidades de disco devem ser ligadas através de um caminho único (sem MPIO; é fornecido suporte a caminhos múltiplos).

**Opções de configuração de HBA**

* (Preferencial) HBA Simples
* HBA RAID – O adaptador deve ser configurado no modo “Pass Through”
* HBA RAID – Os discos devem ser configurados como Disco Único, RAID-0

**Combinações de tipo de barramento suportado e suporte de dados**

* HDD SATA
* HDD SAS
* HDD RAID
* RAID SSD (se o tipo de suporte de dados for especificado/for desconhecido<sup>*</sup>)
* SSD SATA + HDD SATA
* SSD SAS + HDD SAS
* NVMe

<sup>*</sup> Os controladores RAID sem capacidade pass-through não consegue reconhecer o tipo de suporte. Esses controladores marcam HDD e SSD como não especificado. Nesse caso, o SSD é utilizado como armazenamento persistente, em vez de dispositivos de colocação em cache. Por conseguinte, pode implementar o development kit nessas SSDs.

**Exemplo de HBAs**: LSI 9207 8i, LSI-9300-8i ou LSI-9265-8i no modo pass-through

Estão disponíveis configurações do OEM de exemplo.

## <a name="operating-system"></a>Sistema operativo
|  | **Requisitos** |
| --- | --- |
| **Versão do SO** |Windows Server 2016 ou posterior. A versão do sistema operativo não é crítica antes de inicia a implantação, pois irá arrancar o computador anfitrião para o VHD que está incluído na instalação do Azure Stack. O sistema operativo e todos os patches necessários já estão integrados na imagem. Não utilize todas as chaves para ativar qualquer instâncias do Windows Server utilizadas no kit de desenvolvimento. |

> [!TIP]
> Depois de instalar o sistema operativo, pode utilizar o [Verificador de implementação para o Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que o hardware cumpre todos os requisitos.

## <a name="account-requirements"></a>Requisitos de conta
Normalmente, implementa o kit de desenvolvimento com a conectividade de internet, onde pode ligar ao Microsoft Azure. Neste caso, tem de configurar uma conta do Azure Active Directory (Azure AD) para implementar o development kit.

Se seu ambiente não está ligado à internet ou se não quiser utilizar o Azure AD, pode implementar o Azure Stack, utilizando os serviços de Federação do Active Directory (AD FS). O kit de desenvolvimento inclui suas próprias instâncias do AD FS e os serviços de domínio do Active Directory. Se implementar com esta opção, não tem de configurar contas de antecedência.

> [!NOTE]
> Se implementar através da opção do AD FS, tem de voltar a implementar o Azure Stack para mudar para o Azure AD.

### <a name="azure-active-directory-accounts"></a>Contas do Azure Active Directory
Para implementar o Azure Stack, utilizando uma conta do Azure AD, tem de preparar uma conta do Azure AD antes de executar a script do PowerShell de implementação. Esta conta torna-se o Administrador Global do inquilino do Azure AD. É utilizado para aprovisionar e delegar principais de serviço para todos os serviços do Azure Stack que interagem com o Azure Active Directory e a Graph API e aplicações. Também é utilizado como o proprietário da subscrição do fornecedor do padrão (que é possível alterar mais tarde). Pode iniciar sessão portal de administrador do sistema do Azure Stack com esta conta.

1. Crie uma conta do Azure AD que é o administrador de diretórios para, pelo menos, um Azure AD. Se já tiver uma conta, pode utilizá-la. Caso contrário, pode criar uma gratuitamente na [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/free/) (na China, visite <https://go.microsoft.com/fwlink/?LinkID=717821> em vez disso). Se pretender mais tarde [registar o Azure Stack com o Azure](asdk-register.md), também tem de ter uma subscrição no recentemente criada a conta.
   
    Guarde estas credenciais para ser utilizado como o administrador de serviços. Esta conta pode configurar e gerir nuvens de recursos, contas de utilizador, planos de inquilino, quotas e preços. No portal, pode criar nuvens de sites, nuvens privadas de máquinas virtuais, criar planos e gerir subscrições do utilizador.
1. Crie conta de utilizador de pelo menos um teste no seu Azure AD para que pode entrar para o kit de desenvolvimento como um inquilino.
   
   | **Conta do Azure Active Directory** | **Suporte?** |
   | --- | --- |
   | Conta escolar ou profissional com subscrição pública do Azure válida |Sim |
   | Conta da Microsoft com a Subscrição Pública do Azure válida |Sim |
   | Conta escolar ou profissional com uma subscrição do Azure válida na China |Sim |
   | Conta escolar ou profissional com US Government subscrição do Azure válida |Sim |

Após a implementação, a permissão de administrador global do Azure Active Directory não é necessário. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador de fornecedor de recursos ou um novo recurso que requerem uma permissão para ser concedida. Pode temporariamente instate novamente as permissões de administrador global da conta ou utilizar uma conta de administrador global separado que é proprietária dos *predefinido da subscrição do fornecedor*.

## <a name="network"></a>Rede
### <a name="switch"></a>Comutador
Uma porta disponível num comutador para a máquina do kit de desenvolvimento.  

A máquina do kit de desenvolvimento oferece suporte a ligar a uma porta de comutador de acesso ou a porta de ramal. Não são necessárias quaisquer funcionalidades especializadas no comutador. Se estiver a utilizar uma porta de ramal ou se tiver de configurar um ID de VLAN, terá de fornecer o ID de VLAN como um parâmetro de implementação.

### <a name="subnet"></a>Subrede
Não ligar a máquina do kit de desenvolvimento para as sub-redes seguintes:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Estas sub-redes estão reservados para as redes internas no ambiente do kit de desenvolvimento.

### <a name="ipv4ipv6"></a>IPv4/IPv6
É apenas suportado o IPv4. Não pode criar redes IPv6.

### <a name="dhcp"></a>DHCP
Verifique se existe um servidor DHCP disponível na rede à qual liga o NIC. Se o DHCP não estiver disponível, terá de preparar uma rede IPv4 estática adicional para além daquela utilizada pelo anfitrião. Tem de fornecer esse endereço IP e gateway como um parâmetro de implementação.

### <a name="internet-access"></a>Acesso à Internet
O Azure Stack requer acesso à Internet, diretamente ou através de um proxy transparente. O Azure Stack não suporta a configuração de proxy web para ativar o acesso à Internet. O IP do anfitrião e o novo IP atribuído ao AzS-BGPNAT01 (pelo DHCP ou IP estático) tem de ser capaz de acessar a Internet. As portas 80 e 443 são utilizadas em domínios graph.windows.net e login.microsoftonline.com.


## <a name="next-steps"></a>Passos Seguintes
[Transferir o pacote de implementação de ASDK](asdk-download.md)
