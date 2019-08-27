---
title: Usando a autenticação do AAD multifator com o banco de dados SQL do Azure e o Azure SQL Data Warehouse | Microsoft Docs
description: O banco de dados SQL do Azure e o Azure SQL Data Warehouse dão suporte a conexões do SQL Server Management Studio (SSMS) usando a autenticação universal do Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: c648e038cd063524aa2e69ed6d934519aa0e76e6
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019178"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Usando a autenticação do AAD multifator com o banco de dados SQL do Azure e o Azure SQL Data Warehouse (suporte do SSMS para MFA)
O banco de dados SQL do Azure e o Azure SQL Data Warehouse dão suporte a conexões do SQL Server Management Studio (SSMS) usando a *autenticação universal do Active Directory*. Este artigo discute as diferenças entre as várias opções de autenticação e também as limitações associadas ao uso da autenticação universal. 

**Baixe o SSMS mais recente** no computador cliente, baixe a versão mais recente do SSMS, do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Para todos os recursos discutidos neste artigo, use pelo menos julho de 2017, versão 17,2.  A caixa de diálogo de conexão mais recente deve ser semelhante à imagem a seguir:
 
  ![1mfa-universal-Connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Conclui a caixa nome de usuário.")  

## <a name="the-five-authentication-options"></a>As cinco opções de autenticação  

Active Directory autenticação universal dá suporte aos dois métodos de autenticação não interativa:
    - `Active Directory - Password`Authentication
    - `Active Directory - Integrated`Authentication

Há dois modelos de autenticação não interativos também, que podem ser usados em vários aplicativos diferentes (ADO.NET, JDCB, ODC, etc.). Esses dois métodos nunca resultam em caixas de diálogo pop-up: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

O método interativo também dá suporte à MFA (autenticação multifator do Azure): 
- `Active Directory - Universal with MFA` 


A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Ele fornece autenticação forte com uma variedade de opções de verificação fáceis (chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel), permitindo que os usuários escolham o método que preferirem. O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multifator, consulte [autenticação](../active-directory/authentication/multi-factor-authentication.md)multifator.
Para obter as etapas de configuração, consulte [configurar autenticação multifator do banco de dados SQL do Azure para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parâmetro de ID de locatário ou nome de domínio do Azure AD   

A partir do [SSMS versão 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), os usuários que são importados para o Active Directory atual de outros diretórios ativos do Azure como usuários convidados podem fornecer o nome de domínio do Azure ad ou a ID de locatário quando eles se conectam. Os usuários convidados incluem usuários convidados de outros anúncios do Azure, contas da Microsoft, como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Essas informações permitem que **Active Directory universal com a autenticação MFA** identifiquem a autoridade de autenticação correta. Essa opção também é necessária para dar suporte a contas da Microsoft (MSA), como outlook.com, hotmail.com, live.com ou contas não MSA. Todos esses usuários que desejam ser autenticados usando a autenticação universal devem inserir seu nome de domínio ou ID de locatário do Azure AD. Esse parâmetro representa a ID de locatário/nome de domínio do Azure AD atual à qual o servidor do Azure está vinculado. Por exemplo, se o servidor do Azure estiver associado ao domínio `contosotest.onmicrosoft.com` do Azure `joe@contosodev.onmicrosoft.com` AD em que o usuário está hospedado como um usuário `contosodev.onmicrosoft.com`importado do domínio do Azure AD, o `contosotest.onmicrosoft.com`nome de domínio necessário para autenticar esse usuário é. Quando o usuário é um usuário nativo do Azure AD vinculado ao servidor do Azure e não é uma conta MSA, nenhum nome de domínio ou ID de locatário é necessário. Para inserir o parâmetro (começando com o SSMS versão 17,2), na caixa de diálogo **conectar ao banco de dados** , complete a caixa de diálogo, selecionando **Active Directory-universal com autenticação MFA** , clique em **Opções**, complete o **nome de usuário** e, em seguida, clique na guia **Propriedades da conexão** . Verifique a caixa **nome de domínio do AD ou ID do locatário** e forneça a autoridade de autenticação, como o nome de domínio (**ContosoTest.onmicrosoft.com**) ou o GUID da ID do locatário.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Se você estiver executando o SSMS 18. x ou posterior, o nome de domínio do AD ou a ID do locatário não será mais necessário para usuários convidados, pois 18. x ou posterior o reconhecerá automaticamente.

   ![MFA-locatário-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Suporte de negócios do Azure AD para empresas   
Os usuários do Azure AD com suporte para cenários B2B do Azure AD como usuários convidados (consulte [o que é a colaboração do Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) podem se conectar ao banco de dados SQL e SQL data warehouse apenas como parte dos membros de um grupo criado no Azure ad atual e mapeados manualmente usando o TRANSACT-SQL `CREATE USER` instrução em um determinado banco de dados. Por exemplo, se `steve@gmail.com` o for convidado para o `contosotest` Azure AD (com o domínio `contosotest.onmicrosoft.com`do Azure AD) `usergroup` , um grupo do Azure AD, como deve ser criado no Azure AD que `steve@gmail.com` contém o membro. Em seguida, esse grupo deve ser criado para um banco de dados específico (ou seja, MyDatabase) pelo administrador do SQL do Azure ad ou pelo Azure ad dbo `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` executando uma instrução Transact-SQL. Depois que o usuário do banco de dados for criado `steve@gmail.com` , o usuário poderá `MyDatabase` fazer logon usando a opção `Active Directory – Universal with MFA support`de autenticação do SSMS. O UserGroup, por padrão, tem apenas a permissão Connect e qualquer acesso a dados adicional que precisará ser concedido da maneira normal. Observe que o `steve@gmail.com` usuário como um usuário convidado deve marcar a caixa e adicionar o nome `contosotest.onmicrosoft.com` de domínio do AD na caixa de diálogo **propriedade de conexão** do SSMS. A opção de **ID de locatário ou nome de domínio do AD** só tem suporte para as opções de conexão universal com MFA, caso contrário, ela fica esmaecida.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações de autenticação universal para o banco de dados SQL e SQL Data Warehouse
- O SSMS e o SqlPackage. exe são as únicas ferramentas atualmente habilitadas para MFA por meio de Active Directory autenticação universal.
- O SSMS versão 17,2 oferece suporte ao acesso simultâneo de vários usuários usando a autenticação universal com MFA. A versão 17,0 e 17,1, restringiu um logon para uma instância do SSMS usando a autenticação universal para uma única conta de Azure Active Directory. Para fazer logon como outra conta do Azure AD, você deve usar outra instância do SSMS. (Essa restrição é limitada a Active Directory autenticação universal; você pode fazer logon em servidores diferentes usando a autenticação de senha Active Directory, Active Directory autenticação integrada ou SQL Server autenticação).
- O SSMS dá suporte à autenticação universal Active Directory para pesquisador de objetos, editor de consultas e visualização de Repositório de Consultas.
- O SSMS versão 17,2 fornece suporte do assistente de DacFx para o banco de dados de exportação/extração/implantação. Depois que um usuário específico é autenticado por meio da caixa de diálogo de autenticação inicial usando a autenticação universal, o assistente de DacFx funciona da mesma maneira que faz para todos os outros métodos de autenticação.
- O Designer de Tabela do SSMS não oferece suporte à autenticação universal.
- Não há requisitos de software adicionais para Active Directory autenticação universal, exceto que você deve usar uma versão com suporte do SSMS.  
- A versão do Biblioteca de Autenticação do Active Directory (ADAL) para autenticação Universal foi atualizada para a versão de lançamento mais recente do ADAL. dll 3.13.9 disponível. Consulte [biblioteca de autenticação do Active Directory 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Passos seguintes

- Para obter as etapas de configuração, consulte [configurar autenticação multifator do banco de dados SQL do Azure para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Conceder a outros acesso ao seu banco de dados: [Autenticação e autorização do banco de dados SQL: Concedendo acesso](sql-database-manage-logins.md)  
- Verifique se outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor do banco de dados SQL do Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)  
- [Configurar e gerir a autenticação do Azure Active Directory com a Base de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importar um ficheiro BACPAC para uma nova Base de Dados SQL do Azure](../sql-database/sql-database-import.md)  
- [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../sql-database/sql-database-export.md)  
- C#[interface IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx) interface  
- Ao usar o **Active Directory-universal com** autenticação de MFA, o rastreamento de Adal está disponível a partir do [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento de ADAL usando o **menu ferramentas**, **Opções** , em **Serviços do Azure**, **nuvem do Azure**, **Adal janela de saída nível de rastreamento**, seguido por habilitar a **saída** no menu **Exibir** . Os rastreamentos estão disponíveis na janela saída ao selecionar **Azure Active Directory opção**.  
