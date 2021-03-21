---
title: Começa com a autenticação AZure AD utilizando o portal Azure| Microsoft Docs
description: Saiba como utilizar o portal Azure para aceder à autenticação do Azure Ative Directory (Azure AD) para consumir a AZure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013062"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD através do portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Saiba como utilizar o portal Azure para aceder à autenticação do Azure Ative Directory (Azure AD) para aceder à API dos Serviços de Media Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tem conta, comece com um [julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).

Quando utiliza a autenticação AZure AD com a Azure Media Services, tem duas opções de autenticação:

- **Autenticação principal do serviço.** Autenticar um serviço. As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de nível médio ou trabalhos programados: aplicações web, apps de funções, apps lógicas, APIs ou um microserviço.
- **Autenticação de utilizador**. Autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação da consola de gestão utilizada pelos utilizadores autorizados para monitorizar as tarefas de codificação ou a transmissão em direto. 

## <a name="access-the-media-services-api"></a>Aceder à API dos Serviços de Multimédia

Esta página permite selecionar o método de autenticação que pretende utilizar para ligar à API. A página também fornece os valores necessários para se ligar à API.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta De Serviços de Comunicação.
2. Selecione como ligar à API dos Serviços de Mídia.
3. Em **Connect to Media Services API**, selecione a versão API dos Serviços de Mídia a que pretende ligar.

## <a name="service-principal-authentication--recommended"></a>Autenticação principal do serviço (recomendado)

Autentica um serviço utilizando uma aplicação Azure Ative Directory (Azure AD) e secreta. Isto é recomendado para qualquer serviço de nível médio chamado para a API dos Serviços de Mídia. Exemplos são Web Apps, Functions, Logic Apps, APIs e microserviços. Este é o método de autenticação recomendado.

### <a name="manage-your-azure-ad-app-and-secret"></a>Gerencie a sua app AZure AD e o segredo

A **aplicação AAD e** a secção secreta permitem-lhe selecionar ou criar uma nova aplicação AD Azure e gerar um segredo. Por razões de segurança, o segredo não pode ser mostrado depois de a lâmina estar fechada. A aplicação utiliza o ID da aplicação e segredo para autenticação para obter um token válido para os serviços de comunicação.

Certifique-se de que tem permissões suficientes para registar um pedido com o seu inquilino Azure AD e atribuir o pedido a uma função na sua subscrição Azure. Para mais informações, consulte [as permissões necessárias.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

### <a name="connect-to-media-services-api"></a>Ligue-se à API dos Serviços de Mídia

A **API do Connect to Media Services** fornece-lhe valores que utiliza para ligar a sua aplicação principal de serviço. Pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="user-authentication"></a>Autenticação de utilizador

Esta opção poderia ser usada para autenticar um empregado ou membro de um Diretório Azure Ative que está a usar uma aplicação para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Este método de autenticação só deve ser utilizado para aplicações de Gestão.

### <a name="connect-to-media-services-api"></a>Ligue-se à API dos Serviços de Mídia

Copie as suas credenciais para ligar a sua aplicação de utilizador a partir da secção **API de Serviços de Comunicação Social.** Pode obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a tua conta.](media-services-portal-upload-files.md)
