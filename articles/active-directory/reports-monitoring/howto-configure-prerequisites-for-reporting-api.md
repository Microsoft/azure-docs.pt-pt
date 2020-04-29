---
title: Pré-requisitos para o Diretório Ativo Azure reportando API [ Microsoft Docs
description: Conheça os pré-requisitos para aceder à AD Azure reportando API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991267"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para aceder ao Diretório Ativo Azure reportando API

As [APIs de relatórios Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs de linguagens e ferramentas de programação.

O relatório API usa [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) para autorizar o acesso às APIs web.

Para preparar o seu acesso à API reporte, precisa de:

1. [Atribuir funções](#assign-roles)
2. [Requisitos de Licença](#license-requirements)
3. [Registar uma aplicação](#register-an-application)
4. [Conceder permissões](#grant-permissions)
5. [Recolher configurações de configuração](#gather-configuration-settings)

## <a name="assign-roles"></a>Atribuir funções

Para ter acesso aos dados de reporte através da API, é necessário ter uma das seguintes funções atribuídas:

- Leitor de Segurança

- Administrador de Segurança

- Administrador Global

## <a name="license-requirements"></a>Requisitos de Licença

Para aceder aos relatórios de inscrição de um inquilino, um inquilino da Azure AD deve ter associado a licença Azure AD Premium. A licença Azure AD Premium P1 (ou superior) é necessária para aceder a relatórios de inscrição para qualquer inquilino da AD Azure. Alternativamente, se o tipo de diretório for Azure AD B2C, os relatórios de entrada são acessíveis através da API sem qualquer requisito adicional de licença. 


## <a name="register-an-application"></a>Registar uma aplicação

O registo é necessário mesmo que esteja a aceder à API de reporte usando um script. O registo dá-lhe um ID de **aplicação,** que é necessário para as chamadas de autorização e permite que o seu código receba fichas.

Para configurar o seu diretório para aceder à APi de informação da AD Azure, deve inscrever-se no [portal Azure](https://portal.azure.com) com uma conta de administrador azure que também seja membro do papel de diretório do **Administrador Global** no seu inquilino Azure AD.

> [!IMPORTANT]
> As aplicações com credenciais com privilégios de administrador podem ser muito poderosas, por isso, certifique-se de manter o ID da aplicação e credenciais secretas num local seguro.
> 

**Para registar um pedido de AD Azure:**

1. No [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory** a partir do painel de navegação esquerdo.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página de **Diretório Ativo Azure,** selecione registos de **Aplicações**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na página de registos da **App,** selecione **Novo registo**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A página **de Inscrição de uma Candidatura:**

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Na caixa de texto `Reporting API application` **Name,** escreva .

    b. Para o tipo de **contas suportadas,** selecione **Contas apenas neste organizacional**.

    c. No **URL redirecionamento** **selecione** Web textbox, escreva `https://localhost`.

    d. Selecione **Registar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API a que pretende aceder, tem de conceder à sua aplicação as seguintes permissões:  

| API | Permissão |
| --- | --- |
| Microsoft Azure Active Directory | Ler os dados do diretório |
| Microsoft Graph | Leia todos os dados de registo de auditoria |


![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A secção seguinte lista os passos para ambas as APIs. Se não quiser aceder a uma das APIs, pode saltar os passos relacionados.

**Para conceder permissões de pedido para utilizar as APIs:**


1. Selecione **permissões API** e, em seguida, **adicione uma permissão**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **página de permissões da API request**, localize o **legado de suporte API** **Azure Ative Directory Graphy**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na página de **permissões requeridas,** selecione **Permissões de aplicação,** expandir o Diretório da caixa de verificação **de Diretórios.ReadAll** . **Directory.ReadAll**  **Selecione Adicionar permissões**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na **página Reporting API Application - API Permissions,** selecione **Grant admin consent**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: O **Microsoft Graph** é adicionado por padrão durante o registo da API.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Recolher configurações de configuração 

Esta secção mostra-lhe como obter as seguintes definições do seu diretório:

- Nome de domínio
- ID de Cliente
- Segredo do cliente

Precisa destes valores ao configurar chamadas para a API de reporte. 

### <a name="get-your-domain-name"></a>Obtenha o seu nome de domínio

**Para obter o seu nome de domínio:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página do **Diretório Ativo Azure,** selecione nomes de **domínio personalizados**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copie o seu nome de domínio a partir da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha o ID do cliente da sua aplicação

**Para obter o ID do cliente da sua aplicação:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, clique no **Diretório Ativo Azure**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecione a sua aplicação na página de Registos de **Aplicações.**

3. A partir da página de aplicação, navegue para id de **aplicação** e selecione **Clique para copiar**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obtenha o segredo do cliente da sua aplicação
 Evite erros ao tentar aceder a registos de auditoria ou iniciar sessão utilizando a API.

**Para obter o segredo do cliente da sua aplicação:**

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, clique no **Diretório Ativo Azure**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecione a sua aplicação na página de Registos de **Aplicações.**

3.  Selecione **Certificados e Segredos** na página de **Aplicação API,** na secção Segredos do **Cliente,** clique **+ Novo Segredo de Cliente**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na página secreta adicionar uma página secreta do **cliente,** adicione:

    a. Na caixa de texto `Reporting API` **descrição,** escreva .

    b. Como **expira,** selecione **Em 2 anos**.

    c. Clique em **Guardar**.

    d. Copie o valor da chave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Erros de resolução de problemas na API de reporte

Esta secção lista as mensagens de erro comuns que pode encontrar ao aceder a relatórios de atividade utilizando a API do Microsoft Graph e passos para a sua resolução.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Erro: Falhou em obter funções de utilizador do Microsoft Graph

 Inscreva-se na sua conta utilizando ambos os botões de entrada no II do Graph Explorer para evitar um erro ao tentar iniciar sessão utilizando o Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Erro: Falhou na verificação da licença premium a partir do Microsoft Graph 

Se encontrar esta mensagem de erro enquanto tenta aceder a sessão utilizando o Graph Explorer, escolha **Modificar Permissões** por baixo da sua conta no navegano esquerdo e selecione **Tasks.ReadWrite** e **Diretório.Read.All**. 

![Modificar permissões UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: Inquilino não é B2C ou inquilino não tem licença premium

O acesso aos relatórios de entrada requer uma licença azure Ative Diretório premium 1 (P1). Se vir esta mensagem de erro ao aceder a inscrições, certifique-se de que o seu inquilino está licenciado com uma licença Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Erro: As funções permitidas não incluem o Utilizador. 

 Evite erros ao tentar aceder a registos de auditoria ou iniciar sessão utilizando a API. Certifique-se de que a sua conta faz parte do papel do Leitor de **Segurança** ou **do Leitor de Relatórios** no seu inquilino de Diretório Ativo Azure.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: Falha de aplicação AAD 'Ler dados de directório' 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: Falha de aplicação Microsoft Graph API 'Leia todos os dados de registo de auditoria'

Siga os passos nos [Pré-requisitos para aceder à API](howto-configure-prerequisites-for-reporting-api.md) de reporte ativo do Azure para garantir que a sua aplicação está a funcionar com o conjunto certo de permissões. 

## <a name="next-steps"></a>Passos seguintes

* [Obter dados com a API de relatórios do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência da Auditoria API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Relatório de atividade de inatividade referência API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
