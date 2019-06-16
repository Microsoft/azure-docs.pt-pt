---
title: Ligar aos servidores do Azure Analysis Services | Documentos da Microsoft
description: Saiba como ligar e obter dados a partir de um servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a8863189ee9cb63d86b157c0bbebb6fd16116b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61027988"
---
# <a name="connecting-to-servers"></a>Ligar aos servidores

Este artigo descreve a ligar a um servidor ao utilizar modelação de dados e aplicações de gestão, como o SQL Server Management Studio (SSMS) ou SQL Server Data Tools (SSDT). Ou, com o cliente de aplicações como o Microsoft Excel, Power BI Desktop ou aplicativos personalizados de relatório. Ligações ao Azure Analysis Services utilizam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente

[Obter as bibliotecas de cliente mais recente](analysis-services-data-providers.md)

Todas as ligações a um servidor, independentemente do tipo, exigem atualizadas bibliotecas de cliente AMO, ADOMD.NET e OLEDB para ligar a e a interface com um servidor do Analysis Services. Para o SSMS, o SSDT, o Excel 2016 e posterior e o Power BI, as bibliotecas de cliente mais recente forem instaladas ou atualizadas com as versões mensais. No entanto, em alguns casos, é possível que um aplicativo pode não ter a versão mais recente. Por exemplo, quando atualiza o atraso de políticas ou atualizações do Office 365 são no canal diferido.

## <a name="server-name"></a>Nome do servidor

Quando cria um servidor de Analysis Services no Azure, especifique um nome exclusivo e a região onde o servidor está a ser criada. Ao especificar o nome do servidor numa ligação, o esquema de nomenclatura do servidor é:

```
<protocol>://<region>/<servername>
```
 Em que o protocolo é a cadeia de caracteres **asazure**, região é o Uri onde o servidor foi criado (por exemplo, westus.asazure.windows.net) e servername é o nome do seu servidor exclusivo dentro da região.

### <a name="get-the-server-name"></a>Obter o nome do servidor

Na **portal do Azure** > servidor > **descrição geral** > **nome do servidor**, copie o nome de servidor completo. Se a outros utilizadores na sua organização estão a ligar a este servidor também, pode compartilhar este nome de servidor com os mesmos. Ao especificar um nome de servidor, todo o caminho deve ser utilizado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> O protocolo para a região E.U.A. Leste 2 é **aspaaseastus2**.

## <a name="connection-string"></a>Cadeia de ligação

Ao ligar ao Azure Analysis Services usando o modelo de objeto em tabela, utilize os seguintes formatos de cadeia de ligação:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Azure Active Directory

Autenticação integrada do seleciona o cache de credencial do Azure Active Directory se estiver disponível. Caso contrário, é apresentada a janela de início de sessão do Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Active Directory do Azure com o nome de utilizador e palavra-passe

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (segurança integrada)

Utilize a conta de Windows que executa o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Ligar através de um ficheiro. odc

Com as versões mais antigas do Excel, os utilizadores podem ligar a um servidor Azure Analysis Services, utilizando um ficheiro de ligação de dados do Office (. odc). Para obter mais informações, consulte [criar um ficheiro de ligação de dados do Office (. odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Passos Seguintes

[Ligar com o Excel](analysis-services-connect-excel.md)    
[Ligar ao Power BI](analysis-services-connect-pbi.md)   
[Gerir o seu servidor](analysis-services-manage.md)   

