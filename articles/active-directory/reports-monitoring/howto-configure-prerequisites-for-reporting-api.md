---
title: Pré-requisitos para acessar a API de relatório de Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os pré-requisitos para acessar a API de relatórios do Azure AD
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/30/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7b6fab4a4a36691bbdeb11975c7a93b97ab86cb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241633"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para acessar a API de relatório de Azure Active Directory

As [APIs de relatórios Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de ferramentas e linguagens de programação.

A API de relatório usa o [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) para autorizar o acesso às APIs da Web.

Para preparar seu acesso à API de relatórios, você precisa:

1. [Atribuir funções](#assign-roles)
2. [Registar uma aplicação](#register-an-application)
3. [Conceder permissões](#grant-permissions)
4. [Reunir definições de configuração](#gather-configuration-settings)

## <a name="assign-roles"></a>Atribuir funções

Para obter acesso aos dados de relatórios por meio da API, você precisa ter uma das seguintes funções atribuídas:

- Leitor de Segurança

- Administrador de Segurança

- Administrador Global


## <a name="register-an-application"></a>Registar uma aplicação

O registro é necessário mesmo se você estiver acessando a API de relatório usando um script. O registro fornece uma **ID de aplicativo**, que é necessária para as chamadas de autorização e permite que seu código receba tokens.

Para configurar seu diretório para acessar a API de relatórios do Azure AD, você deve entrar no [portal do Azure](https://portal.azure.com) com uma conta de administrador do Azure que também seja membro da função de diretório de **administrador global** em seu locatário do Azure AD.

> [!IMPORTANT]
> Os aplicativos executados sob as credenciais com privilégios de administrador podem ser muito poderosos, portanto, certifique-se de manter as credenciais de ID e segredo do aplicativo em um local seguro.
> 

**Para registrar um aplicativo do Azure AD:**

1. Na [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** no painel de navegação à esquerda.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página **Azure Active Directory** , selecione **registros de aplicativo**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na página **registros de aplicativo** , selecione **novo registro**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A página **registro de um aplicativo** :

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Na caixa de texto **nome** , `Reporting API application`digite.

    b. Para **tipo de contas com suporte**, selecione **contas somente nesta organização**.

    c. Na caixa de texto selecionar URL de `https://localhost`redirecionamento, digite.

    d. Selecione **Registar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API que você deseja acessar, você precisa conceder ao seu aplicativo as seguintes permissões:  

| API | Permissão |
| --- | --- |
| Azure Active Directory do Windows | Ler dados do diretório |
| Microsoft Graph | Ler todos os dados do log de auditoria |


![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A seção a seguir lista as etapas para ambas as APIs. Se você não quiser acessar uma das APIs, poderá ignorar as etapas relacionadas.

**Para conceder permissões de aplicativo para usar as APIs:**


1. Selecione **permissões de API** e **adicione uma permissão**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **página solicitar permissões de API**, localize **suporte à api herdada** **Azure Active Directory grafo**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na página **permissões necessárias** , selecione **permissões de aplicativo**, expanda diretório caixa de seleção **diretório. ReadAll**.  Selecione **adicionar permissões**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na página **aplicativo de API de relatório – permissões de API** , selecione **conceder consentimento de administrador**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: O **Microsoft Graph** é adicionado por padrão durante o registro da API.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Reunir definições de configuração 

Esta seção mostra como obter as seguintes configurações de seu diretório:

- Nome de domínio
- ID de Cliente
- Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatório. 

### <a name="get-your-domain-name"></a>Obter seu nome de domínio

**Para obter o nome de domínio:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página **Azure Active Directory** , selecione **nomes de domínio personalizados**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copie o nome de domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obter a ID do cliente do aplicativo

**Para obter a ID do cliente do aplicativo:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecione seu aplicativo na página **registros do aplicativo** .

3. Na página do aplicativo, navegue até **ID do aplicativo** e selecione **clique para copiar**.

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obter o segredo do cliente do aplicativo
 Evite erros ao tentar acessar logs de auditoria ou entrar usando a API.

**Para obter o segredo do cliente do aplicativo:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecione seu aplicativo na página **registros do aplicativo** .

3.  Selecione **certificados e segredos** na página **aplicativo de API** , na seção **segredos do cliente** , clique em **+ novo segredo do cliente**. 

    ![Registar aplicação](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na página **Adicionar um segredo do cliente** , adicione:

    a. Na caixa de texto **Descrição** , `Reporting API`digite.

    b. Como **expira**, selecione **em 2 anos**.

    c. Clique em **Guardar**.

    d. Copie o valor da chave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Solucionar erros na API de relatórios

Esta seção lista as mensagens de erro comuns que você pode encontrar ao acessar os relatórios de atividade usando o MS API do Graph e as etapas para sua resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>erro de servidor interno HTTP 500 ao acessar o ponto de extremidade Microsoft Graph v2

No momento, não há suporte para o ponto de extremidade Microsoft Graph v2-certifique-se de acessar os logs de atividade usando o ponto de extremidade Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Erro: Falha ao obter funções de usuário do AD Graph

 Entre em sua conta usando os botões de entrada na interface do usuário do Graph Explorer para evitar obter um erro ao tentar entrar usando o explorador do Graph. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Erro: Falha ao fazer a verificação de licença Premium do grafo do AD 

Se você encontrar essa mensagem de erro ao tentar acessar entradas usando o Graph Explorer, escolha **Modificar permissões** abaixo de sua conta na barra de navegação à esquerda e selecione **tarefas. ReadWrite** e **Directory. Read. All**. 

![Modificar a interface do usuário de permissões](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: O locatário não é B2C ou o locatário não tem licença Premium

O acesso a relatórios de entrada requer uma licença Azure Active Directory Premium 1 (P1). Se você vir essa mensagem de erro ao acessar entradas, certifique-se de que seu locatário esteja licenciado com uma licença do Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Erro: As funções permitidas não incluem usuário. 

 Evite erros ao tentar acessar logs de auditoria ou entrar usando a API. Verifique se sua conta faz parte da função **leitor de segurança** ou **leitor de relatório** em seu locatário Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: O aplicativo não tem a permissão ' ler dados do diretório ' do AAD 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Erro: O aplicativo não possui a permissão ' ler todos os dados de log de auditoria ' da API MSGraph

Siga as etapas em [pré-requisitos para acessar a API de relatório de Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja sendo executado com o conjunto certo de permissões. 

## <a name="next-steps"></a>Passos seguintes

* [Obter dados usando a API de relatório de Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência de API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
