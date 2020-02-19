---
title: Fontes de dados apoiadas nos Serviços de Análise do Azure Microsoft Docs
description: Descreve fontes de dados e conectores suportados por modelos de dados tabular 1200 e modelos de dados mais elevados nos Serviços de Análise do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461662"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados apoiadas nos Serviços de Análise do Azure

Fontes de dados e conectores mostrados em Obter Dados ou Assistente de Importação de Tabelas em Estúdio Visual com serviços de análise são mostrados tanto para serviços de análise Azure como serviços de análise de servidores SQL. No entanto, nem todas as fontes de dados e conectores apresentados são suportados nos Serviços de Análise do Azure. Os tipos de fontes de dados a que se pode ligar dependem de muitos fatores, tais como o nível de compatibilidade do modelo, conectores de dados disponíveis, tipo de autenticação e suporte de gateway de dados no local. As tabelas seguintes descrevem fontes de dados suportadas para os Serviços de Análise do Azure.

## <a name="azure-data-sources"></a>Origens de dados do Azure

|Origem de dados  |Dentro da memória  |DirectQuery  |Notas |
|---------|---------|---------|---------|
|Base de Dados SQL do Azure      |   Sim      |    Sim      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (Armazém de Dados SQL)      |   Sim      |   Sim       |<sup>[2](#azprovider)</sup>|
|Armazenamento de Blobs do Azure      |   Sim       |    Não      | <sup>[1](#tab1400a)</sup> |
|Table Storage do Azure     |   Sim       |    Não      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sim        |  Não        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Sim       |    Não      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Sim       |    Não      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Sim     |   Não       |<sup>[1](#tab1400a)</sup> |
|Faísca Azure HDInsight     |   Sim       |   Não       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**Notas:**    
<a name="tab1400a">1</a> - Apenas modelos tabular 1400 e modelos superiores.  
<a name="azprovider">2</a> - Quando especificado como fonte de dados do *fornecedor* em modelos tabular 1200 e superiores, tanto os modelos de memória como os modelos DirectQuery requerem o Microsoft OLE DB Driver para o SQL Server MSOLEDBSQL (recomendado), SQL Server Client Nativo 11.0, ou .NET Framework Data Provider para O Servidor SQL.    
<a name="azsqlmanaged">3</a> - A Instância Gerida pela Base de Dados Azure SQL é suportada. Uma vez que a instância gerida funciona dentro do Azure VNet com um endereço IP privado, o ponto final público deve ser ativado na instância. Se não estiver ativado, é necessário um [portal de dados no local.](analysis-services-gateway.md)    
<a name="databricks">4</a> - Os tijolos de dados azure que utilizam o conector Spark não são suportados atualmente.   
<a name="gen2">5</a> - O conector ADLS Gen2 não é atualmente suportado, no entanto, o conector de armazenamento de Blob Azure pode ser utilizado com uma fonte de dados ADLS Gen2.   

## <a name="other-data-sources"></a>Outras fontes de dados

|Origem de dados | Dentro da memória | DirectQuery |Notas   |
|  --- | --- | --- | --- |
|Base de Dados de Acesso     |  Sim | Não |  |
|Active Directory     |  Sim | Não | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Sim | Não |  |
|Sistema de Plataformas Analytics     |  Sim | Não |  |
|Arquivo CSV  |Sim | Não |  |
|Dynamics 365     |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Livro excel     |  Sim | Não |  |
|Troca      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Pasta      |Sim | Não | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Sim | Não |  |
|Documento JSON      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Linhas do binário      | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Base de Dados MySQL     | Sim | Não |  |
|OData Feed      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Consulta ODBC     | Sim | Não |  |
|OLE DB     |   Sim | Não |  |
|Oracle  | Sim  |Sim  | <sup>[9](#oracle)</sup> |
|Base de Dados PostgreSQL   | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Objetos salesforce|  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Relatórios salesforce |Sim | Não | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Sim | Não |  |
|SAP Business Warehouse    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Lista de Pontos de Partilha      |   Sim | Não | <sup>[6,](#tab1400b)</sup> <sup> [11](#filesSP)</sup> |
|SQL Server |Sim   | Sim  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> | 
|Armazém de dados do SQL Server |Sim   | Sim  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Base de Dados Sybase     |  Sim | Não |  |
|Teradata | Sim  | Sim  | <sup>[10](#teradata)</sup> |
|Arquivo TXT  |Sim | Não |  |
|Mesa XML    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
| | | |

**Notas:**    
<a name="tab1400b">6</a> - Apenas modelos tabular 1400 e modelos superiores.  
<a name="sqlim">7</a> - Quando especificado como fonte de dados do *fornecedor* em modelos tabular 1200 e superiores, especifique o Controlador DB microsoft OLE db para OQL Server MSOLEDBSQL (recomendado), SQL Server Client Nativo 11.0 ou .NET Framework Data Provider para O Servidor SQL.  
<a name="instgw">8</a> - Se especificar o MSOLEDBSQL como fornecedor de dados, pode ser necessário descarregar e instalar o [Microsoft OLE DB Driver para o SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) no mesmo computador que o gateway de dados No local.  
<a name="oracle">9</a> - Para os modelos tabular 1200, ou como fonte de dados do *fornecedor* nos modelos tabular 1400+, especifique o Provedor de Dados oracle para .NET.  
<a name="teradata">10</a> - Para modelos tabular 1200, ou como fonte de dados do *fornecedor* em modelos tabular 1400+, especifique o Fornecedor de Dados teradata para .NET.   
<a name="filesSP">11</a> - Os ficheiros no sharePoint no local não são suportados.

A ligação a fontes de dados no local a partir de um servidor dos [Serviços](analysis-services-gateway.md)de Análise Azure requer um gateway no local . Quando se utiliza um portal, são necessários fornecedores de 64 bits. 

## <a name="understanding-providers"></a>Provedores de compreensão

Ao criar projetos de modelos tabular 1400 e modelos mais elevados no Estúdio Visual, por padrão, não especifica um fornecedor de dados quando se conecta a uma fonte de dados utilizando o **Get Data**. Os modelos Tabular 1400 e superiores utilizam conectores [Power Query](/power-query/power-query-what-is-power-query) para gerir ligações, consultas de dados e mashups entre a fonte de dados e os Serviços de Análise. Estas são por vezes referidas como ligações *estruturadas* de fonte de dados nessa ligação as definições de propriedade são definidas para si. Pode, no entanto, ativar fontes de dados antigas. Quando ativado, pode utilizar o Assistente de Importação de **Tabelas** para se ligar a determinadas fontes de dados tradicionalmente suportadas em modelos tabular 1200 e modelos inferiores como *legado*, ou fontes de dados *do fornecedor.* Quando especificado como fonte de dados do fornecedor, pode especificar um determinado fornecedor de dados e outras propriedades avançadas de ligação. Por exemplo, pode ligar-se a um Armazém de Dados do Servidor SQL no local ou mesmo a uma Base de Dados Azure SQL como fonte de dados antigas. Em seguida, pode selecionar o Controlador OLE DB para o fornecedor de dados MSOLEDBSQL do Servidor SQL. Neste caso, a seleção de um fornecedor de dados OLE DB pode proporcionar um melhor desempenho sobre o conector Power Query. 

Ao utilizar o Assistente de Importação de Tabelas no Estúdio Visual, as ligações a qualquer fonte de dados requerem um fornecedor de dados. Um fornecedor de dados predefinido é selecionado para si. Pode alterar o fornecedor de dados se necessário. O tipo de fornecedor que escolher pode depender do desempenho, quer o modelo esteja ou não a utilizar armazenamento na memória ou directquery, e para que plataforma de Serviços de Análise implementa o seu modelo.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Especificar as fontes de dados do fornecedor em projetos de modelos tabular 1400 e modelos mais elevados

Para ativar as fontes de dados do fornecedor, no Estúdio Visual, clique em **Tools** > **Options** > Analysis **Services Tabular** > **Data Import**, selecione Enable **source solegacy data sources**.

![Ativar fontes de dados antigas](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Com fontes de dados antigas ativadas, no **Tabular Model Explorer,** **fontes** de dados de clique sapadireito > **importar de origem de dados (legado)** .

![Fontes de dados antigas no Tabular Model Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Tal como acontece com os projetos-modelo tabular 1200, utilize o Assistente de Importação de **Tabelas** para se ligar a uma fonte de dados. Na página de ligação, clique **em Avançado**. Especifique o fornecedor de dados e outras definições de ligação em **Definições de Propriedades Avançadas**.

![Fontes de dados antigas Propriedades avançadas](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Personificação
Em alguns casos, pode ser necessário especificar uma conta de imitação diferente. A conta de personificação pode ser especificada no Visual Studio ou sSMS.

Para fontes de dados no local:

* Se utilizar a autenticação SQL, a personificação deve ser conta de serviço.
* Se utilizar a autenticação do Windows, detete o utilizador/senha do Windows. Para o Servidor SQL, a autenticação do Windows com uma conta de imitação específica é suportada apenas para modelos de dados na memória.

Para fontes de dados em nuvem:

* Se utilizar a autenticação SQL, a personificação deve ser conta de serviço.

## <a name="oauth-credentials"></a>Credenciais OAuth

Para os modelos tabular no nível de compatibilidade 1400 e superior utilizando o modo de memória, azure SQL Database, Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 e SharePoint List suportam credenciais OAuth. Os Serviços de Análise Azure gerem a atualização simbólica para fontes de dados da OAuth para evitar intervalos para operações de atualização de longo prazo. Para gerar fichas válidas, detete as credenciais utilizando SSMS.

O modo de consulta direta não é suportado com credenciais OAuth.

## <a name="next-steps"></a>Passos seguintes
[Gateway](analysis-services-gateway.md)   
[Gerir o seu servidor](analysis-services-manage.md)   

