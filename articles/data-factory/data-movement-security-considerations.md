---
title: Considerações de segurança
description: Descreve a infraestrutura básica de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para ajudar a proteger os seus dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 458336f27f01cfb0d127b96cd3df6aa40f8db0b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440563"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerações de segurança para o movimento de dados na Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versão atual](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve a infraestrutura básica de segurança que os serviços de movimento de dados na Azure Data Factory usam para ajudar a proteger os seus dados. Os recursos de gestão da Data Factory são construídos em infraestruturas de segurança Azure e utilizam todas as medidas de segurança possíveis oferecidas pela Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](concepts-pipelines-activities.md) de dados. Um pipeline é um agrupamento lógico de atividades que, em conjunto, executam uma tarefa. Estes oleodutos residem na região onde foi criada a fábrica de dados. 

Embora a Data Factory esteja disponível apenas em poucas regiões, o serviço de movimento de dados está [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location) para garantir a conformidade, eficiência e custos reduzidos de saída da rede. 

A Azure Data Factory não armazena quaisquer dados exceto credenciais de serviço ligadas para lojas de dados em nuvem, que são encriptadas através de certificados. Com a Data Factory, cria fluxos de trabalho baseados em dados para orquestrar o movimento de dados entre lojas de [dados apoiadas,](copy-activity-overview.md#supported-data-stores-and-formats)e o processamento de dados utilizando [serviços de computação](compute-linked-services.md) noutras regiões ou num ambiente no local. Também pode monitorizar e gerir fluxos de trabalho utilizando SDKs e Azure Monitor.

A Data Factory foi certificada para:

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

Se estiver interessado na conformidade do Azure e na forma como o Azure assegura a sua própria infraestrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Para a lista mais recente de todas as ofertas da Azure Compliance, verifique -  https://aka.ms/AzureCompliance .

Neste artigo, analisamos considerações de segurança nos seguintes dois cenários de movimento de dados: 

- **Cenário em nuvem**: Neste cenário, tanto a sua fonte como o seu destino são acessíveis ao público através da internet. Estes incluem serviços de armazenamento em nuvem geridos, tais como Azure Storage, Azure Synapse Analytics (anteriormente SQL Data Warehouse), Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce, e protocolos web como FTP e OData. Encontre uma lista completa de fontes de dados suportadas em [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
- **Cenário híbrido**: Neste cenário, a sua fonte ou o seu destino está por trás de uma firewall ou dentro de uma rede corporativa no local. Ou, a loja de dados está numa rede privada ou rede virtual (na maioria das vezes a fonte) e não é acessível ao público. Os servidores de base de dados alojados em máquinas virtuais também se enquadram neste cenário.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários em nuvem

### <a name="securing-data-store-credentials"></a>Garantir credenciais de armazenamento de dados

- **Armazenar credenciais encriptadas numa loja gerida pela Azure Data Factory**. A Data Factory ajuda a proteger as suas credenciais de loja de dados encriptando-as com certificados geridos pela Microsoft. Estes certificados são rodados de dois em dois anos (que inclui a renovação de certificados e a migração de credenciais). Para obter mais informações sobre a segurança do Azure Storage, consulte [a visão geral da segurança do Azure Storage](../security/fundamentals/storage-overview.md).
- **Armazenar credenciais no Cofre da Chave Azure**. Também pode armazenar a credencial da loja de dados no [Cofre da Chave Azure.](https://azure.microsoft.com/services/key-vault/) A Data Factory recupera a credencial durante a execução de uma atividade. Para obter mais informações, consulte [a credencial da Loja no Cofre da Chave Azure.](store-credentials-in-key-vault.md)

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se a loja de dados em nuvem suportar HTTPS ou TLS, todas as transferências de dados entre os serviços de movimento de dados na Data Factory e uma loja de dados em nuvem são através de um canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as ligações à Base de Dados Azure SQL e à Azure Synapse Analytics requerem encriptação (SSL/TLS) enquanto os dados estão em trânsito de e para a base de dados. Quando estiver a ser autor de um oleoduto utilizando o JSON, adicione a propriedade de encriptação e desacri-a para **ser verdadeira** na cadeia de ligação. Para o armazenamento Azure, pode utilizar **HTTPS** na cadeia de ligação.

> [!NOTE]
> Para permitir a encriptação em trânsito enquanto os dados de deslocação da Oracle seguem uma das opções abaixo:
> 1. No servidor Oracle, vá à Oracle Advanced Security (OAS) e configufique as definições de encriptação, que suporta a Encriptação Triple-DES (3DES) e a Padrão de Encriptação Avançada (AES), consulte [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) para mais detalhes. A ADF negoceia automaticamente o método de encriptação para utilizar o que configura na OAS ao estabelecer a ligação à Oráculo.
> 2. Em ADF, pode adicionar EncryptionMethod=1 na cadeia de ligação (no Serviço Ligado). Isto utilizará o SSL/TLS como método de encriptação. Para utilizar isto, é necessário desativar as definições de encriptação não-SSL no lado do servidor OS no lado do servidor Oracle para evitar conflitos de encriptação.

> [!NOTE]
> A versão TLS utilizada é 1.2.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Algumas lojas de dados suportam encriptação de dados em repouso. Recomendamos que ative o mecanismo de encriptação de dados para essas lojas de dados. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
A Encriptação de Dados Transparente (TDE) no Azure Synapse Analytics ajuda a proteger contra a ameaça de atividade maliciosa, realizando encriptação em tempo real e desencriptação dos seus dados em repouso. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [Secure a database in Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
O Azure SQL Database também suporta encriptação de dados transparentes (TDE), que ajuda a proteger contra a ameaça de atividade maliciosa através da encriptação e desencriptação em tempo real dos dados, sem exigir alterações na aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação de dados transparente para a Base de Dados SQL e o Data Warehouse.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Azure Data Lake Store também fornece encriptação para dados armazenados na conta. Quando ativada, a Data Lake Store encripta automaticamente os dados antes de persistir e desencriptar antes da recuperação, tornando-os transparentes para o cliente que acede aos dados. Para mais informações, consulte [a Segurança na Azure Data Lake Store.](../data-lake-store/data-lake-store-security-overview.md) 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento Azure Blob e armazenamento da mesa Azure
O armazenamento Azure Blob e o suporte de armazenamento da Azure Table suportam encriptação do serviço de armazenamento (SSE), que encripta automaticamente os seus dados antes de persistir no armazenamento e desencripta antes da recuperação. Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso.](../storage/common/storage-service-encryption.md)

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 suporta a encriptação de dados tanto do cliente como do servidor em repouso. Para obter mais informações, consulte [proteger dados usando encriptação.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)

#### <a name="amazon-redshift"></a>Amazon Redshift
A Amazon Redshift suporta a encriptação do cluster para os dados em repouso. Para obter mais informações, consulte [a Encriptação da Base de Dados Redshift da Amazon.](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta encriptação da plataforma Shield que permite encriptação de todos os ficheiros, anexos e campos personalizados. Para obter mais informações, consulte [compreender o fluxo de autenticação de autenticação OAuth do servidor web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Cenários híbridos
Os cenários híbridos requerem que o tempo de integração auto-hospedado seja instalado numa rede no local, dentro de uma rede virtual (Azure), ou dentro de uma nuvem privada virtual (Amazon). O tempo de integração auto-acolôdo deve ser capaz de aceder às lojas de dados locais. Para obter mais informações sobre o tempo de integração auto-hospedado, consulte [Como criar e configurar o tempo de integração auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![canais de execução de integração auto-hospedados](media/data-movement-security-considerations/data-management-gateway-channels.png)

O canal de comando permite a comunicação entre os serviços de movimento de dados na Data Factory e o tempo de integração auto-hospedado. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para a transferência de dados entre lojas de dados no local e lojas de dados em nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de loja de dados no local
As credenciais podem ser armazenadas dentro da fábrica de dados ou ser [referenciadas pela fábrica](store-credentials-in-key-vault.md) de dados durante o tempo de funcionamento do Azure Key Vault. Se armazenar credenciais dentro da fábrica de dados, é sempre armazenado encriptado no tempo de integração auto-hospedado. 
 
- **Armazenar credenciais localmente.** Se utilizar diretamente o cmdlet **Set-AzDataFactoryV2LinkedService** com as cordas de ligação e credenciais em linha no JSON, o serviço ligado é encriptado e armazenado em tempo de integração auto-hospedado.  Neste caso, as credenciais fluem através do serviço de backend Azure, que é extremamente seguro, para a máquina de integração auto-hospedada onde é finalmente encriptada e armazenada. O tempo de integração auto-hospedado utiliza o [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows para encriptar os dados sensíveis e informações credenciais.

- **Armazenar credenciais no Cofre da Chave Azure**. Também pode armazenar a credencial da loja de dados no [Cofre da Chave Azure.](https://azure.microsoft.com/services/key-vault/) A Data Factory recupera a credencial durante a execução de uma atividade. Para obter mais informações, consulte [a credencial da Loja no Cofre da Chave Azure.](store-credentials-in-key-vault.md)

- **Armazenar credenciais localmente sem fluir as credenciais através do backend de Azure para o tempo de integração auto-hospedado**. Se pretender encriptar e armazenar credenciais localmente no tempo de integração auto-hospedado sem ter de fluir as credenciais através do backend da fábrica de dados, siga os passos nas [credenciais de Encriptação para lojas de dados no local na Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores suportam esta opção. O tempo de integração auto-hospedado utiliza o [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows para encriptar os dados sensíveis e informações credenciais. 

   Utilize o **cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** para encriptar credenciais de serviço ligadas e detalhes sensíveis no serviço ligado. Em seguida, pode utilizar o JSON devolvido (com o elemento **CriptografadoCredential** na cadeia de ligação) para criar um serviço ligado utilizando o **cmdlet Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas utilizadas ao encriptar o serviço ligado no tempo de integração auto-hospedado
Por predefinição, a PowerShell utiliza a porta 8060 na máquina com tempo de integração auto-hospedado para uma comunicação segura. Se necessário, esta porta pode ser mudada.  

![Porta HTTPS para o gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Todas as transferências de dados são através do canal seguro HTTPS e TLS sobre TCP para evitar ataques man-in-the-middle durante a comunicação com os serviços Azure.

Também pode utilizar [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Azure ExpressRoute](../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a sua rede no local e a Azure.

A Azure Virtual Network é uma representação lógica da sua rede na nuvem. Pode ligar uma rede no local à sua rede virtual, configurando o IPSec VPN (site-to-site) ou o ExpressRoute (peering privado).    

A tabela seguinte resume as recomendações de configuração de tempo de integração de rede e auto-hospedadas com base em diferentes combinações de locais de origem e destino para movimento de dados híbridos.

| Origem      | Destino                              | Configuração de rede                    | Configuração do runtime de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| No local | Máquinas virtuais e serviços em nuvem implantados em redes virtuais | IPSec VPN (ponto a local ou site-a-site) | O tempo de integração auto-hospedado deve ser instalado numa máquina virtual Azure na rede virtual.  |
| No local | Máquinas virtuais e serviços em nuvem implantados em redes virtuais | ExpressRoute (peering privado)           | O tempo de integração auto-hospedado deve ser instalado numa máquina virtual Azure na rede virtual.  |
| No local | Serviços baseados em Azure que têm um ponto final público | ExpressRoute (espreitar microsoft)            | O tempo de funcionamento de integração auto-hospedado pode ser instalado no local ou numa máquina virtual Azure. |

As imagens que se seguem mostram a utilização do tempo de integração auto-hospedado para mover dados entre uma base de dados no local e serviços Azure utilizando o ExpressRoute e o IPSec VPN (com a Rede Virtual Azure):

**ExpressRoute**

![Use ExpressRoute com gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN com gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a> Configurações de firewall e permitir configuração de lista para endereços IP

> [!NOTE] 
> Poderá ter de gerir portas ou configurar a lista de autorizações para domínios ao nível da firewall corporativa, conforme exigido pelas respetivas fontes de dados. Esta tabela utiliza apenas a Base de Dados Azure SQL, Azure Synapse Analytics e Azure Data Lake Store como exemplos.

> [!NOTE] 
> Para mais informações sobre estratégias de acesso a dados através da Azure Data Factory, consulte [este artigo.](https://docs.microsoft.com/azure/data-factory/data-access-strategies#data-access-strategies-through-azure-data-factory)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para as redes no local/rede privada    
Numa empresa, uma firewall corporativa funciona no router central da organização. O Windows Firewall funciona como um daemon na máquina local em que o tempo de funcionação de integração auto-hospedado é instalado. 

O quadro que se segue fornece requisitos de porta e domínio de saída para firewalls corporativos:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Poderá ter de gerir portas ou configurar a lista de autorizações para domínios ao nível da firewall corporativa, conforme exigido pelas respetivas fontes de dados. Esta tabela utiliza apenas a Base de Dados Azure SQL, Azure Synapse Analytics e Azure Data Lake Store como exemplos.   

O quadro seguinte fornece requisitos de porta de entrada para o Windows Firewall:

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Exigido pelo cmdlet de encriptação PowerShell, tal como descrito nas [credenciais de encriptação para lojas de dados no local na Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), e pela aplicação do gestor credencial para definir de forma segura credenciais para lojas de dados no local sobre o tempo de integração auto-hospedado. |

![Requisitos da porta gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Configurações IP e permitir configuração de listas em lojas de dados
Algumas lojas de dados na nuvem também exigem que você permita o endereço IP da máquina que acede à loja. Certifique-se de que o endereço IP da máquina de execução de integração auto-hospedada é permitido ou configurado na firewall adequadamente.

As seguintes lojas de dados em nuvem requerem que você permita o endereço IP da máquina de execução de integração auto-hospedada. Algumas destas lojas de dados, por padrão, podem não necessitar de uma lista de autorizações. 

- [Base de Dados SQL do Azure](../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O tempo de integração auto-organizado pode ser partilhado em diferentes fábricas de dados?**

Sim. Mais detalhes [aqui.](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)

**Quais são os requisitos portuários para o tempo de funcionamento da integração auto-acolhida?**

O tempo de integração auto-hospedado faz ligações baseadas em HTTP para aceder à internet. As portas de saída 443 devem ser abertas para o tempo de integração auto-hospedado para fazer esta ligação. Abra a porta de entrada 8060 apenas ao nível da máquina (não ao nível de firewall corporativa) para aplicação de gestor credencial. Se a Base de Dados Azure SQL ou a Azure Synapse Analytics forem usadas como fonte ou destino, também precisa de abrir a porta 1433. Para obter mais informações, consulte as configurações de [Firewall e permita a configuração da lista para a secção de endereços IP.](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 


## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho da Atividade de Cópia da Fábrica de Dados Azure, consulte [o desempenho da Atividade de Cópia e o guia de afinação](copy-activity-performance.md).

 
