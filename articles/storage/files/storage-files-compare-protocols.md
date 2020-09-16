---
title: Protocolos de Ficheiros Azure disponíveis
description: Saiba mais sobre os protocolos disponíveis antes de criar uma partilha de ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8082e694c4282759c9c38560c14eb3659fcd55ec
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708143"
---
# <a name="azure-file-share-protocols"></a>Protocolos de partilha de ficheiros Azure

A Azure Files oferece dois protocolos para ligar e montar as suas ações de ficheiroS Azure. [Protocolo do Bloco de Mensagens do Servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e [protocolo do Sistema de Ficheiros de Rede (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (pré-visualização). A Azure Files não suporta atualmente o acesso a vários protocolos, pelo que uma partilha só pode ser uma partilha NFS ou uma participação SMB. Devido a isso, recomendamos determinar qual o protocolo que melhor se adequa às suas necessidades antes de criar ações de ficheiros Azure.

## <a name="differences-at-a-glance"></a>Diferenças num ápice

|Funcionalidade  |NFS (pré-visualização)  |SMB  |
|---------|---------|---------|
|Protocolos de acesso     |NFS 4.1         |SMB 2.1, SMB 3.0         |
|SoA apoiado     |Linux versão 4.3+         |Windows 2008 R2+, linux versão 4.11+         |
|Escalões disponíveis     |Armazenamento Premium         |Armazenamento premium, armazenamento padrão, quente, fresco         |
|Replicação     |LRS         |LRS, ZRS, GRS         |
|Autenticação     |Apenas autenticação baseada no anfitrião        |Autenticação baseada em identidade, autenticação baseada no utilizador         |
|Permissões     |Permissões ao estilo UNIX         |Permissões ao estilo NTFS         |
|Semântica do sistema de ficheiros     |Compatível com POSIX         |Não compatível com POSIX         |
|Sensibilidade ao caso     |Sensível a maiúsculas e minúsculas         |Não sensível a casos         |
|Suporte de ligação rígida     |Suportado         |Não suportado         |
|Suporte de links simbólicos     |Suportado         |Não suportado         |
|Apagar ou modificar ficheiros abertos     |Suportado         |Não suportado         |
|Bloquear     |Gestor de bloqueio de rede de aconselhamento byte-range         |Suportado         |
|Listagem de cofre ip público | Não suportado | Suportado|
|Protocolo interop| Não suportado | FileREST|

## <a name="nfs-shares-preview"></a>Ações NFS (pré-visualização)

A montagem de ações de ficheiros Azure com a NFS 4.1 está atualmente em pré-visualização. Oferece uma integração mais apertada com o Linux. Esta é uma oferta totalmente compatível com POSIX que é um padrão através de variantes de Unix e outros sistemas operativos baseados em *nix. Este serviço de armazenamento de ficheiros de nível empresarial escala-se para atender às suas necessidades de armazenamento e pode ser acedido simultaneamente por milhares de casos de computação.

### <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Mais adequado

NFS com Ficheiros Azure é ideal para:

- Cargas de trabalho que requerem partilhas de ficheiros compatíveis com POSIX, sensibilidade ao caso ou permissões de estilo Unix (UID/GID).
- Cargas de trabalho centradas no Linux que não requerem acesso ao Windows.

### <a name="security"></a>Segurança

Todos os dados do Azure Files estão encriptados em repouso. Para encriptação em trânsito, o Azure fornece uma camada de encriptação para todos os dados em trânsito entre datacenters Azure usando [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Através desta, existe encriptação quando os dados são transferidos entre centros de dados Azure. Ao contrário dos Ficheiros Azure que utilizam o protocolo SMB, as ações de ficheiros que utilizam o protocolo NFS não oferecem autenticação baseada no utilizador. A autenticação das ações NFS baseia-se nas regras de segurança de rede configuradas. Devido a isso, para garantir que apenas as ligações seguras são estabelecidas na sua quota NFS, deve utilizar ambos os pontos finais de serviço ou pontos finais privados. Se quiser aceder a ações a partir de instalações, então, para além de um ponto final privado, tem de configurar uma VPN ou ExpressRoute. Os pedidos que não tenham origem nas seguintes fontes serão rejeitados:

- [Um ponto final privado](storage-files-networking-overview.md#private-endpoints)
- [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [VPN ponto a local (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Site-a-Site](https://docs.microsoft.com/azure/vpn-gateway/design#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Um ponto final público restrito](storage-files-networking-overview.md#storage-account-firewall-settings)

Para obter mais detalhes sobre as opções de networking disponíveis, consulte [as considerações de networking do Azure Files](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Ações da SMB

As ações de ficheiros Azure montadas com SMB oferecem mais funcionalidades de Ficheiros Azure e não têm restrições de funcionalidades Azure Files, uma vez que geralmente está disponível.

### <a name="features"></a>Funcionalidades

- Sincronização de ficheiros Azure
- Autenticação baseada em identidade
- Suporte de backup Azure
- Instantâneos
- Eliminação recuperável
- Encriptação em trânsito e encriptação em repouso

### <a name="best-suited"></a>Mais adequado

SMB com Ficheiros Azure é ideal para:

- Ambientes de produção
- Clientes que requerem qualquer uma das funcionalidades listadas em [Funcionalidades](#features)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma partilha de ficheiros NFS](storage-files-how-to-create-nfs-shares.md)
- [Criar uma partilha de ficheiros SMB](storage-how-to-create-file-share.md)