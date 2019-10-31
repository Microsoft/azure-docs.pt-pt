---
title: Conectando-se a servidores Azure Analysis Services | Microsoft Docs
description: Saiba como se conectar e obter dados de um servidor Analysis Services no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147308"
---
# <a name="connecting-to-servers"></a>Ligar aos servidores

Este artigo descreve como se conectar a um servidor usando aplicativos de modelagem e gerenciamento de dados como o SQL Server Management Studio (SSMS) ou o Visual Studio com projetos Analysis Services ou com aplicativos de relatório de cliente como o Microsoft Excel, Power BI Aplicativos de área de trabalho ou personalizados. Conexões com Azure Analysis Services usam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente

[Obter as bibliotecas de cliente mais recentes](analysis-services-data-providers.md)

Todas as conexões com um servidor, independentemente do tipo, exigem bibliotecas de cliente AMO, ADOMD.NET e OLEDB atualizadas para se conectar e interagir com um servidor Analysis Services. Para SSMS, Visual Studio, Excel 2016 e posterior e Power BI, as bibliotecas de cliente mais recentes são instaladas ou atualizadas com versões mensais. No entanto, em alguns casos, é possível que um aplicativo não tenha o mais recente. Por exemplo, quando as políticas atrasam atualizações ou as atualizações do Office 365 estão no canal adiado.

## <a name="server-name"></a>Nome do servidor

Ao criar um servidor de Analysis Services no Azure, você especifica um nome exclusivo e a região em que o servidor deve ser criado. Ao especificar o nome do servidor em uma conexão, o esquema de nomenclatura do servidor é:

```
<protocol>://<region>/<servername>
```
 Quando o protocolo é uma cadeia de caracteres **asazure**, região é o URI em que o servidor foi criado (por exemplo, westus.asazure.Windows.net) e ServerName é o nome do seu servidor exclusivo na região.

### <a name="get-the-server-name"></a>Obter o nome do servidor

Em **portal do Azure** > Server > **visão geral** > **nome do servidor**, copie todo o nome do servidor. Se outros usuários em sua organização estiverem se conectando a esse servidor também, você poderá compartilhar esse nome de servidor com eles. Ao especificar um nome de servidor, o caminho inteiro deve ser usado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> O protocolo para a região leste dos EUA 2 é **aspaaseastus2**.

## <a name="connection-string"></a>Cadeia de ligação

Ao se conectar a Azure Analysis Services usando o modelo de objeto de tabela, use os seguintes formatos de cadeia de conexão:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação de Azure Active Directory integrada

A autenticação integrada seleciona o cache de credencial Azure Active Directory, se disponível. Caso contrário, a janela de logon do Azure será mostrada.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory autenticação com nome de usuário e senha

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (segurança integrada)

Use a conta do Windows que executa o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Conectar-se usando um arquivo. odc

Com versões mais antigas do Excel, os usuários podem se conectar a um servidor de Azure Analysis Services usando um arquivo de conexão de dados do Office (. odc). Para saber mais, confira [criar um arquivo de conexão de dados do Office (. odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Passos seguintes

[Conectar-se ao Excel](analysis-services-connect-excel.md)    
[Conectar-se com Power BI](analysis-services-connect-pbi.md)   
[Gerenciar seu servidor](analysis-services-manage.md)   

