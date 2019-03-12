---
title: Considerações de segurança para movimento de dados no Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como proteger o movimento de dados no Azure Data Factory.
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
ms.openlocfilehash: 083770c24a6c8939f8d1ff9f0efd5d18aff9dcb0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539620"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>O Azure Data Factory - considerações de segurança para movimento de dados

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [considerações de segurança de movimento de dados do Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura de segurança básica que serviços de movimento de dados no Azure Data Factory utilizam para proteger os dados. Recursos de gerenciamento de fábrica de dados do Azure são criados com a infraestrutura de segurança do Azure e utilizam todas as medidas de segurança possível oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](data-factory-create-pipelines.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Esses pipelines residem na região em que foi criada a fábrica de dados. 

Apesar do Data Factory estar disponível apenas nas **E.U.A. oeste**, **E.U.A. Leste**, e **Europa do Norte** regiões, o serviço de movimento de dados está disponível [globalmente no várias regiões](data-factory-data-movement-activities.md#global). Serviço do Data Factory garante que os dados não saem uma área geográfica / região, a menos que instruir explicitamente o serviço para utilizar uma região alternativa, se o serviço de movimento de dados ainda não foi implementado nessa região. 

O Azure Data Factory propriamente dito não armazena quaisquer dados, exceto para as credenciais do serviço ligado para arquivos de dados de cloud, que são encriptados utilizando certificados. Permite-lhe criar fluxos de trabalho condicionados por dados para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

Movimento de dados com o Azure Data Factory tem sido **certified** para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se estiver interessado em conformidade do Azure e como o Azure protege a sua própria infra-estrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Neste artigo, vamos rever considerações de segurança nos seguintes cenários de movimento de dados de dois: 

- **Cenário de nuvem**-neste cenário, a origem e de destino são publicamente acessíveis através da internet. Estes incluem os serviços de armazenamento gerida na cloud, como o armazenamento do Azure, Azure SQL Data Warehouse, SQL Database do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como o Salesforce e protocolos de web, como o FTP e OData. Pode encontrar uma lista completa das origens de dados suportadas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Cenário híbrido**- neste cenário, a origem ou de destino é protegido por uma firewall ou dentro de uma rede empresarial no local ou os dados de arquivo está numa rede privada / virtual de rede (com mais frequência a origem) e não está acessível ao público. Servidores de base de dados hospedados em máquinas virtuais também abrangidos por este cenário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvem
### <a name="securing-data-store-credentials"></a>A proteção dos dados armazena credenciais
O Azure Data Factory protege as credenciais de arquivo de dados por **encriptar** -los utilizando **certificados geridos pelo Microsoft**. Estes certificados são revezados cada **dois anos** (que inclui a renovação do certificado e a migração de credenciais). Estas credenciais encriptadas são armazenadas em segurança num **armazenamento do Azure geridas por serviços de gestão do Azure Data Factory**. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [descrição geral de segurança do armazenamento do Azure](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se o arquivo de dados na cloud suporta HTTPS ou TLS, todos os dados transferidos entre os serviços de movimento de dados no Data Factory e um arquivo de dados na cloud são através do canal seguro, HTTPS ou TLS.

> [!NOTE]
> Todas as ligações ao **Azure SQL Database** e **Azure SQL Data Warehouse** sempre exigir encriptação (SSL/TLS), enquanto os dados se encontram em trânsito para e da base de dados. Criação de um pipeline com um editor de JSON, adicione o **encriptação** propriedade e defini-lo como **true** no **cadeia de ligação**. Quando utiliza a [Assistente para copiar](data-factory-azure-copy-wizard.md), o assistente define esta propriedade, por predefinição. Para **armazenamento do Azure**, pode utilizar **HTTPS** na cadeia de ligação.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns dados armazena a encriptação de suporte de dados em repouso. Sugerimos que ative o mecanismo de criptografia de dados para esses arquivos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Encriptação de dados transparente (TDE) no armazém de dados SQL do Azure ajuda a proteger contra ameaças de atividades maliciosas através de encriptação em tempo real e a descriptografia dos seus dados em repouso. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger uma base de dados no SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Base de dados SQL do Azure também suporta a encriptação de dados transparente (TDE), que ajuda a proteger contra ameaças de atividades maliciosas através de encriptação em tempo real e a descriptografia dos dados sem a necessidade de alterações à aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação de dados transparente com a base de dados do Azure SQL](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store também fornece encriptação para dados armazenados na conta. Quando ativada, o Data Lake store é automaticamente encripta os dados antes de persistir e desencripta antes de recuperação, tornando-a transparente para o cliente a aceder aos dados. Para obter mais informações, consulte [segurança no Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure
Armazenamento do Azure, armazenamento de BLOBs e tabelas do Azure suporta Storage Service Encryption (SSE), que automaticamente encripta os dados antes de continuar a armazenar e desencriptar antes da obtenção. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 suporta a encriptação de cliente e servidor de dados em repouso. Para obter mais informações, consulte [proteger dados através de encriptação](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, o Data Factory não suporta Amazon S3 dentro de uma cloud privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift suporta a encriptação de cluster para dados inativos. Para obter mais informações, consulte [encriptação de base de dados do Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, o Data Factory não suporta o Amazon Redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta a encriptação de plataforma do escudo que permite que a encriptação de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [Noções básicas sobre o fluxo de autenticação do Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários híbridos (usando o Data Management Gateway)
Cenários híbridos exigem Data Management Gateway ser instalado numa rede no local ou dentro de uma rede virtual (Azure) ou uma cloud privada virtual (Amazon). O gateway tem de ser capaz de acessar os arquivos de dados local. Para obter mais informações sobre o gateway, consulte [Data Management Gateway](data-factory-data-management-gateway.md). 

![Canais de Gateway de gestão de dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre serviços de movimento de dados no Data Factory e o Data Management Gateway. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para transferir dados entre arquivos de dados no local e arquivos de dados na cloud.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de arquivo de dados no local
As credenciais para os arquivos de dados no local são armazenadas localmente (não na cloud). Eles podem ser definidos de três formas diferentes. 

- Usando **texto sem formatação** (menos seguro) através de HTTPS a partir do Portal do Azure / Assistente para copiar. As credenciais são transmitidas em texto simples para o gateway no local.
- Usando **biblioteca de criptografia de JavaScript com o Assistente de cópia de**.
- Usando **clique-uma vez com base na aplicação Gestor de credenciais**. O clique-assim que o aplicativo é executado na máquina no local que tenha acesso ao gateway e define as credenciais para o arquivo de dados. Esta opção e o seguinte são as opções mais seguras. A aplicação do Gestor de credenciais, por predefinição, utiliza a porta 8050 na máquina com o gateway para uma comunicação segura.  
- Uso [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) cmdlet do PowerShell para encriptar as credenciais. O cmdlet utiliza o certificado que esse gateway é configurado para utilizar para encriptar as credenciais. Pode utilizar as credenciais encriptadas devolvidas por este cmdlet e adicioná-lo ao **EncryptedCredential** elemento da **connectionString** no ficheiro JSON que utiliza com o [ Novo AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet ou no fragmento JSON no Editor do Data Factory no portal. Esta opção e um simples clique-assim que a aplicação são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Encriptação de baseada na biblioteca de criptografia em JavaScript
Pode encriptar credenciais de arquivo de dados usando [biblioteca de JavaScript criptografia](https://www.microsoft.com/download/details.aspx?id=52439) da [Assistente de cópia](data-factory-copy-wizard.md). Quando seleciona esta opção, o Assistente para copiar obtém a chave pública do gateway e utiliza-o para encriptar as credenciais de arquivo de dados. As credenciais são desencriptadas pela máquina de gateway e protegidas pelo Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Browsers suportados:** IE8, IE9, IE10, IE11, Microsoft Edge e mais recente Firefox, Chrome, Opera, navegadores Safari. 

#### <a name="click-once-credentials-manager-app"></a>Clique em-uma vez na aplicação Gestor de credenciais
Pode iniciar o clique-uma vez com a base de aplicação do Gestor de credenciais do portal do Azure/copiar assistente durante a criação de pipelines. Esse aplicativo garante que as credenciais não são transferidas em texto simples durante a transmissão. Por predefinição, utiliza a porta **8050** na máquina com o gateway para uma comunicação segura. Se necessário, esta porta pode ser alterada.  
  
![Porta HTTPS para o gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, o Data Management Gateway utiliza um único **certificado**. Este certificado é criado durante a instalação do gateway (aplica-se a Data Management Gateway criada depois de Novembro de 2016 e versão 2.4.xxxx.x ou posterior). Pode substituir este certificado com o seu próprio certificado SSL/TLS. Este certificado é utilizado por clique-uma vez a aplicação para ligar em segurança para a máquina de gateway para a definição de credenciais de arquivo de dados do Gestor de credenciais. Armazena os dados credenciais de arquivo de forma segura no local utilizando o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na máquina com o gateway. 

> [!NOTE]
> Os gateways mais antigos que foram instalados antes de Novembro de 2016 ou de versão 2.3.xxxx.x continuam a utilizar as credenciais encriptadas e armazenadas na cloud. Mesmo que Atualize o gateway para a versão mais recente, as credenciais não são migradas para uma máquina no local    
  
| Versão do gateway (durante a criação) | Credenciais armazenadas | Encriptação de credenciais / segurança | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na cloud | Encriptados com o certificado (diferente da utilizada pela aplicação do Gestor de credenciais) | 
| > = 2.4.xxxx.x | No local | Protegido por DPAPI | 
  

### <a name="encryption-in-transit"></a>Encriptação em trânsito
Todas as transferências de dados são através do canal seguro **HTTPS** e **TLS através de TCP** para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.
 
Também pode utilizar [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Express Route](../../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a rede no local e o Azure.

Rede virtual é uma representação lógica da sua rede na cloud. Pode ligar uma rede no local à sua rede virtual do Azure (VNet) ao configurar a VPN IPSec (site a site) ou Express Route (Peering privado)     

A tabela seguinte resume as recomendações de configuração de rede e um gateway com base em diferentes combinações de localizações de origem e destino para movimento de dados híbrida.

| Origem | Destino | Configuração da rede | Configuração do gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | VPN IPSec (point-to-site ou site a site) | Gateway pode ser instalado no local ou numa Azure virtual machine (VM) na VNet | 
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | ExpressRoute (Peering privado) | Gateway pode ser instalado no local ou numa VM do Azure na VNet | 
| Local | Serviços baseados no Azure que tem um ponto final público | ExpressRoute (Peering público) | Gateway tem de ser instalado no local | 

As seguintes imagens mostram o uso do Gateway de gestão de dados para mover dados entre uma base de dados no local e serviços do Azure com o expressroute e VPN IPSec (com a rede Virtual):

**Expressroute:**
 
![Utilizar o Expressroute com o gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![VPN IPSec com o gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e o endereço IP da lista de permissões do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede no local/privada  
Numa empresa, uma **firewall Corporativo** é executado no router central da organização. E, **firewall do Windows** é executado como um daemon no computador local no qual o gateway está instalado. 

A tabela seguinte fornece **porta de saída** e os requisitos de domínio para o **firewall Corporativo**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Exigidos pelo gateway para ligar aos serviços de movimento de dados no Data Factory |
| `*.core.windows.net` | 443 | Utilizado pelo gateway para ligar à conta de armazenamento do Azure ao utilizar o [cópia faseada](data-factory-copy-activity-performance.md#staged-copy) funcionalidade. | 
| `*.frontend.clouddatahub.net` | 443 | Exigidos pelo gateway para ligar ao serviço Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Opcional) necessárias quando o destino é a base de dados SQL do Azure / Azure SQL Data Warehouse. Utilize a funcionalidade de cópia faseada para copiar dados para o Azure SQL da base de dados/do Azure SQL Data Warehouse sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (Opcional) necessárias quando o destino é o Azure Data Lake store | 

> [!NOTE] 
> Poderá ter de gerir as portas / domínios de listas de permissões do firewall Corporativo de nível conforme exigido por origens de dados correspondentes. Esta tabela só utiliza a base de dados do Azure SQL, o Azure SQL Data Warehouse, o Azure Data Lake Store como exemplos.   

A tabela seguinte fornece **porta de entrada** requisitos para o **firewall do windows**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Necessário para a aplicação do Gestor de credenciais com segurança definir credenciais para arquivos de dados no local no gateway. | 

![Requisitos de porta de gateway](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Configurações de IP / armazenar a lista de permissões nos dados
Alguns arquivos de dados na cloud também exigem a lista de permissões do endereço IP da máquina acesso aos mesmos. Certifique-se de que o endereço IP do computador gateway está na lista de permissões / configuradas na firewall adequadamente.

Os seguintes arquivos de dados de nuvem necessitam de listas de permissões do endereço IP do computador do gateway. Alguns desses arquivos de dados, por predefinição, não, requerer a lista de permissões do endereço IP. 

- [Base de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta:** O Gateway pode ser compartilhado entre as fábricas de dados diferentes?
**Resposta:** Podemos ainda não suporta esta funcionalidade. Estamos a trabalhar ativamente no mesmo.

**Pergunta:** Quais são os requisitos de porta para o gateway funcionar?
**Resposta:** Gateway faz conexões baseadas em HTTP para a internet aberta. O **portas de saída 443 e 80** tem de ser aberto para o gateway de fazer essa conexão. Open **8050 de porta de entrada** apenas ao nível da máquina (não ao nível do firewall Corporativo) para a aplicação do Gestor de credenciais. Se a base de dados do Azure SQL ou armazém de dados SQL do Azure é utilizado como origem / destino, em seguida, precisa abrir **1433** porta também. Para obter mais informações, consulte [endereços IP da lista de permissões e configurações de Firewall](#firewall-configurations-and-whitelisting-ip-address-of gateway) secção. 

**Pergunta:** Quais são os requisitos de certificado para o Gateway?
**Resposta:** Gateway atual requer um certificado que é utilizado pela aplicação do Gestor de credenciais para a definição de forma segura as credenciais do arquivo de dados. Este certificado é um certificado autoassinado criado e configurado, o programa de configuração do gateway. Pode utilizar o seu próprio TLS / SSL de certificado em vez disso. Para obter mais informações, consulte [clique-uma vez a aplicação do Gestor de credenciais](#click-once-credentials-manager-app) secção. 

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o desempenho de atividade de cópia, consulte [copie o guia de sintonização de desempenho de atividade e](data-factory-copy-activity-performance.md).

 
