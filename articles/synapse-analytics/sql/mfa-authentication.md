---
title: Utilização da autenticação AAD multifactor
description: Ligações de suporte Synapse SQL do SQL Server Management Studio (SSMS) utilizando a Autenticação Universal do Diretório Ativo.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424924"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Utilização da autenticação AAD multifactor com Synapse SQL (suporte SSMS para MFA)

Ligações de suporte Synapse SQL do SQL Server Management Studio (SSMS) utilizando a autenticação universal do *Diretório Ativo*. 

Este artigo discute as diferenças entre as várias opções de autenticação, bem como as limitações associadas à utilização da Autenticação Universal. 

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, a partir do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, a partir do [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Para todas as funcionalidades discutidas neste artigo, utilize pelo menos julho de 2017, versão 17.2.  A mais recente caixa de diálogo de ligação deve ser semelhante à seguinte imagem:

![1mfa-universal-connect](./media/mfa-authentication/1mfa-universal-connect.png "Completa a caixa de nomes do Utilizador.")  

## <a name="the-five-authentication-options"></a>As cinco opções de autenticação  

A Autenticação Universal do Diretório Ativo suporta os dois métodos de autenticação não interativos:
    - `Active Directory - Password`autenticação
    - `Active Directory - Integrated`autenticação

Existem também dois modelos de autenticação não interativa, que podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDCB, ODC, etc.). Estes dois métodos nunca resultam em caixas de diálogo pop-up:

- `Active Directory - Password`
- `Active Directory - Integrated`

O método interativo é que também suporta a autenticação multifactor Azure (MFA) é:

- `Active Directory - Universal with MFA`

A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Oferece uma autenticação forte com um leque de opções de verificação fáceis (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicação móvel), permitindo aos utilizadores escolherem o método que preferem. O MFA interativo com o Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multi-factor, consulte a [autenticação de vários fatores](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nome de domínio azure AD ou parâmetro de identificação do inquilino

Começando pela versão 17 do [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)os utilizadores que são importados para o atual Diretório Ativo de outros Diretórios Ativos do Azure como utilizadores convidados, podem fornecer o nome de domínio Azure AD, ou ID do inquilino quando se conectam. 

Os utilizadores convidados incluem utilizadores convidados de outros Anúncios Azure, contas da Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Esta informação permite que o **Diretório Ativo Universal com Autenticação MFA** identifique a autoridade autenticadora correta. Esta opção também é necessária para suportar contas da Microsoft (MSA) tais como contas outlook.com, hotmail.com, live.com ou não-MSA. 

Todos estes utilizadores que pretendam ser autenticados utilizando a Autenticação Universal devem introduzir o seu nome de domínio Azure AD ou identificação do inquilino. Este parâmetro representa o atual nome de domínio/id de domínio Azure AD com o qual o Servidor Azure está ligado. 

Por exemplo, se o Azure Server estiver `contosotest.onmicrosoft.com` associado `joe@contosodev.onmicrosoft.com` ao domínio Azure AD onde o `contosodev.onmicrosoft.com`utilizador está hospedado como utilizador importado `contosotest.onmicrosoft.com`do domínio AD Azure, o nome de domínio necessário para autenticar este utilizador é . 

Quando o utilizador é um utilizador nativo do Azure AD ligado ao Azure Server, e não é uma conta MSA, não é necessário qualquer nome de domínio ou ID de inquilino. 

Para introduzir o parâmetro (começando com a versão SSMS 17.2), na caixa de diálogo **Connect to Database,** complete a caixa de diálogo, selecionando Ative Directory - Universal com autenticação **MFA,** clique em **Opções,** complete a caixa de **nomedo utilizador** e clique no separador Propriedades de **Ligação.** 

Verifique o nome de **domínio AD ou** a caixa de identificação do inquilino e forneça autoridade autenticante, como o nome de domínio **(contosotest.onmicrosoft.com**) ou o GUIA do ID do inquilino.  

   ![mfa-inquilino-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Se estiver a executar SSMS 18.x ou mais tarde, o nome de domínio ad ou id do inquilino já não é necessário para os utilizadores convidados porque 18.x ou mais tarde o reconhece automaticamente.

   ![mfa-inquilino-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Negócio da Azure AD para apoio às empresas   
Os utilizadores da Azure AD suportados para cenários Azure AD B2B como utilizadores convidados (ver [What is Azure B2B collaboration](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) can connect to Synapse SQL only `CREATE USER` as members of a group created in current Azure AD and maped manually using the Transact-SQL statement in a given database. 

Por exemplo, `steve@gmail.com` se for convidado para `contosotest` a AD Azure `contosotest.onmicrosoft.com`(com o domínio Azure `usergroup` Ad), um grupo Azure `steve@gmail.com` AD, como deve ser criado no Azure AD que contém o membro. Em seguida, este grupo deve ser criado para uma base de dados específica (isto é, MyDatabase) por administração Azure `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` AD SQL ou Azure AD DBO executando uma declaração transact-SQL. 

Após a criação do utilizador `steve@gmail.com` da base `MyDatabase` de dados, o utilizador `Active Directory – Universal with MFA support`pode iniciar sessão na utilização da opção de autenticação SSMS . 

O grupo de utilizadores, por padrão, tem apenas a permissão de ligação e qualquer acesso adicional a dados que tenha de ser concedido da forma normal. 

Note que `steve@gmail.com` o utilizador como utilizador convidado deve verificar `contosotest.onmicrosoft.com` a caixa e adicionar o nome de domínio AD na caixa de diálogo SSMS **Connection Property.** O nome de **domínio AD ou** opção de ID do inquilino só é suportado para o Universal com opções de ligação MFA, caso contrário está acinzentado.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Limitações de autenticação universal para Synapse SQL

- SSMS e SqlPackage.exe são as únicas ferramentas atualmente ativadas para MFA através da Autenticação Universal do Diretório Ativo.
- A versão SSMS 17.2 suporta o acesso simultâneo de vários utilizadores utilizando a Autenticação Universal com MFA. As versões 17.0 e 17.1, restringiram um login para uma instância de SSMS utilizando a Autenticação Universal numa única conta de Diretório Ativo Azure. Para iniciar sessão como outra conta Azure AD, deve utilizar outra instância de SSMS. (Esta restrição está limitada à autenticação universal do Diretório Ativo; pode iniciar sessão em diferentes servidores utilizando a autenticação de palavra-passe do Diretório Ativo, Autenticação Integrada de Diretório Ativo ou Autenticação do Servidor SQL).
- O SSMS suporta a autenticação universal do Diretório Ativo para o Object Explorer, O Editor de Consulta e visualização da Loja de Consultas.
- A versão SSMS 17.2 fornece suporte ao Assistente DacFx para base de dados de dados de exportação/extração/implementação. Uma vez autenticado um utilizador específico através do diálogo inicial de autenticação utilizando a Autenticação Universal, o Assistente DacFx funciona da mesma forma que funciona para todos os outros métodos de autenticação.
- O Designer de Mesa SSMS não suporta a Autenticação Universal.
- Não existem requisitos adicionais de software para a Autenticação Universal do Diretório Ativo, exceto que deve utilizar uma versão suportada do SSMS.  
- The Active Directory Authentication Library (ADAL) version for Universal authentication was updated to its latest ADAL.dll 3.13.9 available released version. Consulte a Biblioteca de Autenticação do [Diretório Ativo 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte o [Connect to Synapse SQL com](get-started-ssms.md) o artigo do SQL Server Management Studio. 

