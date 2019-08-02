---
title: Considerações de segurança para movimentação de dados no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como proteger a movimentação de dados no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b425db761375c705d3c810002234a937bac46d78
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610160"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Considerações de Azure Data Factory sobre segurança para movimentação de dados

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [considerações de segurança de movimentação de dados para data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura básica de segurança que os serviços de movimentação de dados em Azure Data Factory usados para proteger seus dados. Azure Data Factory recursos de gerenciamento são criados na infraestrutura de segurança do Azure e usam todas as medidas de segurança possíveis oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](data-factory-create-pipelines.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Esses pipelines residem na região em que o data factory foi criado. 

Embora Data Factory esteja disponível apenas nas regiões **oeste dos EUA**, **leste dos EUA**e **Europa setentrional** , o serviço de movimentação de dados está disponível [globalmente em várias regiões](data-factory-data-movement-activities.md#global). Data Factory serviço garante que os dados não saiam de uma área geográfica/região, a menos que você instrua explicitamente o serviço a usar uma região alternativa se o serviço de movimentação de dados ainda não tiver sido implantado nessa região. 

Azure Data Factory em si não armazena nenhum dado, exceto as credenciais de serviço vinculadas para armazenamentos de dados de nuvem, que são criptografados usando certificados. Permite-lhe criar fluxos de trabalho condicionados por dados para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

A movimentação de dados usando Azure Data Factory foi certificada para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA ESTRELA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se você estiver interessado em conformidade com o Azure e como o Azure protege sua própria infraestrutura, visite a [central de confiabilidade da Microsoft](https://microsoft.com/en-us/trustcenter/default.aspx). 

Neste artigo, examinaremos as considerações de segurança nos dois cenários de movimentação de dados a seguir: 

- **Cenário de nuvem**– nesse cenário, a origem e o destino estão publicamente acessíveis pela Internet. Isso inclui serviços de armazenamento em nuvem gerenciados como armazenamento do Azure, Azure SQL Data Warehouse, banco de dados SQL do Azure, Azure Data Lake Store, Amazon S3, Amazon redshift, serviços SaaS como Salesforce e protocolos da Web, como FTP e OData. Você pode encontrar uma lista completa de fontes de dados com suporte [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Cenário híbrido**– nesse cenário, sua origem ou destino está atrás de um firewall ou dentro de uma rede corporativa local ou o armazenamento de dados está em uma rede privada/rede virtual (geralmente a origem) e não é acessível publicamente. Os servidores de banco de dados hospedados em máquinas virtuais também se enquadram nesse cenário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvem
### <a name="securing-data-store-credentials"></a>Protegendo as credenciais do repositório de dados
O Azure Data Factory protege suas credenciais de armazenamento de dados criptografando-as usando **certificados gerenciados pela Microsoft**. Esses certificados são girados a cada **dois anos** (o que inclui a renovação de certificado e a migração de credenciais). Essas credenciais criptografadas são armazenadas com segurança em um **armazenamento do Azure gerenciado pelo Azure data Factory Management Services**. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [visão geral de segurança do armazenamento do Azure](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Criptografia de dados em trânsito
Se o armazenamento de dados de nuvem oferecer suporte a HTTPS ou TLS, todas as transferências de dados entre os serviços de movimentação de dados no Data Factory e um armazenamento de dados de nuvem serão por meio do canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as conexões com o banco de dados **SQL do Azure** e o **Azure SQL data warehouse** sempre exigem Criptografia (SSL/TLS), enquanto os dados estão em trânsito para e do banco. Ao criar um pipeline usando um editor de JSON, adicione a propriedade de **criptografia** e defina-a como **true** na **cadeia de conexão**. Quando você usa o [Assistente de cópia](data-factory-azure-copy-wizard.md), o assistente define essa propriedade por padrão. Para o **armazenamento do Azure**, você pode usar **https** na cadeia de conexão.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns armazenamentos de dados dão suporte à criptografia de dados em repouso. Sugerimos que você habilite o mecanismo de criptografia de dados para esses armazenamentos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
O Transparent Data Encryption (TDE) no Azure SQL Data Warehouse ajuda na proteção contra a ameaça de atividades mal-intencionadas, executando criptografia e descriptografia em tempo real de seus dados em repouso. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger um banco de dados no SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
O banco de dados SQL do Azure também dá suporte à TDE (Transparent Data Encryption), que ajuda na proteção contra a ameaça de atividades mal-intencionadas executando criptografia e descriptografia em tempo real dos dados sem a necessidade de alterações no aplicativo. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Transparent Data Encryption com o banco de dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Azure Data Lake Store também fornece criptografia para os dados armazenados na conta. Quando habilitado, o armazenamento de Data Lake criptografa automaticamente os dados antes de persistir e descriptografar antes da recuperação, tornando-o transparente para o cliente que está acessando os dados. Para obter mais informações, consulte [segurança em Azure data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de BLOBs do Azure e armazenamento de tabelas do Azure
O armazenamento de BLOBs do Azure e o armazenamento de tabelas do Azure dão suporte a Criptografia do Serviço de Armazenamento (SSE), que criptografa automaticamente os dados antes de persistir para o armazenamento e descriptografar antes da recuperação. Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 dá suporte à criptografia de cliente e de servidor de dados em repouso. Para obter mais informações, consulte [protegendo dados usando criptografia](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, Data Factory não dá suporte ao Amazon S3 dentro de uma nuvem privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
O Amazon redshift dá suporte à criptografia de cluster para dados em repouso. Para obter mais informações, consulte o [Amazon redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, Data Factory não dá suporte ao Amazon redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
O Salesforce dá suporte à criptografia de plataforma de blindagem que permite a criptografia de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [noções básicas sobre o fluxo de autenticação do servidor Web OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários híbridos (usando Gerenciamento de Dados gateway)
Os cenários híbridos exigem que Gerenciamento de Dados gateway seja instalado em uma rede local ou dentro de uma rede virtual (Azure) ou em uma nuvem privada virtual (Amazon). O gateway deve ser capaz de acessar os armazenamentos de dados locais. Para obter mais informações sobre o gateway, consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md). 

![Canais de gateway Gerenciamento de Dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimentação de dados em Data Factory e gerenciamento de dados gateway. A comunicação contém informações relacionadas à atividade. O canal de dados é usado para transferir dados entre armazenamentos de dados locais e armazenamentos de dados de nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do repositório de dados local
As credenciais para seus armazenamentos de dados locais são armazenadas localmente (não na nuvem). Eles podem ser definidos de três maneiras diferentes. 

- Usando o **texto sem formatação** (menos seguro) via HTTPS do portal do Azure/assistente de cópia. As credenciais são passadas em texto sem formatação para o gateway local.
- Usando a **biblioteca de criptografia do JavaScript do assistente de cópia**.
- Usando o **aplicativo Gerenciador de credenciais com base em um clique**. O aplicativo de clique único é executado no computador local que tem acesso ao gateway e define as credenciais para o armazenamento de dados. Essa opção e a próxima são as opções mais seguras. Por padrão, o aplicativo Gerenciador de credenciais usa a porta 8050 no computador com o gateway para comunicação segura.  
- Use o cmdlet do PowerShell [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) para criptografar credenciais. O cmdlet usa o certificado que o gateway está configurado para usar para criptografar as credenciais. Você pode usar as credenciais criptografadas retornadas por este cmdlet e adicioná-las ao elemento **EncryptedCredential** da **CONNECTIONSTRING** no arquivo JSON que você usa com o cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no trecho JSON no Data Factory editor no Portal. Essa opção e o aplicativo de clique único são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Criptografia baseada em biblioteca de criptografia do JavaScript
Você pode criptografar credenciais de armazenamento de dados usando a [biblioteca de criptografia de JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) do assistente de [cópia](data-factory-copy-wizard.md). Quando você seleciona essa opção, o assistente de cópia recupera a chave pública do gateway e a usa para criptografar as credenciais do armazenamento de dados. As credenciais são descriptografadas pelo computador do gateway e protegidas pelo Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Navegadores com suporte:** IE8, IE9, IE10, IE11, Microsoft Edge e mais recentes navegadores Firefox, Chrome, Opera e Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplicativo Gerenciador de credenciais de clique único
Você pode iniciar o aplicativo Gerenciador de credenciais baseado em clique único do assistente de portal do Azure/cópia ao criar pipelines. Esse aplicativo garante que as credenciais não sejam transferidas em texto sem formatação pela conexão. Por padrão, ele usa a porta **8050** no computador com o gateway para comunicação segura. Se necessário, essa porta pode ser alterada.  
  
![Porta HTTPS para o gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, Gerenciamento de Dados gateway usa um único **certificado**. Esse certificado é criado durante a instalação do gateway (aplica-se a Gerenciamento de Dados gateway criado após novembro de 2016 e versão 2.4. xxxx. x ou posterior). Você pode substituir esse certificado pelo seu próprio certificado SSL/TLS. Esse certificado é usado pelo aplicativo Gerenciador de credenciais de clique único para se conectar com segurança ao computador do gateway para configurar as credenciais do repositório de dados. Ele armazena as credenciais do armazenamento de dados com segurança no local usando a [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows no computador com o gateway. 

> [!NOTE]
> Os gateways mais antigos que foram instalados antes de novembro de 2016 ou da versão 2.3. xxxx. x continuam a usar as credenciais criptografadas e armazenadas na nuvem. Mesmo que você atualize o gateway para a versão mais recente, as credenciais não são migradas para um computador local    
  
| Versão do gateway (durante a criação) | Credenciais armazenadas | Criptografia/segurança de credencial | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na nuvem | Criptografado usando o certificado (diferente daquele usado pelo aplicativo Gerenciador de credenciais) | 
| > = 2.4.xxxx.x | No local | Protegido via DPAPI | 
  

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Todas as transferências de dados são por meio de canal seguro **https** e **TLS sobre TCP** para evitar ataques man-in-the-Middle durante a comunicação com os serviços do Azure.
 
Você também pode usar [VPN IPSec](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [rota expressa](../../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre sua rede local e o Azure.

Rede virtual é uma representação lógica da sua rede na nuvem. Você pode conectar uma rede local à sua rede virtual do Azure (VNet) configurando a VPN IPSec (site a site) ou a rota expressa (emparelhamento privado)     

A tabela a seguir resume as recomendações de configuração de gateway e rede com base em diferentes combinações de locais de origem e de destino para movimentação de dados híbridos.

| Source | Destino | Configuração de rede | Configuração do gateway |
| ------ | ----------- | --------------------- | ------------- | 
| No local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPSec (ponto a site ou site a site) | O gateway pode ser instalado localmente ou em uma VM (máquina virtual) do Azure na VNet | 
| No local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (emparelhamento privado) | O gateway pode ser instalado localmente ou em uma VM do Azure na VNet | 
| No local | Serviços baseados no Azure que têm um ponto de extremidade público | ExpressRoute (emparelhamento público) | O gateway deve ser instalado localmente | 

As imagens a seguir mostram o uso de Gerenciamento de Dados gateway para mover dados entre um banco de dados local e os serviços do Azure usando a rota expressa e a VPN IPSec (com rede virtual):

**Rota expressa:**
 
![Usar a rota expressa com o gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![VPN IPSec com gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e lista de permissões do endereço IP do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para rede local/privada  
Em uma empresa, um **firewall corporativo** é executado no roteador central da organização. E, o **Firewall do Windows** é executado como um daemon no computador local em que o gateway está instalado. 

A tabela a seguir fornece os requisitos de porta e domínio de **saída** para o **firewall corporativo**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Exigido pelo gateway para se conectar aos serviços de movimentação de dados no Data Factory |
| `*.core.windows.net` | 443 | Usado pelo gateway para se conectar à conta de armazenamento do Azure quando você usa o recurso de [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) . | 
| `*.frontend.clouddatahub.net` | 443 | Exigido pelo gateway para se conectar ao serviço de Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Opcional) necessário quando o destino é o banco de dados SQL do Azure/Azure SQL Data Warehouse. Use o recurso de cópia em etapas para copiar dados para o Azure SQL Data Database/SQL Data Warehouses sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (Opcional) necessário quando o destino é Azure Data Lake repositório | 

> [!NOTE] 
> Talvez você precise gerenciar portas/domínios de lista de permissões no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o banco de dados SQL do Azure, o Azure SQL Data Warehouse, Azure Data Lake Store como exemplos.   

A tabela a seguir fornece os requisitos de **porta de entrada** para o **Firewall do Windows**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Exigido pelo aplicativo Gerenciador de credenciais para definir com segurança as credenciais para armazenamentos de dados locais no gateway. | 

![Requisitos de porta do gateway](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Configurações de IP/lista de permissões no repositório de dados
Alguns armazenamentos de dados na nuvem também exigem a lista de permissões do endereço IP do computador que os acessa. Verifique se o endereço IP do computador do gateway está na lista de permissões/configurado no firewall adequadamente.

Os seguintes armazenamentos de dados de nuvem exigem a lista de permissões do endereço IP do computador do gateway. Por padrão, alguns desses armazenamentos de dados podem não exigir a lista de permissões do endereço IP. 

- [Base de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta** O gateway pode ser compartilhado entre diferentes fábricas de dados?
**Atenda** Ainda não há suporte para esse recurso. Estamos a trabalhar ativamente para esse fim.

**Pergunta** Quais são os requisitos de porta para que o gateway funcione?
**Atenda** O gateway faz conexões baseadas em HTTP para abrir a Internet. As **portas de saída 443 e 80** devem ser abertas para o gateway para fazer essa conexão. Abra a **porta de entrada 8050** somente no nível do computador (não no nível do firewall corporativo) para o aplicativo Gerenciador de credenciais. Se o banco de dados SQL do Azure ou o SQL Data Warehouse do Azure for usado como origem/destino, você precisará abrir a porta **1433** também. Para obter mais informações, consulte [as configurações de firewall e a seção endereços IP de lista de](#firewall-configurations-and-whitelisting-ip-address-of gateway) permissões. 

**Pergunta** Quais são os requisitos de certificado para o gateway?
**Atenda** O gateway atual requer um certificado que é usado pelo aplicativo Gerenciador de credenciais para configurar com segurança as credenciais do repositório de dados. Esse certificado é um certificado autoassinado criado e configurado pela instalação do gateway. Em vez disso, você pode usar seu próprio certificado TLS/SSL. Para obter mais informações, consulte [a seção aplicativo Gerenciador de credenciais de clique único](#click-once-credentials-manager-app) . 

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o desempenho da atividade de cópia, consulte [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

 
