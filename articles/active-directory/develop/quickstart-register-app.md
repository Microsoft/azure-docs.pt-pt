---
title: 'Quickstart: Registe aplicações com plataforma de identidade microsoft / Azure'
description: Neste arranque rápido, aprende-se a adicionar e registar uma aplicação com a plataforma de identidade da Microsoft.
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
ms.openlocfilehash: 79983678d13b810a521a00ba2c1978de92a5029f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309514"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Quickstart: Registe uma aplicação com a plataforma de identidade da Microsoft

Neste arranque rápido, regista uma aplicação utilizando a experiência de **registos** da App no portal Azure. 

A sua aplicação está integrada na plataforma de identidade da Microsoft, registando-a com um inquilino do Azure Ative Directory. Os desenvolvedores empresariais e fornecedores de software como um serviço (SaaS) podem desenvolver serviços de nuvem comercial ou aplicações de linha de negócio que podem ser integradas com a plataforma de identidade da Microsoft. A integração fornece um sinal de inscrição e autorização seguros para tais serviços.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um [inquilino da AD Azure.](quickstart-create-new-tenant.md)

## <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Detete a sua sessão de portal para o inquilino da AD Azure que quiser.
1. Procure e selecione **Azure Active Directory**. Em **Gerir**, selecione **Registos de aplicações**.
1. Selecione **Novo registo**.
1. No **Registo de uma aplicação,** introduza um nome de aplicação significativo para exibir aos utilizadores.
1. Especifique quem pode utilizar a aplicação, da seguinte forma:

    | Tipos de conta suportados | Descrição |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se não estiver a registar a candidatura num diretório.<br><br>Esta opção mapeia para o inquilino único do Azure AD.<br><br>Esta opção é o padrão a menos que esteja a registar a app fora de um diretório. Nos casos em que a aplicação é registada fora de um diretório, a predefinição é contas da Microsoft pessoais e de multi-inquilino do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser visar todos os clientes comerciais ou pedagógicos.<br><br>Esta opção mapeia para um multi-inquilino único do Azure AD.<br><br>Se registou a aplicação como Azure AD apenas um único inquilino, pode atualizá-la para ser multi-inquilino da Azure AD e voltar a inquilino único através da página **de Autenticação.** |
    | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Esta opção mapeia para contas da Microsoft pessoais e de multi-inquilino do Azure AD.<br><br>Se registou a aplicação como multi-inquilino e contas pessoais da Microsoft, não pode alterar esta definição na UI. Em vez disso, tem de utilizar o editor de manifesto de aplicação para alterar os tipos de conta suportados. |

1. Em **Redirect URI (opcional)**, selecione o tipo de aplicação que está a construir: **Web** ou Cliente Público (ambiente de trabalho **& móvel)**. Em seguida, introduza o URI redirecionamento, ou URL de resposta, para a sua aplicação.

    * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `https://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web.
    * Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico para a aplicação, por exemplo `myapp://auth`.

    Por exemplo, para aplicações web ou aplicações nativas, consulte os quickstarts na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Quando terminar, selecione **Registar**.

    ![Mostra o ecrã para registar uma nova aplicação no portal Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

A Azure AD atribui uma aplicação única, ou cliente, ID para a sua aplicação. O portal abre a página **de visão geral** da sua aplicação. Para adicionar capacidades à sua aplicação, pode selecionar outras opções de configuração, incluindo branding, certificados e segredos, permissões API e muito mais.

![Exemplo de uma página de visão geral de aplicações recém-registada](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Passos seguintes

* Para aceder a APIs web, consulte [Quickstart: Configure uma aplicação de cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md)

* Para saber mais sobre as permissões, consulte [Permissões e consentimento no ponto final da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).

* Para expor apis web, consulte [Quickstart: Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).

* Para gerir contas suportadas, consulte [Quickstart: Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md).

* Para construir uma aplicação e adicionar funcionalidade, consulte os quickstarts na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

* Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

* Para saber mais sobre as diretrizes de marca que deve utilizar no desenvolvimento de apps, consulte as diretrizes de [Branding para aplicações.](howto-add-branding-in-azure-ad-apps.md)
