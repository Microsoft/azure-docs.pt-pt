---
title: Descrição geral da encriptação do Azure | Documentos da Microsoft
description: Saiba mais sobre as várias opções de encriptação no Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 272cc843ab90eade06525f665d3cf2decf74a26f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444521"
---
# <a name="azure-encryption-overview"></a>Descrição geral da encriptação do Azure

Este artigo fornece uma descrição geral de como a encriptação é utilizada no Microsoft Azure. Ela abrange as principais áreas de encriptação, incluindo encriptação em repouso, encriptação em trânsito e gestão de chaves com o Azure Key Vault. Cada secção inclui ligações para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados inativos

Dados Inativos incluem informações que residem no armazenamento persistente em mídia física, em qualquer formato digital. O suporte de dados pode incluir ficheiros em meio magnético ou óptico, os dados arquivados e cópias de segurança de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para satisfazer diferentes necessidades, incluindo arquivo, o disco, o blob e o armazenamento de tabelas. A Microsoft também fornece encriptação para proteger [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [do Azure Cosmos DB](../cosmos-db/introduction.md)e o Azure Data Lake.

Encriptação de dados em repouso está disponível para serviços em software como serviço (SaaS), plataforma como serviço (PaaS) e infraestrutura como um modelos de nuvem de serviço (IaaS). Este artigo resume e fornece recursos para ajudar a utilizar as opções de encriptação do Azure.

Para obter uma discussão mais detalhada de como os dados Inativos são encriptados no Azure, consulte [dados do Azure encriptação em repouso](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de encriptação do Azure

O Azure suporta vários modelos de encriptação, incluindo a encriptação do lado do servidor que utilize chaves geridas pelo serviço, chaves geridas pelo cliente no Key Vault ou chaves geridas pelo cliente em hardware controlado pelo cliente. Com a encriptação do lado do cliente, pode gerir e armazenar as chaves no local ou em outro Proteja a localização.

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

Encriptação do lado do cliente é executada fora do Azure. Ele inclui:

- Dados criptografados por uma aplicação que está a executar no Centro de dados do cliente ou por um aplicativo de serviço.
- Dados que já são encriptados quando for recebida pelo Azure.

Com a encriptação do lado do cliente, o fornecedor de serviços cloud não tem acesso às chaves de encriptação e não é possível desencriptar estes dados. Mantém o controle completo das chaves.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os três modelos de encriptação do lado do servidor de características de gestão de chaves diferentes, o que pode escolher de acordo com os requisitos da oferta:

- **Chaves geridas pelo serviço**: Fornece uma combinação de controle e a conveniência baixa sobrecarga.

- **Chaves geridas pelo cliente**: Dá-lhe controlo sobre as chaves, incluindo o suporte de Bring Your Own chaves (BYOK), ou permite-lhe gerar novos.

- **Chaves geridas pelo serviço no hardware controlado pelo cliente**: Permite-lhe gerir as chaves no seu repositório proprietária, fora do controlo de Microsoft. Essa característica é chamada de Host Your Own Key (HYOK). No entanto, a configuração é complexa e serviços mais do Azure não suportam este modelo.

### <a name="azure-disk-encryption"></a>Encriptação de disco do Azure

Pode proteger máquinas virtuais Windows e Linux utilizando [encriptação de disco do Azure](azure-security-disk-encryption.md), que utiliza [BitLocker do Windows](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) tecnologia e Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) para proteger tanto discos do sistema operativo e discos de dados com criptografia de volume completo.

Encriptação de chaves e segredos são salvaguardados no seu [subscrição do Azure Key Vault](../key-vault/key-vault-whatis.md). Com o serviço de cópia de segurança do Azure, pode criar cópias de segurança e restaurar máquinas de virtuais (VMs) encriptadas que utilizam a configuração de chave de encriptação de chaves (KEK).

### <a name="azure-storage-service-encryption"></a>Encriptação do Serviço de Armazenamento do Azure

Dados Inativos no armazenamento de Blobs do Azure e partilhas de ficheiros do Azure podem ser encriptados em cenários de servidor e do lado do cliente.

[Encriptação de serviço de armazenamento do Azure (SSE)](../storage/common/storage-service-encryption.md) automaticamente pode encriptar dados antes de serem armazenados e automaticamente descriptografa os dados quando recuperá-la. O processo é completamente transparente para os utilizadores. Encriptação do serviço de armazenamento utiliza 256 bits [encriptação Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), que é uma das codificações de bloco mais fortes disponíveis. AES processa transparente de encriptação, desencriptação e gestão de chaves.

### <a name="client-side-encryption-of-azure-blobs"></a>Encriptação do lado do cliente de blobs do Azure

Pode executar o lado do cliente de blobs de encriptação do Azure de várias formas.

Pode utilizar a biblioteca de cliente de armazenamento do Azure para o pacote .NET NuGet para criptografar dados em seus aplicativos de cliente antes de carregá-lo para o armazenamento do Azure.

Para saber mais sobre e transferir a biblioteca de cliente de armazenamento do Azure para o pacote .NET NuGet, consulte [armazenamento do Azure Windows 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Quando utiliza a encriptação do lado do cliente com o Key Vault, os dados são encriptados com uma única simétrica conteúdo encriptação de chave (CEK) que é gerado pelo SDK de cliente de armazenamento do Azure. O CEK é encriptado com uma chave de encriptação de chaves (KEK), que pode ser uma chave simétrica ou um par de chaves assimétricas. Pode gerenciá-lo localmente ou armazená-lo no Key Vault. Os dados encriptados, em seguida, são carregados para o armazenamento do Azure.

Para saber mais sobre a encriptação do lado do cliente com o Key Vault e começar a utilizar com instruções sobre como proceder, veja [Tutorial: Encriptar e desencriptar blobs no armazenamento do Azure com o Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Por fim, também pode utilizar a biblioteca de cliente de armazenamento do Azure para Java para efetuar a encriptação do lado do cliente antes de carregar dados para o armazenamento do Azure e para descriptografar os dados ao transferi-lo ao cliente. Esta biblioteca também suporta a integração com [Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Encriptação de dados em repouso com a base de dados do Azure SQL

[Base de dados SQL do Azure](../sql-database/sql-database-technical-overview.md) é um serviço de base de dados relacional para fins gerais do Azure que suporta estruturas como dados relacionais, JSON, espaciais e XML. Base de dados SQL oferece suporte tanto a encriptação do lado do servidor através da funcionalidade de encriptação de dados transparente (TDE), como a encriptação do lado do cliente por meio do recurso são sempre encriptados.

#### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) é utilizado para encriptar [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [base de dados do Azure SQL](../sql-database/sql-database-technical-overview.md), e [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ficheiros de dados em tempo real com uma chave de encriptação da base de dados (DEK) , que é armazenado no registo de arranque de base de dados de disponibilidade durante a recuperação.

TDE protege os ficheiros de registo e dados, AES e dados padrão de criptografia triplo (3DES) a utilizar os algoritmos de criptografia. Criptografia do arquivo de banco de dados é executada no nível da página. As páginas num banco de dados encriptado são encriptadas antes que eles são escritos em disco e são desencriptadas quando eles são lidos na memória. TDE está agora ativada por predefinição nas bases de dados SQL do Azure recentemente criados.

#### <a name="always-encrypted-feature"></a>Sempre encriptado funcionalidade

Com o [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funcionalidade no Azure SQL pode criptografar dados em aplicativos de cliente antes de armazená-la na base de dados do Azure SQL. Também pode ativar a delegação da administração de banco de dados no local para terceiros e manter a separação entre aqueles que o proprietário e pode ver os dados e aqueles que geri-lo, mas não devem ter acesso a ele.

#### <a name="cell-level-or-column-level-encryption"></a>Encriptação ao nível da célula ou ao nível da coluna

Base de dados SQL do Azure, pode aplicar a criptografia simétrica para uma coluna de dados com o Transact-SQL. Essa abordagem é chamada [ao nível da célula criptografia ou encriptação de nível de coluna (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), porque pode usá-lo para encriptar colunas específicas ou até mesmo específicas células de dados com chaves de encriptação diferente. Se o fizer, dá-lhe capacidade de encriptação mais granular do que a TDE, que criptografa dados nas páginas.

CLE tem funções internas que pode utilizar para encriptar dados utilizando chaves simétricas ou assimétricas, a chave pública de um certificado ou uma frase de acesso com o algoritmo 3DES.

### <a name="cosmos-db-database-encryption"></a>Encriptação de base de dados do cosmos DB

[O Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) é com múltiplos modelo distribuída globalmente, base de dados da Microsoft do. Dados de utilizador que são armazenados no Cosmos DB no armazenamento de não-volátil (unidades de estado sólido) são encriptados por predefinição. Não há nenhum controle para ativá-la ativada ou desativada. Encriptação inativa é implementada com um número de tecnologias de segurança, incluindo sistemas de armazenamento de chaves segura, redes encriptadas e APIs criptográficas. Chaves de encriptação são geridas pela Microsoft e são revezadas por diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Encriptação em repouso no Data Lake

[O Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) é um repositório de toda a empresa de todos os tipos de dados reunidos num único local antes de qualquer definição formal de requisitos ou esquemas. Data Lake Store suporta "por predefinição," encriptação transparente de dados inativos, que é configurada durante a criação da sua conta. Por predefinição, o Azure Data Lake Store gerencia as chaves para, mas tem a opção para geri-los por conta própria.

Três tipos de chaves são utilizados no criptografando e descriptografando dados: a chave de encriptação mestra (MEK), a chave de encriptação de dados (DEK) e a chave de encriptação de bloco (BEK). A MEK é utilizada para encriptar o DEK, que é armazenado no suporte de dados persistente, e a BEK é obtida a partir da DEK e do bloco de dados. Se estiver a gerir as suas próprias chaves, pode rodar a MEK.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O Azure oferece vários mecanismos para manter os dados privados pois ela se move de uma localização para outra.

### <a name="tlsssl-encryption-in-azure"></a>Encriptação de TLS/SSL no Azure

A Microsoft utiliza a [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) protocol (TLS) para proteger os dados quando ele está em transição entre os serviços cloud e os clientes. Os datacenters da Microsoft negociar uma ligação de TLS com sistemas de cliente que se ligam aos serviços do Azure. TLS fornece autenticação forte, privacidade de mensagens e integridade (ativar a deteção de adulteração de mensagem, intercepção e falsificação), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.

[Perfeitas Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protege ligações entre os sistemas cliente e serviços cloud da Microsoft dos clientes por chaves exclusivas. Ligações também utilizam os comprimentos de chave de encriptação de 2.048 bits com base em RSA. Esta combinação torna difícil para alguém intercepção e aceda aos dados que se encontram em trânsito.

### <a name="azure-storage-transactions"></a>Transações de armazenamento do Azure

Quando interage com o armazenamento do Azure através do portal do Azure, todas as transações ocorrem através de HTTPS. Também pode utilizar a API de REST de armazenamento através de HTTPS para interagir com o armazenamento do Azure. Pode impor a utilização de HTTPS ao chamar as APIs REST para objetos de acesso nas contas de armazenamento, permitindo a transferência segura necessária para a conta de armazenamento.

Assinaturas de acesso partilhado ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), que pode ser utilizado para delegar acesso a objetos de armazenamento do Azure, incluir uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utiliza assinaturas de acesso partilhado. Esta abordagem garante que qualquer pessoa que envia a ligações com SAS tokens usa o protocolo apropriado.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), que são utilizadas para aceder a ficheiros do Azure partilhas, suporta a encriptação e ele estão disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10. Ele permite o acesso entre regiões e até mesmo acessar no ambiente de trabalho.

Encriptação do lado do cliente encripta os dados antes de ser enviada para a sua instância do armazenamento do Azure, para que este é encriptado enquanto trafegam por rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Encriptação SMB nas redes virtuais do Azure 

Usando [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) nas VMs que executam o Windows Server 2012 ou posterior, pode fazer com dados de transferências seguro ao encriptar os dados em trânsito através de redes virtuais do Azure. Ao encriptar os dados, ajudar a proteger contra adulteração e interceptação de ataques. Os administradores podem ativar a encriptação SMB para o servidor completo ou partilhas específicas.

Por predefinição, depois de encriptação SMB está ativada para uma partilha ou o servidor, são permitidos apenas os clientes do SMB 3.0 para aceder às partilhas encriptadas.

## <a name="in-transit-encryption-in-vms"></a>Encriptação em trânsito em VMs

Dados em trânsito para, de e entre as VMs que estejam a executar o Windows são encriptados de diversas formas, dependendo da natureza da ligação.

### <a name="rdp-sessions"></a>Sessões RDP

Pode ligar e inicie sessão a uma VM com o [protocolo RDP (Remote Desktop)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) de um computador de cliente do Windows, ou a partir de um Mac com um cliente RDP instalado. Os dados em trânsito através da rede em sessões RDP podem ser protegidos por TLS.

Também pode utilizar o ambiente de trabalho remoto para ligar a uma VM do Linux no Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Proteger o acesso a VMs do Linux com SSH

Para a gestão remota, pode usar [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) para ligar a VMs do Linux em execução no Azure. O SSH é um protocolo de conexão criptografada que permite inícios de sessão seguros através de ligações não seguras. É o protocolo de ligação predefinido para VMs do Linux alojados no Azure. Ao utilizar chaves SSH para autenticação, elimina a necessidade de palavras-passe para iniciar sessão. SSH utiliza um par de chaves públicas/privadas (criptografia assimétrica) para autenticação.

## <a name="azure-vpn-encryption"></a>Encriptação de VPN do Azure

Pode ligar ao Azure através de uma rede privada virtual que cria um túnel seguro para proteger a privacidade dos dados que está a ser enviados através da rede.

### <a name="azure-vpn-gateways"></a>Gateways VPN do Azure

Pode utilizar um [gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) para enviar tráfego encriptado entre a rede virtual e sua localização no local através de uma ligação pública, ou para enviar tráfego entre redes virtuais.

Utilização de VPNs de site a site [IPsec](https://en.wikipedia.org/wiki/IPsec) para a encriptação de transporte. Os gateways VPN do Azure utilizam um conjunto de propostas de predefinição. Pode configurar gateways de VPN do Azure para utilizar uma política de IPsec/IKE personalizada com restrições de chave e algoritmos de criptográficos específicos, em vez de define a política predefinida do Azure.

### <a name="point-to-site-vpns"></a>VPNs ponto a site

As VPNs ponto a site permitam acesso de computadores a uma rede virtual do Azure de cliente individual. [Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) é utilizado para criar o túnel VPN. Ele pode atravessar firewalls (o túnel aparece como uma ligação HTTPS). Pode usar sua própria autoridade de certificação de raiz de infraestrutura de chaves públicas (PKI) interna (AC) para conetividade ponto a site.

Pode configurar uma ligação de VPN ponto a site a uma rede virtual com o portal do Azure com a autenticação de certificados ou o PowerShell.

Para saber mais sobre ligações de VPN de ponto a site para redes virtuais do Azure, veja:

[Configure uma ligação de ponto a site a uma rede virtual, utilizando a autenticação de certificação: Portal do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Configure uma ligação de ponto a site a uma rede virtual, utilizando a autenticação de certificado: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>VPNs site a site 

Pode utilizar uma ligação de gateway VPN de site a site para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN no local que tenha um endereço IP público face externa atribuído a ele.

Pode configurar uma ligação de VPN de site a site a uma rede virtual, utilizando o portal do Azure, o PowerShell ou a CLI do Azure.

Para obter mais informações, consulte:

[Criar uma ligação site a site no portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Criar uma ligação site a site no PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma ligação de VPN de site a site com a CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Encriptação em trânsito no Data Lake

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Para além de encriptar dados antes de armazená-los no suporte de dados persistentes, os dados também são sempre protegidos em trânsito através de HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

Para saber mais sobre a encriptação de dados em trânsito no Data Lake, veja [encriptação de dados no Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Gestão de chaves com o Key Vault

Sem proteção adequada e a gestão das chaves, encriptação é processada inútil. O Key Vault é a solução recomendadas pela Microsoft para gerenciar e controlar o acesso às chaves de encriptação utilizado pelos serviços cloud. Permissões para chaves de acesso podem ser atribuídas aos serviços ou aos usuários por meio de contas do Azure Active Directory.

Key Vault libera as organizações a necessidade de configurar, corrigir e manter módulos de segurança de hardware (HSMs) e o software de gestão de chaves. Quando utiliza o Cofre de chaves, mantém o controlo. Microsoft nunca vê as suas chaves e aplicativos não têm acesso direto a elas. Também pode importar ou gerar chaves nos HSM.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da segurança do Azure](security-get-started-overview.md)
- [Descrição geral da segurança de rede do Azure](security-network-overview.md)
- [Descrição geral da segurança da base de dados do Azure](azure-database-security-overview.md)
- [Descrição geral da segurança de máquinas virtuais do Azure](security-virtual-machines-overview.md)
- [Encriptação de dados inativa](azure-security-encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](azure-security-data-encryption-best-practices.md)
