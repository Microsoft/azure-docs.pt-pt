---
title: Fontes de dados apoiadas nos Serviços de Análise da Azure Microsoft Docs
description: Descreve fontes de dados e conectores suportados para modelos de dados tabulares 1200 e mais elevados nos Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 174ad4692d043390e6773a98e31f0985d75c8e2e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018819"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Data sources supported in Azure Analysis Services (Origens de dados suportadas no Azure Analysis Services)

As fontes de dados e conectores mostrados no Get Data ou no Table Import Wizard em Estúdio Visual com projetos de Serviços de Análise são mostrados tanto para os Serviços de Análise Azure como para os Serviços de Análise de Servidores SQL. No entanto, nem todas as fontes de dados e conectores apresentados são suportados nos Serviços de Análise Azure. Os tipos de fontes de dados que pode ligar dependem de muitos fatores, tais como o nível de compatibilidade do modelo, os conectores de dados disponíveis, o tipo de autenticação e o suporte de gateway de dados on-in. As tabelas que se seguem descrevem fontes de dados suportadas para os Serviços de Análise Azure.

## <a name="azure-data-sources"></a>Origens de dados do Azure

|Origem de dados  |Dentro da memória  |DirectQuery  |Notas |
|---------|---------|---------|---------|
|Base de Dados SQL do Azure      |   Sim      |    Sim      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Sim      |   Sim       |<sup>[2](#azprovider)</sup>|
|Armazenamento de Blobs do Azure      |   Yes       |    Não      | <sup>[1](#tab1400a)</sup> |
|Armazenamento de Tabelas do Azure     |   Sim       |    Não      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sim        |  Não        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Sim       |    Não      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Sim       |    Não      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Yes     |   Não       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Sim       |   Não       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**Notas:**

<a name="tab1400a">1</a> - Tabular 1400 e apenas modelos mais altos.  
<a name="azprovider">2</a> - Quando especificado como fonte de dados *do fornecedor* em modelos tabulares 1200 e mais altos, tanto os modelos em memória como os modelos DirectQuery requerem o Microsoft OLE DB Driver para o SQL Server MSOLEDBSQL (recomendado), o SQL Server Native Client 11.0 ou o Fornecedor de Dados Quadros .NET para o SQL Server Server MSOLEDBSQL (recomendado), o SQL Server Native Client 11.0 ou o Fornecedor de Dados Quadros .NET para o SQL Server Server MSOLEDBSQL (recomendado), o SQL Server Native Client 11.0 ou o Fornecedor de Dados Quadros .NET para o SQL Server Server MSOLEDBSQL (recomendado), SQL Server Native Client 11.0 ou .NET Framework Data Provider para o SQL Server.  
<a name="azsqlmanaged">3</a> - Azure SQL Managed Instance é suportado. Uma vez que o SQL Managed Instance é executado dentro do Azure VNet com um endereço IP privado, o ponto final público deve ser ativado no caso. Se não estiver ativado, é necessário um [gateway de dados no local.](analysis-services-gateway.md)  
<a name="databricks">4</a> - Azure Databricks que utiliza o conector Spark não está atualmente suportado.  
<a name="gen2">5</a> - O conector ADLS Gen2 não está atualmente suportado, no entanto, o conector de armazenamento Azure Blob pode ser utilizado com uma fonte de dados ADLS Gen2.

## <a name="other-data-sources"></a>Outras origens de dados

|Origem de dados | Dentro da memória | DirectQuery |Notas   |
|  --- | --- | --- | --- |
|Base de Dados do Access     |  Yes | Não |  |
|Active Directory     |  Yes | Não | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Sim | Não |  |
|Sistema de Plataformas analíticas     |  Sim | Não |  |
|Ficheiro CSV  |Sim | Não |  |
|Dynamics 365     |  Yes | Não | <sup>[6](#tab1400b)</sup> |
|Livro do Excel     |  Sim | Não |  |
|Troca      |  Yes | Não | <sup>[6](#tab1400b)</sup> |
|Pasta      |Sim | Não | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Sim | Não |  |
|Documento JSON      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Linhas do binário      | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Base de Dados MySQL     | Yes | Não |  |
|Feed OData      |  Yes | Não | <sup>[6](#tab1400b)</sup> |
|Consulta ODBC     | Sim | Não |  |
|OLEDB     |   Yes | Não |  |
|Oracle  | Sim  |Sim  | <sup>[9](#oracle)</sup> |
|Base de Dados PostgreSQL   | Yes | Não | <sup>[6](#tab1400b)</sup> |
|Objetos do Salesforce|  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Relatórios do Salesforce |Sim | Não | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Sim | Não |  |
|SAP Business Warehouse    |  Yes | Não | <sup>[6](#tab1400b)</sup> |
|Lista do SharePoint      |   Sim | Não | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Sim   | Yes  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Armazém de dados do sql server |Sim   | Yes  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Base de Dados Sybase     |  Yes | Não |  |
|Teradata | Sim  | Yes  | <sup>[10](#teradata)</sup> |
|Ficheiro TXT  |Sim | Não |  |
|Mesa XML    |  Yes | Não | <sup>[6](#tab1400b)</sup> |
| | | |

**Notas:**  
<a name="tab1400b">6</a> - Tabular 1400 e apenas modelos mais altos.  
<a name="sqlim">7</a> - Quando especificado como fonte de dados *do fornecedor* em modelos tabulares 1200 e mais altos, especifique o Controlador DB do Microsoft OLE para o SQL Server MSOLEDBSQL (recomendado), o SQL Server Native Client 11.0 ou .NET Framework Data Provider para o SQL Server.  
<a name="instgw">8</a> - Se especificar o MSOLEDBSQL como fornecedor de dados, poderá ser necessário descarregar e instalar o [Controlador DB do Microsoft OLE para](/sql/connect/oledb/oledb-driver-for-sql-server) o SQL Server no mesmo computador que o gateway de dados no local.  
<a name="oracle">9</a> - Para modelos tabulares 1200, ou como fonte de dados *do fornecedor* nos modelos tabulares de 1400+ especifique o Oracle Data Provider para .NET. Se especificado como uma fonte de dados estruturada, certifique-se de que permite o [fornecedor gerido pela Oracle](#enable-oracle-managed-provider).   
<a name="teradata">10</a> - Para modelos tabulares 1200, ou como fonte de dados *do fornecedor* nos modelos tabulares de 1400+ especifique o Fornecedor de Dados teradata para .NET.  
<a name="filesSP">11</a> - Os ficheiros nas instalações do SharePoint não são suportados.

A ligação a fontes de dados no local a partir de um servidor de Serviços de Análise Azure requer um [gateway no local](analysis-services-gateway.md). Ao utilizar um gateway, são necessários fornecedores de 64 bits.

## <a name="understanding-providers"></a>Compreender fornecedores

Ao criar projetos de modelo tabular 1400 e modelos mais elevados no Visual Studio, por padrão não especifica um fornecedor de dados ao ligar-se a uma fonte de dados utilizando **o Get Data**. Os modelos Tabulares 1400 e mais altos utilizam conectores [de consulta de potência](/power-query/power-query-what-is-power-query) para gerir ligações, consultas de dados e mashups entre a fonte de dados e os Serviços de Análise. Estas são por vezes referidas como ligações *estruturadas* de fonte de dados nessa ligação as configurações da propriedade são definidas para si. No entanto, pode ativar fontes de dados antigas para um projeto modelo no Visual Studio. Quando ativado, pode utilizar **o Table Import Wizard** para ligar a determinadas fontes de dados tradicionalmente suportadas em modelos tabulares 1200 e inferiores como *legado*, ou fontes de dados *do fornecedor.* Quando especificado como fonte de dados do fornecedor, pode especificar um determinado fornecedor de dados e outras propriedades avançadas de conexão. Por exemplo, pode ligar-se a uma instância sql server Data Warehouse ou até mesmo a uma Base de Dados Azure SQL como uma fonte de dados antiga. Em seguida, pode selecionar o controlador OLE DB para o fornecedor de dados MSOLEDBSQL do SQL Server. Neste caso, a seleção de um fornecedor de dados OLE DB pode proporcionar um melhor desempenho sobre o conector de consulta de energia. 

Ao utilizar o Assistente de Importação de Tabelas em Estúdio Visual, as ligações a qualquer fonte de dados requerem um fornecedor de dados. Um fornecedor de dados predefinido é selecionado para si. Pode alterar o fornecedor de dados se necessário. O tipo de fornecedor que escolher pode depender do desempenho, quer o modelo esteja ou não a utilizar o armazenamento na memória ou o DirectQuery, e a que plataforma de Serviços de Análise implementa o seu modelo.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Especificar as fontes de dados do fornecedor em projetos de modelo tabulares 1400 e mais elevados

Para ativar as fontes de dados do fornecedor, no Estúdio Visual, clique em **Tools**  >  **Options Analysis**Services  >  **Tabular**Data  >  **Import**, selecione **Enable Legacy data sources**.

![Ativar fontes de dados antigas](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Com fontes de dados antigas ativadas, no **Tabular Model Explorer,** clique à direita Fontes de **Dados**  >  **Importa da Fonte de Dados (Legado)**.

![Fontes de dados antigas no Tabular Model Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Tal como acontece com os projetos de modelo tabulares 1200, utilize o **Table Import Wizard** para se ligar a uma fonte de dados. Na página de ligação, clique **em Advanced**. Especifique o fornecedor de dados e outras definições de conexão em **Propriedades Avançadas Definidas**.

![Fontes de dados antigas Propriedades avançadas](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Personificação
Em alguns casos, pode ser necessário especificar uma conta de imitação diferente. A conta de imitação pode ser especificada no Visual Studio ou no SQL Server Management Studio (SSMS).

Para fontes de dados no local:

* Se utilizar a autenticação SQL, a personificação deve ser conta de serviço.
* Se utilizar a autenticação do Windows, desapedae o utilizador/palavra-passe do Windows. Para o SQL Server, a autenticação do Windows com uma conta de imitação específica é suportada apenas para modelos de dados em memória.

Para fontes de dados em nuvem:

* Se utilizar a autenticação SQL, a personificação deve ser conta de serviço.

## <a name="oauth-credentials"></a>Credenciais OAuth

Para modelos tabulares no nível de compatibilidade 1400 e mais elevado utilizando o modo de memória, Azure SQL Database, Azure Synapse (anteriormente SQL Data Warehouse), Dynamics 365 e SharePoint List suportam credenciais OAuth. A Azure Analysis Services gere a atualização simbólica para fontes de dados da OAuth para evitar intervalos de tempo para operações de atualização de longa duração. Para gerar fichas válidas, desempenhe as credenciais utilizando a Consulta de Potência.

O modo de consulta direta não é suportado com credenciais OAuth.

## <a name="enable-oracle-managed-provider"></a>Ativar o fornecedor gerido pela Oracle

Em alguns casos, as consultas do DAX a uma fonte de dados da Oracle podem devolver resultados inesperados. Isto pode ser devido ao facto de o fornecedor ser utilizado para a ligação à fonte de dados.

Conforme descrito na secção [de fornecedores de Compreensão,](#understanding-providers) os modelos tabulares conectam-se a fontes de dados como uma fonte de dados *estruturada* ou uma fonte de dados *do fornecedor.* Para modelos com uma fonte de dados da Oracle especificada como fonte de dados do fornecedor, certifique-se de que o fornecedor especificado é o Oracle Data Provider for.NET (Oracle.DataAccess.Client). 

Se a fonte de dados da Oracle for especificada como uma fonte de dados estruturada, ative a propriedade do servidor **MDataEngine\UseManagedOracleProvider.** A definição desta propriedade garante que o seu modelo se conecta à fonte de dados da Oracle utilizando o fornecedor de dados oracle recomendado para o fornecedor gerido .NET.
 
Para ativar o fornecedor gerido pela Oracle:

1. No SQL Server Management Studio, ligue-se ao seu servidor.
2. Crie uma consulta XMLA com o seguinte script. Substitua **o Nome do Servidor** pelo nome completo do servidor e, em seguida, execute a consulta.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Reinicie o servidor.


## <a name="next-steps"></a>Passos seguintes

* [Porta de entrada no local](analysis-services-gateway.md)
* [Gerencie o seu servidor](analysis-services-manage.md)