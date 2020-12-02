---
title: Considerações de segurança para o movimento de dados na Azure Data Factory
description: Saiba como garantir o movimento de dados na Azure Data Factory.
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
ms.openlocfilehash: e995cd8f300787a19934e9b9eeae1dea73e8576c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457083"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - Considerações de segurança para o movimento de dados

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte considerações de [segurança do movimento de dados para data factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura básica de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para proteger os seus dados. Os recursos de gestão da Azure Data Factory são construídos em infraestruturas de segurança Azure e utilizam todas as medidas de segurança possíveis oferecidas pela Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](data-factory-create-pipelines.md) de dados. Um pipeline é um agrupamento lógico de atividades que, em conjunto, executam uma tarefa. Estes oleodutos residem na região onde foi criada a fábrica de dados. 

Embora a Data Factory esteja disponível apenas nas regiões **oeste dos EUA**, Leste dos **EUA** e norte **da Europa,** o serviço de movimento de dados está disponível [globalmente em várias regiões.](data-factory-data-movement-activities.md#global) O serviço Data Factory garante que os dados não saem de uma área geográfica/região a menos que instrua explicitamente o serviço a utilizar uma região alternativa se o serviço de movimento de dados ainda não estiver implantado naquela região. 

A Azure Data Factory em si não armazena quaisquer dados, exceto para credenciais de serviço ligadas para lojas de dados em nuvem, que são encriptadas usando certificados. Permite criar fluxos de trabalho baseados em dados para orquestrar o movimento de dados entre lojas de [dados apoiadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados utilizando [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite monitorizar [e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) utilizando mecanismos programáticos e de UI.

O movimento de dados utilizando a Azure Data Factory foi **certificado** para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [ESTRELA CSA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se estiver interessado na conformidade com o Azure e como a Azure assegura a sua própria infraestrutura, visite o [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Neste artigo, analisamos considerações de segurança nos seguintes dois cenários de movimento de dados: 

- **Cenário em nuvem**- Neste cenário, tanto a sua fonte como o seu destino são acessíveis ao público através da Internet. Estes incluem serviços de armazenamento em nuvem geridos como Azure Storage, Azure Synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce, e protocolos web como FTP e OData. Pode encontrar uma lista completa de fontes de dados suportadas [aqui.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- **Cenário híbrido**- Neste cenário, quer a sua fonte ou destino esteja por trás de uma firewall ou dentro de uma rede corporativa no local ou a loja de dados está numa rede privada/rede virtual (na maioria das vezes a fonte) e não é acessível ao público. Os servidores de base de dados alojados em máquinas virtuais também se enquadram neste cenário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários em nuvem
### <a name="securing-data-store-credentials"></a>Garantir credenciais de armazenamento de dados
A Azure Data Factory protege as suas credenciais de loja de dados **encriptando-as** utilizando **certificados geridos pela Microsoft.** Estes certificados são rodados de **dois em dois anos** (que inclui renovação do certificado e migração de credenciais). Estas credenciais encriptadas são armazenadas de forma segura num **Azure Storage gerido pelos serviços de gestão da Azure Data Factory**. Para obter mais informações sobre a segurança do armazenamento Azure, consulte [a Visão Geral de Segurança do Armazenamento Azure](../../storage/blobs/security-recommendations.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se a loja de dados em nuvem suportar HTTPS ou TLS, todas as transferências de dados entre os serviços de movimento de dados na Data Factory e uma loja de dados em nuvem são através de um canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as ligações à **Base de Dados Azure SQL** e **à Azure Synapse Analytics** requerem sempre encriptação (SSL/TLS) enquanto os dados estão em trânsito de e para a base de dados. Enquanto autoriza um oleoduto usando um editor JSON, adicione a propriedade **de encriptação** e desacri-o para **ser verdadeiro** na cadeia **de ligação**. Quando utiliza o [Copy Wizard,](data-factory-azure-copy-wizard.md)o assistente define esta propriedade por predefinição. Para **o armazenamento Azure,** pode utilizar **HTTPS** na cadeia de ligação.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Algumas lojas de dados suportam encriptação de dados em repouso. Sugerimos que permita o mecanismo de encriptação de dados para essas lojas de dados. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
A Encriptação de Dados Transparente (TDE) no Azure Synapse Analytics ajuda a proteger contra a ameaça de atividade maliciosa através da encriptação e desencriptação em tempo real dos seus dados em repouso. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [Secure a database in Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
O Azure SQL Database também suporta encriptação de dados transparentes (TDE), que ajuda a proteger contra a ameaça de atividade maliciosa, realizando encriptação e desencriptação em tempo real dos dados sem exigir alterações à aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação de dados transparentes com base de dados Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A loja Azure Data Lake também fornece encriptação para dados armazenados na conta. Quando ativado, a loja Data Lake encripta automaticamente os dados antes de persistir e desencriptar antes da recuperação, tornando-os transparentes para o cliente que acede aos dados. Para mais informações, consulte [a Segurança na Azure Data Lake Store.](../../data-lake-store/data-lake-store-security-overview.md) 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento Azure Blob e Armazenamento de Mesa Azure
O armazenamento de Azure Blob Storage e Azure Table suporta a Encriptação do Serviço de Armazenamento (SSE), que encripta automaticamente os seus dados antes de persistir no armazenamento e desencripta antes da recuperação. Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso.](../../storage/common/storage-service-encryption.md)

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 suporta a encriptação de dados tanto do cliente como do servidor em Rest. Para obter mais informações, consulte [proteger dados usando encriptação.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html) Atualmente, a Data Factory não suporta o Amazon S3 dentro de uma nuvem privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
A Amazon Redshift suporta a encriptação do cluster para os dados em repouso. Para obter mais informações, consulte [a Encriptação da Base de Dados Redshift da Amazon.](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) Atualmente, a Data Factory não suporta a Amazon Redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta encriptação da plataforma Shield que permite encriptação de todos os ficheiros, anexos, campos personalizados. Para obter mais informações, consulte [compreender o fluxo de autenticação de autenticação OAuth do servidor web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários Híbridos (utilizando o Gateway de Gestão de Dados)
Os cenários híbridos exigem que o Data Management Gateway seja instalado numa rede no local ou dentro de uma rede virtual (Azure) ou numa nuvem privada virtual (Amazon). O portal deve poder aceder às lojas de dados locais. Para mais informações sobre o gateway, consulte [o Data Management Gateway](data-factory-data-management-gateway.md). 

![Canais Gateway de Gestão de Dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimento de dados na Data Factory e na Data Management Gateway. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para a transferência de dados entre lojas de dados no local e lojas de dados em nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais de loja de dados no local
As credenciais para as suas lojas de dados no local são armazenadas localmente (não na nuvem). Podem ser definidos de três maneiras diferentes. 

- Utilizando **texto simples** (menos seguro) via HTTPS do Portal Azure/ Copy Wizard. As credenciais são passadas em texto simples para o portal no local.
- Utilização da **biblioteca de criptografia JavaScript do Copy Wizard**.
- Utilizando **a aplicação do gestor de credenciais baseada em cliques.** A aplicação click-once executa na máquina no local que tem acesso ao gateway e define credenciais para a loja de dados. Esta opção e a próxima são as opções mais seguras. A aplicação de gestor de credenciais, por padrão, utiliza a porta 8050 na máquina com porta de entrada para uma comunicação segura.  
- Utilize [o cmdlet PowerShell new-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) para encriptar credenciais. O cmdlet usa o certificado que gateway está configurado para usar para encriptar as credenciais. Pode utilizar as credenciais encriptadas devolvidas por este cmdlet e adicioná-la ao elemento **Criptografado** da **ligaçãoStragem** no ficheiro JSON que utiliza com o cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no snippet JSON no Editor de Fábrica de Dados no portal. Esta opção e a aplicação click-once são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Encriptação baseada em criptografia JavaScript
Pode encriptar credenciais de loja de dados utilizando a [biblioteca de criptografia JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) a partir do [Copy Wizard](data-factory-copy-wizard.md). Ao selecionar esta opção, o Copy Wizard recupera a chave pública do gateway e utiliza-a para encriptar as credenciais da loja de dados. As credenciais são desencriptadas pela máquina de gateway e protegidas pelo Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)).

**Navegadores suportados:** IE8, IE9, IE10, IE11, Microsoft Edge e os mais recentes navegadores Firefox, Chrome, Opera, Safari. 

#### <a name="click-once-credentials-manager-app"></a>Clique uma vez na aplicação do gestor de credenciais
Pode lançar a aplicação de gestor credencial baseada em cliques a partir do portal Azure/Copy Wizard ao autorizar pipelines. Esta aplicação garante que as credenciais não são transferidas em texto simples por cima do fio. Por predefinição, utiliza a porta **8050** na máquina com porta de entrada para uma comunicação segura. Se necessário, esta porta pode ser mudada.  
  
![Porta HTTPS para o gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, o Data Management Gateway utiliza um único **certificado.** Este certificado é criado durante a instalação gateway (aplica-se ao Data Management Gateway criado após novembro de 2016 e à versão 2.4.xxxx.x ou posterior). Pode substituir este certificado pelo seu próprio certificado SSL/TLS. Este certificado é utilizado pela aplicação do gestor credencial click-once para ligar-se de forma segura à máquina de gateway para definir credenciais de loja de dados. Armazena as credenciais de armazenamento de dados de forma segura no local, utilizando o [DPAPI](/previous-versions/ms995355(v=msdn.10)) do Windows na máquina com gateway. 

> [!NOTE]
> Gateways mais antigos que foram instalados antes de novembro de 2016 ou da versão 2.3.xxxx.x continuam a usar credenciais encriptadas e armazenadas na nuvem. Mesmo que atualize a porta de entrada para a versão mais recente, as credenciais não são migradas para uma máquina no local    
  
| Versão Gateway (durante a criação) | Credenciais Armazenadas | Encriptação credencial/ segurança | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na nuvem | Encriptado usando certificado (diferente do usado pela app De gestor credencial) | 
| > = 2.4.xxxx.x | No local | Garantido via DPAPI | 
  

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Todas as transferências de dados são através do canal seguro **HTTPS** e **TLS sobre TCP** para evitar ataques man-in-the-middle durante a comunicação com os serviços Azure.
 
Também pode utilizar [o IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [a Rota Expressa](../../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a sua rede no local e o Azure.

A rede virtual é uma representação lógica da sua rede na nuvem. Pode ligar uma rede no local à sua rede virtual Azure (VNet) configurando IPSec VPN (site-to-site) ou Rota Expressa (Private Peering)     

A tabela seguinte resume as recomendações de configuração da rede e gateway com base em diferentes combinações de origem e locais de destino para movimento de dados híbridos.

| Origem | Destino | Configuração de rede | Configuração gateway |
| ------ | ----------- | --------------------- | ------------- | 
| No local | Máquinas virtuais e serviços em nuvem implantados em redes virtuais | IPSec VPN (ponto a local ou site-a-site) | Gateway pode ser instalado no local ou numa máquina virtual Azure (VM) em VNet | 
| No local | Máquinas virtuais e serviços em nuvem implantados em redes virtuais | ExpressRoute (Peering Privado) | Gateway pode ser instalado no local ou em um VM Azure em VNet | 
| No local | Serviços baseados em Azure que têm um ponto final público | ExpressRoute (Espreitamento Público) | Gateway deve ser instalado no local | 

As imagens que se seguem mostram a utilização do Data Management Gateway para mover dados entre uma base de dados no local e serviços Azure utilizando a rota Express e o IPSec VPN (com rede virtual):

**Rota Expressa:**
 
![Use a Rota Expressa com gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN com gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Configurações de firewall e endereço IP filtrante do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para as redes no local/rede privada  
Numa empresa, uma **firewall corporativa** funciona no router central da organização. E a **firewall do Windows** funciona como um daemon na máquina local na qual o portal está instalado. 

A tabela seguinte fornece requisitos **de porta** e domínio de saída para a **firewall corporativa**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Requerido pela porta de entrada para se ligar aos serviços de movimento de dados na Data Factory |
| `*.core.windows.net` | 443 | Utilizado pelo portal para ligar à Conta de Armazenamento Azure quando utilizar a [função de cópia encenada.](data-factory-copy-activity-performance.md#staged-copy) | 
| `*.frontend.clouddatahub.net` | 443 | Requerido pelo gateway para ligar ao serviço Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (OPCIONAL) necessário quando o seu destino é Azure SQL Database/ Azure Synapse Analytics. Utilize a função de cópia encenada para copiar dados para Azure SQL Database/Azure Synapse Analytics sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCIONAL) necessário quando o seu destino é a loja Azure Data Lake | 

> [!NOTE] 
> Poderá ter de gerir portas/domínios de filtragem ao nível da firewall corporativa, conforme exigido pelas respetivas fontes de dados. Esta tabela utiliza apenas a Base de Dados Azure SQL, Azure Synapse Analytics, Azure Data Lake Store como exemplos.   

A tabela seguinte fornece requisitos de **porta de entrada** para a firewall do **windows**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Exigido pela aplicação do gestor de credenciais para definir de forma segura credenciais para lojas de dados no local no gateway. | 

![Requisitos da porta gateway](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>Configurações/filtragem ip na loja de dados
Algumas lojas de dados na nuvem também requerem a aprovação do endereço IP da máquina que os acede. Certifique-se de que o endereço IP da máquina de gateway é aprovado/configurado na firewall adequadamente.

As seguintes lojas de dados em nuvem requerem a aprovação do endereço IP da máquina de gateway. Algumas destas lojas de dados, por padrão, podem não exigir a aprovação do endereço IP. 

- [Base de Dados SQL do Azure](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta:** O Gateway pode ser partilhado em diferentes fábricas de dados?
**Resposta:** Ainda não apoiamos esta funcionalidade. Estamos a trabalhar ativamente para esse fim.

**Pergunta:** Quais são os requisitos portuários para a porta de entrada para o trabalho?
**Resposta:** Gateway faz ligações baseadas em HTTP para abrir internet. As **portas de saída 443 e 80** devem ser abertas para porta de entrada para estoiá-lo. Abra **o Porto de Entrada 8050** apenas ao nível da máquina (não ao nível da firewall corporativa) para a aplicação Do Gestor Credencial. Se a Base de Dados Azure SQL ou a Azure Synapse Analytics forem usadas como fonte/destino, então também precisa abrir a porta **1433.** Para obter mais informações, consulte as configurações de Firewall e a secção [de endereços IP de filtragem.](#firewall-configurations-and-filtering-ip-address-of gateway) 

**Pergunta:** Quais são os requisitos de certificado para gateway?
**Resposta:** O gateway atual requer um certificado que é usado pela aplicação do gestor credencial para definir de forma segura credenciais de armazenamento de dados. Este certificado é um certificado auto-assinado criado e configurado pela configuração do gateway. Em vez disso, pode utilizar o seu próprio certificado TLS/SSL. Para obter mais informações, consulte a secção [de aplicação do gestor credencial.](#click-once-credentials-manager-app) 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho da atividade de cópia, consulte [o desempenho da atividade de Copy e o guia de afinação](data-factory-copy-activity-performance.md).