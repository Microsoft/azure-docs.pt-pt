---
title: Utilizar a autenticação do AAD de multi-factor com SQL Database do Azure e Azure SQL Data Warehouse | Documentos da Microsoft
description: Azure SQL Data Warehouse e base de dados SQL do Azure suportam ligações do SQL Server Management Studio (SSMS) com o Active Directory Universal Authentication.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494906"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Utilizar a autenticação do AAD de multi-factor com SQL Database do Azure e Azure SQL Data Warehouse (suporte do SSMS para a MFA)
Azure SQL Data Warehouse e base de dados SQL do Azure suportam ligações do SQL Server Management Studio (SSMS) usando *Active Directory Universal Authentication*. Este artigo aborda as diferenças entre as várias opções de autenticação e também as limitações associadas através da autenticação Universal. 

**Transferir o SSMS mais recente** – no computador cliente, transfira a versão mais recente do SSMS, da [baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Para todos os recursos discutidos neste artigo, utilize, pelo menos, Julho de 2017, versão 17,2.  A caixa de diálogo de ligação mais recente, deve ter um aspeto semelhante à seguinte imagem:
 
  ![Ligue-se 1mfa-universal](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "conclui a caixa de nome de utilizador.")  

## <a name="the-five-authentication-options"></a>As opções de cinco autenticação  

Active Directory Universal Authentication suporta os dois métodos de autenticação não interativa:
    - `Active Directory - Password` autenticação
    - `Active Directory - Integrated` autenticação

Há dois autenticação não interativa modelos, que podem ser usados em muitos aplicativos diferentes (ADO.NET, JDCB, ODC, etc.). Esses dois métodos nunca resultam em caixas de diálogo pop-up: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

O método interativo é que também suporta a multi-factor authentication (MFA) é: 
- `Active Directory - Universal with MFA` 


A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Proporciona uma autenticação segura com uma variedade de opções de verificação simples (telefonema, mensagem de texto, smart cards com pin ou notificação de aplicação móvel), permitindo aos utilizadores escolher o método que preferem. MFA interativa com o Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição de multi-factor Authentication, consulte [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Para obter os passos de configuração, consulte [configurar a base de dados de SQL do Azure multi-factor authentication para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>O Azure AD domínio inquilino ou nome do parâmetro ID   

A partir [SSMS versão 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), os utilizadores que são importados para o Active Directory atual de outros diretórios Active Directory do Azure como os utilizadores convidados, pode fornecer o nome de domínio do Azure AD ou ID de inquilino, quando se ligam. Os utilizadores convidados incluem usuários convidados a partir de outros anúncios do Azure, as contas Microsoft, como o outlook.com, hotmail.com, live.com ou outras contas, como o gmail.com. Estas informações, permite **Active Directory Universal com a autenticação de MFA** para identificar a autoridade de autenticação correta. Esta opção também é necessária para suportar as contas Microsoft (MSA), como o outlook.com, hotmail.com, live.com ou as contas não MSA. Todos esses usuários que desejam ser autenticado através de autenticação Universal tem de introduzir o respetivo nome de domínio do Azure AD ou ID de inquilino Este ID de inquilino/nome de domínio de representa o Azure AD atual de parâmetro o servidor do Azure está associado. Por exemplo, se o servidor do Azure está associado a domínio do Azure AD `contosotest.onmicrosoft.com` onde usuário `joe@contosodev.onmicrosoft.com` está hospedado como um usuário importado do domínio do Azure AD `contosodev.onmicrosoft.com`, o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com`. Quando o utilizador é um utilizador nativo do Azure AD ligada ao servidor do Azure e não uma conta MSA, nenhum ID de inquilino ou nome de domínio é necessário. Inserir o parâmetro (início com o SSMS versão 17,2), o **ligar à base de dados** caixa de diálogo caixa, preencha a caixa de diálogo Selecionar **Active Directory - Universal com a MFA** autenticação, clique em  **Opções**, complete o **nome de utilizador** caixa e, em seguida, clique no **propriedades de ligação** separador. Verifique os **ID de inquilino ou nome de domínio do AD** caixa e fornecer a autoridade de autenticação, tais como o nome de domínio (**contosotest.onmicrosoft.com**) ou o GUID do ID do inquilino.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Suporte de empresa-empresa do Azure AD   
Os utilizadores do AD do Azure suportados para cenários B2B do Azure AD, como os utilizadores convidados (consulte [o que é a colaboração B2B do Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) pode ligar-se a base de dados SQL e SQL Data Warehouse apenas como parte de membros de um grupo criado no Azure AD atual e mapeado manualmente com o Transact-SQL `CREATE USER` instrução num determinado banco de dados. Por exemplo, se `steve@gmail.com` é convidado para o Azure AD `contosotest` (com o domínio do Azure Ad `contosotest.onmicrosoft.com`), grupo de um Azure AD, como `usergroup` tem de ser criada no Azure AD que contém o `steve@gmail.com` membro. Em seguida, este grupo tem de ser criado uma base de dados específicos (ou seja, MyDatabase) ao administrador de SQL do Azure AD ou Azure AD DBO executando um Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instrução. Depois do utilizador de base de dados é criado, em seguida, o usuário `steve@gmail.com` pode iniciar sessão na `MyDatabase` utilizando a opção de autenticação do SSMS `Active Directory – Universal with MFA support`. Por predefinição, o grupo de utilizadores, tem apenas a permissão connect e qualquer acesso a dados adicional que terá de ser concedidas da forma normal. Tenha em atenção que o usuário `steve@gmail.com` como um utilizador convidado tem da caixa de verificação e adicione o nome de domínio do AD `contosotest.onmicrosoft.com` no SSMS **propriedade de ligação** caixa de diálogo. O **ID de inquilino ou nome de domínio do AD** opção só é suportada para o caminho Universal com opções de ligação do MFA, caso contrário, ele está esbatido.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações de autenticação universais para a base de dados SQL e SQL Data Warehouse
- SSMS e SqlPackage.exe são as ferramentas apenas atualmente ativadas para a MFA através do Active Directory Universal Authentication.
- SSMS versão 17,2, oferece suporte a acesso simultâneo de vários utilizador através da autenticação Universal com a MFA. Versão 17.0 e 17,1, restrito um início de sessão para uma instância do SSMS através da autenticação Universal para uma única conta do Azure Active Directory. Para iniciar sessão como outra conta do Azure AD, tem de utilizar outra instância do SSMS. (Esta restrição é limitada ao Active Directory Universal Authentication; pode iniciar sessão em servidores diferentes com autenticação de palavra-passe do Active Directory, com autenticação integrada do Active Directory ou autenticação do SQL Server).
- SSMS suporta o Active Directory Universal Authentication para visualização de Object Explorer, o Editor de consultas e Store de consulta.
- SSMS versão 17,2 fornece suporte de assistente DacFx para base de dados de exportação/extração/Deploy. Assim que um utilizador específico é autenticado por meio da caixa de diálogo de autenticação inicial através da autenticação Universal, as funções de assistente DacFx da mesma forma que faz para todos os outros métodos de autenticação.
- O Designer de tabela do SSMS não suporta a autenticação Universal.
- Não existem não requisitos de software adicionais para o Active Directory Universal Authentication, exceto pelo fato de que tem de utilizar uma versão suportada do SSMS.  
- A versão do Active Directory Authentication Library (ADAL) para autenticação Universal foi atualizada para a última versão lançada disponível adal 3.13.9. Ver [biblioteca de autenticação do Active Directory 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Passos Seguintes

- Para obter os passos de configuração, consulte [configurar a base de dados de SQL do Azure multi-factor authentication para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Outras pessoas conceda acesso à base de dados: [Autorização e autenticação de base de dados SQL: Conceder acesso](sql-database-manage-logins.md)  
- Certifique-se de que outras pessoas podem ligar-se através da firewall: [Configurar uma regra de firewall ao nível do servidor de base de dados do Azure SQL com o portal do Azure](sql-database-configure-firewall-settings.md)  
- [Configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 DG)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importar um ficheiro BACPAC para uma nova base de dados do Azure SQL](../sql-database/sql-database-import.md)  
- [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../sql-database/sql-database-export.md)  
- Interface da c# [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Ao usar **Active Directory - Universal com a MFA** autenticação, o rastreamento da ADAL é disponíveis a partir [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desativado por predefinição, pode ativar o rastreio da ADAL ao utilizar o **ferramentas**, **opções** menu, em **dos serviços do Azure**, **na Cloud do Azure**,  **O nível de rastreio de janela de saída da ADAL**, seguido pela ativação **saída** no **vista** menu. Os rastreios estão disponíveis na janela de saída ao selecionar **opção do Azure Active Directory**.  
