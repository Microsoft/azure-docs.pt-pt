---
title: 'Quickstart: Registar aplicações com plataforma de identidade da Microsoft Rio Azure'
description: Neste arranque rápido, aprende-se a adicionar e a registar uma aplicação com a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115361"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Quickstart: Registar uma aplicação com a plataforma de identidade microsoft

Neste arranque rápido, regista-se uma aplicação utilizando a experiência de **registos** da App no portal Azure. 

A sua aplicação está integrada na plataforma de identidade da Microsoft, registando-a com um inquilino do Azure Ative Directory. Os desenvolvedores empresariais e fornecedores de software-as-a-service (SaaS) podem desenvolver serviços de nuvem comercial ou aplicações de linha de negócio que podem ser integradas com a plataforma de identidade da Microsoft. A integração fornece uma inscrição segura e uma autorização para esses serviços.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um [inquilino da AD Azure.](quickstart-create-new-tenant.md)

## <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Desconfiem da sua sessão de portal para o inquilino da AD Azure que pretende.
1. Procure e selecione **Azure Active Directory**. Em **Gerir**, selecione **Registos de aplicações**.
1. Selecione **Novo registo**.
1. No **Registo de uma aplicação**, introduza um nome de aplicação significativo para mostrar aos utilizadores.
1. Especificar quem pode usar a aplicação da seguinte forma:

    | Tipos de conta suportados | Descrição |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se não estiver a registar o pedido num diretório.<br><br>Esta opção mapeia para o inquilino único do Azure AD.<br><br>Esta opção é o padrão, a menos que esteja a registar a aplicação fora de um diretório. Nos casos em que a aplicação é registada fora de um diretório, a predefinição é contas da Microsoft pessoais e de multi-inquilino do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser visar todos os clientes comerciais ou pedagógicos.<br><br>Esta opção mapeia para um multi-inquilino único do Azure AD.<br><br>Se você registrou a app como Azure AD apenas um único inquilino, você pode atualizá-lo para ser Azure AD multi-inquilino e de volta para inquilino único através da página **de Autenticação.** |
    | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Esta opção mapeia para contas da Microsoft pessoais e de multi-inquilino do Azure AD.<br><br>Se registou a aplicação como multi-inquilino e contas pessoais da Microsoft, não pode alterar esta definição na UI. Em vez disso, tem de utilizar o editor de manifesto de aplicação para alterar os tipos de conta suportados. |

1. Em **Redirecionamento URI (opcional)**, selecione o tipo de aplicação que está a construir: **Web** ou Cliente **Público (mobile & desktop)**. Em seguida, insira o URI de redirecionamento, ou URL de resposta, para a sua aplicação.

    * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `https://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web.
    * Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico para a aplicação, por exemplo `myapp://auth`.

    Para exemplos para aplicações web ou aplicações nativas, consulte os quickstarts na [plataforma de identidade da Microsoft](./index.yml).

1. Quando terminar, selecione **Registar**.

    ![Mostra o ecrã para registar uma nova aplicação no portal Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

A Azure AD atribui uma aplicação única, ou cliente, ID à sua aplicação. O portal abre a página **geral** da sua aplicação. Para adicionar capacidades à sua aplicação, pode selecionar outras opções de configuração, incluindo branding, certificados e segredos, permissões API e muito mais.

![Exemplo de uma página geral de aplicações recentemente registada](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Passos seguintes

* Para aceder a APIs web, consulte [Quickstart: Configurar uma aplicação do cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md)

* Para saber mais sobre as permissões, consulte [permissões e consentimento no ponto final da plataforma de identidade da Microsoft.](v2-permissions-and-consent.md)

* Para expor apis web, consulte [Quickstart: Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).

* Para gerir contas suportadas, consulte [Quickstart: Modifique as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md).

* Para construir uma aplicação e adicionar funcionalidade, consulte os quickstarts na [plataforma de identidade da Microsoft.](./index.yml)

* Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

* Para saber mais sobre as diretrizes de marca que deve utilizar ao desenvolver aplicações, consulte [as diretrizes de Branding para aplicações.](howto-add-branding-in-azure-ad-apps.md)