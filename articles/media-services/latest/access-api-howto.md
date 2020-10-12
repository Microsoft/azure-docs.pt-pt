---
title: Começar com a autenticação AD AZure
description: Saiba como aceder à autenticação do Azure Ative Directory (Azure AD) para consumir a Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00808c25ac84da852cce6169fb210767ee2b56cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265886"
---
# <a name="get-credentials-to-access-media-services-api"></a>Obtenha credenciais para aceder à API dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando utiliza a autenticação Azure AD para aceder à API dos Serviços Azure Media, tem duas opções de autenticação:

- **Autenticação principal do serviço** (recomendado)

    Autenticar um serviço. As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de nível médio ou trabalhos programados: aplicações web, apps de funções, apps lógicas, APIs ou um microserviço.
- **Autenticação de utilizador**

    Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação da consola de gestão utilizada pelos utilizadores autorizados para monitorizar as tarefas de codificação ou a transmissão em direto. 

Este artigo descreve passos para obter credenciais para aceder à API dos Serviços de Media. Escolha entre os seguintes separadores.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tem conta, comece com um [julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta Azure Media Services utilizando o portal Azure](create-account-howto.md).

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>Acesso a API

A página **de acesso API** permite selecionar o método de autenticação que pretende utilizar para ligar à API. A página também fornece os valores necessários para se ligar à API.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta De Serviços de Comunicação.
2. Selecione a lâmina **de acesso API (nova)** na barra de navegação esquerda.
3. No **Âmbito do Connect to Media Services API**, selecione a versão API dos Serviços de Mídia a que pretende ligar (v3 é a versão mais recente do serviço).

### <a name="service-principal-authentication--recommended"></a>Autenticação principal do serviço (recomendado)

Autentica um serviço utilizando uma aplicação Azure Ative Directory (Azure AD) e secreta. Isto é recomendado para qualquer serviço de nível médio chamado para a API dos Serviços de Mídia. Exemplos são Web Apps, Functions, Logic Apps, APIs e microserviços. Este é o método de autenticação recomendado.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gerencie a sua app AZure AD e o segredo

A **aplicação AAD e** a secção secreta permitem-lhe selecionar ou criar uma nova aplicação AD Azure e gerar um segredo. Por razões de segurança, o segredo não pode ser mostrado depois de a lâmina estar fechada. A aplicação utiliza o ID da aplicação e segredo para autenticação para obter um token válido para os serviços de comunicação.

Certifique-se de que tem permissões suficientes para registar um pedido com o seu inquilino Azure AD e atribuir o pedido a uma função na sua subscrição Azure. Para mais informações, consulte [as permissões necessárias.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

#### <a name="connect-to-media-services-api"></a>Ligue-se à API dos Serviços de Mídia

A **API do Connect to Media Services** fornece-lhe valores que utiliza para ligar a sua aplicação principal de serviço. Pode obter valores de texto ou copiar os blocos JSON ou XML.

### <a name="user-authentication"></a>Autenticação de utilizador

Esta opção poderia ser usada para autenticar um empregado ou membro de um Diretório Azure Ative que está a usar uma aplicação para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Este método de autenticação só deve ser utilizado para aplicações de Gestão.

#### <a name="connect-to-media-services-api"></a>Ligue-se à API dos Serviços de Mídia

Copie as suas credenciais para ligar a sua aplicação de utilizador a partir da secção **API de Serviços de Comunicação Social.** Pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Carregar, codificar e transmitir vídeos com o Media Services v3](stream-files-tutorial-with-api.md).
