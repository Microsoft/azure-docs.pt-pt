---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de dados e conectores com suporte para os modelos de dados tabulares 1200 e superiores no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c92baf5c97597a0161f402cc458e90bb3e637d6c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170666"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

As fontes de dados e conectores mostrados no Assistente para obter dados ou importação no Visual Studio são mostrados para Azure Analysis Services e SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados aos quais você pode se conectar dependem de vários fatores, como nível de compatibilidade de modelo, conectores de dados disponíveis, tipo de autenticação, provedores e suporte a gateway de dados local. 

## <a name="azure-data-sources"></a>Origens de dados do Azure

|Origem de dados  |Dentro da memória  |DirectQuery  |
|---------|---------|---------|
|Banco de dados SQL do Azure<sup>[2](#azsqlmanaged)</sup>     |   Sim      |    Sim      |
|Azure SQL Data Warehouse     |   Sim      |   Sim       |
|Armazenamento de BLOBs do Azure<sup>[1](#tab1400a)</sup>     |   Sim       |    Não      |
|Armazenamento de tabelas do Azure<sup>[1](#tab1400a)</sup>    |   Sim       |    Não      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Sim        |  Não        |
|Azure Data Lake Store (GEN1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Sim       |    Não      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Sim     |   Não       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Sim       |   Não       |
||||

<a name="tab1400a">1</a> -somente modelos tabulares 1400 e superiores.   
Há suporte para <a name="azsqlmanaged">2</a> -instância gerenciada do banco de dados SQL do Azure. Como a instância gerenciada é executada na VNet do Azure com um endereço IP privado, o ponto de extremidade público deve ser habilitado na instância. Se não estiver habilitado, um gateway de dados local será necessário.    
Não há <a name="databricks">suporte para o</a> Azure Databricks usando o conector do Spark no momento.   
no momento, não há suporte para o conector de <a name="gen2">4</a> ADLS Gen2, no entanto, o conector de armazenamento de blob pode ser usado com uma fonte de dados de ADLS Gen2.


  do **provedor**  
Modelos na memória e DirectQuery que se conectam a fontes de dados do Azure usam .NET Framework Provedor de Dados para SQL Server.

## <a name="other-data-sources"></a>Outras fontes de dados

Conectar-se a fontes de dados locais de um servidor Azure Analysis Services requer um gateway local. Ao usar um gateway, são necessários provedores de 64 bits.

### <a name="in-memory-and-directquery"></a>Na memória e DirectQuery

|Origem de dados | Provedor na memória | Provedor DirectQuery |
|  --- | --- | --- |
| SQL Server |Microsoft OLE DB driver for SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11,0, .NET Framework Provedor de Dados para SQL Server | .NET Framework Provedor de Dados para SQL Server |
| Armazém de dados do SQL Server |Microsoft OLE DB driver for SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11,0, .NET Framework Provedor de Dados para SQL Server | .NET Framework Provedor de Dados para SQL Server |
| Oracle | Provedor de OLE DB para Oracle, Oracle Provedor de Dados para .NET |Oracle Provedor de Dados para .NET |
| Teradata |Provedor de OLE DB para Teradata, Teradata Provedor de Dados para .NET |Provedor de Dados Teradata para .NET |
| | | |

### <a name="in-memory-only"></a>Somente na memória

|Origem de dados  |  
|---------|
|Banco de dados do Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Sistema de plataforma de análise     |  
|Arquivo CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Pasta de trabalho do Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Pasta<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|Documento JSON<sup>[1](#tab1400b)</sup>     |  
|Linhas do binário<sup>[1](#tab1400b)</sup>     | 
|Base de Dados MySQL     | 
|Feed do OData<sup>[1](#tab1400b)</sup>     |  
|Consulta ODBC     | 
|OLE DB     |   
|Banco de dados PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Objetos do Salesforce<sup>[1](#tab1400b)</sup> |  
|Relatórios do Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint List<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Banco de dados Sybase     |  
|Arquivo TXT  |
|Tabela XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> -somente modelos tabulares 1400 e superiores.   
<a name="filesSP">2</a> -não há suporte para os arquivos no SharePoint local.

## <a name="specifying-a-different-provider"></a>Especificando um provedor diferente

Os modelos de dados no Azure Analysis Services podem exigir provedores de dados diferentes ao se conectar a determinadas fontes de dados. Em alguns casos, os modelos de tabela que se conectam a fontes de dados usando provedores nativos como SQL Server Native Client (SQLNCLI11) podem retornar um erro. Se estiver usando provedores nativos diferentes de MSOLEDBSQL, você poderá ver a mensagem **de erro: o provedor ' sqlncli 11.1 ' não está registrado**. Ou, se você tiver um modelo DirectQuery conectando-se a fontes de dados locais e usar provedores nativos, poderá ver a mensagem de erro: **erro ao criar OLE DB conjunto de linhas. Sintaxe incorreta próxima a ' LIMIT '** .

Ao migrar um modelo de tabela de SQL Server Analysis Services local para Azure Analysis Services, pode ser necessário alterar o provedor.

**Para especificar um provedor**

1. No Visual Studio > o **Gerenciador de modelos de tabela** > fontes de **dados**, clique com o botão direito do mouse em uma conexão de fonte de dados e clique em **Editar fonte de dados**.
2. Em **Editar conexão**, clique em **avançado** para abrir a janela Propriedades avançadas.
3. Em **definir propriedades avançadas** > **provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no Visual Studio ou no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação do SQL, a representação deverá ser a conta de serviço.
* Se estiver usando a autenticação do Windows, defina usuário/senha do Windows. Para SQL Server, a autenticação do Windows com uma conta de representação específica tem suporte apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação do SQL, a representação deverá ser a conta de serviço.

## <a name="oauth-credentials"></a>Credenciais do OAuth

Para modelos de tabela no nível de compatibilidade 1400 e superior, o banco de dados SQL do Azure, o Azure SQL Data Warehouse, o Dynamics 365 e a lista do SharePoint dão suporte a credenciais OAuth. Azure Analysis Services gerencia a atualização de token para fontes de dados OAuth para evitar tempos limite para operações de atualização de execução longa. Para gerar tokens válidos, defina as credenciais usando o SSMS.

## <a name="next-steps"></a>Passos seguintes
  [de gateway local](analysis-services-gateway.md)  
[Gerenciar seu servidor](analysis-services-manage.md)   

