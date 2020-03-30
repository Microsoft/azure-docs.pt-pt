---
title: Ligação aos servidores dos Serviços de Análise Azure. Microsoft Docs
description: Saiba como se conectar e obter dados de um servidor de Serviços de Análise no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73147308"
---
# <a name="connecting-to-servers"></a>Ligar aos servidores

Este artigo descreve a ligação a um servidor utilizando aplicações de modelação e gestão de dados como o SQL Server Management Studio (SSMS) ou o Visual Studio com projetos de Serviços de Análise, ou com aplicações de reporte de clientes como o Microsoft Excel, Power BI Desktop, ou aplicações personalizadas. As ligações aos Serviços de Análise Azure utilizam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente

[Obtenha as mais recentes bibliotecas de clientes](analysis-services-data-providers.md)

Todas as ligações a um servidor, independentemente do tipo, requerem bibliotecas de clientes AMO, ADOMD.NET e OLEDB atualizadas para se conectarem e interagirem com um servidor de Serviços de Análise. Para SSMS, Visual Studio, Excel 2016 e mais tarde, e Power BI, as mais recentes bibliotecas de clientes são instaladas ou atualizadas com lançamentos mensais. No entanto, em alguns casos, é possível que uma aplicação não tenha o mais recente. Por exemplo, quando as políticas atrasam as atualizações, ou as atualizações do Office 365 estão no Canal Diferido.

## <a name="server-name"></a>Nome do servidor

Quando cria um servidor de Serviços de Análise em Azure, especifica um nome único e a região onde o servidor deve ser criado. Ao especificar o nome do servidor numa ligação, o esquema de nomeação do servidor é:

```
<protocol>://<region>/<servername>
```
 Onde o protocolo é **string asazure**, região é o Uri onde o servidor foi criado (por exemplo, westus.asazure.windows.net) e o nome do servidor é o nome do seu servidor único dentro da região.

### <a name="get-the-server-name"></a>Obtenha o nome do servidor

No **portal Azure** > servidor >**nome**do Servidor de **Visão Geral,** > copie todo o nome do servidor. Se outros utilizadores da sua organização também estiverem ligados a este servidor, pode partilhar este nome de servidor com eles. Ao especificar um nome de servidor, todo o caminho deve ser utilizado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> O protocolo para a região leste dos EUA 2 é **aspaaseastus2**.

## <a name="connection-string"></a>Cadeia de ligação

Ao ligar-se aos Serviços de Análise Azure utilizando o Modelo de Objetos Tabular, utilize os seguintes formatos de cordas de ligação:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Diretório Ativo Azure

A autenticação integrada capta a cache credencial do Diretório Ativo Azure, se disponível. Caso contrário, mostra-se a janela de login Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Diretório Ativo Azure com nome de utilizador e senha

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (segurança integrada)

Utilize a conta Windows a executar o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Conecte-se usando um ficheiro .odc

Com versões mais antigas do Excel, os utilizadores podem ligar-se a um servidor dos Serviços de Análise Do Azure utilizando um ficheiro de Ligação de Dados do Office (.odc). Para saber mais, consulte Criar um ficheiro de Ligação de [Dados do Office (.odc).](analysis-services-odc.md)


## <a name="next-steps"></a>Passos seguintes

[Conecte-se com Excel](analysis-services-connect-excel.md)    
[Conecte-se com power BI](analysis-services-connect-pbi.md)   
[Gerir o seu servidor](analysis-services-manage.md)   

