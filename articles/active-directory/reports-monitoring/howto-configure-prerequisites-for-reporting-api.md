---
title: Pré-requisitos para o Azure Ative Directy reportando API Microsoft Docs
description: Conheça os pré-requisitos para aceder à API de reporte da AZure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348743"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para aceder ao Diretório Ativo Azure reportando API

As [APIs de relatórios Azure Active Directory (Azure AD)](./concept-reporting-api.md) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs de linguagens e ferramentas de programação.

A API de reporte utiliza [a OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) para autorizar o acesso às APIs web.

Para preparar o seu acesso à API que reporta, precisa:

1. [Atribuir funções](#assign-roles)
2. [Requisitos de licença](#license-requirements)
3. [Registar uma aplicação](#register-an-application)
4. [Conceder permissões](#grant-permissions)
5. [Recolher configurações de configuração](#gather-configuration-settings)

## <a name="assign-roles"></a>Atribuir funções

Para ter acesso aos dados de reporte através da API, é necessário ter uma das seguintes funções atribuídas:

- Leitor de Segurança

- Administrador de Segurança

- Administrador Global

## <a name="license-requirements"></a>Requisitos de licença

Para aceder aos relatórios de inscrição para um inquilino, um inquilino da AD Azure deve ter associado a licença Azure AD Premium. A licença Azure AD Premium P1 (ou acima) é necessária para aceder a relatórios de inscrição para qualquer inquilino AZure AD. Alternativamente, se o tipo de diretório for Azure AD B2C, os relatórios de inscrição são acessíveis através da API sem qualquer requisito adicional de licença. 


## <a name="register-an-application"></a>Registar uma aplicação

O registo é necessário mesmo que esteja a aceder à API de reporte usando um script. O registo dá-lhe um **ID de inscrição,** que é necessário para as chamadas de autorização e permite que o seu código receba fichas.

Para configurar o seu diretório para aceder à API de reporte da Azure, tem de entrar no [portal Azure](https://portal.azure.com) com uma conta de administrador da Azure que também é membro do diretório **do Administrador Global** no seu inquilino AZure AD.

> [!IMPORTANT]
> As aplicações que executam com credenciais com privilégios de administrador podem ser muito poderosas, por isso, certifique-se de manter o ID da aplicação e credenciais secretas em um local seguro.
> 

**Para registar uma aplicação AD Azure:**

1. No [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory** a partir do painel de navegação à esquerda.
   
    ![O Screenshot mostra o Azure Ative Directory selecionado a partir do menu do portal Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página do **Diretório Ativo Azure,** selecione **registos de Aplicações**.

    ![O Screenshot mostra os registos de aplicações selecionados no menu Gerir.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na página de **registos** da App, selecione **Novo registo.**

    ![A screenshot mostra novo registo selecionado.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A **página de Inscrição de Um Pedido:**

    ![A screenshot mostra ao Registo uma página de aplicação onde pode introduzir os valores neste passo.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Na caixa de texto **'Nome',** escreva `Reporting API application` .

    b. Para **o tipo de contas suportadas**, selecione Contas **apenas nesta organização**.

    c. No **URL de redirecionamento**  selecione **web** textbox, escreva `https://localhost` .

    d. Selecione **Registar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API a que pretende aceder, tem de conceder à sua aplicação as seguintes permissões:  

| API | Permissão |
| --- | --- |
| Microsoft Azure Active Directory | Ler os dados do diretório |
| Microsoft Graph | Leia todos os dados do registo de auditoria |

![A screenshot mostra onde pode selecionar Adicione uma permissão no painel de permissões A P I.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A secção seguinte lista os passos para ambas as APIs. Se não quiser aceder a uma das APIs, pode saltar os passos relacionados.

**Para conceder permissões de aplicação para utilizar as APIs:**


1. Selecione **permissões API** e, em **seguida, adicione uma permissão**. 

    ![A screenshot mostra a página A P I Permissões onde pode selecionar Adicionar uma permissão.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na página de **permissões da API do Pedido**, localize **o suporte legado API** **Azure Ative Directory Graph**. 

    ![A screenshot mostra a página de permissões request A P I onde pode selecionar O Gráfico de Diretório Ativo Azure.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na página de **permissões necessárias,** selecione **Permissões de Aplicação**, expanda **o Diretório** da caixa de verificação do **Diretório.ReadAll**.  **Selecione Permissões de adicionar**.

    ![A screenshot mostra a página de permissões request A P I onde pode selecionar permissões de aplicação.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na **página De Relatórios API Application - API Permissões,** selecione **Grant admin consent**. 

    ![A screenshot mostra a página de permissões de aplicação P I Reporting A P I onde pode selecionar o consentimento de administração grant.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: **O Microsoft Graph** é adicionado por padrão durante o Registo de API.

    ![A screenshot mostra a página de permissões A P I onde pode selecionar Adicionar uma permissão.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Recolher configurações de configuração 

Esta secção mostra-lhe como obter as seguintes definições do seu diretório:

- Nome de domínio
- ID de Cliente
- Segredo do cliente

Você precisa destes valores ao configurar chamadas para a API reportando. 

### <a name="get-your-domain-name"></a>Obtenha o seu nome de domínio

**Para obter o seu nome de domínio:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.
   
    ![O Screenshot mostra o Azure Ative Directory selecionado a partir do menu do portal Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página do **Diretório Ativo Azure,** selecione **nomes de domínio personalizados**.

    ![A screenshot mostra nomes de domínio personalizados selecionados a partir do Azure Ative Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copie o seu nome de domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha o ID do seu cliente

**Para obter a iD do cliente da sua candidatura:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, clique no **Diretório Ativo Azure**.
   
    ![O Screenshot mostra o Azure Ative Directory selecionado a partir do menu do portal Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecione a sua candidatura a partir da página Registos de **Aplicações.**

3. A partir da página de aplicação, navegue para **O ID da aplicação** e selecione **Clique para copiar.**

    ![A screenshot mostra a página reporte a aplicação P I onde pode copiar a Aplicação I D.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obtenha o segredo do seu cliente
 Evite erros que tentem aceder a registos de auditoria ou de login utilizando a API.

**Para obter o segredo do cliente da sua candidatura:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, clique no **Diretório Ativo Azure**.
   
    ![O Screenshot mostra o Azure Ative Directory selecionado a partir do menu do portal Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecione a sua candidatura a partir da página Registos de **Aplicações.**

3.  Selecione **Certificados e Segredos** na página aplicação da **API,** na secção Segredos do **Cliente,** clique **em + Novo Segredo de Cliente**. 

    ![A screenshot mostra a página de segredos & Certificados onde pode adicionar um segredo de cliente.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na página secreta do **Add a Client,** adicione:

    a. Na caixa de texto **Descrição,** escreva `Reporting API` .

    b. Como **Expira**, selecione **Em 2 anos**.

    c. Clique em **Guardar**.

    d. Copie o valor da chave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Erros de resolução de problemas na API de reporte

Esta secção lista as mensagens de erro comuns que pode encontrar ao aceder a relatórios de atividade utilizando a API do Gráfico microsoft e passos para a sua resolução.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Erro: Não conseguiu obter as funções de utilizador do Microsoft Graph

 Inscreva-se na sua conta utilizando ambos os botões de entrada no UI do Graph Explorer para evitar um erro ao tentar iniciar sação usando o Graph Explorer. 

![Explorador de Gráficos](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Erro: Falhou em fazer verificação de licença premium a partir do Microsoft Graph 

Se encontrar esta mensagem de erro enquanto tenta aceder a insusões utilizando o Graph Explorer, escolha **Modificar permissões** por baixo da sua conta na navegação esquerda e selecione **Tarefas.ReadWrite** e **Diretório.Read.All**. 

![Modificar permissões UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: Inquilino não é B2C ou inquilino não tem licença premium

Aceder a relatórios de inscrição requer uma licença Azure Ative Directory premium 1 (P1). Se vir esta mensagem de erro ao aceder a insiná-lo, certifique-se de que o seu inquilino é licenciado com uma licença Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Erro: As funções permitidas não incluem o Utilizador. 

 Evite erros que tentem aceder a registos de auditoria ou de login utilizando a API. Certifique-se de que a sua conta faz parte do papel de Leitor de **Segurança** ou **Leitor de Relatórios** no seu inquilino Azure Ative Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: Aplicação em falta AAD 'Ler dados de directório' 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: Aplicação em falta da API do Gráfico do Microsoft 'Leia todos os dados do registo de auditoria'

Siga os passos nos [Pré-requisitos para aceder à API de reporte de relatórios Azure Ative](howto-configure-prerequisites-for-reporting-api.md) para garantir que a sua aplicação está a funcionar com o conjunto de permissões adequado. 

## <a name="next-steps"></a>Passos seguintes

* [Obter dados com a API de relatórios do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência de API de auditoria](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Referência de API do relatório de atividade de inscrição](/graph/api/resources/signin?view=graph-rest-beta)