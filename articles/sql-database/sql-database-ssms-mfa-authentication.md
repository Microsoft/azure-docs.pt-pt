---
title: Utilização da autenticação AAD multifactor
description: Azure SQL Database e Azure Synapse suportam ligações do SQL Server Management Studio (SSMS) utilizando a Autenticação Universal do Diretório Ativo.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194041"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Utilização da autenticação AAD multifactor com base de dados Azure SQL e Azure Synapse Analytics (suporte SSMS para MFA)
Azure SQL Database e Azure Synapse suportam ligações do SQL Server Management Studio (SSMS) utilizando a *Autenticação Universal do Diretório Ativo.* Este artigo discute as diferenças entre as várias opções de autenticação, bem como as limitações associadas à utilização da Autenticação Universal. 

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, a partir do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Para todas as funcionalidades discutidas neste artigo, utilize pelo menos julho de 2017, versão 17.2.  A mais recente caixa de diálogo de ligação deve ser semelhante à seguinte imagem:
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Completa a caixa de nomes do Utilizador.")  

## <a name="the-five-authentication-options"></a>As cinco opções de autenticação  

A Autenticação Universal do Diretório Ativo suporta os dois métodos de autenticação não interativos:
    - `Active Directory - Password` autenticação
    - `Active Directory - Integrated` autenticação

Existem também dois modelos de autenticação não interativa, que podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDCB, ODC, etc.). Estes dois métodos nunca resultam em caixas de diálogo pop-up: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

O método interativo é que também suporta a autenticação multifactor Azure (MFA) é: 
- `Active Directory - Universal with MFA` 


A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Oferece uma autenticação forte com um leque de opções de verificação fáceis (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicação móvel), permitindo aos utilizadores escolherem o método que preferem. O MFA interativo com o Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multi-factor, consulte a [autenticação de vários fatores](../active-directory/authentication/multi-factor-authentication.md).
Para os passos de configuração, consulte a [autenticação multifactor de base de dados Configure Azure SQL para o Estúdio de Gestão de Servidores SQL](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nome de domínio azure AD ou parâmetro de identificação do inquilino   

Começando pela versão 17 do [SSMS,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)os utilizadores que são importados para o atual Diretório Ativo de outros Diretórios Ativos do Azure como utilizadores convidados, podem fornecer o nome de domínio Azure AD, ou ID do inquilino quando se conectam. Os utilizadores convidados incluem utilizadores convidados de outros Anúncios Azure, contas da Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Esta informação permite que o **Diretório Ativo Universal com Autenticação MFA** identifique a autoridade autenticadora correta. Esta opção também é necessária para suportar contas da Microsoft (MSA) tais como contas outlook.com, hotmail.com, live.com ou não-MSA. Todos estes utilizadores que pretendam ser autenticados utilizando a Autenticação Universal devem introduzir o seu nome de domínio Azure AD ou identificação do inquilino. Este parâmetro representa o atual nome de domínio/id de domínio Azure AD com o qual o Servidor Azure está ligado. Por exemplo, se o Azure Server estiver associado ao domínio Da Azure `contosotest.onmicrosoft.com` onde o utilizador `joe@contosodev.onmicrosoft.com` está hospedado como utilizador importado do domínio Azure AD `contosodev.onmicrosoft.com`, o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com`. Quando o utilizador é um utilizador nativo do Azure AD ligado ao Azure Server, e não é uma conta MSA, não é necessário qualquer nome de domínio ou ID de inquilino. Para introduzir o parâmetro (começando com a versão SSMS 17.2), na caixa de diálogo Connect to **Database,** complete a caixa de diálogo, selecionando Ative Directory - Universal com autenticação **MFA,** clique em **Opções,** complete a caixa de nome do **Utilizador** e clique no separador Propriedades de **Ligação** **contosotest.onmicrosoft.com.**  
   ![mfa-inquilino-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Se estiver a executar SSMS 18.x ou mais tarde, o nome de domínio ad ou id do inquilino já não é necessário para os utilizadores convidados porque 18.x ou mais tarde o reconhece automaticamente.

   ![mfa-inquilino-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Negócio da Azure AD para apoio às empresas   
Os utilizadores de AD Azure suportados para cenários Azure AD B2B como utilizadores convidados (ver [What is Azure B2B colaboração)](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)podem ligar-se à Base de Dados SQL e ao Azure Synapse apenas como parte de um grupo criado na atual AD Azure e mapeado manualmente usando a declaração de `CREATE USER` Transact-SQL numa determinada base de dados. Por exemplo, se `steve@gmail.com` for convidado para o Azure AD `contosotest` (com o domínio Azure Ad `contosotest.onmicrosoft.com`), um grupo Azure AD, como `usergroup` deve ser criado no Azure AD que contém o membro `steve@gmail.com`. Em seguida, este grupo deve ser criado para uma base de dados específica (isto é, MyDatabase) por administração Azure AD SQL ou Azure AD DBO executando uma declaração de `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` Transact-SQL. Após a criação do utilizador da base de dados, o utilizador `steve@gmail.com` pode iniciar sessão para `MyDatabase` utilizando a opção de autenticação SSMS `Active Directory – Universal with MFA support`. O grupo de utilizadores, por padrão, tem apenas a permissão de ligação e qualquer acesso adicional a dados que tenha de ser concedido da forma normal. Note que o utilizador `steve@gmail.com` como utilizador convidado deve verificar a caixa e adicionar o nome de domínio AD `contosotest.onmicrosoft.com` na caixa de diálogo SSMS **Connection Property.** O nome de **domínio AD ou** opção de ID do inquilino só é suportado para o Universal com opções de ligação MFA, caso contrário está acinzentado.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Limitações de autenticação universal para Base de Dados SQL e Synapse Azure
- SSMS e SqlPackage.exe são as únicas ferramentas atualmente ativadas para MFA através da Autenticação Universal do Diretório Ativo.
- A versão SSMS 17.2 suporta o acesso simultâneo de vários utilizadores utilizando a Autenticação Universal com MFA. As versões 17.0 e 17.1, restringiram um login para uma instância de SSMS utilizando a Autenticação Universal numa única conta de Diretório Ativo Azure. Para iniciar sessão como outra conta Azure AD, deve utilizar outra instância de SSMS. (Esta restrição está limitada à autenticação universal do Diretório Ativo; pode iniciar sessão em diferentes servidores utilizando a autenticação de palavra-passe do Diretório Ativo, Autenticação Integrada de Diretório Ativo ou Autenticação do Servidor SQL).
- O SSMS suporta a autenticação universal do Diretório Ativo para o Object Explorer, O Editor de Consulta e visualização da Loja de Consultas.
- A versão SSMS 17.2 fornece suporte ao Assistente DacFx para base de dados de dados de exportação/extração/implementação. Uma vez autenticado um utilizador específico através do diálogo inicial de autenticação utilizando a Autenticação Universal, o Assistente DacFx funciona da mesma forma que funciona para todos os outros métodos de autenticação.
- O Designer de Mesa SSMS não suporta a Autenticação Universal.
- Não existem requisitos adicionais de software para a Autenticação Universal do Diretório Ativo, exceto que deve utilizar uma versão suportada do SSMS.  
- The Active Directory Authentication Library (ADAL) version for Universal authentication was updated to its latest ADAL.dll 3.13.9 available released version. Consulte a Biblioteca de Autenticação do [Diretório Ativo 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Passos seguintes

- Para os passos de configuração, consulte a [autenticação multifactor de base de dados Configure Azure SQL para o Estúdio de Gestão de Servidores SQL](sql-database-ssms-mfa-authentication-configure.md).
- Conceda a outros acesso à sua base de dados: Autenticação e Autorização da Base de [Dados SQL: Acesso de Concessão](sql-database-manage-logins.md)  
- Certifique-se de que outros podem ligar através da firewall: Configure uma regra de firewall de nível de servidor de base [de dados Azure SQL utilizando o portal Azure](sql-database-configure-firewall-settings.md)  
- [Configure e gerea autenticação de diretório ativo Azure com Base de Dados SQL ou Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importar um ficheiro BACPAC para uma nova Base de Dados SQL do Azure](../sql-database/sql-database-import.md)  
- [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../sql-database/sql-database-export.md)  
- C#[interface IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Ao utilizar **o Diretório Activo- Universal com** autenticação MFA, o rastreio da ADAL está disponível a partir do [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desligado por defeito, pode ligar o rastreio ADAL utilizando o menu **Tools**, **Options,** em **Serviços Azure,** **Nuvem Azure,** Nível de Traço de Janela de **Saída ADAL,** seguido de ativação **da saída** no menu **'Ver'.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.  
