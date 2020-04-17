---
title: Visão geral da encriptação azure [ Microsoft Docs
description: Conheça várias opções de encriptação em Azure
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
ms.author: mbaldwin
ms.openlocfilehash: ce78ade4df3c5bcea9e4e44750c430065cbfc5b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454650"
---
# <a name="azure-encryption-overview"></a>Visão geral da encriptação azure

Este artigo fornece uma visão geral de como a encriptação é usada no Microsoft Azure. Abrange as principais áreas de encriptação, incluindo encriptação em repouso, encriptação em voo e gestão de chaves com o Azure Key Vault. Cada secção inclui links para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

Os dados em repouso incluem informações que residem no armazenamento persistente em meios físicos, em qualquer formato digital. Os meios de comunicação podem incluir ficheiros sobre meios magnéticos ou óticos, dados arquivados e cópias de segurança de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para atender às diferentes necessidades, incluindo arquivo, disco, blob e armazenamento de mesa. A Microsoft também fornece encriptação para proteger a Base de [Dados Azure SQL,](../../sql-database/sql-database-technical-overview.md) [O Azure Cosmos DB](../../data-factory/introduction.md)e o Lago de Dados Azure.

A encriptação de dados em repouso está disponível para serviços em todo o software como um serviço (SaaS), plataforma como serviço (PaaS), e infraestrutura como um serviço (IaaS) modelos de nuvem. Este artigo resume e fornece recursos para ajudá-lo a usar as opções de encriptação Azure.

Para uma discussão mais detalhada de como os dados em repouso são encriptados em Azure, consulte [Azure Data Encryption-at-Rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de encriptação Azure

O Azure suporta vários modelos de encriptação, incluindo encriptação do lado do servidor que utiliza chaves geridas pelo serviço, chaves geridas pelo cliente no Key Vault ou chaves geridas pelo cliente em hardware controlado pelo cliente. Com encriptação do lado do cliente, você pode gerir e armazenar chaves no local ou em outro local seguro.

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

A encriptação do lado do cliente é realizada fora do Azure. Inclui:

- Dados encriptados por uma aplicação que está a ser gerido no centro de dados do cliente ou por uma aplicação de serviço.
- Dados que já estão encriptados quando são recebidos pelo Azure.

Com a encriptação do lado do cliente, os fornecedores de serviços na nuvem não têm acesso às chaves de encriptação e não conseguem desencriptar estes dados. Mantenha o controlo total das chaves.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os três modelos de encriptação do lado do servidor oferecem diferentes características de gestão chave, que pode escolher de acordo com os seus requisitos:

- **Chaves geridas pelo serviço**: Proporciona uma combinação de controlo e conveniência com sobrecargas baixas.

- **Chaves geridas pelo cliente**: Dá-lhe controlo sobre as teclas, incluindo o suporte Bring Your Own Keys (BYOK) ou permite-lhe gerar novas.

- **Chaves geridas pelo serviço em hardware controlado pelo cliente**: Permite-lhe gerir chaves no seu repositório proprietário, fora do controlo da Microsoft. Esta característica chama-se Host Your Own Key (HYOK). No entanto, a configuração é complexa, e a maioria dos serviços Azure não suporta mato.

### <a name="azure-disk-encryption"></a>Encriptação de disco azure

Pode proteger as máquinas virtuais Windows e Linux utilizando encriptação de [disco Azure](/azure/security/fundamentals/azure-disk-encryption-vms-vmss), que utiliza a tecnologia [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) e O [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) linux para proteger tanto os discos do sistema operativo como os discos de dados com encriptação de volume completo.

As chaves e segredos de encriptação são salvaguardados na subscrição do [Cofre de Chaves Azure.](../../key-vault/general/overview.md) Ao utilizar o serviço de backup Azure, pode fazer backup e restaurar máquinas virtuais encriptadas (VMs) que utilizam a configuração da Chave de Encriptação (KEK).

### <a name="azure-storage-service-encryption"></a>Encriptação do serviço de armazenamento Azure

Os dados em repouso no armazenamento do Azure Blob e nas partilhas de ficheiros Azure podem ser encriptados tanto em cenários do lado do servidor como do lado do cliente.

A encriptação do Serviço de [Armazenamento Azure (SSE)](../../storage/common/storage-service-encryption.md) pode encriptar automaticamente os dados antes de ser armazenado e desencripta automaticamente os dados quando os recupera. O processo é completamente transparente para os utilizadores. A encriptação do serviço de armazenamento utiliza [encriptação Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, que é uma das cifras de blocos mais fortes disponíveis. A AES lida com encriptação, desencriptação e gestão de chaves de forma transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Encriptação do lado do cliente de bolhas Azure

Pode executar encriptação do lado do cliente de blobs Azure de várias maneiras.

Pode utilizar a Biblioteca de Clientes de Armazenamento Azure para o pacote NuGet .NET para encriptar dados dentro das suas aplicações de cliente antes de os enviar para o seu armazenamento Azure.

Para saber mais sobre e baixar a Biblioteca de Clientes de Armazenamento Azure para o pacote .NET NuGet, consulte [o Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Quando utiliza encriptação do lado do cliente com o Key Vault, os seus dados são encriptados utilizando uma chave de encriptação de conteúdo simétrico (CEK) que é gerada pelo sdK do cliente de Armazenamento Azure. O CEK é encriptado usando uma chave de encriptação (KEK), que pode ser uma chave simétrica ou um par de chaves assimétricas. Você pode geri-lo localmente ou armazená-lo em Key Vault. Os dados encriptados são depois enviados para o Armazenamento Azure.

Para saber mais sobre encriptação do lado do cliente com key vault e começar com instruções de como fazer, consulte [Tutorial: Criptografia e desencripta](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)bolhas no Armazenamento Azure utilizando o Key Vault .

Por fim, também pode utilizar a Biblioteca de Clientes de Armazenamento Azure para Java para executar encriptação do lado do cliente antes de fazer o upload de dados para o Armazenamento Azure e desencriptar os dados quando os descarrega para o cliente. Esta biblioteca também apoia a integração com [o Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Encriptação de dados em repouso com base de dados Azure SQL

A Base de [Dados Azure SQL](../../sql-database/sql-database-technical-overview.md) é um serviço de base de dados relacional de uso geral em Azure que suporta estruturas como dados relacionais, JSON, espacial e XML. A Base de Dados SQL suporta a encriptação do lado do servidor através da funcionalidade de encriptação de dados transparente (TDE) e encriptação do lado do cliente através da funcionalidade Always Encrypted.

#### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

[O TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) é utilizado para encriptar ficheiros de dados do [SQL Server,](https://www.microsoft.com/sql-server/sql-server-2016) [Azure SQL](../../sql-database/sql-database-technical-overview.md)e [Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em tempo real, utilizando uma Chave de Encriptação de Base de Dados (DEK), que está armazenada no registo de boot da base de dados para disponibilidade durante a recuperação.

O TDE protege os dados e os ficheiros de registo, utilizando algoritmos de encriptação Padrão de Encriptação AES e Triple Data (3DES). A encriptação do ficheiro base de dados é realizada ao nível da página. As páginas de uma base de dados encriptada são encriptadas antes de serem escritas em disco e desencriptadas quando são lidas na memória. O TDE está agora ativado por padrão nas bases de dados Azure SQL recém-criadas.

#### <a name="always-encrypted-feature"></a>Funcionalidade sempre encriptada

Com a funcionalidade [Sempre Encriptada](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no Azure SQL pode encriptar dados dentro das aplicações do cliente antes de os armazenar na Base de Dados Azure SQL. Pode também permitir a delegação de administração de bases de dados no local a terceiros e manter a separação entre os que possuem e podem ver os dados e aqueles que os gerem, mas que não devem ter acesso aos mesmos.

#### <a name="cell-level-or-column-level-encryption"></a>Encriptação ao nível celular ou ao nível da coluna

Com a Base de Dados Azure SQL, pode aplicar encriptação simétrica a uma coluna de dados utilizando o Transact-SQL. Esta abordagem [chama-se encriptação ao nível celular ou encriptação ao nível da coluna (CLE),](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)porque pode usá-la para encriptar colunas específicas ou até mesmo células específicas de dados com diferentes chaves de encriptação. Fazê-lo dá-lhe mais capacidade de encriptação granular do que o TDE, que encripta dados em páginas.

A CLE tem funções incorporadas que pode utilizar para encriptar dados utilizando chaves simétricas ou assimétricas, a chave pública de um certificado ou uma palavra-passe utilizando 3DES.

### <a name="cosmos-db-database-encryption"></a>Encriptação da base de dados Cosmos DB

[O Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) é a base de dados multimodelo da Microsoft distribuída globalmente. Os dados dos utilizadores armazenados no Cosmos DB em armazenamento não volátil (unidades de estado sólido) são encriptados por padrão. Não há controlos para ligá-lo ou desligar. A encriptação em repouso é implementada utilizando uma série de tecnologias de segurança, incluindo sistemas de armazenamento de chaves seguras, redes encriptadas e APIs criptográficos. As chaves de encriptação são geridas pela Microsoft e são giradas de acordo com as diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Encriptação em repouso em Data Lake

O Lago de [Dados Azure](../../data-lake-store/data-lake-store-encryption.md) é um repositório em toda a empresa de todos os tipos de dados recolhidos num único local antes de qualquer definição formal de requisitos ou esquemas. Data Lake Store suporta "on by default", encriptação transparente de dados em repouso, que é configurada durante a criação da sua conta. Por padrão, a Azure Data Lake Store gere as chaves para si, mas tem a opção de as gerir por si mesma.

Três tipos de teclas são usadas em encriptação e desencriptação de dados: a Chave de Encriptação Master (MEK), a Chave de Encriptação de Dados (DEK) e a Chave de Encriptação do Bloco (BEK). O MEK é usado para encriptar o DEK, que é armazenado em meios persistentes, e o BEK é derivado do DEK e do bloco de dados. Se estiver a gerir as suas próprias chaves, pode rodar o MEK.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O Azure oferece muitos mecanismos para manter os dados privados à medida que se move de um local para outro.

### <a name="tlsssl-encryption-in-azure"></a>Encriptação TLS/SSL em Azure

A Microsoft utiliza o protocolo [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) para proteger os dados quando viaja entre os serviços de nuvem e os clientes. Os datacenters da Microsoft negoceiam uma ligação TLS com sistemas de clientes que se ligam aos serviços Do Azure. O TLS proporciona autenticação forte, privacidade de mensagens e integridade (permitindo a deteção de adulteração de mensagens, interceção e falsificação), interoperabilidade, flexibilidade de algoritmos e facilidade de implementação e utilização.

[O Perfect Forward Secrety](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protege as ligações entre os sistemas de clientes dos clientes e os serviços de nuvem da Microsoft através de chaves únicas. As ligações também usam comprimentos de chave de encriptação baseados em RSA de 2.048 bits. Esta combinação dificulta a interceção e acesso de dados que estão em trânsito.

### <a name="azure-storage-transactions"></a>Transações de Armazenamento Azure

Quando interage com o Azure Storage através do portal Azure, todas as transações ocorrem em HTTPS. Também pode utilizar a API DO REST de Armazenamento em HTTPS para interagir com o Armazenamento Azure. Pode impor o uso de HTTPS quando ligar para as APIs REST para aceder a objetos em contas de armazenamento, permitindo a transferência segura que é necessária para a conta de armazenamento.

As Assinaturas de Acesso Partilhado[(SAS),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)que podem ser utilizadas para delegar acesso a objetos de Armazenamento Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar Assinaturas de Acesso Partilhado. Esta abordagem garante que qualquer pessoa que envie ligações com tokens SAS utilize o protocolo adequado.

[O SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), que costumava aceder a partilhas do Azure Files, suporta encriptação e está disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10. Permite o acesso transversal e até o acesso no ambiente de trabalho.

A encriptação do lado do cliente encripta os dados antes de ser enviado para a sua instância de Armazenamento Azure, de modo a que seja encriptado à medida que viaja através da rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Encriptação SMB sobre redes virtuais Azure 

Ao utilizar [o SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) em VMs que estão a executar o Windows Server 2012 ou mais tarde, pode tornar as transferências de dados seguras encriptando dados em trânsito através das Redes Virtuais Azure. Ao encriptar dados, ajuda a proteger contra ataques de adulteração e escuta. Os administradores podem ativar a encriptação SMB para todo o servidor, ou apenas partilhas específicas.

Por predefinição, depois de a encriptação SMB ser ligada para uma ação ou servidor, apenas os clientes SMB 3.0 podem aceder às partilhas encriptadas.

## <a name="in-transit-encryption-in-vms"></a>Encriptação em trânsito em VMs

Os dados em trânsito para, a partir e entre VMs que estão a executar o Windows, são encriptados de várias formas, dependendo da natureza da ligação.

### <a name="rdp-sessions"></a>Sessões de RDP

Pode ligar e iniciar sessão num VM utilizando o Protocolo de Ambiente de [Trabalho Remoto (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) a partir de um computador cliente Windows ou de um Mac com um cliente RDP instalado. Os dados em trânsito sobre a rede em sessões de RDP podem ser protegidos por TLS.

Também pode utilizar o Remote Desktop para se ligar a um Linux VM em Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Acesso seguro a VMs Linux com SSH

Para gestão remota, pode utilizar [a Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) para ligar aos VMs Linux em Funcionamento em Azure. O SSH é um protocolo de ligação encriptado que permite inscrições seguras sobre ligações não seguras. É o protocolo de ligação padrão para Os VMs Linux hospedados em Azure. Ao utilizar as teclas SSH para autenticação, elimina a necessidade de as palavras-passe iniciarem o seu sessão. O SSH utiliza um par de chaves público/privado (encriptação assimétrica) para autenticação.

## <a name="azure-vpn-encryption"></a>Encriptação Azure VPN

Pode ligar-se ao Azure através de uma rede privada virtual que cria um túnel seguro para proteger a privacidade dos dados que estão a ser enviados através da rede.

### <a name="azure-vpn-gateways"></a>Gateways de VPN do Azure

Pode utilizar um [portal Azure VPN](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) para enviar tráfego encriptado entre a sua rede virtual e a sua localização no local através de uma ligação pública, ou para enviar tráfego entre redes virtuais.

As VPNs do site-a-site usam [IPsec](https://en.wikipedia.org/wiki/IPsec) para encriptação de transporte. Os gateways Azure VPN usam um conjunto de propostas padrão. Pode configurar gateways VpN Azure para utilizar uma política personalizada de IPsec/IKE com algoritmos criptográficos específicos e pontos fortes chave, em vez dos conjuntos de políticas padrão Azure.

### <a name="point-to-site-vpns"></a>VPNs Ponto a site

As VPNs ponto-a-site permitem o acesso individual de computadores clientes a uma rede virtual Azure. O Protocolo de Túnel de [Tomada Segura (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) é utilizado para criar o túnel VPN. Pode atravessar firewalls (o túnel aparece como uma ligação HTTPS). Você pode usar a sua própria infraestrutura de infraestrutura pública interna (PKI) autoridade de certificado de raiz (CA) para conectividade ponto-a-local.

Pode configurar uma ligação VPN ponto-a-local a uma rede virtual utilizando o portal Azure com autenticação de certificado ou PowerShell.

Para saber mais sobre as ligações VPN ponto-a-site às redes virtuais Azure, consulte:

[Configure uma ligação ponto-a-local a uma rede virtual utilizando a autenticação de certificação: portal Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Configure uma ligação ponto-a-local a uma rede virtual utilizando a autenticação de certificado: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-site VPNs 

Pode utilizar uma ligação de gateway VPN site-to-site para ligar a sua rede no local a uma rede virtual Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN no local que tenha um endereço IP público virado para o exterior atribuído ao mesmo.

Pode configurar uma ligação VPN site-to-site a uma rede virtual utilizando o portal Azure, PowerShell ou Azure CLI.

Para obter mais informações, consulte:

[Criar uma ligação site-a-local no portal Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Criar uma ligação site-a-site no PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma ligação VPN site-to-site utilizando o CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Encriptação em trânsito no Lago dos Dados

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Além de encriptar dados antes de os armazenar em meios persistentes, os dados também são sempre protegidos em trânsito através da utilização de HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

Para saber mais sobre encriptação de dados em trânsito em Data Lake, consulte [encriptação de dados na Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Gestão chave com cofre chave

Sem uma proteção e gestão adequadas das chaves, a encriptação torna-se inútil. Key Vault é a solução recomendada pela Microsoft para gerir e controlar o acesso às chaves de encriptação utilizadas pelos serviços na nuvem. As permissões de acesso às chaves podem ser atribuídas a serviços ou utilizadores através de contas de Diretório Ativo Azure.

Key Vault alivia as organizações da necessidade de configurar, patch e manter módulos de segurança de hardware (HSMs) e software de gestão de chaves. Quando usas o Cofre chave, mantéuses o controlo. A Microsoft nunca vê as suas chaves e as aplicações não têm acesso direto às mesmas. Também pode importar ou gerar chaves em HSMs.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da segurança do Azure](get-started-overview.md)
- [Visão geral da segurança da rede Azure](network-overview.md)
- [Visão geral de segurança da base de dados Azure](database-security-overview.md)
- [Visão geral de segurança das máquinas virtuais azure](virtual-machines-overview.md)
- [Encriptação de dados inativa](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)
