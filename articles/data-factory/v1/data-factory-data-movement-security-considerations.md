---
title: Considerações de segurança para o movimento de dados na Fábrica de Dados Azure
description: Saiba mais sobre a segurança do movimento de dados na Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80130827"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - Considerações de segurança para o movimento de dados

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as considerações de segurança](../data-movement-security-considerations.md)do movimento de dados para data Factory .

## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura básica de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para proteger os seus dados. Os recursos de gestão da Azure Data Factory são construídos em infraestruturas de segurança Azure e utilizam todas as medidas de segurança possíveis oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](data-factory-create-pipelines.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Estes gasodutos residem na região onde foi criada a fábrica de dados. 

Embora a Data Factory esteja disponível apenas nas regiões **dos EUA Ocidentais**, **Leste dos EUA**e norte da **Europa,** o serviço de movimento de dados está disponível [globalmente em várias regiões.](data-factory-data-movement-activities.md#global) O serviço Data Factory garante que os dados não saem de uma área geográfica/região a menos que instrua explicitamente o serviço a utilizar uma região alternativa se o serviço de movimento de dados ainda não estiver implantado naquela região. 

A própria Azure Data Factory não armazena quaisquer dados, exceto credenciais de serviço ligadas para lojas de dados em nuvem, que são encriptadas utilizando certificados. Permite criar fluxos de trabalho baseados em dados para orquestrar a circulação de dados entre lojas de [dados suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados utilizando [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos](data-factory-monitor-manage-pipelines.md) de trabalho utilizando mecanismos programáticos e ui.

O movimento de dados utilizando a Azure Data Factory foi **certificado** para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se está interessado na conformidade com o Azure e como o Azure assegura a sua própria infraestrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Neste artigo, revemos considerações de segurança nos seguintes dois cenários de movimento de dados: 

- **Cenário cloud**- Neste cenário, tanto a sua origem como o seu destino são acessíveis ao público através da internet. Estes incluem serviços geridos de armazenamento em nuvem como O Armazenamento Azure, Armazém de Dados Azure SQL, Base de Dados Azure SQL, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce, e protocolos web como FTP e OData. Pode encontrar [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats)uma lista completa de fontes de dados suportadas .
- **Cenário híbrido**- Neste cenário, ou a sua fonte ou destino está por detrás de uma firewall ou dentro de uma rede corporativa no local ou a loja de dados está numa rede privada/ rede virtual (na maioria das vezes a fonte) e não é acessível ao público. Os servidores de base de dados alojados em máquinas virtuais também se enquadram neste cenário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvens
### <a name="securing-data-store-credentials"></a>Garantir credenciais de loja de dados
A Azure Data Factory protege as credenciais da sua loja de dados **encriptando-as** utilizando **certificados geridos pela Microsoft**. Estes certificados são rotativos de **dois em dois anos** (o que inclui renovação do certificado e migração de credenciais). Estas credenciais encriptadas são armazenadas de forma segura num **Armazenamento Azure gerido pelos serviços de gestão da Azure Data Factory.** Para mais informações sobre a segurança do Armazenamento Azure, consulte a visão geral da [Segurança do Armazenamento do Azure](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se a loja de dados na nuvem suportar HTTPS ou TLS, todas as transferências de dados entre os serviços de movimento de dados na Data Factory e uma loja de dados na nuvem são via canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as ligações à Base de **Dados Azure SQL** e ao **Azure SQL Data Warehouse** requerem sempre encriptação (SSL/TLS) enquanto os dados estão em trânsito de e para a base de dados. Enquanto é autora de um pipeline utilizando um editor DaJSON, adicione a propriedade de **encriptação** e detetete-a como **verdadeira** na cadeia de **ligação**. Quando utiliza o [Assistente de Cópia,](data-factory-azure-copy-wizard.md)o assistente define esta propriedade por defeito. Para **armazenamento Azure,** pode utilizar **HTTPS** na cadeia de ligação.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Algumas lojas de dados suportam encriptação de dados em repouso. Sugerimos que ative o mecanismo de encriptação de dados para essas lojas de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
A Encriptação transparente de dados (TDE) no Azure SQL Data Warehouse ajuda a proteger contra a ameaça de atividades maliciosas através da encriptação e desencriptação dos seus dados em tempo real. Este comportamento é transparente para o cliente. Para mais informações, consulte [Secure a database in SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
A Azure SQL Database também suporta encriptação transparente de dados (TDE), o que ajuda a proteger contra a ameaça de atividades maliciosas através da encriptação e desencriptação em tempo real dos dados sem exigir alterações na aplicação. Este comportamento é transparente para o cliente. Para mais informações, consulte encriptação transparente de dados com base de [dados Azure SQL](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A loja Azure Data Lake também fornece encriptação para dados armazenados na conta. Quando ativado, a loja Data Lake encripta automaticamente os dados antes de persistir e desencripta-se antes de recuperar, tornando-os transparentes para o cliente aceder aos dados. Para mais informações, consulte [a Segurança na Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de blob azure e armazenamento de mesa azul
O armazenamento de Blob Azure e o armazenamento da mesa Azure suportam encriptação do serviço de armazenamento (SSE), que encripta automaticamente os seus dados antes de persistir em armazenamento e desencriptação antes da recuperação. Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 suporta a encriptação de dados do cliente e do servidor em Rest. Para mais informações, consulte [Proteger dados usando encriptação](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, a Data Factory não suporta a Amazon S3 dentro de uma nuvem privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
A Amazon Redshift suporta a encriptação do cluster para dados em repouso. Para mais informações, consulte a Encriptação da Base de [Dados Redshift da Amazon](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, a Data Factory não suporta a Amazon Redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
A Salesforce suporta a encriptação da Plataforma Shield que permite a encriptação de todos os ficheiros, anexos, campos personalizados. Para mais informações, consulte [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários Híbridos (utilizando gateway de gestão de dados)
Os cenários híbridos exigem que o Portal de Gestão de Dados seja instalado numa rede no local ou dentro de uma rede virtual (Azure) ou numa nuvem privada virtual (Amazon). O portal deve poder aceder aos armazéns de dados locais. Para mais informações sobre o portal, consulte Data [Management Gateway](data-factory-data-management-gateway.md). 

![Canais Gateway de Gestão de Dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimento de dados na Data Factory e data Management Gateway. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para a transferência de dados entre lojas de dados no local e lojas de dados na nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de loja de dados no local
As credenciais para as suas lojas de dados no local são armazenadas localmente (não na nuvem). Podem ser definidos de três maneiras diferentes. 

- Utilizando **texto simples** (menos seguro) via HTTPS do Portal Do Azure/Assistente de Cópia. As credenciais são passadas em texto simples para o gateway no local.
- Utilizando **a biblioteca de criptografia JavaScript do Imitador Assistente**.
- Utilizando uma aplicação de **gestor de credenciais baseada em cliques.** A aplicação click-once executa na máquina no local que tem acesso ao gateway e define credenciais para o armazenamento de dados. Esta opção e a próxima são as opções mais seguras. A aplicação de gestor de credenciais, por padrão, utiliza a porta 8050 na máquina com porta de entrada para comunicação segura.  
- Utilize [o novo AzDataFactoryEncryptValueValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell cmdlet para encriptar credenciais. O cmdlet utiliza o certificado que o gateway está configurado para ser utilizado para encriptar as credenciais. Pode utilizar as credenciais encriptadas devolvidas por este cmdlet e adicioná-la ao elemento **EncryptedCredential** da **ligaçãoString** no ficheiro JSON que utiliza com o cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no snippet JSON no Data Factory Editor no portal. Esta opção e a aplicação click-once são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Encriptação baseada na biblioteca da criptografia JavaScript
Pode encriptar credenciais de loja de dados utilizando [a biblioteca de criptografia JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) a partir do [Copy Wizard](data-factory-copy-wizard.md). Ao selecionar esta opção, o Copy Wizard recupera a chave pública do gateway e utiliza-a para encriptar as credenciais da loja de dados. As credenciais são desencriptadas pela máquina gateway e protegidas pelo Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Navegadores suportados:** IE8, IE9, IE10, IE11, Microsoft Edge e os mais recentes navegadores Firefox, Chrome, Opera, Safari. 

#### <a name="click-once-credentials-manager-app"></a>Clique-uma vez credenciais manager app
Pode lançar a aplicação de gestor de credenciais baseada em cliques a partir do portal Azure/Copy Wizard ao autorizar os gasodutos. Esta aplicação garante que as credenciais não são transferidas em texto simples por cima do fio. Por defeito, utiliza a porta **8050** na máquina com porta de entrada para uma comunicação segura. Se necessário, esta porta pode ser alterada.  
  
![Porta HTTPS para o gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, o Portal de Gestão de Dados utiliza um único **certificado.** Este certificado é criado durante a instalação do gateway (aplica-se ao Data Management Gateway criado após novembro de 2016 e versão 2.4.xxxx.x ou posterior). Pode substituir este certificado pelo seu próprio certificado SSL/TLS. Este certificado é utilizado pela aplicação do gestor da credencial click-once para ligar de forma segura à máquina gateway para definir credenciais de loja de dados. Armazena credenciais de loja de dados de forma segura no local, utilizando o [Windows DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na máquina com gateway. 

> [!NOTE]
> Os portões mais antigos que foram instalados antes de novembro de 2016 ou da versão 2.3.xxxx.x continuam a usar credenciais encriptadas e armazenadas na nuvem. Mesmo que atualize a porta de entrada para a versão mais recente, as credenciais não são migradas para uma máquina no local    
  
| Versão gateway (durante a criação) | Credenciais Armazenadas | Encriptação/segurança credencial | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na nuvem | Encriptado usando certificado (diferente do usado pela aplicação Credential manager) | 
| > = 2.4.xxxx.x | Nas instalações | Seguro via DPAPI | 
  

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Todas as transferências de dados são via canal seguro **HTTPS** e **TLS através** da TCP para evitar ataques man-in-the-middle durante a comunicação com os serviços Azure.
 
Também pode utilizar [o IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [a Express Route](../../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a sua rede no local e o Azure.

A rede virtual é uma representação lógica da sua rede na nuvem. Pode ligar uma rede no local à sua rede virtual Azure (VNet) através da criação de VPN IPSec (site-a-site) ou Express Route (Private Peering)     

O quadro seguinte resume as recomendações de configuração da rede e gateway com base em diferentes combinações de locais de origem e destino para o movimento de dados híbridos.

| Origem | Destino | Configuração da rede | Configuração gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPSec (ponto-a-local ou site-a-site) | Gateway pode ser instalado no local ou numa máquina virtual Azure (VM) em VNet | 
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (Private Peering) | Gateway pode ser instalado no local ou em um VM Azure em VNet | 
| Local | Serviços baseados em Azure que têm um ponto final público | Rota expressa (Público) | Gateway deve ser instalado no local | 

As seguintes imagens mostram a utilização do Portal de Gestão de Dados para movimentar dados entre uma base de dados no local e os serviços Azure utilizando a rota Express e a VPN IPSec (com Rede Virtual):

**Rota expressa:**
 
![Utilizar rota expressa com porta de entrada](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**VPN IPSec:**

![VPN IPSec com porta de entrada](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e endereço IP de listagem branca do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede privada/local  
Numa empresa, uma **firewall corporativa** funciona no router central da organização. E, a **firewall do Windows** funciona como um daemon na máquina local em que o portal está instalado. 

A tabela seguinte fornece requisitos de porta de **saída** e de domínio para a **firewall corporativa**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Exigido pela porta de entrada para ligar aos serviços de movimento de dados na Fábrica de Dados |
| `*.core.windows.net` | 443 | Utilizado pelo portal para ligar à Conta de Armazenamento Azure quando utilizar a função de [cópia encenada.](data-factory-copy-activity-performance.md#staged-copy) | 
| `*.frontend.clouddatahub.net` | 443 | Exigido pela porta de entrada para ligar ao serviço Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (OPCIONAL) necessário quando o seu destino é Azure SQL Database/ Azure SQL Data Warehouse. Utilize a função de cópia encenou para copiar dados para a Base de Dados Azure SQL/Azure SQL Data Warehouse sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCIONAL) necessário quando o seu destino é loja Azure Data Lake | 

> [!NOTE] 
> Pode ter de gerir os domínios de listagem de portas/whitelisting ao nível da firewall corporativa, conforme exigido pelas respetivas fontes de dados. Esta tabela utiliza apenas a Base de Dados Azure SQL, o Azure SQL Data Warehouse, a Azure Data Lake Store como exemplos.   

A tabela que se segue fornece requisitos de **porta de entrada** para a firewall das **janelas**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Exigido pela aplicação do gestor de credenciais para definir de forma segura credenciais para as lojas de dados no local no gateway. | 

![Requisitos da porta gateway](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Configurações IP/whitelisting na loja de dados
Algumas lojas de dados na nuvem também requerem a lista branca do endereço IP da máquina que os acede. Certifique-se de que o endereço IP da máquina gateway está classificado de branco/configurado na firewall adequadamente.

As seguintes lojas de dados em nuvem requerem listagem branca do endereço IP da máquina gateway. Algumas destas lojas de dados, por defeito, podem não necessitar de whitelisting do endereço IP. 

- [Base de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta:** O Gateway pode ser partilhado em diferentes fábricas de dados?
**Resposta:** Ainda não apoiamos esta funcionalidade. Estamos a trabalhar ativamente para esse fim.

**Pergunta:** Quais são os requisitos portuários para que a porta de entrada funcione?
**Resposta:** Gateway faz conexões baseadas em HTTP para abrir internet. As portas de **saída 443 e 80** devem ser abertas para a porta de entrada para fazer esta ligação. Abrir **o Porto de Entrada 8050** apenas ao nível da máquina (não ao nível da firewall corporativa) para aplicação Credential Manager. Se a Base de Dados Azure SQL ou o Azure SQL Data Warehouse forem utilizados como fonte/destino, então também terá de abrir **1433** portas. Para mais informações, consulte as configurações da Firewall e a secção de [endereços IP de listagem branca.](#firewall-configurations-and-whitelisting-ip-address-of gateway) 

**Pergunta:** Quais são os requisitos de certificado para gateway?
**Resposta:** O gateway atual requer um certificado que é usado pelo pedido do gestor credencial para definir de forma segura credenciais de loja de dados. Este certificado é um certificado auto-assinado criado e configurado pela configuração do gateway. Em vez disso, pode utilizar o seu próprio certificado TLS/SSL. Para mais informações, consulte a secção de aplicação do gestor de [credenciais click-once.](#click-once-credentials-manager-app) 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho da atividade da cópia, consulte o desempenho da atividade da cópia e o guia de [afinação.](data-factory-copy-activity-performance.md)

 
