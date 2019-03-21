---
title: Origens de dados suportadas no Azure Analysis Services | Documentos da Microsoft
description: Descreve as origens de dados suportadas para modelos de dados no Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: eeb3b2098cf477004ba7ad85a903f1a8e2267392
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117834"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origens de dados suportadas no Azure Analysis Services

Origens de dados e conectores mostradas em obter dados ou o Assistente de importação no Visual Studio são apresentadas para o Azure Analysis Services e o SQL Server Analysis Services. No entanto, nem todas as origens de dados e conectores mostrados são suportadas no Azure Analysis Services. Os tipos de origens de dados, que pode ligar a dependem muitos fatores, tais como modelam o nível de compatibilidade, conectores de dados disponíveis, tipo de autenticação, fornecedores e suporte de gateway de dados no local. 

## <a name="azure-data-sources"></a>Origens de dados do Azure

|Origem de dados  |Dentro da memória  |DirectQuery  |
|---------|---------|---------|
|Base de dados SQL do Azure<sup>[2](#azsqlmanaged)</sup>     |   Sim      |    Sim      |
|Azure SQL Data Warehouse     |   Sim      |   Sim       |
|Armazenamento de Blobs do Azure<sup>[1](#tab1400a)</sup>     |   Sim       |    Não      |
|Armazenamento de tabelas do Azure<sup>[1](#tab1400a)</sup>    |   Sim       |    Não      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Sim        |  Não        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Sim       |    Não      |
|O Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Sim     |   Não       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Sim       |   Não       |
||||

<a name="tab1400a">1</a> -1400 em tabela e apenas a modelos superior.   
<a name="azsqlmanaged">2</a> -instância gerida da base de dados SQL do azure é suportada. Uma vez que uma instância gerida é executado dentro de VNet do Azure com um endereço IP privado, um Gateway de dados no local é necessário.   
<a name="databricks">3</a> - azure Databricks com o Spark conector não é atualmente suportado.   
<a name="gen2">4</a> -ADLS Gen2 não é atualmente suportada.


**Fornecedor**   
Dentro da memória e os modelos do DirectQuery ligar a origens de dados do Azure utilizam o fornecedor de dados do .NET Framework para o SQL Server.

## <a name="on-premises-data-sources"></a>Origens de dados no local

A ligar no local origens de dados de e o servidor do Azure requerem um gateway no local. Quando utilizar um gateway, os fornecedores de 64 bits são necessários.

### <a name="in-memory-and-directquery"></a>Dentro da memória e DirectQuery

|Origem de dados | Fornecedor de dentro da memória | Fornecedor de DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, fornecedor Microsoft OLE DB para SQL Server, o fornecedor de dados do .NET Framework para o SQL Server | Fornecedor de dados do .NET framework para o SQL Server |
| Armazém de dados do SQL Server |SQL Server Native Client 11.0, fornecedor Microsoft OLE DB para SQL Server, o fornecedor de dados do .NET Framework para o SQL Server | Fornecedor de dados do .NET framework para o SQL Server |
| Oracle |Fornecedor Microsoft OLE DB para Oracle, o provedor de dados Oracle para .NET |Fornecedor de dados do Oracle para .NET |
| Teradata |Fornecedor OLE DB para Teradata, fornecedor de dados do Teradata para .NET |Fornecedor de dados do Teradata para .NET |
| | | |

### <a name="in-memory-only"></a>Na memória apenas

|Origem de dados  |  
|---------|
|Base de dados de acesso     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Livro do Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Pasta<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|Um documento JSON<sup>[1](#tab1400b)</sup>     |  
|Linhas de binário<sup>[1](#tab1400b)</sup>     | 
|Base de Dados MySQL     | 
|OData Feed<sup>[1](#tab1400b)</sup>     |  
|Consulta ODBC     | 
|OLE DB     |   
|Base de dados Postgre SQL<sup>[1](#tab1400b)</sup>    | 
|Objetos do Salesforce<sup>[1](#tab1400b)</sup> |  
|Salesforce Reports<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint<sup>[1](#tab1400b)</sup>     |   
|Base de dados Sybase     |  
|Tabela XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> 1400 em tabela e apenas a modelos superior.

## <a name="specifying-a-different-provider"></a>Especificar um fornecedor diferente

Modelos de dados no Azure Analysis Services podem exigir provedores de dados diferentes, ao ligar a determinadas origens de dados. Em alguns casos, os modelos em tabela, ligar a origens de dados utilizar fornecedores nativos, como o SQL Server Native Client (SQLNCLI11) podem devolver um erro. Se utilizar fornecedores nativos que não seja SQLOLEDB, poderá ver a mensagem de erro: **O fornecedor 'SQLNCLI11.1' não está registada**. Em alternativa, se tem um modelo DirectQuery, ligação a origens de dados no local e utilizar fornecedores nativos, poderá ver a mensagem de erro: **Erro ao criar o conjunto de linhas OLE DB. Sintaxe incorreta perto de "LIMIT"**.

Ao migrar um modelo de tabela do SQL Server Analysis Services no local para o Azure Analysis Services, poderá ser necessário alterar o fornecedor.

**Para especificar um fornecedor**

1. No SSDT > **Explorador de modelos tabulares** > **origens de dados**, uma ligação de origem de dados com o botão direito e, em seguida, clique em **Editar origem de dados**.
2. Na **ligação editar**, clique em **avançadas** para abrir a janela de propriedades de avanço.
3. Na **definir propriedades avançadas** > **fornecedores**, em seguida, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, poderá ser necessário especificar uma conta de representação diferentes. Conta de representação pode ser especificada no Visual Studio (SSDT) ou o SSMS.

Para origens de dados no local:

* Se utilizar a autenticação de SQL, representação deve ser a conta de serviço.
* Se utilizar a autenticação do Windows, defina Windows utilizador/palavra-passe. Para o SQL Server, autenticação do Windows com uma conta de representação específico só é suportada para modelos de dados na memória.

Para origens de dados de cloud:

* Se utilizar a autenticação de SQL, representação deve ser a conta de serviço.

## <a name="next-steps"></a>Passos Seguintes
[Gateway no local](analysis-services-gateway.md)   
[Gerir o seu servidor](analysis-services-manage.md)   

