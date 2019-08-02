---
title: Considerações de segurança no Azure Data Factory | Microsoft Docs
description: Descreve a infraestrutura básica de segurança que os serviços de movimentação de dados em Azure Data Factory usados para ajudar a proteger seus dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: c42e70efc8543e1d255690070ffb51b865e1754f
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608592"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerações de segurança para movimentação de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versão atual](data-movement-security-considerations.md)

Este artigo descreve a infraestrutura básica de segurança que os serviços de movimentação de dados em Azure Data Factory usados para ajudar a proteger seus dados. Data Factory recursos de gerenciamento são criados na infraestrutura de segurança do Azure e usam todas as medidas de segurança possíveis oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](concepts-pipelines-activities.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Esses pipelines residem na região em que o data factory foi criado. 

Embora Data Factory só esteja disponível em algumas regiões, o serviço de movimentação de dados está [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location) para garantir a conformidade dos dados, a eficiência e os custos de saída de rede reduzidos. 

Azure Data Factory não armazena nenhum dado, exceto as credenciais de serviço vinculadas para armazenamentos de dados de nuvem, que são criptografados usando certificados. Com o Data Factory, você cria fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre [os armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats)e o processamento de dados usando [serviços de computação](compute-linked-services.md) em outras regiões ou em um ambiente local. Você também pode monitorar e gerenciar fluxos de trabalho usando SDKs e Azure Monitor.

O Data Factory foi certificado para:

| **[Certificação CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Se você estiver interessado em conformidade com o Azure e como o Azure protege sua própria infraestrutura, visite a [central de confiabilidade da Microsoft](https://microsoft.com/en-us/trustcenter/default.aspx). Para obter a lista mais recente de todas as ofertas de conformidade https://aka.ms/AzureCompliance do Azure, verifique-.

Neste artigo, examinaremos as considerações de segurança nos dois cenários de movimentação de dados a seguir: 

- **Cenário de nuvem**: Nesse cenário, sua origem e seu destino estão publicamente acessíveis pela Internet. Isso inclui serviços de armazenamento em nuvem gerenciados, como o armazenamento do Azure, SQL Data Warehouse do Azure, banco de dados SQL do Azure, Azure Data Lake Store, Amazon S3, Amazon redshift, serviços SaaS como Salesforce e protocolos da Web, como FTP e OData. Encontre uma lista completa de fontes de dados com suporte em [formatos e armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
- **Cenário híbrido**: Nesse cenário, sua origem ou seu destino está atrás de um firewall ou dentro de uma rede corporativa local. Ou, o armazenamento de dados está em uma rede privada ou rede virtual (geralmente a origem) e não é acessível publicamente. Os servidores de banco de dados hospedados em máquinas virtuais também se enquadram nesse cenário.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvem

### <a name="securing-data-store-credentials"></a>Protegendo as credenciais do repositório de dados

- **Armazene credenciais criptografadas em um repositório gerenciado Azure data Factory**. Data Factory ajuda a proteger suas credenciais de armazenamento de dados criptografando-as com certificados gerenciados pela Microsoft. Esses certificados são girados a cada dois anos (o que inclui a renovação de certificados e a migração de credenciais). As credenciais criptografadas são armazenadas com segurança em uma conta de armazenamento do Azure gerenciada pelo Azure Data Factory Management Services. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [visão geral de segurança do armazenamento do Azure](../security/fundamentals/storage-overview.md).
- **Armazene as credenciais no Azure Key Vault**. Você também pode armazenar a credencial do repositório de dados no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory recupera a credencial durante a execução de uma atividade. Para obter mais informações, consulte [armazenar credencial em Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Criptografia de dados em trânsito
Se o armazenamento de dados de nuvem oferecer suporte a HTTPS ou TLS, todas as transferências de dados entre os serviços de movimentação de dados no Data Factory e um armazenamento de dados de nuvem serão por meio do canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as conexões com o banco de dados SQL do Azure e o Azure SQL Data Warehouse exigem Criptografia (SSL/TLS) enquanto os dados estão em trânsito para e do banco de dado. Quando você estiver criando um pipeline usando JSON, adicione a propriedade de criptografia e defina-a como **true** na cadeia de conexão. Para o armazenamento do Azure, você pode usar **https** na cadeia de conexão.

> [!NOTE]
> Para habilitar a criptografia em trânsito ao mover dados do Oracle, siga uma das opções abaixo:
> 1. No servidor Oracle, vá para Oracle Advanced Security (OAS) e defina as configurações de criptografia, que dão suporte a criptografia triple-DES (3DES) e criptografia AES (AES), consulte [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) para obter detalhes. O ADF negocia automaticamente o método de criptografia para usar aquele que você configurar no OAS ao estabelecer conexão com o Oracle.
> 2. No ADF, você pode adicionar EncryptionMethod = 1 na cadeia de conexão (no serviço vinculado). Isso usará SSL/TLS como o método de criptografia. Para usar isso, você precisa desabilitar as configurações de criptografia não SSL no OAS no lado do servidor Oracle para evitar o conflito de criptografia.

> [!NOTE]
> A versão do TLS usada é 1,2.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns armazenamentos de dados dão suporte à criptografia de dados em repouso. Recomendamos que você habilite o mecanismo de criptografia de dados para esses armazenamentos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
O Transparent Data Encryption (TDE) no Azure SQL Data Warehouse ajuda a proteger contra a ameaça de atividades mal-intencionadas, executando criptografia e descriptografia em tempo real de seus dados em repouso. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger um banco de dados no SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
O banco de dados SQL do Azure também dá suporte à TDE (Transparent Data Encryption), que ajuda a proteger contra a ameaça de atividades mal-intencionadas, executando criptografia e descriptografia em tempo real dos dados, sem a necessidade de alterações no aplicativo. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte Transparent [Data Encryption para o banco de dados SQL e data warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store também fornece criptografia para os dados armazenados na conta. Quando habilitado, o Data Lake Store criptografa automaticamente os dados antes de persistir e descriptografar antes da recuperação, tornando-o transparente para o cliente que acessa os dados. Para obter mais informações, consulte [segurança em Azure data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de BLOBs do Azure e armazenamento de tabelas do Azure
O armazenamento de BLOBs do Azure e o Criptografia do Serviço de Armazenamento de suporte do armazenamento de tabelas do Azure (SSE), que criptografa os dados automaticamente antes de persistir para o armazenamento e descriptografar antes da recuperação. Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 dá suporte à criptografia de cliente e de servidor de dados em repouso. Para obter mais informações, consulte [protegendo dados usando criptografia](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
O Amazon redshift dá suporte à criptografia de cluster para dados em repouso. Para obter mais informações, consulte o [Amazon redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
O Salesforce dá suporte à criptografia de plataforma de blindagem que permite a criptografia de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [noções básicas sobre o fluxo de autenticação do servidor Web OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Cenários híbridos
Os cenários híbridos exigem o tempo de execução de integração auto-hospedado para serem instalados em uma rede local, dentro de uma rede virtual (Azure) ou dentro de uma Amazon (nuvem privada virtual). O tempo de execução de integração auto-hospedado deve ser capaz de acessar os armazenamentos de dados locais. Para obter mais informações sobre o Integration Runtime de hospedagem interna, consulte [como criar e configurar o tempo de execução de integração auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![canais de tempo de execução de integração auto-hospedado](media/data-movement-security-considerations/data-management-gateway-channels.png)

O canal de comando permite a comunicação entre os serviços de movimentação de dados em Data Factory e o tempo de execução de integração auto-hospedado. A comunicação contém informações relacionadas à atividade. O canal de dados é usado para transferir dados entre armazenamentos de dados locais e armazenamentos de dados de nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do repositório de dados local
As credenciais para seus armazenamentos de dados locais são sempre criptografadas e armazenadas. Eles podem ser armazenados localmente no computador do Integration Runtime de hospedagem interna ou armazenados em Azure Data Factory armazenamento gerenciado (assim como as credenciais de armazenamento em nuvem). 

- **Armazene as credenciais localmente**. Se você quiser criptografar e armazenar credenciais localmente no Integration Runtime de hospedagem interna, siga as etapas em [criptografar credenciais para armazenamentos de dados locais no Azure data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores dão suporte a essa opção. O tempo de execução de integração auto-hospedado usa o [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows para criptografar os dados confidenciais e as informações de credenciais. 

   Use o cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** para criptografar credenciais de serviço vinculadas e detalhes confidenciais no serviço vinculado. Em seguida, você pode usar o JSON retornado (com o elemento **EncryptedCredential** na cadeia de conexão) para criar um serviço vinculado usando o cmdlet **set-AzDataFactoryV2LinkedService** .  

- **Armazenar em Azure data Factory armazenamento gerenciado**. Se você usar diretamente o cmdlet **set-AzDataFactoryV2LinkedService** com as cadeias de conexão e as credenciais embutidas no JSON, o serviço vinculado será criptografado e armazenado em Azure data Factory armazenamento gerenciado. As informações confidenciais ainda são criptografadas pelo certificado e a Microsoft gerencia esses certificados.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas usadas ao criptografar o serviço vinculado no tempo de execução de integração auto-hospedado
Por padrão, o PowerShell usa a porta 8060 no computador com o tempo de execução de integração auto-hospedado para comunicação segura. Se necessário, essa porta pode ser alterada.  

![Porta HTTPS para o gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Criptografia em trânsito
Todas as transferências de dados são por meio de canal seguro HTTPS e TLS sobre TCP para evitar ataques man-in-the-Middle durante a comunicação com os serviços do Azure.

Você também pode usar [VPN IPSec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Azure ExpressRoute](../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre sua rede local e o Azure.

A rede virtual do Azure é uma representação lógica da sua rede na nuvem. Você pode conectar uma rede local à sua rede virtual Configurando a VPN IPSec (site a site) ou o ExpressRoute (emparelhamento privado).    

A tabela a seguir resume a rede e as recomendações de configuração de tempo de execução de integração auto-hospedado com base em diferentes combinações de locais de origem e de destino para movimentação de dados híbridos.

| Source      | Destino                              | Configuração de rede                    | Configuração do runtime de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| No local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPSec (ponto a site ou site a site) | O tempo de execução de integração auto-hospedado deve ser instalado em uma máquina virtual do Azure na rede virtual.  |
| No local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (emparelhamento privado)           | O tempo de execução de integração auto-hospedado deve ser instalado em uma máquina virtual do Azure na rede virtual.  |
| No local | Serviços baseados no Azure que têm um ponto de extremidade público | ExpressRoute (emparelhamento da Microsoft)            | O Integration Runtime de hospedagem interna pode ser instalado localmente ou em uma máquina virtual do Azure. |

As imagens a seguir mostram o uso do tempo de execução de integração auto-hospedado para mover dados entre um banco de dados local e os serviços do Azure usando o ExpressRoute e a VPN IPSec (com a rede virtual do Azure):

**ExpressRoute**

![Usar o ExpressRoute com gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![VPN IPSec com gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e endereços IP de lista de permissões

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para rede local/privada  
Em uma empresa, um firewall corporativo é executado no roteador central da organização. O Firewall do Windows é executado como um daemon no computador local no qual o tempo de execução de integração auto-hospedado está instalado. 

A tabela a seguir fornece os requisitos de porta e domínio de saída para firewalls corporativos:

| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar aos serviços de movimentação de dados no Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar ao serviço de Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para baixar as atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar isso. |
| `*.core.windows.net`          | 443            | Usado pelo tempo de execução de integração auto-hospedado para se conectar à conta de armazenamento do Azure quando você usa o recurso de [cópia em etapas](copy-activity-performance.md#staged-copy) . |
| `*.database.windows.net`      | 1433           | Adicional Necessário ao copiar de ou para o banco de dados SQL do Azure ou o Azure SQL Data Warehouse. Use o recurso de cópia em etapas para copiar dados para o Azure SQL Database ou para o SQL Data Warehouse de recursos de backup sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Adicional Necessário quando você copia de ou para Azure Data Lake Store. |

> [!NOTE] 
> Talvez seja necessário gerenciar portas ou domínios de lista de permissões no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o banco de dados SQL do Azure, o Azure SQL Data Warehouse e Azure Data Lake Store como exemplos.   

A tabela a seguir fornece os requisitos de porta de entrada para o Firewall do Windows:

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Exigido pelo cmdlet de criptografia do PowerShell, conforme descrito em [criptografar credenciais para armazenamentos de dados locais no Azure data Factory](encrypt-credentials-self-hosted-integration-runtime.md)e pelo aplicativo Gerenciador de credenciais para definir com segurança as credenciais para armazenamentos de dados locais no local de hospedagem interna Integration Runtime. |

![Requisitos de porta do gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Configurações de IP e lista de permissões em repositórios de dados
Alguns armazenamentos de dados na nuvem também exigem que você adicione a lista de permissões do endereço IP do computador que está acessando o repositório. Verifique se o endereço IP do computador de tempo de execução de integração auto-hospedado está na lista de permissões ou configurado no firewall adequadamente.

Os seguintes armazenamentos de dados de nuvem exigem que você adicione a lista de permissões do endereço IP do computador do Integration Runtime de hospedagem interna. Por padrão, alguns desses armazenamentos de dados podem não exigir a lista de permissões. 

- [Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O tempo de execução de integração auto-hospedado pode ser compartilhado entre diferentes fábricas de dados?**

Sim. Mais detalhes [aqui](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Quais são os requisitos de porta para o tempo de execução de integração auto-hospedado funcionar?**

O Integration Runtime de hospedagem interna faz conexões baseadas em HTTP acessarem a Internet. As portas de saída 443 devem ser abertas para que o tempo de execução de integração auto-hospedado faça essa conexão. Abra a porta de entrada 8060 somente no nível do computador (não no nível do firewall corporativo) para o aplicativo Gerenciador de credenciais. Se o banco de dados SQL do Azure ou o SQL Data Warehouse do Azure for usado como a origem ou o destino, você precisará abrir a porta 1433 também. Para obter mais informações, consulte a seção [configurações de firewall e endereços IP de lista de](#firewall-configurations-and-whitelisting-ip-address-of-gateway) permissões. 


## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre Azure Data Factory desempenho da atividade de cópia, consulte [Guia de desempenho e ajuste da atividade de cópia](copy-activity-performance.md).

 
