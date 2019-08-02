---
title: Visão geral da criptografia do Azure | Microsoft Docs
description: Saiba mais sobre as várias opções de criptografia no Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 62fcc19f3cfe9109097d1f2a70d785060b5bdc53
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727360"
---
# <a name="azure-encryption-overview"></a>Visão geral da criptografia do Azure

Este artigo fornece uma visão geral de como a criptografia é usada em Microsoft Azure. Ele abrange as principais áreas de criptografia, incluindo criptografia em repouso, criptografia em trânsito e gerenciamento de chaves com Azure Key Vault. Cada seção inclui links para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

Os dados em repouso incluem informações que residem no armazenamento persistente em mídia física, em qualquer formato digital. A mídia pode incluir arquivos em mídia magnética ou óptica, dados arquivados e backups de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para atender a diferentes necessidades, incluindo o armazenamento de arquivos, discos, BLOBs e tabelas. A Microsoft também fornece criptografia para proteger o [banco de dados SQL do Azure](../../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)e Azure data Lake.

A criptografia de dados em repouso está disponível para serviços em um software como serviço (SaaS), plataforma como serviço (PaaS) e modelos de nuvem de IaaS (infraestrutura como serviço). Este artigo resume e fornece recursos para ajudá-lo a usar as opções de criptografia do Azure.

Para obter uma discussão mais detalhada sobre como os dados em repouso são criptografados no Azure, consulte [criptografia de dados do Azure em repouso](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de criptografia do Azure

O Azure dá suporte a vários modelos de criptografia, incluindo a criptografia do lado do servidor que usa chaves gerenciadas pelo serviço, chaves gerenciadas pelo cliente no Key Vault ou chaves gerenciadas pelo cliente no hardware controlado pelo cliente. Com a criptografia do lado do cliente, você pode gerenciar e armazenar chaves locais ou em outro local seguro.

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

A criptografia do lado do cliente é executada fora do Azure. Ele inclui:

- Dados criptografados por um aplicativo em execução no datacenter do cliente ou por um aplicativo de serviço.
- Dados que já estão criptografados quando são recebidos pelo Azure.

Com a criptografia do lado do cliente, os provedores de serviço de nuvem não têm acesso às chaves de criptografia e não podem descriptografar esses dados. Você mantém o controle total das chaves.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os três modelos de criptografia do lado do servidor oferecem diferentes características de gerenciamento de chaves, que podem ser escolhidas de acordo com seus requisitos:

- **Chaves gerenciadas pelo serviço**: Fornece uma combinação de controle e conveniência com baixa sobrecarga.

- **Chaves gerenciadas pelo cliente**: Fornece controle sobre as chaves, incluindo o suporte a BYOK (Traga suas próprias chaves) ou permite que você gere novas.

- **Chaves gerenciadas pelo serviço em hardware controlado pelo cliente**: Permite que você gerencie chaves em seu repositório proprietário, fora do Microsoft Control. Essa característica é chamada de host de sua própria chave (HYOK). No entanto, a configuração é complexa e a maioria dos serviços do Azure não oferece suporte a esse modelo.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Você pode proteger as máquinas virtuais do Windows e do Linux usando o [Azure Disk Encryption](/azure/security/azure-security-disk-encryption), que usa a tecnologia do [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) e o [DM](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para proteger os discos do sistema operacional e os discos de dados com a criptografia de volume completo.

As chaves de criptografia e os segredos são protegidos em sua [assinatura do Azure Key Vault](../../key-vault/key-vault-whatis.md). Usando o serviço de backup do Azure, você pode fazer backup e restaurar VMs (máquinas virtuais) criptografadas que usam a configuração KEK (chave de criptografia de chave).

### <a name="azure-storage-service-encryption"></a>Encriptação do Serviço de Armazenamento do Azure

Dados em repouso no armazenamento de BLOBs do Azure e compartilhamentos de arquivos do Azure podem ser criptografados em cenários do lado do servidor e do lado do cliente.

O [Azure criptografia do serviço de armazenamento (SSE)](../../storage/common/storage-service-encryption.md) pode criptografar automaticamente os dados antes de serem armazenados, e ele descriptografa automaticamente os dados quando você o recupera. O processo é completamente transparente para os usuários. O Criptografia do Serviço de Armazenamento usa a [criptografia AES (256 bits criptografia AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), que é uma das codificações de bloco mais fortes disponíveis. O AES manipula a criptografia, a descriptografia e o gerenciamento de chaves de forma transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Criptografia do lado do cliente de BLOBs do Azure

Você pode executar a criptografia do lado do cliente de BLOBs do Azure de várias maneiras.

Você pode usar o pacote NuGet da biblioteca de cliente do armazenamento do Azure para .NET para criptografar dados em seus aplicativos cliente antes de carregá-los no armazenamento do Azure.

Para saber mais sobre o e baixar a biblioteca de cliente do armazenamento do Azure para o pacote NuGet do .NET, confira [8.3.0 de armazenamento do Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

Quando você usa a criptografia do lado do cliente com Key Vault, seus dados são criptografados usando uma CEK (chave de criptografia de conteúdo simétrico) única que é gerada pelo SDK do cliente de armazenamento do Azure. O CEK é criptografado usando uma chave de criptografia de chave (KEK), que pode ser uma chave simétrica ou um par de chaves assimétricas. Você pode gerenciá-lo localmente ou armazená-lo em Key Vault. Os dados criptografados são então carregados no armazenamento do Azure.

Para saber mais sobre a criptografia do cliente com o Key Vault e introdução às instruções, consulte [o tutorial: Criptografar e descriptografar BLOBs no armazenamento do](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)Azure usando Key Vault.

Por fim, você também pode usar a biblioteca de cliente de armazenamento do Azure para Java para executar a criptografia do lado do cliente antes de carregar dados no armazenamento do Azure e para descriptografar os dados ao baixá-los para o cliente. Essa biblioteca também dá suporte à integração com o [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves da conta de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Criptografia de dados em repouso com o banco de dado SQL do Azure

O banco de dados [SQL do Azure](../../sql-database/sql-database-technical-overview.md) é um serviço de banco de dados relacional de uso geral no Azure que dá suporte a estruturas como dados relacionais, JSON, espacial e XML. O banco de dados SQL dá suporte à criptografia do lado do servidor por meio do recurso Transparent Data Encryption (TDE) e da criptografia do lado do cliente por meio do recurso Always Encrypted.

#### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

O [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) é usado para criptografar [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), o banco de dados [SQL do Azure](../../sql-database/sql-database-technical-overview.md)e os arquivos [do Azure SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) data em tempo real, usando uma DEK (chave de criptografia de banco de), que é armazenada no registro de inicialização do banco de dado para disponibilidade durante a recuperação.

O TDE protege arquivos de dados e de log, usando algoritmos de criptografia AES e 3DES (padrão de criptografia de dados triplo). A criptografia do arquivo de banco de dados é executada no nível da página. As páginas em um banco de dados criptografado são criptografadas antes de serem gravadas no disco e são descriptografadas quando são lidas na memória. O TDE agora está habilitado por padrão em bancos de dados SQL do Azure recém-criados.

#### <a name="always-encrypted-feature"></a>Always Encrypted recurso

Com o recurso [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no SQL do Azure, você pode criptografar dados em aplicativos cliente antes de armazená-los no banco de dados SQL do Azure. Você também pode habilitar a delegação de administração de banco de dados local para terceiros e manter a separação entre aqueles que possuem e pode exibir os dados e aqueles que o gerenciam, mas não devem ter acesso a ele.

#### <a name="cell-level-or-column-level-encryption"></a>Criptografia em nível de célula ou de coluna

Com o banco de dados SQL do Azure, você pode aplicar a criptografia simétrica a uma coluna de data usando o Transact-SQL. Essa abordagem é chamada de criptografia em nível de [célula ou cle (criptografia em nível de coluna)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), pois você pode usá-la para criptografar colunas específicas ou até mesmo células de dados específicas com chaves de criptografia diferentes. Isso proporciona um recurso de criptografia mais granular do que o TDE, que criptografa dados em páginas.

O CLE tem funções internas que você pode usar para criptografar dados usando chaves simétricas ou assimétricas, a chave pública de um certificado ou uma senha usando 3DES.

### <a name="cosmos-db-database-encryption"></a>Criptografia de banco de dados Cosmos DB

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) é o banco de dados multimodelo distribuído globalmente da Microsoft. Os dados do usuário armazenados em Cosmos DB no armazenamento não volátil (unidades de estado sólido) são criptografados por padrão. Não há controles para ativar ou desativar. Encriptação inativa é implementada com um número de tecnologias de segurança, incluindo sistemas de armazenamento de chaves segura, redes encriptadas e APIs criptográficas. As chaves de criptografia são gerenciadas pela Microsoft e são giradas de acordo com as diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Criptografia em repouso no Data Lake

[Azure data Lake](../../data-lake-store/data-lake-store-encryption.md) é um repositório de toda a empresa de todos os tipos de dados coletados em um único local antes de qualquer definição formal de requisitos ou esquema. O Data Lake Store dá suporte a "ativado por padrão", criptografia transparente de dados em repouso, que é configurada durante a criação de sua conta. Por padrão, o Azure Data Lake Store gerencia as chaves para você, mas você tem a opção de gerenciá-las por conta própria.

Três tipos de chaves são usados para criptografar e descriptografar dados: a chave de criptografia mestra (MEK), a chave de criptografia de dados (DEK) e a BEK (chave de criptografia de bloco). O MEK é usado para criptografar o DEK, que é armazenado em mídia persistente, e o BEK é derivado do DEK e do bloco de dados. Se você estiver gerenciando suas próprias chaves, poderá girar o MEK.

## <a name="encryption-of-data-in-transit"></a>Criptografia de dados em trânsito

O Azure oferece muitos mecanismos para manter os dados privados à medida que eles se movem de um local para outro.

### <a name="tlsssl-encryption-in-azure"></a>Criptografia TLS/SSL no Azure

A Microsoft usa [o protocolo](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS para proteger dados quando está viajando entre os serviços de nuvem e os clientes. Os data centers da Microsoft negociam uma conexão TLS com sistemas cliente que se conectam aos serviços do Azure. O TLS fornece autenticação forte, privacidade de mensagens e integridade (permitindo a detecção de adulteração de mensagem, interceptação e falsificação), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.

[Sigilo de encaminhamento perfeito](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protege as conexões entre os sistemas cliente dos clientes e os serviços de nuvem da Microsoft por chaves exclusivas. As conexões também usam comprimentos de chave de criptografia de 2.048 bits baseados em RSA. Essa combinação torna difícil para alguém interceptar e acessar dados em trânsito.

### <a name="azure-storage-transactions"></a>Transações de armazenamento do Azure

Quando você interage com o armazenamento do Azure por meio do portal do Azure, todas as transações ocorrem por HTTPS. Você também pode usar a API REST de armazenamento por HTTPS para interagir com o armazenamento do Azure. Você pode impor o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a transferência segura necessária para a conta de armazenamento.

[SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)(assinaturas de acesso compartilhado), que podem ser usadas para delegar acesso a objetos de armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser usado quando você usa assinaturas de acesso compartilhado. Essa abordagem garante que qualquer pessoa que envie links com tokens SAS use o protocolo adequado.

O [SMB 3,0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), que costumava acessar compartilhamentos de arquivos do Azure, dá suporte à criptografia e está disponível no windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10. Ele permite acesso entre regiões e até mesmo acesso na área de trabalho.

A criptografia do lado do cliente criptografa os dados antes de enviá-los para a instância do armazenamento do Azure, para que ele seja criptografado à medida que ele viaja pela rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Criptografia SMB em redes virtuais do Azure 

Usando o [SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) em VMs que executam o Windows Server 2012 ou posterior, você pode tornar as transferências de dados seguras criptografando os dados em trânsito nas redes virtuais do Azure. Ao criptografar dados, você ajuda a proteger contra ataques de violação e espionagem. Os administradores podem habilitar a criptografia SMB para todo o servidor ou apenas compartilhamentos específicos.

Por padrão, após a criptografia SMB ser ativada para um compartilhamento ou servidor, somente clientes SMB 3,0 têm permissão para acessar os compartilhamentos criptografados.

## <a name="in-transit-encryption-in-vms"></a>Criptografia em trânsito em VMs

Os dados em trânsito para, de e entre as VMs que executam o Windows são criptografados de várias maneiras, dependendo da natureza da conexão.

### <a name="rdp-sessions"></a>Sessões RDP

Você pode se conectar e entrar em uma VM usando o [protocolo RDP (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) de um computador cliente do Windows ou de um Mac com um cliente RDP instalado. Os dados em trânsito pela rede em sessões RDP podem ser protegidos pelo TLS.

Você também pode usar Área de Trabalho Remota para se conectar a uma VM do Linux no Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Proteger o acesso às VMs do Linux com o SSH

Para o gerenciamento remoto, você pode usar [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) para se conectar a VMs Linux em execução no Azure. O SSH é um protocolo de conexão criptografado que permite entradas seguras em conexões não seguras. É o protocolo de conexão padrão para VMs Linux hospedadas no Azure. Usando chaves SSH para autenticação, você elimina a necessidade de senhas para entrar. O SSH usa um par de chaves pública/privada (criptografia assimétrica) para autenticação.

## <a name="azure-vpn-encryption"></a>Criptografia de VPN do Azure

Você pode se conectar ao Azure por meio de uma rede virtual privada que cria um túnel seguro para proteger a privacidade dos dados que estão sendo enviados pela rede.

### <a name="azure-vpn-gateways"></a>Gateways de VPN do Azure

Você pode usar um [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) para enviar o tráfego criptografado entre sua rede virtual e seu local em uma conexão pública ou para enviar tráfego entre redes virtuais.

As VPNs site a site usam [IPSec](https://en.wikipedia.org/wiki/IPsec) para criptografia de transporte. Os gateways de VPN do Azure usam um conjunto de propostas padrão. Você pode configurar os gateways de VPN do Azure para usar uma política de IPsec/IKE personalizada com algoritmos de criptografia e forças de chave específicos, em vez dos conjuntos de políticas padrão do Azure.

### <a name="point-to-site-vpns"></a>VPNs ponto a site

As VPNs ponto a site permitem que computadores cliente individuais acessem uma rede virtual do Azure. [O SSTP](https://technet.microsoft.com/library/2007.06.cableguy.aspx) é usado para criar o encapsulamento de VPN. Ele pode atravessar firewalls (o túnel aparece como uma conexão HTTPS). Você pode usar sua própria autoridade de certificação (CA) raiz interna de PKI (infraestrutura de chave pública) para conectividade ponto a site.

Você pode configurar uma conexão VPN ponto a site para uma rede virtual usando o portal do Azure com autenticação de certificado ou o PowerShell.

Para saber mais sobre conexões VPN ponto a site com redes virtuais do Azure, consulte:

[Configurar uma conexão ponto a site com uma rede virtual usando a autenticação de certificação: portal do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Configurar uma conexão ponto a site com uma rede virtual usando a autenticação de certificado: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>VPNs site a site 

Você pode usar uma conexão de gateway de VPN site a site para conectar sua rede local a uma rede virtual do Azure por meio de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão requer um dispositivo VPN local que tenha um endereço IP público voltado para o externo atribuído a ele.

Você pode configurar uma conexão VPN site a site para uma rede virtual usando o portal do Azure, o PowerShell ou o CLI do Azure.

Para obter mais informações, consulte:

[Criar uma conexão site a site no portal do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Criar uma conexão site a site no PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma conexão VPN site a site usando a CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Criptografia em trânsito no Data Lake

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Além de criptografar os dados antes de armazená-los em mídia persistente, os dados também são sempre protegidos em trânsito usando HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

Para saber mais sobre a criptografia de dados em trânsito no Data Lake, consulte [criptografia de dados em data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Gerenciamento de chaves com Key Vault

Sem a proteção adequada e o gerenciamento das chaves, a criptografia é inutilizável. Key Vault é a solução recomendada pela Microsoft para gerenciar e controlar o acesso às chaves de criptografia usadas pelos serviços de nuvem. As permissões para chaves de acesso podem ser atribuídas a serviços ou usuários por meio de contas de Azure Active Directory.

Key Vault alivia as organizações da necessidade de configurar, aplicar patches e manter os HSMs (módulos de segurança de hardware) e o software de gerenciamento de chaves. Ao usar Key Vault, você mantém o controle. A Microsoft nunca vê suas chaves e os aplicativos não têm acesso direto a elas. Você também pode importar ou gerar chaves em HSMs.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da segurança do Azure](get-started-overview.md)
- [Visão geral da segurança de rede do Azure](network-overview.md)
- [Visão geral da segurança do banco de dados Azure](database-security-overview.md)
- [Visão geral de segurança de máquinas virtuais do Azure](virtual-machines-overview.md)
- [Encriptação de dados inativa](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)
