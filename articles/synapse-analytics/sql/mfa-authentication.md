---
title: Utilização da autenticação AAD multi-factor
description: Ligações de suporte Sinaapse SQL do SQL Server Management Studio (SSMS) utilizando a Autenticação Universal do Diretório Ativo.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 7b3b8aae1345339dc34137550f3fe4c5be915ae5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672726"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Utilize a autenticação AAD multi-factor com sinaapse SQL (suporte SSMS para MFA)

Ligações de suporte Sinaapse SQL do SQL Server Management Studio (SSMS) utilizando *a Autenticação Universal do Diretório Ativo*. 

Este artigo discute as diferenças entre as várias opções de autenticação, bem como as limitações associadas à utilização da Autenticação Universal. 

**Descarregue o mais recente SSMS** - No computador cliente, descarregue a versão mais recente do SSMS, do [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true).

Para todas as funcionalidades discutidas neste artigo, use pelo menos julho de 2017, versão 17.2.  A mais recente caixa de diálogo de ligação deve ser semelhante à seguinte imagem:

![A screenshot mostra a caixa de diálogo Do Servidor, onde pode selecionar um nome de servidor e uma opção de autenticação.](./media/mfa-authentication/1mfa-universal-connect.png "Completa a caixa de nome do utilizador.")  

## <a name="the-five-authentication-options"></a>As cinco opções de autenticação  

O Diretório Ativo Universal Authentication suporta os dois métodos de autenticação não interativo:
    - `Active Directory - Password` autenticação
    - `Active Directory - Integrated` autenticação

Existem também dois modelos de autenticação não interativa, que podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDCB, ODC, etc.). Estes dois métodos nunca resultam em caixas de diálogo pop-up:

- `Active Directory - Password`
- `Active Directory - Integrated`

O método interativo é que também suporta a autenticação multi-factor Azure AD (MFA) é:

- `Active Directory - Universal with MFA`

A MFA do Azure AD ajuda a salvaguardar o acesso a dados e aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Oferece uma autenticação forte com um leque de opções de verificação fáceis (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis), permitindo que os utilizadores escolham o método que preferem. O MFA interativo com Azure AD pode resultar numa caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multi-factor, consulte [a autenticação multi-factor.](../../active-directory/authentication//concept-mfa-howitworks.md)

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nome de domínio AD azul ou parâmetro de identificação do inquilino

Começando pela [versão SSMS 17,](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true)os utilizadores que são importados para o atual Diretório Ativo de outros Diretórios Azure Ative como utilizadores convidados, podem fornecer o nome de domínio AD Azure ou ID do inquilino quando se conectam. 

Os utilizadores convidados incluem utilizadores convidados de outros ADs Azure, contas da Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Esta informação permite ao **Ative Directory Universal com Autenticação MFA** identificar a autoridade de autenticação correta. Esta opção também é necessária para suportar contas microsoft (MSA), tais como contas outlook.com, hotmail.com, live.com ou não MSA. 

Todos estes utilizadores que pretendam ser autenticados através da Autenticação Universal devem introduzir o seu nome de domínio AZure AD ou ID do inquilino. Este parâmetro representa o nome de domínio azure AD atual/ID inquilino com o qual o Servidor Azure está ligado. 

Por exemplo, se o Azure Server estiver associado ao domínio Azure AD `contosotest.onmicrosoft.com` onde o utilizador é hospedado como um utilizador importado do domínio `joe@contosodev.onmicrosoft.com` AD AZure, `contosodev.onmicrosoft.com` o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com` . 

Quando o utilizador é um utilizador nativo do AD Azure ligado ao Azure Server, e não é uma conta MSA, não é necessário nenhum nome de domínio ou ID do inquilino. 

Para introduzir o parâmetro (começando com a versão SSMS 17.2), na caixa de diálogo **'Ligar à Base de Dados',** completar a caixa de diálogo, selecionar o Ative Directory - Universal com autenticação **MFA,** selecionar **Opções,** completar a caixa **de nome de Utilizador** e, em seguida, selecionar o separador **'Propriedades de Ligação'.** 

Verifique o **nome de domínio da AD ou** a caixa de identificação do inquilino, e forneça autoridade autenticante, como o nome de domínio **(contosotest.onmicrosoft.com)** ou o GUIADOr do ID do inquilino.  

   ![A screenshot mostra 'Ligar ao Servidor' no separador Propriedades de Ligação com valores introduzidos.](./media/mfa-authentication/mfa-tenant-ssms.png)

Se estiver a executar SSMS 18.x ou mais tarde, então o nome de domínio AD ou iD do inquilino já não é necessário para os utilizadores convidados porque 18.x ou mais tarde automaticamente o reconhece.

   ![mfa-inquilino-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Negócio da AD AZure para apoio ao negócio   
Os utilizadores da AZure AD suportados por cenários Azure AD B2B como utilizadores convidados (ver [What is Azure B2B collaboration](../../active-directory/external-identities/what-is-b2b.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) can connect to Synapse SQL only as part of a group created in current Azure AD and mapeed manualmente using the Transact-SQL statement in a given `CREATE USER` database. 

Por exemplo, se `steve@gmail.com` for convidado para a Azure AD `contosotest` (com o domínio Azure `contosotest.onmicrosoft.com` Ad), um grupo AD Azure, tal como `usergroup` deve ser criado no AD Azure que contém o `steve@gmail.com` membro. Em seguida, este grupo deve ser criado para uma base de dados específica (isto é, MyDatabase) por Azure AD SQL ou Azure AD DBO executando uma declaração Transact-SQL. `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 

Após a criação do utilizador da base de dados, o utilizador `steve@gmail.com` pode iniciar sessão na `MyDatabase` opção de autenticação SSMS `Active Directory – Universal with MFA support` . 

O grupo de utilizador, por padrão, tem apenas a permissão de ligação e qualquer acesso adicional de dados que terá de ser concedido da forma normal. 

Como utilizador convidado, `steve@gmail.com` deve verificar a caixa e adicionar o nome de domínio AD `contosotest.onmicrosoft.com` na caixa de diálogo SSMS **Connection Property.** O **nome de domínio AD ou** a opção de ID do inquilino só é suportado para o Universal com opções de conexão MFA, caso contrário, está acinzentado.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Limitações de autenticação universal para sinapse SQL

- SSMS e SqlPackage.exe são as únicas ferramentas atualmente ativadas para MFA através da Autenticação Universal do Diretório Ativo.
- A versão SSMS 17.2 suporta o acesso simultâneo a vários utilizadores utilizando a Autenticação Universal com MFA. As versões 17.0 e 17.1, restringiram um login para uma instância de SSMS utilizando a Autenticação Universal para uma única conta do Azure Ative Directory. Para iniciar sessão como outra conta Azure AD, deve utilizar outra instância de SSMS. (Esta restrição está limitada à Autenticação Universal do Diretório Ativo; pode iniciar sessão em diferentes servidores utilizando autenticação de passwords de direção ativa, autenticação integrada do Diretório ativo ou autenticação do servidor SQL).
- O SSMS suporta a autenticação universal do Diretório Ativo para explorador de objetos, editor de consulta e visualização da Loja de Consultas.
- A versão SSMS 17.2 fornece suporte do Assistente DacFx para base de dados de exportação/extração/implantação de dados. Uma vez que um utilizador específico é autenticado através do diálogo de autenticação inicial utilizando a Autenticação Universal, o Assistente DacFx funciona da mesma forma que funciona para todos os outros métodos de autenticação.
- O SSMS Table Designer não suporta a Autenticação Universal.
- Não existem requisitos de software adicionais para a Autenticação Universal do Diretório Ativo, exceto que deve utilizar uma versão suportada do SSMS.  
- A versão Ative Directory Authentication Library (ADAL) para autenticação Universal foi atualizada para a sua versão mais recente ADAL.dll 3.13.9 disponível. Consulte a Biblioteca de Autenticação do [Diretório Ativo 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Connect to Synapse SQL com](get-started-ssms.md) o artigo do SQL Server Management Studio.