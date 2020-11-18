---
title: Utilização de autenticação de diretório ativo Azure multi-factor
description: Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics suportam ligações do SQL Server Management Studio (SSMS) utilizando a Autenticação Universal do Diretório Ativo.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841136"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Utilização de autenticação de diretório ativo Azure multi-factor
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics suportam ligações do [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) utilizando *o Azure Ative Directory - Universal com* autenticação MFA. Este artigo discute as diferenças entre as várias opções de autenticação, bem como as limitações associadas à utilização da Autenticação Universal no Diretório Ativo Azure (Azure AD) para o Azure SQL.

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, do [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Para todas as funcionalidades discutidas neste artigo, use pelo menos julho de 2017, versão 17.2. A mais recente caixa de diálogo de ligação deve ser semelhante à seguinte imagem:

  ![Screenshot do diálogo 'Connect to Server' no SQL Server Management Studio, mostrando as definições para o tipo de servidor, nome do servidor e autenticação.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Opções de autenticação

Existem dois modelos de autenticação não interativa para a Azure AD, que podem ser usados em muitas aplicações diferentes (ADO.NET, JDCB, ODC, e assim por diante). Estes dois métodos nunca resultam em caixas de diálogo pop-up:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

O método interativo que também suporta a autenticação multi-factor Azure AD (MFA) é: 

- `Azure Active Directory - Universal with MFA`

O Azure AD MFA ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um simples processo de inscrição. Oferece uma autenticação forte com um leque de opções de verificação fáceis (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis), permitindo que os utilizadores escolham o método que preferem. O MFA interativo com Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multi-factor Azure AD, consulte [a autenticação multi-factor](../../active-directory/authentication/concept-mfa-howitworks.md).
Para etapas de configuração, consulte a [autenticação multi-factor Configure Azure SQL Database para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nome de domínio AD azul ou parâmetro de identificação do inquilino

Começando pela [versão SSMS 17,](/sql/ssms/download-sql-server-management-studio-ssms)os utilizadores que são importados para o atual AD Azure de outros Azure Ative Directies como utilizadores convidados, podem fornecer o nome de domínio AD Azure ou ID do inquilino quando se conectam. Os utilizadores convidados incluem utilizadores convidados de outros ADs Azure, contas da Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Esta informação permite que `Azure Active Directory - Universal with MFA` a autenticação identifique a autoridade de autenticação correta. Esta opção também é necessária para suportar contas microsoft (MSA), tais como contas outlook.com, hotmail.com, live.com ou não MSA. 

Todos os utilizadores convidados que pretendam ser autenticados através da Autenticação Universal devem introduzir o seu nome de domínio AZure AD ou ID do inquilino. Este parâmetro representa o nome de domínio AD azure atual ou iD do inquilino que o servidor lógico Azure SQL está associado. Por exemplo, se o servidor lógico SQL estiver associado ao domínio Azure AD, onde o `contosotest.onmicrosoft.com` utilizador é hospedado como utilizador importado do domínio `joe@contosodev.onmicrosoft.com` AD Azure, `contosodev.onmicrosoft.com` o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com` . Quando o utilizador é um utilizador nativo do AD Azure associado ao servidor lógico SQL, e não é uma conta MSA, não é necessário nenhum nome de domínio ou ID do inquilino. Para introduzir o parâmetro (começando com a versão SSMS 17.2):


1. Abra uma ligação em SSMS. Insira o nome do seu servidor e selecione **Azure Ative Directory - Universal com** autenticação MFA. Adicione o **nome de Utilizador** com o que pretende iniciar sins.
1. Selecione a caixa **Opções** e vá até ao separador **Propriedades de Ligação.** Na caixa de diálogo **'Ligar à base de dados',** preencha a caixa de diálogo para a sua base de dados. Verifique o **nome de domínio da AD ou** a caixa de identificação do inquilino, e forneça autoridade autenticante, como o nome de domínio **(contosotest.onmicrosoft.com)** ou o GUIADOr do ID do inquilino. 

   ![Screenshot do separador Connection Properties realçando as definições de Connect para base de dados e nome de domínio AD ou ID do inquilino.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Se estiver a executar SSMS 18.x ou mais tarde, o nome de domínio AD ou iD do inquilino já não é necessário para os utilizadores convidados porque 18.x ou mais tarde o reconhece automaticamente.

   ![A screenshot do separador 'Propriedades de Ligação' no diálogo 'Ligar ao Servidor' em S S M. S. "MyDatabase" é selecionada no campo 'Ligar à base de dados'.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Negócio da AD AZure para apoio ao negócio

> [!IMPORTANT]
> O suporte para os utilizadores convidados ligarem-se à Base de Dados Azure SQL, à SqL Managed Instance e ao Azure Synapse sem a necessidade de fazerem parte de um grupo está atualmente em **pré-visualização pública.** Para obter mais informações, consulte [os utilizadores convidados create AD da Create Ezure e definido como um administrador AD Azure](authentication-aad-guest-users.md).

Os utilizadores AZure AD que são suportados para cenários Azure AD B2B como utilizadores convidados (ver [What is Azure B2B collaboration)](../../active-directory/external-identities/what-is-b2b.md)podem ligar-se à SQL Database e a Azure Synapse apenas como parte de membros de um grupo criado no Azure AD associado, e mapeado manualmente usando a declaração [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) numa determinada base de dados. Por exemplo, se `steve@gmail.com` for convidado para a Azure AD `contosotest` (com o domínio Azure `contosotest.onmicrosoft.com` AD), um grupo AD Azure, tal como `usergroup` deve ser criado no AD Azure que contém o `steve@gmail.com` membro. Em seguida, este grupo deve ser criado para uma base de dados específica (por exemplo, `MyDatabase` ) por um administrador AD SQL Azure ou Azure AD DBO, executando a declaração Transact-SQL. `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 

Após a criação do utilizador da base de dados, o utilizador `steve@gmail.com` pode iniciar sômus na `MyDatabase` utilização da opção de autenticação SSMS `Azure Active Directory – Universal with MFA` . Por predefinição, a `usergroup` permissão de ligação tem apenas. Qualquer acesso adicional a dados terá de ser [concedido](/sql/t-sql/statements/grant-transact-sql) na base de dados por um utilizador com privilégio suficiente. 

> [!NOTE]
> Para SSMS 17.x, utilizando `steve@gmail.com` como utilizador convidado, deve verificar o nome de **domínio AD ou** a caixa de identificação do inquilino e adicionar o nome de domínio AD `contosotest.onmicrosoft.com` na caixa de diálogo Connection **Property.** O **nome de domínio AD ou** a opção de ID do inquilino só são suportados para o **Diretório Ativo Azure - Universal com** autenticação MFA. Caso contrário, a caixa de verificação está acinzentado.

## <a name="universal-authentication-limitations"></a>Limitações de autenticação universal

- SSMS e SqlPackage.exe são as únicas ferramentas atualmente ativadas para MFA através da Autenticação Universal do Diretório Ativo.
- A versão SSMS 17.2 suporta o acesso simultâneo a vários utilizadores utilizando a Autenticação Universal com MFA. Para as versões SSMS 17.0 e 17.1, a ferramenta restringe um login para um caso de SSMS que utiliza a Autenticação Universal para uma única conta do Azure Ative Directory. Para se inscrever como outra conta Azure AD, deve utilizar outra instância de SSMS. Esta restrição está limitada à Autenticação Universal do Diretório Ativo; pode iniciar sação num servidor diferente utilizando `Azure Active Directory - Password` `Azure Active Directory - Integrated` autenticação, autenticação ou `SQL Server Authentication` .
- O SSMS suporta a autenticação universal do Diretório Ativo para explorador de objetos, editor de consulta e visualização da Loja de Consultas.
- A versão SSMS 17.2 fornece suporte do Assistente DacFx para base de dados de exportação/extração/implantação de dados. Uma vez que um utilizador específico é autenticado através do diálogo de autenticação inicial utilizando a Autenticação Universal, o Assistente DacFx funciona da mesma forma que funciona para todos os outros métodos de autenticação.
- O SSMS Table Designer não suporta a Autenticação Universal.
- Não existem requisitos de software adicionais para a Autenticação Universal do Diretório Ativo, exceto que deve utilizar uma versão suportada do SSMS.  
- Consulte o seguinte link para a mais recente versão da Ative Directory Authentication Library (ADAL) para autenticação universal: [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Passos seguintes

- Para etapas de configuração, consulte a [autenticação multi-factor Configure Azure SQL Database para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Concede acesso à sua base de dados: [Autenticação e Autorização de Dados sql: Concessão de Acesso](logins-create-manage.md)  
- Certifique-se de que outros podem ligar através da firewall: [Configurar uma regra de firewall ao nível do servidor utilizando o portal Azure](firewall-configure.md)  
- [Configurar e gerir a autenticação do Azure Ative Directory com base de dados SQL ou Azure Synapse](authentication-aad-configure.md)
- [Criar utilizadores convidados do Azure AD e definir como administrador do Azure AD](authentication-aad-guest-users.md) 
- [Quadro de Data-Tier de aplicação do Microsoft SQL Server (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Importar um ficheiro BACPAC para uma nova base de dados](database-import.md)  
- [Exportar uma base de dados para um ficheiro BACPAC](database-export.md)  
- Interface C# [IUniversalAuthProvider Interface](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- Ao utilizar **o Azure Ative Directory- Universal com** autenticação MFA, o rastreio ADAL está disponível a partir de [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms). Desligado por predefinição, pode ligar o rastreio ADAL utilizando o menu **Ferramentas**, **Opções,** em **Serviços Azure**, **Azure Cloud,** **Nível de Rastreio da Janela de Saída ADAL,** seguido de ativar a **saída** no menu **Ver.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.