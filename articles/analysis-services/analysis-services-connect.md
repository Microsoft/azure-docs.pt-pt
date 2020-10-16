---
title: Ligação aos servidores dos Serviços de Análise Azure Microsoft Docs
description: Saiba como se conectar e obter dados de um servidor de Serviços de Análise em Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 93b1e9a2ea052a39e891cf9084b632f434c341df
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013700"
---
# <a name="connecting-to-servers"></a>Ligar aos servidores

Este artigo descreve a ligação a um servidor utilizando aplicações de modelação e gestão de dados como o SQL Server Management Studio (SSMS) ou o Visual Studio com projetos de Serviços de Análise, ou com aplicações de reporte de clientes como Microsoft Excel, Power BI Desktop ou aplicações personalizadas. As ligações aos Serviços de Análise Azure utilizam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente

[Obtenha as mais recentes bibliotecas de clientes](/analysis-services/client-libraries?view=azure-analysis-services-current)

Todas as ligações a um servidor, independentemente do tipo, requerem bibliotecas de clientes amo, ADOMD.NET e OLEDB atualizadas para se conectarem e interagirem com um servidor de Serviços de Análise. Para sSMS, Visual Studio, Excel 2016 e mais tarde, e Power BI, as mais recentes bibliotecas de clientes são instaladas ou atualizadas com lançamentos mensais. No entanto, em alguns casos, é possível que uma aplicação não tenha a mais recente. Por exemplo, quando as apólices atrasam as atualizações ou as atualizações do Microsoft 365 estão no Canal Diferido.

> [!NOTE]
> As bibliotecas do cliente não podem ligar-se aos Serviços de Análise Azure através de servidores proxy que requerem um nome de utilizador e senha. 

## <a name="server-name"></a>Nome do servidor

Quando cria um servidor de Serviços de Análise em Azure, especifica um nome único e a região onde o servidor será criado. Ao especificar o nome do servidor numa ligação, o esquema de nomeação do servidor é:

```
<protocol>://<region>/<servername>
```
 Onde o protocolo é **string asazure**, região é o Uri onde o servidor foi criado (por exemplo, westus.asazure.windows.net) e o nome de servidor é o nome do seu servidor único dentro da região.

### <a name="get-the-server-name"></a>Obtenha o nome do servidor

No **portal Azure** > servidor > nome **do Servidor de Visão Geral**,  >  **Server name**copie todo o nome do servidor. Se outros utilizadores da sua organização também estiverem ligados a este servidor, pode partilhar este nome do servidor com eles. Ao especificar um nome de servidor, todo o caminho deve ser utilizado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> O protocolo para a região leste dos EUA 2 é **aspaaseastus2**.

## <a name="connection-string"></a>Cadeia de ligação

Ao ligar aos Serviços de Análise Azure utilizando o Modelo de Objeto Tabular, utilize os seguintes formatos de cadeia de ligação:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Diretório Ativo Azure

A autenticação integrada recolhe a cache credencial Azure Ative Directory, se disponível. Caso contrário, é mostrada a janela de login Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Azure Ative Directory com nome de utilizador e senha

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (segurança integrada)

Utilize a conta do Windows a executar o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Conecte-se usando um ficheiro .odc

Com versões mais antigas do Excel, os utilizadores podem ligar-se a um servidor Azure Analysis Services utilizando um ficheiro De Ligação de Dados do Office (.odc). Para saber mais, consulte [Criar um ficheiro De Ligação de Dados do Office (.odc).](analysis-services-odc.md)

## <a name="connect-as-a-linked-server-from-sql-server"></a>Conecte-se como um servidor ligado do SQL Server

O SQL Server pode ligar-se a um recurso Azure Analysis Services como um [servidor ligado,](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) especificando o MSOLAP como fornecedor de fonte de dados. Antes de configurar uma ligação do servidor ligada, certifique-se de instalar a mais recente [biblioteca de clientes MSOLAP](/analysis-services/client-libraries?view=azure-analysis-services-current) (fornecedor). 

Para ligações de servidores ligados aos Serviços de Análise Azure, o fornecedor MSOLAP deve ser instantâneo fora do processo do SQL Server. Ao configurar opções de servidor ligado, certifique-se de que a opção **de inprocessamento permitir** **não está selecionada**.

Se **o processo de acesso** permitir ser selecionado e o fornecedor for instantâneo no processo SQL Server, o seguinte erro é devolvido:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Passos seguintes

[Conecte-se com o Excel](analysis-services-connect-excel.md)    
[Conecte-se com Power BI](analysis-services-connect-pbi.md)   
[Gerencie o seu servidor](analysis-services-manage.md)