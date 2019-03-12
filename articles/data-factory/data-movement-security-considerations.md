---
title: Considerações de segurança no Azure Data Factory | Documentos da Microsoft
description: Descreve a infraestrutura de segurança básica que serviços de movimento de dados no Azure Data Factory utilizam para ajudar a proteger seus dados.
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
ms.openlocfilehash: 03976d321a92f6049d06cea88604618d5a2dee67
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532275"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerações de segurança para movimento de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Versão 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versão atual](data-movement-security-considerations.md)

Este artigo descreve a infraestrutura de segurança básica que serviços de movimento de dados no Azure Data Factory utilizam para ajudar a proteger seus dados. Recursos de gestão de fábrica de dados são criados com a infraestrutura de segurança do Azure e utilizam todas as medidas de segurança possível oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](concepts-pipelines-activities.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Esses pipelines residem na região em que foi criada a fábrica de dados. 

Apesar do Data Factory só está disponível em algumas regiões, o serviço de movimento de dados é [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location) para garantir a conformidade de dados, eficiência e a rede reduzido os custos de saída. 

O Azure Data Factory não armazena quaisquer dados, exceto para as credenciais do serviço ligado para arquivos de dados de cloud, que são encriptados utilizando certificados. Com o Data Factory, pode criar fluxos de trabalho condicionados por dados para orquestrar o movimento de dados entre [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats)e o processamento de dados utilizando [serviços de computação](compute-linked-services.md) noutras regiões ou num ambiente no local. Também pode monitorizar e gerir fluxos de trabalho ao utilizar SDKs e o Azure Monitor.

Certificação fábrica de dados para:
| **[Certificação em ESTRELA da CSA](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Se estiver interessado em conformidade do Azure e como o Azure protege a sua própria infra-estrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Para obter a lista mais recente de todas as verificações de ofertas de conformidade do Azure - https://aka.ms/AzureCompliance.

Neste artigo, vamos rever considerações de segurança nos seguintes cenários de movimento de dados de dois: 

- **Cenário de nuvem**: Neste cenário, a origem e destino estão publicamente acessíveis pela internet. Estes incluem os serviços de armazenamento gerida na cloud, como o armazenamento do Azure, Azure SQL Data Warehouse, SQL Database do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como o Salesforce e protocolos de web, como o FTP e OData. Encontrar uma lista completa das origens de dados suportados no [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
- **Cenário híbrido**: Neste cenário, a origem ou de destino é protegido por uma firewall ou dentro de uma rede empresarial no local. Em alternativa, o arquivo de dados está numa privada, rede ou de rede virtual (com mais frequência a origem) e não está acessível ao público. Servidores de base de dados hospedados em máquinas virtuais também abrangidos por este cenário.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvem

### <a name="securing-data-store-credentials"></a>A proteção dos dados armazena credenciais

- **Store credenciais encriptadas num arquivo gerido do Azure Data Factory**. O Data Factory ajuda a proteger as suas credenciais de arquivo de dados criptografando-os com certificados gerenciados pela Microsoft. Estes certificados são revezados de cada dois anos (que inclui a renovação do certificado e a migração de credenciais). As credenciais encriptadas são armazenadas em segurança numa conta de armazenamento do Azure gerida por serviços de gestão do Azure Data Factory. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [descrição geral da segurança de armazenamento do Azure](../security/security-storage-overview.md).
- **Store credenciais no Azure Key Vault**. Pode também armazenar credenciais do arquivo de dados no [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Fábrica de dados obtém a credencial durante a execução de uma atividade. Para obter mais informações, consulte [credencial Store no Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se o arquivo de dados na cloud suporta HTTPS ou TLS, todos os dados transferidos entre os serviços de movimento de dados no Data Factory e um arquivo de dados na cloud são através do canal seguro, HTTPS ou TLS.

> [!NOTE]
> Todas as ligações à base de dados do Azure SQL e o Azure SQL Data Warehouse exigem encriptação (SSL/TLS), enquanto os dados se encontram em trânsito para e da base de dados. Quando estiver criando um pipeline com o JSON, adicione a propriedade de encriptação e configurá-lo para **true** na cadeia de ligação. Armazenamento do Azure, pode utilizar **HTTPS** na cadeia de ligação.

> [!NOTE]
> Para ativar a encriptação em trânsito ao mover dados do Oracle siga um das seguintes opções:
> 1. No servidor do Oracle, aceda a Oracle Advanced Security (OAS) e configurar as definições de encriptação, que suporta a encriptação de Triple-DES (3DES) e encriptação AES (Advanced Standard), consulte [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) para obter detalhes. ADF negocia automaticamente o método de encriptação a utilizar um que configurar no OAS quando estabelecer ligação ao Oracle.
> 2. No ADF, é possível adicionar EncryptionMethod = 1 na cadeia de ligação (no serviço ligado). Isto irá utilizar SSL/TLS como método de encriptação. Para utilizá-lo, terá de desativar o SSL não as definições de encriptação no OAS no lado do servidor Oracle para evitar conflitos de encriptação.

> [!NOTE]
> Versão do TLS utilizada é 1.2.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns dados armazena a encriptação de suporte de dados em repouso. Recomendamos que ative o mecanismo de encriptação de dados para esses arquivos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Armazém de Dados SQL do Azure
Encriptação de dados transparente (TDE) no armazém de dados SQL do Azure ajuda a proteger contra ameaças de atividades maliciosas através de encriptação em tempo real e a descriptografia dos seus dados em repouso. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger uma base de dados no SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Base de dados SQL do Azure também suporta a encriptação de dados transparente (TDE), que ajuda a proteger contra ameaças de atividades maliciosas, ao efetuar a encriptação em tempo real e a descriptografia dos dados, sem a necessidade de alterações à aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação de dados transparente para a base de dados SQL e o armazém de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Arquivo do Azure Data Lake
Azure Data Lake Store também fornece encriptação para dados armazenados na conta. Quando ativada, o Data Lake Store é automaticamente encripta os dados antes de persistir e desencripta antes de recuperação, tornando-a transparente para o cliente que acessa os dados. Para obter mais informações, consulte [segurança no Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure
Armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure suportam Storage Service Encryption (SSE), que automaticamente encripta os dados antes de continuar a armazenar e desencriptar antes da obtenção. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 suporta a encriptação de cliente e servidor de dados em repouso. Para obter mais informações, consulte [proteger dados através de encriptação](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift suporta a encriptação de cluster para dados inativos. Para obter mais informações, consulte [encriptação de base de dados do Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta a encriptação de plataforma do escudo que permite que a encriptação de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [Noções básicas sobre o fluxo de autenticação do Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Cenários híbridos
Cenários híbridos necessitam de runtime de integração autoalojado para ser instalado numa rede no local, numa rede virtual (Azure) ou dentro de uma cloud privada virtual (Amazon). O runtime de integração autoalojado tem de ser capaz de acessar os arquivos de dados local. Para obter mais informações sobre o integration runtime autoalojado, consulte [runtime de integração de autoalojado de como criar e configurar](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![canais de runtime de integração autoalojado](media/data-movement-security-considerations/data-management-gateway-channels.png)

O canal de comando permite a comunicação entre serviços de movimento de dados no Data Factory e o integration runtime autoalojado. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para transferir dados entre arquivos de dados no local e arquivos de dados na cloud.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de arquivo de dados no local
As credenciais para os arquivos de dados no local são sempre encriptadas e armazenadas. Eles podem ser armazenados localmente no computador runtime de integração autoalojado ou armazenados no armazenamento do Azure Data Factory gerido (tal como credenciais de armazenamento da cloud). 

- **Store credenciais localmente**. Se pretende encriptar e armazenar credenciais localmente no runtime de integração autoalojado, siga os passos em [encriptar as credenciais para arquivos de dados no local no Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores de suportar esta opção. O runtime de integração autoalojado utiliza o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para encriptar as informações de dados e as credenciais confidenciais. 

   Utilize o **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet para encriptar as credenciais do serviço ligado e detalhes confidenciais no serviço ligado. Em seguida, pode utilizar o JSON devolvido (com o **EncryptedCredential** elemento na cadeia de ligação) para criar um serviço ligado com o **conjunto AzDataFactoryV2LinkedService** cmdlet.  

- **Store no armazenamento do Azure Data Factory geridos**. Se usar diretamente o **Set-AzDataFactoryV2LinkedService** cmdlet com a ligação de cadeias de caracteres e credenciais inline no JSON, o serviço ligado é encriptado e armazenado no armazenamento do Azure Data Factory geridos. As informações confidenciais ainda são encriptadas pelo certificado e Microsoft gere estes certificados.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas utilizadas quando encriptar um serviço ligado no runtime de integração autoalojado
Por predefinição, o PowerShell utiliza a porta 8050 na máquina com o runtime de integração autoalojado para uma comunicação segura. Se necessário, esta porta pode ser alterada.  

![Porta HTTPS para o gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Encriptação em trânsito
Todas as transferências de dados são através do canal seguro HTTPS e TLS através de TCP para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.

Também pode utilizar [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Azure ExpressRoute](../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a rede no local e o Azure.

Rede Virtual do Azure é uma representação lógica da sua rede na cloud. Pode ligar uma rede no local à sua rede virtual ao configurar a VPN IPSec (site a site) ou do ExpressRoute (peering privado).    

A tabela seguinte resume a rede e recomendações de configuração do runtime de integração autoalojado com base em diferentes combinações de origem e destino localizações para movimento de dados híbrido.

| Origem      | Destino                              | Configuração de rede                    | Configuração do runtime de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| No local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | VPN IPSec (point-to-site ou site a site) | O runtime de integração autoalojado pode ser instalado no local ou numa máquina virtual do Azure numa rede virtual. |
| No local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | ExpressRoute (peering privado)           | O runtime de integração autoalojado pode ser instalado no local ou numa máquina virtual do Azure numa rede virtual. |
| No local | Serviços baseados no Azure que tem um ponto final público | ExpressRoute (peering público)            | O runtime de integração autoalojado tem de ser instalado no local. |

As seguintes imagens mostram o uso do runtime de integração autoalojado para mover dados entre uma base de dados no local e serviços do Azure com o ExpressRoute e VPN IPSec (com a rede Virtual do Azure):

**ExpressRoute**

![Utilizar o ExpressRoute com o gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![VPN IPSec com o gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Configurações de firewall e os endereços IP da lista de permissões

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede no local/privada  
Numa empresa, uma firewall empresarial é executado no router central da organização. Firewall do Windows é executado como um daemon no computador local em que o runtime de integração autoalojado é instalado. 

A tabela seguinte fornece uma saída requisitos de porta e o domínio para firewalls corporativos:

| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Necessário pelo tempo de execução de integração autoalojado para ligar aos serviços de movimento de dados no Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Necessário pelo tempo de execução de integração autoalojado para ligar ao serviço Data Factory. |
| `download.microsoft.com`    | 443            | O runtime de integração autoalojado para transferir as atualizações necessárias. Se tiver desativado a atualização automática, em seguida, pode ignorar isto. |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de execução de integração autoalojado para ligar à conta de armazenamento do Azure ao utilizar o [cópia faseada](copy-activity-performance.md#staged-copy) funcionalidade. |
| `*.database.windows.net`      | 1433           | (Opcional) É necessário quando copiar a partir de ou para a base de dados do Azure SQL ou armazém de dados SQL do Azure. Utilize a funcionalidade de cópia faseada para copiar dados para a base de dados do Azure SQL ou o Azure SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Opcional) É necessário quando copiar do ou para o Azure Data Lake Store. |

> [!NOTE] 
> Poderá ter de gerir as portas ou domínios de listas de permissões ao nível do firewall Corporativo, conforme necessário, as origens de dados correspondentes. Esta tabela só utiliza a base de dados do Azure SQL, o Azure SQL Data Warehouse e o Azure Data Lake Store como exemplos.   

A tabela seguinte fornece os requisitos de porta de entrada para o Firewall do Windows:

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Necessário para o cmdlet de encriptação do PowerShell, conforme descrito em [encriptar as credenciais para arquivos de dados no local no Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)e pela aplicação de Gestor de credenciais com segurança definir credenciais para arquivos de dados no local o tempo de execução de integração autoalojado. |

![Requisitos de porta de gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Configurações de IP e a lista de permissões nos arquivos de dados
Alguns arquivos de dados na cloud também exigem whitelist o endereço IP da máquina acesso à loja. Certifique-se de que o endereço IP do computador runtime de integração autoalojado é o na lista de permissões ou configurado adequadamente na firewall.

Os seguintes arquivos de dados de nuvem necessitam de adicionar o endereço IP do computador runtime de integração autoalojado. Alguns desses arquivos de dados, por predefinição, poderão não requerer a lista de permissões. 

- [Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O runtime de integração autoalojado pode ser compartilhado entre as fábricas de dados diferentes?**

Podemos ainda não suporta esta funcionalidade. Estamos a trabalhar ativamente no mesmo.

**Quais são os requisitos de porta para o runtime de integração autoalojado trabalhar?**

O runtime de integração autoalojado torna conexões baseadas em HTTP para aceder à internet. As portas de saída 443 tem de ser abertas para o runtime de integração autoalojado fazer essa conexão. Abra a porta de entrada 8050 apenas ao nível da máquina (e não ao nível do firewall Corporativo) para a aplicação do Gestor de credenciais. Se a base de dados do Azure SQL ou armazém de dados SQL do Azure é utilizado como a origem ou destino, terá de abrir a porta 1433 também. Para obter mais informações, consulte a [endereços IP da lista de permissões e configurações de Firewall](#firewall-configurations-and-whitelisting-ip-address-of-gateway) secção. 


## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o desempenho de atividade de cópia de fábrica de dados do Azure, consulte [guia de sintonização de desempenho de atividade de cópia e](copy-activity-performance.md).

 
