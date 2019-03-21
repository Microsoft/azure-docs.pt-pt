---
title: Introdução à autenticação do Azure AD com o portal do Azure | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure para aceder a autenticação do Azure Active Directory (Azure AD) para consumir API de serviços de multimédia do Azure.
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
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258433"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD com o portal do Azure

Saiba como utilizar o portal do Azure para aceder a autenticação do Azure Active Directory (Azure AD) para acessar a API de serviços de multimédia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece com um [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).
- Certifique-se de que reveja os [aceder ao suporte de dados de API dos serviços Azure com a descrição geral da autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Quando utiliza a autenticação do Azure AD com os serviços de multimédia do Azure, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autenticar-se uma pessoa que está a utilizar a aplicação para interagir com os recursos de serviços de multimédia. O aplicativo interativo do primeiro deve pedir ao utilizador as credenciais. Um exemplo é uma aplicação de consola de gestão utilizada por utilizadores autorizados para monitorizar tarefas de codificação ou transmissão em direto. 
- **Autenticação do principal de serviço**. Autenticar-se um serviço. Os aplicativos geralmente usam este método de autenticação são aplicações que executem serviços de daemon, serviços de camada intermediária ou tarefas agendadas: aplicações web, aplicações de funções, aplicações lógicas, APIs ou um microsserviço.

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="select-the-authentication-method"></a>Selecione o método de autenticação

1. Na [portal do Azure](https://portal.azure.com/), selecione a sua conta de Media Services.
2. Selecione a forma de ligar à API de serviços de multimédia.

    ![Selecione a página de método de ligação](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticação de utilizador

Para ligar à API de serviços de multimédia do utilizando a opção de autenticação de utilizador, a aplicação cliente precisa de pedir um token do Azure AD que tenha os seguintes parâmetros:  

* Ponto final de inquilino do Azure AD
* URI do recurso de serviços de multimédia
* ID de cliente da aplicação de serviços de multimédia (nativo) 
* URI de redirecionamento da aplicação de serviços de multimédia (nativo) 
* URI para serviços de multimédia REST do recurso

Pode obter os valores para estes parâmetros **API de serviços de multimédia com a autenticação de utilizador** página. 

![Ligue-se a página de autenticação de utilizador](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Se ligar à API de serviços de multimédia com o SDK de .NET da Microsoft de serviços de suporte de dados, os valores necessários estão disponíveis para, como parte do SDK. Para obter mais informações, consulte [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o SDK de cliente .NET de serviços de suporte de dados, tem de criar manualmente um pedido de token do Azure AD com os parâmetros discutidos anteriormente. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para ligar à API de serviços de multimédia do utilizando a opção de principal de serviço, a sua aplicação de camada intermediária (web API ou aplicação web) precisa pedir um token do Azure AD que tenha os seguintes parâmetros:  

* Ponto final de inquilino do Azure AD
* URI do recurso de serviços de multimédia 
* URI para serviços de multimédia REST do recurso
* Valores de aplicações do Azure AD: os **ID de cliente** e **segredo do cliente**

Pode obter os valores para estes parâmetros **ligar à API de serviços de multimédia com o principal de serviço** página. Utilize esta página para criar uma nova aplicação do Azure AD ou selecionar um já existente. Depois de selecionar a aplicação do Azure AD, pode obter o ID de cliente (ID de aplicação) e gerar os valores de segredo (chave) de cliente. 

![Ligue-se a página principal de serviço](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Quando o **Principal de serviço** é aberto o painel, está selecionada a primeira aplicação do Azure AD que cumpre os seguintes critérios:

- É registado aplicação do Azure AD.
- Ele tem permissões de controlo de acesso de Owner Role-Based ou contribuinte na conta.

Depois de criar ou selecionar uma aplicação do Azure AD, pode criar e copie um segredo do cliente (chave) e o ID de cliente (ID de aplicação). É necessário o segredo do cliente e o ID de cliente para obter o acesso ao token neste cenário.

Se não tiver permissões para criar aplicações do Azure AD no seu domínio, não são apresentados os controlos de aplicação do Azure AD do painel e é apresentada uma mensagem de aviso.

Se ligar à API de serviços de multimédia do, utilizando o SDK .NET dos Media Services, consulte [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o SDK de cliente .NET de serviços de suporte de dados, tem de criar manualmente um pedido de token do Azure AD utilizando os parâmetros discutidos anteriormente. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obtenha o ID de cliente e segredo do cliente

Depois de selecionar uma aplicação do Azure AD existente ou selecione a opção para criar um novo, são apresentados os botões seguintes:

![Gerir permissões de botões e gerir aplicações](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Para abrir o painel de aplicações do Azure AD, clique em **Gerir aplicação**. Sobre o **Gerir aplicação** painel, pode obter o ID da aplicação cliente (ID de aplicação). Para gerar um segredo do cliente (chave), selecione **chaves**.

![Gerir a opção de chaves do painel de aplicações](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gerir permissões e a aplicação

Depois de selecionar a aplicação do Azure AD, pode gerir a aplicação e as permissões. Para configurar a sua aplicação do Azure AD para aceder a outras aplicações, clique em **gerir permissões**. Para tarefas de gestão, tal como alterar chaves e URLs de resposta ou editar o manifesto da aplicação, clique em **Gerir aplicação**.

### <a name="edit-the-apps-settings-or-manifest"></a>Editar definições da aplicação ou do manifesto

Para editar as definições ou manifesto da aplicação, clique em **Gerir aplicação**.

![Gerir a página de aplicativo](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Passos Seguintes

Introdução ao [carregar ficheiros para a conta](media-services-portal-upload-files.md).
