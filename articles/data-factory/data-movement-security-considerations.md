---
title: Considerações de segurança
description: Descreve a infraestrutura de segurança básica que os serviços de movimento de dados na Azure Data Factory utilizam para ajudar a proteger os seus dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bee627ade4f66206cd5254fc32bc7aa9973c7bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131305"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerações de segurança para o movimento de dados na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versão atual](data-movement-security-considerations.md)

Este artigo descreve a infraestrutura básica de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para ajudar a proteger os seus dados. Os recursos de gestão da Fábrica de Dados são construídos em infraestruturas de segurança Azure e utilizam todas as medidas de segurança possíveis oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](concepts-pipelines-activities.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Estes gasodutos residem na região onde foi criada a fábrica de dados. 

Embora a Data Factory esteja disponível apenas em poucas regiões, o serviço de movimento de dados está [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location) para garantir a conformidade, eficiência e redução dos custos de egress da rede. 

A Azure Data Factory não armazena quaisquer dados, exceto credenciais de serviço ligadas para lojas de dados em nuvem, que são encriptadas através da utilização de certificados. Com a Data Factory, cria fluxos de trabalho orientados para dados para orquestrar a circulação de dados entre lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats)e o processamento de dados utilizando [serviços de computação](compute-linked-services.md) noutras regiões ou num ambiente no local. Também pode monitorizar e gerir fluxos de trabalho utilizando SDKs e Monitor Azure.

Data Factory foi certificado para:

| **[Certificação CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[BAA HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Se está interessado na conformidade com o Azure e como o Azure assegura a sua própria infraestrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Para a mais recente lista de todas https://aka.ms/AzureComplianceas ofertas da Azure Compliance verifique - .

Neste artigo, revemos considerações de segurança nos seguintes dois cenários de movimento de dados: 

- **Cenário de nuvem**: Neste cenário, tanto a sua origem como o seu destino são acessíveis ao público através da internet. Estes incluem serviços geridos de armazenamento em nuvem como O Armazenamento Azure, Armazém de Dados Azure SQL, Base de Dados Azure SQL, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce, e protocolos web como FTP e OData. Encontre uma lista completa de fontes de dados suportadas em [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
- **Cenário híbrido**: Neste cenário, ou a sua fonte ou o seu destino estão atrás de uma firewall ou dentro de uma rede corporativa no local. Ou, a loja de dados está numa rede privada ou rede virtual (na maioria das vezes a fonte) e não é acessível ao público. Os servidores de base de dados alojados em máquinas virtuais também se enquadram neste cenário.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvens

### <a name="securing-data-store-credentials"></a>Garantir credenciais de loja de dados

- **Guarde credenciais encriptadas numa loja gerida pela Azure Data Factory.** Data Factory ajuda a proteger as credenciais da sua loja de dados encriptando-as com certificados geridos pela Microsoft. Estes certificados são rotativos de dois em dois anos (o que inclui a renovação de certificados e a migração de credenciais). Para mais informações sobre a segurança do Armazenamento Azure, consulte a visão geral da [segurança do Armazenamento Azure](../security/fundamentals/storage-overview.md).
- **Guarde as credenciais no Cofre de Chaves Azure.** Também pode armazenar a credencial da loja de dados no [Cofre de Chaves Azure.](https://azure.microsoft.com/services/key-vault/) Data Factory recupera a credencial durante a execução de uma atividade. Para mais informações, consulte a [credencial da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se a loja de dados na nuvem suportar HTTPS ou TLS, todas as transferências de dados entre os serviços de movimento de dados na Data Factory e uma loja de dados em nuvem são via canal seguro HTTPS ou TLS .

> [!NOTE]
> Todas as ligações à Base de Dados Azure SQL e ao Armazém de Dados Azure SQL requerem encriptação (SSL/TLS) enquanto os dados estão em trânsito de e para a base de dados. Quando estiver a ser autor de um pipeline utilizando o JSON, adicione a propriedade de encriptação e deteteteta-a de **forma verdadeira** na cadeia de ligação. Para o Armazenamento Azure, pode utilizar **HTTPS** na cadeia de ligação.

> [!NOTE]
> Para permitir a encriptação em trânsito enquanto movem dados da Oracle siga uma das opções abaixo:
> 1. No servidor Oracle, vá ao Oracle Advanced Security (OAS) e configure as definições de encriptação, que suporta mishética triplas (3DES) e Advanced Encryption Standard (AES), consulte [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) para mais detalhes. A ADF negoceia automaticamente o método de encriptação para utilizar o que configura na OAS ao estabelecer a ligação à Oracle.
> 2. Na ADF, pode adicionar EncryptionMethod=1 na cadeia de ligação (no Serviço Linked). Isto utilizará o SSL/TLS como método de encriptação. Para usá-lo, é necessário desativar as definições de encriptação não-SSL no OAS do lado do servidor Oracle para evitar conflitos de encriptação.

> [!NOTE]
> A versão TLS utilizada é de 1.2.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Algumas lojas de dados suportam encriptação de dados em repouso. Recomendamos que ative o mecanismo de encriptação de dados para essas lojas de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
A Encriptação transparente de dados (TDE) no Azure SQL Data Warehouse ajuda a proteger contra a ameaça de atividades maliciosas através da encriptação e desencriptação dos seus dados em tempo real em repouso. Este comportamento é transparente para o cliente. Para mais informações, consulte [Secure a database in SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
A Base de Dados Azure SQL também suporta encriptação transparente de dados (TDE), que ajuda a proteger contra a ameaça de atividade maliciosa através da encriptação e desencriptação em tempo real dos dados, sem exigir alterações na aplicação. Este comportamento é transparente para o cliente. Para mais informações, consulte [encriptação transparente de dados para Base de Dados SQL e Armazém de Dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Azure Data Lake Store também fornece encriptação para dados armazenados na conta. Quando ativado, a Data Lake Store encripta automaticamente os dados antes de persistir e desencripta-se antes de recuperar, tornando-o transparente para o cliente que acede aos dados. Para mais informações, consulte [a Segurança na Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento azure Blob e armazenamento de mesa azure
Armazenamento Azure Blob e suporte de armazenamento de mesa Azure Storage Service Encriptação (SSE), que encripta automaticamente os seus dados antes de persistir em armazenamento e desencriptação antes de recuperar. Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para dados em repouso](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 suporta a encriptação de dados do cliente e do servidor em repouso. Para mais informações, consulte [Proteger dados usando encriptação](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
A Amazon Redshift suporta a encriptação do cluster para dados em repouso. Para mais informações, consulte a Encriptação da Base de [Dados Redshift da Amazon](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
A Salesforce suporta a encriptação da Plataforma Shield que permite a encriptação de todos os ficheiros, anexos e campos personalizados. Para mais informações, consulte [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Cenários híbridos
Os cenários híbridos exigem que o tempo de execução da integração auto-hospedada seja instalado numa rede no local, dentro de uma rede virtual (Azure), ou dentro de uma nuvem privada virtual (Amazon). O tempo de execução da integração auto-hospedado deve poder aceder às lojas de dados locais. Para obter mais informações sobre o tempo de funcionação da integração auto-hospedada, consulte Como criar e configurar o tempo de funcionação da [integração auto-hospedado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) 

![canais de execução de integração auto-hospedados](media/data-movement-security-considerations/data-management-gateway-channels.png)

O canal de comando permite a comunicação entre os serviços de movimento de dados na Data Factory e o tempo de execução de integração auto-hospedado. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para a transferência de dados entre lojas de dados no local e lojas de dados na nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de loja de dados no local
As credenciais podem ser armazenadas dentro da fábrica de dados ou ser [referenciadas por uma fábrica](store-credentials-in-key-vault.md) de dados durante o tempo de funcionação do Azure Key Vault. Se armazenar credenciais dentro da fábrica de dados, é sempre armazenado encriptado no tempo de funcionação de integração auto-hospedado. 
 
- **Armazenar credenciais localmente.** Se utilizar diretamente o **cmdlet Set-AzDataFactoryV2LinkedService** com as cordas de ligação e credenciais inline no JSON, o serviço ligado é encriptado e armazenado no tempo de execução de integração auto-hospedado.  Neste caso, as credenciais fluem através do serviço de backend azul, que é extremamente seguro, para a máquina de integração auto-hospedada onde é finalmente encriptada e armazenada. O tempo de execução de integração auto-hospedado utiliza o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para encriptar os dados sensíveis e informações credenciais.

- **Guarde as credenciais no Cofre de Chaves Azure.** Também pode armazenar a credencial da loja de dados no [Cofre de Chaves Azure.](https://azure.microsoft.com/services/key-vault/) Data Factory recupera a credencial durante a execução de uma atividade. Para mais informações, consulte a [credencial da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md).

- **Guarde as credenciais localmente sem fluir as credenciais através do backend azure para o tempo de execução da integração auto-hospedado.** Se quiser encriptar e armazenar credenciais localmente sobre o tempo de execução de integração auto-hospedado sem ter de fluir as credenciais através do backend da fábrica de dados, siga os passos em [credenciais de encriptação para lojas de dados no local na Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores suportam esta opção. O tempo de execução de integração auto-hospedado utiliza o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para encriptar os dados sensíveis e informações credenciais. 

   Utilize o **Cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** para encriptar credenciais de serviço ligadas e detalhes sensíveis no serviço ligado. Em seguida, pode utilizar o JSON devolvido (com o elemento **EncryptedCredential** na cadeia de ligação) para criar um serviço ligado utilizando o cmdlet **Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas utilizadas ao encriptar serviço ligado em tempo de execução de integração auto-hospedada
Por predefinição, a PowerShell utiliza a porta 8060 na máquina com tempo de execução de integração auto-hospedado para uma comunicação segura. Se necessário, esta porta pode ser alterada.  

![Porta HTTPS para o gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Todas as transferências de dados são via canal seguro HTTPS e TLS através da TCP para evitar ataques man-in-the-middle durante a comunicação com os serviços Azure.

Também pode utilizar [o IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [o Azure ExpressRoute](../expressroute/expressroute-introduction.md) para garantir ainda mais o canal de comunicação entre a sua rede no local e o Azure.

A Rede Virtual Azure é uma representação lógica da sua rede na nuvem. Pode ligar uma rede no local à sua rede virtual, configurando O IPSec VPN (site-to-site) ou expressRoute (peering privado).    

O quadro seguinte resume as recomendações de configuração de tempo de execução de integração auto-hospedada com base em diferentes combinações de locais de origem e destino para o movimento de dados híbridos.

| Origem      | Destino                              | Configuração da rede                    | Configuração do runtime de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPSec (ponto-a-local ou site-a-site) | O tempo de execução de integração auto-hospedado deve ser instalado numa máquina virtual Azure na rede virtual.  |
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (peering privado)           | O tempo de execução de integração auto-hospedado deve ser instalado numa máquina virtual Azure na rede virtual.  |
| Local | Serviços baseados em Azure que têm um ponto final público | ExpressRoute (peering da Microsoft)            | O tempo de execução de integração auto-hospedado pode ser instalado no local ou numa máquina virtual Azure. |

As imagens que se seguem mostram o uso do tempo de execução da integração auto-hospedado para mover dados entre uma base de dados no local e serviços Azure utilizando a ExpressRoute e a VPN IPSec (com rede virtual Azure):

**ExpressRoute**

![Use expressRoute com gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![VPN IPSec com porta de entrada](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Configurações de firewall e permitir a configuração da lista para endereços IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede privada/local    
Numa empresa, uma firewall corporativa funciona no router central da organização. O Windows Firewall funciona como um daemon na máquina local em que o tempo de funcionação de integração auto-hospedado é instalado. 

A tabela seguinte fornece requisitos de porta de saída e domínio para firewalls corporativos:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Poderá ter de gerir portas ou configurar a lista de domínios ao nível da firewall corporativa, conforme exigido pelas respetivas fontes de dados. Esta tabela utiliza apenas a Base de Dados Azure SQL, o Azure SQL Data Warehouse e a Azure Data Lake Store como exemplos.   

A tabela que se segue fornece requisitos de porta de entrada para o Windows Firewall:

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Exigido pela encriptação PowerShell cmdlet como descrito nas [credenciais Encrypt para lojas de dados no local na Azure Data Factory,](encrypt-credentials-self-hosted-integration-runtime.md)e pela aplicação do gestor credencial para definir credenciais seguras para lojas de dados no local no tempo de execução de integração auto-hospedada. |

![Requisitos da porta gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Configurações IP e permitir a configuração da lista em lojas de dados
Algumas lojas de dados na nuvem também requerem que permita o endereço IP da máquina de acesso à loja. Certifique-se de que o endereço IP da máquina de tempo de execução de integração auto-hospedada é permitido ou configurado na firewall adequadamente.

As seguintes lojas de dados em nuvem exigem que permita o endereço IP da máquina de tempo de execução de integração auto-hospedada. Algumas destas lojas de dados, por defeito, podem não necessitar de lista de permitir. 

- [Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) 
- [Armazém de dados Azure SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O tempo de execução da integração auto-hospedado pode ser partilhado em diferentes fábricas de dados?**

Sim. Mais detalhes [aqui](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Quais são os requisitos portuários para que o tempo de funcionação da integração auto-hospedada funcione?**

O tempo de execução de integração auto-hospedado faz com que as ligações baseadas em HTTP acedam à internet. As portas de saída 443 devem ser abertas para o tempo de execução de integração auto-hospedado para fazer esta ligação. Abra a porta de entrada 8060 apenas ao nível da máquina (não ao nível da firewall corporativa) para aplicação de gestor de credenciais. Se a Base de Dados Azure SQL ou o Azure SQL Data Warehouse forem utilizados como fonte ou destino, também precisa de abrir a porta 1433. Para mais informações, consulte as configurações do Firewall e permita a configuração da lista para a secção de [endereços IP.](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 


## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho da atividade da cópia de dados do Azure, consulte o desempenho da Atividade de Cópia e o guia de [afinação.](copy-activity-performance.md)

 
