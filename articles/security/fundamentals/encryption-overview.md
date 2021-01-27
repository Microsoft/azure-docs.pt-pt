---
title: Visão geral da encriptação Azure | Microsoft Docs
description: Saiba mais sobre opções de encriptação em Azure. Consulte as informações para encriptação em repouso, encriptação em voo e gestão de chaves com a Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879719"
---
# <a name="azure-encryption-overview"></a>Descrição geral da encriptação do Azure

Este artigo fornece uma visão geral de como a encriptação é usada no Microsoft Azure. Cobre as principais áreas de encriptação, incluindo encriptação em repouso, encriptação em voo, e gestão chave com Azure Key Vault. Cada secção inclui links para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

Os dados em repouso incluem informação que reside no armazenamento persistente em meios físicos, em qualquer formato digital. Os meios de comunicação podem incluir ficheiros em meios magnéticos ou óticos, dados arquivados e cópias de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para atender a diferentes necessidades, incluindo arquivo, disco, bolha e armazenamento de mesa. A Microsoft também fornece encriptação para proteger [a Base de Dados Azure SQL](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)e Azure Data Lake.

A encriptação de dados em repouso está disponível para serviços em todo o software como um serviço (SaaS), plataforma como um serviço (PaaS) e infraestrutura como um modelo de nuvem de serviço (IaaS). Este artigo resume e fornece recursos para ajudá-lo a usar as opções de encriptação Azure.

Para uma discussão mais detalhada sobre como os dados em repouso são encriptados em Azure, consulte [Azure Data Encryption-at-Rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de encriptação Azure

O Azure suporta vários modelos de encriptação, incluindo encriptação do lado do servidor que utiliza chaves geridas pelo serviço, chaves geridas pelo cliente no Key Vault ou chaves geridas pelo cliente em hardware controlado pelo cliente. Com a encriptação do lado do cliente, pode gerir e armazenar chaves no local ou noutra localização segura.

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

A encriptação do lado do cliente é realizada fora de Azure. Inclui:

- Dados encriptados por uma aplicação que está a ser em execução no datacenter do cliente ou por uma aplicação de serviço.
- Dados que já estão encriptados quando são recebidos pelo Azure.

Com a encriptação do lado do cliente, os fornecedores de serviços na nuvem não têm acesso às chaves de encriptação e não podem desencriptar estes dados. Mantém o controlo total das chaves.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os três modelos de encriptação do lado do servidor oferecem diferentes características de gestão chave, que pode escolher de acordo com os seus requisitos:

- **Teclas geridas pelo serviço**: Proporciona uma combinação de controlo e conveniência com sobrecargas baixas.

- **Teclas geridas pelo cliente : Dá-lhe** controlo sobre as teclas, incluindo o suporte Bring Your Own Keys (BYOK) ou permite-lhe gerar novas.

- **Chaves geridas pelo serviço no hardware controlado pelo cliente : Permite-lhe** gerir as chaves no seu repositório proprietário, fora do controlo da Microsoft. Esta característica chama-se Host Your Own Key (HYOK). No entanto, a configuração é complexa, e a maioria dos serviços Azure não suportam este modelo.

### <a name="azure-disk-encryption"></a>Encriptação do disco Azure

Pode proteger as máquinas virtuais Windows e Linux utilizando [a encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md), que utiliza a tecnologia [BitLocker windows](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) e o Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) para proteger tanto os discos do sistema operativo como os discos de dados com encriptação de volume completo.

Chaves e segredos de encriptação estão protegidos na sua [assinatura Azure Key Vault](../../key-vault/general/overview.md). Ao utilizar o serviço Azure Backup, pode fazer backup e restaurar máquinas virtuais encriptadas (VMs) que utilizam a configuração da Chave de Encriptação (KEK).

### <a name="azure-storage-service-encryption"></a>Encriptação do Serviço de Armazenamento Azure

Os dados em repouso no armazenamento de Azure Blob e nas ações de ficheiros Azure podem ser encriptados em cenários do lado do servidor e do lado do cliente.

[A Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) pode encriptar automaticamente os dados antes de serem armazenados e desencripta automaticamente os dados quando os recupera. O processo é completamente transparente para os utilizadores. A Encriptação do Serviço de Armazenamento utiliza encriptação avançada de 256 bits [(AES),](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)que é uma das cifras de blocos mais fortes disponíveis. A AES lida com encriptação, desencriptação e gestão de chaves de forma transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Encriptação do lado do cliente das bolhas Azure

Pode executar a encriptação do lado do cliente das bolhas Azure de várias maneiras.

Pode utilizar a Biblioteca do Cliente de Armazenamento Azure para o pacote .NET NuGet para encriptar dados dentro das aplicações do seu cliente antes de o enviar para o seu armazenamento Azure.

Para saber mais sobre e baixar a Biblioteca do Cliente de Armazenamento Azure para o pacote .NET NuGet, consulte o [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Quando utiliza encriptação do lado do cliente com o Key Vault, os seus dados são encriptados utilizando uma chave de encriptação de conteúdo simétrico única (CEK) que é gerada pelo cliente de armazenamento Azure SDK. O CEK é encriptado usando uma chave de encriptação (KEK), que pode ser uma chave simétrica ou um par de chave assimétrica. Você pode geri-lo localmente ou armazená-lo em Key Vault. Os dados encriptados são então enviados para o Azure Storage.

Para saber mais sobre a encriptação do lado do cliente com o Key Vault e começar com instruções de como fazer, consulte [Tutorial: Criptografe e desencripta as bolhas no Azure Storage utilizando o Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Finalmente, também pode utilizar a Biblioteca do Cliente de Armazenamento Azure para a Java para realizar encriptação do lado do cliente antes de enviar dados para o Azure Storage e desencriptar os dados quando os descarrega para o cliente. Esta biblioteca também suporta a integração com [o Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Encriptação de dados em repouso com base de dados Azure SQL

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) é um serviço de base de dados relacional de propósito geral em Azure que suporta estruturas como dados relacionais, JSON, espacial e XML. A BASE de Dados SQL suporta a encriptação do lado do servidor através da funcionalidade De encriptação de dados transparentes (TDE) e encriptação do lado do cliente através da funcionalidade Always Encrypted.

#### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

[O TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) é utilizado para encriptar ficheiros de dados [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL](../../azure-sql/database/sql-database-paas-overview.md)e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em tempo real, utilizando uma Chave de Encriptação de Bases de Dados (DEK), que é armazenada no registo de arranque da base de dados para disponibilidade durante a recuperação.

O TDE protege os ficheiros de dados e registos, utilizando algoritmos de encriptação padrão de encriptação de dados AES e Triple Data (3DES). A encriptação do ficheiro de base de dados é realizada ao nível da página. As páginas de uma base de dados encriptada são encriptadas antes de serem escritas para o disco e são desencriptadas quando são lidas na memória. O TDE está agora ativado por padrão nas bases de dados Azure SQL recém-criadas.

#### <a name="always-encrypted-feature"></a>Funcionalidade sempre encriptada

Com a funcionalidade [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) no Azure SQL pode encriptar dados dentro das aplicações do cliente antes de os armazenar na Base de Dados Azure SQL. Pode também permitir a delegação da administração de bases de dados no local a terceiros e manter a separação entre os que possuem e podem ver os dados e aqueles que os gerem, mas não devem ter acesso aos mesmos.

#### <a name="cell-level-or-column-level-encryption"></a>Encriptação ao nível celular ou ao nível da coluna

Com a Base de Dados Azure SQL, pode aplicar encriptação simétrica a uma coluna de dados utilizando o Transact-SQL. Esta abordagem [chama-se encriptação ao nível celular ou encriptação ao nível da coluna (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), porque pode usá-la para encriptar colunas específicas ou mesmo células específicas de dados com diferentes teclas de encriptação. Ao fazê-lo, dá-lhe mais capacidade de encriptação granular do que o TDE, que encripta dados em páginas.

O CLE tem funções incorporadas que pode utilizar para encriptar dados utilizando chaves simétricas ou assimétricas, a chave pública de um certificado ou uma palavra-passe utilizando 3DES.

### <a name="cosmos-db-database-encryption"></a>Encriptação da base de dados do Cosmos DB

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) é a base de dados multi-modelo da Microsoft distribuída globalmente. Os dados do utilizador que são armazenados em Cosmos DB em armazenamento não volátil (unidades de estado sólido) são encriptados por padrão. Não há controlos para ligá-lo ou desligar. A encriptação em repouso é implementada utilizando uma série de tecnologias de segurança, incluindo sistemas de armazenamento de chaves seguras, redes encriptadas e APIs criptográficos. As chaves de encriptação são geridas pela Microsoft e são giradas de acordo com as diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Encriptação de repouso no Lago de Dados

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) é um repositório de todo o tipo de dados recolhidos num único local antes de qualquer definição formal de requisitos ou esquema. A Data Lake Store suporta "on by default", encriptação transparente de dados em repouso, que é configurada durante a criação da sua conta. Por padrão, a Azure Data Lake Store gere as chaves para si, mas tem a opção de as gerir sozinho.

Três tipos de chaves são usadas na encriptação e desencriptação de dados: a Chave De Encriptação Principal (MEK), a Chave de Encriptação de Dados (DEK) e a Chave de Encriptação de Blocos (BEK). O MEK é usado para encriptar o DEK, que é armazenado em meios persistentes, e o BEK é derivado do DEK e do bloco de dados. Se estiver a gerir as suas próprias chaves, pode rodar o MEK.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O Azure oferece muitos mecanismos para manter os dados privados à medida que se move de um local para outro.

### <a name="data-link-layer-encryption-in-azure"></a>Encriptação de camada de ligação de dados em Azure

Sempre que o tráfego do cliente Azure se move entre datacenters-- fora dos limites físicos não controlados pela Microsoft (ou em nome da Microsoft)-- um método de encriptação da camada de ligação de dados utilizando as Normas de [Segurança MAC IEEE 802.1AE](https://1.ieee802.org/security/802-1ae/) (também conhecidas como MACsec) é aplicado de ponto a ponto em todo o hardware de rede subjacente. Os pacotes são encriptados e desencriptados nos dispositivos antes de serem enviados, evitando ataques físicos "homem-no-meio" ou ataques de espionagem/escutas telefónicas. Como esta tecnologia está integrada no próprio hardware de rede, fornece encriptação de taxa de linha no hardware da rede sem aumento de latência de ligação mensurável. Esta encriptação do MACsec está em padrão para todo o tráfego Azure que viaja dentro de uma região ou entre regiões, e não é necessária nenhuma ação por parte dos clientes para permitir. 

### <a name="tls-encryption-in-azure"></a>Encriptação TLS em Azure

A Microsoft dá aos clientes a capacidade de usar o protocolo [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) para proteger os dados quando este viaja entre os serviços na nuvem e os clientes. Os datacenters da Microsoft negoceiam uma ligação TLS com sistemas de clientes que se ligam aos serviços Azure. O TLS proporciona uma autenticação forte, privacidade e integridade da mensagem (permitindo a deteção de adulteração de mensagens, interceção e falsificação), interoperabilidade, flexibilidade de algoritmos e facilidade de implantação e utilização.

[O Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protege as ligações entre os sistemas de clientes dos clientes e os serviços de cloud da Microsoft através de chaves únicas. As ligações também usam comprimentos de encriptação baseados em RSA de 2.048 bits. Esta combinação dificulta a interceção e acesso de dados que estão em trânsito.

### <a name="azure-storage-transactions"></a>Transações de Armazenamento Azure

Quando interage com o Azure Storage através do portal Azure, todas as transações ocorrem através de HTTPS. Também pode utilizar a API de armazenamento REST sobre HTTPS para interagir com o Azure Storage. Pode impor a utilização de HTTPS quando ligar para as APIs REST para aceder a objetos em contas de armazenamento, permitindo a transferência segura necessária para a conta de armazenamento.

As Assinaturas de Acesso Partilhado[(SAS),](../../storage/common/storage-sas-overview.md)que podem ser usadas para delegar o acesso a objetos de armazenamento Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas de acesso partilhado. Esta abordagem garante que qualquer pessoa que envie ligações com fichas SAS utilize o protocolo adequado.

[O SMB 3.0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), que costumava aceder às ações do Azure Files, suporta encriptação e está disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10. Permite o acesso transversal e até mesmo o acesso ao ambiente de trabalho.

A encriptação do lado do cliente encripta os dados antes de serem enviados para a sua instância de Armazenamento Azure, de modo a que seja encriptada à medida que viaja através da rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Encriptação SMB sobre redes virtuais Azure 

Ao utilizar [o SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) em VMs que estão a executar o Windows Server 2012 ou posteriormente, pode tornar as transferências de dados seguras, encriptando dados em trânsito através das Redes Virtuais Azure. Ao encriptar dados, ajuda a proteger contra ataques de adulteração e escuta. Os administradores podem ativar a encriptação SMB para todo o servidor, ou apenas ações específicas.

Por padrão, após a encriptação SMB ser ligada para uma partilha ou servidor, apenas os clientes SMB 3.0 estão autorizados a aceder às ações encriptadas.

## <a name="in-transit-encryption-in-vms"></a>Encriptação em trânsito em VMs

Os dados em trânsito para, a partir e entre VMs que estão a executar o Windows, podem ser encriptados de várias maneiras, dependendo da natureza da ligação.

### <a name="rdp-sessions"></a>Sessões RDP

Pode ligar e iniciar sôm num VM utilizando o [Protocolo de Ambiente de Trabalho Remoto (RDP)](/windows/win32/termserv/remote-desktop-protocol) a partir de um computador cliente Windows ou de um Mac com um cliente RDP instalado. Os dados em trânsito sobre a rede em sessões RDP podem ser protegidos por TLS.

Também pode utilizar o Remote Desktop para ligar a um Linux VM em Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Acesso seguro a VMs Linux com SSH

Para uma gestão remota, pode utilizar [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) para ligar aos VMs Linux em execução em Azure. SSH é um protocolo de ligação encriptado que permite iniciar s-ins seguros sobre ligações não seguras. É o protocolo de ligação padrão para os VMs Linux hospedados em Azure. Ao utilizar as teclas SSH para autenticação, elimina a necessidade de senhas para iniciar sação. O SSH utiliza um par de chaves público/privado (encriptação assimétrica) para a autenticação.

## <a name="azure-vpn-encryption"></a>Encriptação Azure VPN

Pode ligar-se ao Azure através de uma rede privada virtual que cria um túnel seguro para proteger a privacidade dos dados enviados através da rede.

### <a name="azure-vpn-gateways"></a>Gateways de VPN do Azure

Pode utilizar uma [porta de entrada Azure VPN](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) para enviar tráfego encriptado entre a sua rede virtual e a sua localização no local através de uma ligação pública, ou para enviar tráfego entre redes virtuais.

As VPNs locais usam [o IPsec](https://en.wikipedia.org/wiki/IPsec) para encriptação de transporte. Os gateways Azure VPN usam um conjunto de propostas predefinidas. Pode configurar gateways Azure VPN para usar uma política IPsec/IKE personalizada com algoritmos criptográficos específicos e pontos fortes chave, em vez dos conjuntos de políticas padrão Azure.

### <a name="point-to-site-vpns"></a>VPNs Ponto a site

As VPNs ponto-a-local permitem o acesso de computadores clientes individuais a uma rede virtual Azure. [O Protocolo de Túnel da Tomada Segura (SSTP)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) é utilizado para criar o túnel VPN. Pode atravessar firewalls (o túnel aparece como uma ligação HTTPS). Pode utilizar a sua própria infraestrutura de chaves públicas internas (PKI) para a conectividade ponto-a-local.

Pode configurar uma ligação VPN ponto a local a uma rede virtual utilizando o portal Azure com autenticação de certificado ou PowerShell.

Para saber mais sobre as ligações VPN ponto a local às redes virtuais Azure, consulte:

[Configure uma ligação ponto-a-local a uma rede virtual utilizando a autenticação de certificação: Portal Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Configure uma ligação ponto-a-local a uma rede virtual utilizando a autenticação de certificado: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-site VPNs 

Pode utilizar uma ligação de gateway VPN local para ligar a sua rede de entrada no local a uma rede virtual Azure num túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN no local que tenha um endereço IP público virado para o exterior atribuído.

Pode configurar uma ligação VPN site-to-site a uma rede virtual utilizando o portal Azure, PowerShell ou Azure CLI.

Para obter mais informações, consulte:

[Criar uma ligação site-a-local no portal Azure](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Criar uma ligação site-a-local em PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma ligação VPN site-to-site utilizando o CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Encriptação em trânsito em Data Lake

Os dados em trânsito (também denominados dados em movimento) também são sempre encriptados no Data Lake Store. Além de encriptar dados antes de os armazenar em meios persistentes, os dados também são sempre protegidos em trânsito através do HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

Para saber mais sobre encriptação de dados em trânsito em Data Lake, consulte [encriptação de dados na Data Lake Store.](../../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-key-vault"></a>Gestão de chaves com Cofre-Chave

Sem proteção e gestão adequadas das chaves, a encriptação torna-se inútil. Key Vault é a solução recomendada pela Microsoft para gerir e controlar o acesso a chaves de encriptação usadas pelos serviços na nuvem. As permissões de acesso às chaves podem ser atribuídas a serviços ou a utilizadores através de contas do Azure Ative Directory.

Com o Key Vault as organizações poupam tempo na configuração, aplicação de patches e manutenção dos módulos de segurança de hardware (HSMs) e software de gestão de chaves. Quando se usa o Cofre de Chaves, mantém-se o controlo. A Microsoft nunca vê as suas chaves e as aplicações não têm acesso direto às mesmos. Também pode importar ou gerar chaves em HSMs.

## <a name="next-steps"></a>Próximos passos

- [Descrição geral da segurança do Azure](./overview.md)
- [Descrição geral da segurança de rede do Azure](network-overview.md)
- [Visão geral da segurança da base de dados Azure](../../azure-sql/database/security-overview.md)
- [Visão geral de segurança das máquinas virtuais Azure](virtual-machines-overview.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)