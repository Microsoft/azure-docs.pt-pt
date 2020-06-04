---
title: Utilização de autenticação de diretório ativo Azure multi-factor
description: Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics suportam ligações do SQL Server Management Studio (SSMS) utilizando a Autenticação Universal do Diretório Ativo.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 607644a67fad966be25915e55a1bec83d70a1fe0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344820"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Utilização de autenticação de diretório ativo Azure multi-factor
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure Managed Instance e Azure Synapse Analytics suportam ligações do SQL Server Management Studio (SSMS) utilizando *o Azure Ative Directory - Universal com* autenticação MFA. Este artigo discute as diferenças entre as várias opções de autenticação, bem como as limitações associadas à utilização da Autenticação Universal.

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

Para todas as funcionalidades discutidas neste artigo, use pelo menos julho de 2017, versão 17.2. A mais recente caixa de diálogo de ligação deve ser semelhante à seguinte imagem:

  ![1mfa-universal-connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Completa a caixa de nome do utilizador.")  

## <a name="the-five-authentication-options"></a>As cinco opções de autenticação  

O Diretório Ativo Universal Authentication suporta os dois métodos de autenticação não interativo:
    - `Azure Active Directory - Password`autenticação
    - `Azure Active Directory - Integrated`autenticação

Existem também dois modelos de autenticação não interativa, que podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDCB, ODC, etc.). Estes dois métodos nunca resultam em caixas de diálogo pop-up:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

O método interativo que também suporta a autenticação multi-factor Azure (MFA) é:`Active Directory - Universal with MFA`

A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Oferece uma autenticação forte com um leque de opções de verificação fáceis (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis), permitindo que os utilizadores escolham o método que preferem. O MFA interativo com Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multi-factor Azure, consulte [a autenticação multi-factor](../../active-directory/authentication/multi-factor-authentication.md).
Para etapas de configuração, consulte a [autenticação multi-factor Configure Azure SQL Database para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nome de domínio AD azul ou parâmetro de identificação do inquilino

Começando pela [versão SSMS 17,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)os utilizadores que são importados para o atual Diretório Ativo de outros Diretórios Azure Ative como utilizadores convidados, podem fornecer o nome de domínio AD Azure ou ID do inquilino quando se conectam. Os utilizadores convidados incluem utilizadores convidados de outros ADs Azure, contas da Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Esta informação permite ao **Ative Directory Universal com Autenticação MFA** identificar a autoridade de autenticação correta. Esta opção também é necessária para suportar contas microsoft (MSA), tais como contas outlook.com, hotmail.com, live.com ou não MSA. Todos estes utilizadores que pretendam ser autenticados através da Autenticação Universal devem introduzir o seu nome de domínio AZure AD ou ID do inquilino. Este parâmetro representa o nome de domínio AD azure atual ou iD do inquilino, o Azure Server está ligado. Por exemplo, se o Azure Server estiver associado ao domínio Azure AD `contosotest.onmicrosoft.com` onde o utilizador é hospedado como um utilizador importado do domínio `joe@contosodev.onmicrosoft.com` AD AZure, `contosodev.onmicrosoft.com` o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com` . Quando o utilizador é um utilizador nativo do AD Azure ligado ao Azure Server, e não é uma conta MSA, não é necessário nenhum nome de domínio ou ID do inquilino. Para introduzir o parâmetro (começando com a versão SSMS 17.2), na caixa de diálogo **'Ligar à Base de Dados',** completar a caixa de diálogo, selecionar o Ative Directory - Universal com autenticação **MFA,** clicar **em Opções,** completar a caixa **de nome de Utilizador** e, em seguida, clicar no separador Connection **Properties.** Consulte o **nome de domínio AD ou** a caixa de identificação do inquilino e forneça autoridade autenticadora, como o nome de domínio (**contosotest.onmicrosoft.com)** ou o GUIA do ID do arrendatário.  
   ![mfa-inquilino-ssms](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Se estiver a executar SSMS 18.x ou mais tarde, o nome de domínio AD ou iD do inquilino já não é necessário para os utilizadores convidados porque 18.x ou mais tarde o reconhece automaticamente.

   ![mfa-inquilino-ssms](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Negócio da AD AZure para apoio ao negócio

Os utilizadores AZure AD que são suportados para cenários Azure AD B2B como utilizadores convidados (ver [What is Azure B2B collaboration)](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)podem ligar-se à SQL Database e ao Azure Synapse apenas como parte de membros de um grupo criado no Azure AD atual e mapeado manualmente usando a declaração Transact-SQL `CREATE USER` numa determinada base de dados. Por exemplo, se `steve@gmail.com` for convidado para a Azure AD `contosotest` (com o domínio Azure `contosotest.onmicrosoft.com` AD), um grupo AD Azure, tal como `usergroup` deve ser criado no AD Azure que contém o `steve@gmail.com` membro. Em seguida, este grupo deve ser criado para uma base de dados específica (isto é, MyDatabase) por Azure AD SQL ou Azure AD DBO executando uma declaração Transact-SQL. `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` Após a criação do utilizador da base de dados, o utilizador `steve@gmail.com` pode iniciar sessão na `MyDatabase` opção de autenticação SSMS `Active Directory – Universal with MFA support` . O grupo de utilizador, por padrão, tem apenas a permissão de ligação e qualquer acesso adicional de dados que terá de ser concedido da forma normal. Note que o utilizador `steve@gmail.com` como utilizador convidado deve verificar a caixa e adicionar o nome de domínio AD `contosotest.onmicrosoft.com` na caixa de diálogo SSMS **Connection Property.** O **nome de domínio AD ou** a opção de ID do inquilino só é suportado para o Universal com opções de conexão MFA, caso contrário, está acinzentado.

## <a name="universal-authentication-limitations"></a>Limitações de autenticação universal

- SSMS e SqlPackage.exe são as únicas ferramentas atualmente ativadas para MFA através da Autenticação Universal do Diretório Ativo.
- A versão SSMS 17.2 suporta o acesso simultâneo a vários utilizadores utilizando a Autenticação Universal com MFA. A versão 17.0 e 17.1, restringe um login para um exemplo de SSMS utilizando a Autenticação Universal para uma única conta do Azure Ative Directory. Para iniciar sessão como outra conta Azure AD, deve utilizar outra instância de SSMS. (Esta restrição está limitada à Autenticação Universal do Diretório Ativo; pode iniciar sessão em diferentes servidores utilizando autenticação de passwords de direção ativa, autenticação integrada do Diretório ativo ou autenticação do servidor SQL).
- O SSMS suporta a autenticação universal do Diretório Ativo para explorador de objetos, editor de consulta e visualização da Loja de Consultas.
- A versão SSMS 17.2 fornece suporte do Assistente DacFx para base de dados de exportação/extração/implantação de dados. Uma vez que um utilizador específico é autenticado através do diálogo de autenticação inicial utilizando a Autenticação Universal, o Assistente DacFx funciona da mesma forma que funciona para todos os outros métodos de autenticação.
- O SSMS Table Designer não suporta a Autenticação Universal.
- Não existem requisitos de software adicionais para a Autenticação Universal do Diretório Ativo, exceto que deve utilizar uma versão suportada do SSMS.  
- A versão Ative Directory Authentication Library (ADAL) para autenticação Universal foi atualizada para a sua versão mais recente, ADAL.dll 3.13.9 disponível. Consulte a Biblioteca de Autenticação do [Diretório Ativo 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Próximos passos

- Para etapas de configuração, consulte a [autenticação multi-factor Configure Azure SQL Database para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Concede acesso à sua base de dados: [Autenticação e Autorização de Dados sql: Concessão de Acesso](logins-create-manage.md)  
- Certifique-se de que outros podem ligar através da firewall: [Configurar uma regra de firewall ao nível do servidor utilizando o portal Azure](firewall-configure.md)  
- [Configurar e gerir a autenticação do Azure Ative Directory com base de dados SQL ou Azure Synapse](authentication-aad-configure.md)  
- [Quadro de aplicações de nível de dados do servidor do Microsoft SQL (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importar um ficheiro BACPAC para uma nova base de dados](database-import.md)  
- [Exportar uma base de dados para um ficheiro BACPAC](database-export.md)  
- Interface C# [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Ao utilizar **o Ative Directory- Universal com** autenticação MFA, o rastreio ADAL está disponível a partir de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desligado por predefinição, pode ligar o rastreio ADAL utilizando o menu **Ferramentas**, **Opções,** em **Serviços Azure**, **Azure Cloud,** **Nível de Rastreio da Janela de Saída ADAL,** seguido de ativar a **saída** no menu **Ver.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.  
