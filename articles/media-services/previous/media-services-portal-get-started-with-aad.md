---
title: Começar com a autenticação da AD Azure utilizando o portal Azure Microsoft Docs
description: Saiba como utilizar o portal Azure para aceder à autenticação azure Ative Directory (Azure AD) para consumir a API azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162774"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Começar com a autenticação azure AD usando o portal Azure

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Saiba como utilizar o portal Azure para aceder à autenticação azure Ative Directory (Azure AD) para aceder à API azure Media Services.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece com um [teste gratuito azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
- Certifique-se de que revê a [API accessing Azure Media Services com visão geral da autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Quando utiliza a autenticação Azure AD com o Azure Media Services, tem duas opções de autenticação:

- **Autenticação do utilizador**. Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo. 
- **Autenticação do principal de serviço.** Autenticar um serviço. As aplicações que usam normalmente este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos programados: aplicações web, aplicações de função, aplicações lógicas, APIs ou um microserviço.

> [!IMPORTANT]
> Atualmente, a Media Services suporta o modelo de autenticação do serviço De controlo de acesso Azure. No entanto, a autorização de Controlo de Acesso será deprecada a 1 de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="select-the-authentication-method"></a>Selecione o método de autenticação

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta Media Services.
2. Selecione como ligar à API dos Serviços de Media.

    ![Selecione página de método de ligação](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticação de utilizador

Para se ligar à API dos Serviços de Media utilizando a opção de autenticação do utilizador, a aplicação do cliente necessita de solicitar um token Azure AD que tenha os seguintes parâmetros:  

* Ponto final do inquilino da AD Azure
* Recurso uri dos Serviços de Media
* Id do cliente de pedido de mídia (nativo) 
* Media Services (nativo) aplicação redirecionamento URI 
* Recursos URI para Serviços de Mídia REST

Pode obter os valores para estes parâmetros na API dos Serviços de Media com página de **autenticação do utilizador.** 

![Conecte-se com a página de autenticação do utilizador](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Se ligar à API dos Serviços de Media através do Media Services Microsoft .NET SDK, os valores necessários estão disponíveis para si como parte do SDK. Para mais informações, consulte [AUA de Utilização Para aceder à API azure Media Services com .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o Cliente SDK do Media Services .NET, tem de criar manualmente um pedido de ficha azure AD utilizando os parâmetros discutidos anteriormente. Para mais informações, consulte Como utilizar a Biblioteca de [Autenticação AD Azure para obter o token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para ligar à API dos Serviços de Media utilizando a opção principal de serviço, a sua aplicação de nível médio (Web API ou aplicação web) precisa de solicitar um token Azure AD que tenha os seguintes parâmetros:  

* Ponto final do inquilino da AD Azure
* Recurso uri dos Serviços de Media 
* Recursos URI para Serviços de Mídia REST
* Valores de aplicação Azure AD: o ID do **cliente** e **o segredo** do cliente

Pode obter os valores para estes parâmetros na **API Connect to Media Services com** página principal de serviço. Utilize esta página para criar uma nova aplicação Azure AD ou para selecionar uma existente. Depois de selecionar a aplicação Azure AD, pode obter o ID do cliente (ID de aplicação) e gerar os valores secretos (chave) do cliente. 

![Conecte-se com a página principal do serviço](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Quando a lâmina principal de **serviço** abrir, é selecionada a primeira aplicação Azure AD que satisfaça os seguintes critérios:

- É um pedido azure ad registado.
- Tem permissões de Controlo de Acesso Baseadas em Funções de Colaborador ou Proprietário na conta.

Depois de criar ou selecionar uma aplicação Azure AD, pode criar e copiar um segredo de cliente (chave) e o ID do cliente (ID de aplicação). O segredo do cliente e a identificação do cliente são necessários para obter o sinal de acesso neste cenário.

Se não tiver permissões para criar aplicações Azure AD no seu domínio, os controlos da aplicação Azure AD da lâmina não são mostrados e é apresentada uma mensagem de aviso.

Se ligar à API dos Media Services utilizando o Media Services .NET SDK, consulte a [autenticação AD Do Uso Azure para aceder à API azure Media Services com .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o Cliente SDK do Media Services .NET, deve criar manualmente um pedido de ficha adad Azure utilizando os parâmetros discutidos anteriormente. Para mais informações, consulte Como utilizar a Biblioteca de [Autenticação AD Azure para obter o token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obtenha a identificação do cliente e o segredo do cliente

Depois de selecionar uma aplicação Azure AD existente ou selecionar a opção de criar uma nova, aparecem os seguintes botões:

![Gerir o botão de permissões e gerir o botão de aplicação](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Para abrir a lâmina de aplicação Azure AD, clique em Gerir a **aplicação**. Na lâmina de **aplicação Manage,** pode obter o ID do cliente da aplicação (ID de aplicação). Para gerar um segredo de cliente (chave), selecione **Keys**.

![Gerir a opção chaves da lâmina de aplicação](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gerir permissões e o pedido

Depois de selecionar o pedido DeD Azure, pode gerir a aplicação e permissões. Para configurar a sua aplicação Azure AD para aceder a outras aplicações, clique em **Gerir permissões**. Para tarefas de gestão, tais como alterar chaves e responder URLs, ou editar o manifesto da aplicação, clique em Gerir a **aplicação**.

### <a name="edit-the-apps-settings-or-manifest"></a>Editar as definições da aplicação ou manifestar

Para editar as definições ou manifestar da aplicação, clique em Gerir a **aplicação**.

![Gerir página de candidatura](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a sua conta.](media-services-portal-upload-files.md)
