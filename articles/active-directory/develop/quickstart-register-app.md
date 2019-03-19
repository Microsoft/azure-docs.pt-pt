---
title: Registar uma aplicação na plataforma de identidade da Microsoft (Pré-visualização) | Azure
description: Saiba como adicionar e registar uma aplicação na plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/02/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d6fe8921a5330f48da2879444e0b80cbc9bd2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090717"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>Início rápido: Registar uma aplicação com a plataforma de identidade da Microsoft (pré-visualização)

Os programadores empresariais e os fornecedores de software como serviço (SaaS) podem desenvolver serviços cloud comerciais ou aplicações de linha de negócio que podem ser integrados na plataforma de identidade da Microsoft, de modo a proporcionar início de sessão protegido e autorização para os serviços que oferecem.

Este início rápido mostra como adicionar e registar uma aplicação com a experiência **Registos de aplicações (Pré-visualização)** no portal do Azure para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Para saber mais sobre os novos recursos e aprimoramentos na nova experiência de registos de aplicação, veja [nesta mensagem de blogue](https://developer.microsoft.com/graph/blogs/new-app-registration/). 

## <a name="prerequisite"></a>Pré-requisito

Para começar, tem de se inscrever na experiência de Pré-visualização para registos de aplicações no portal do Azure. Os passos neste início rápido correspondem à nova IU e apenas funcionam se tiver optado pela experiência de Pré-visualização.

## <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino do Azure AD que pretende.
1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização) > Novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:

   - **Nome** - Introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação.
   - **Tipos de conta suportados** - Selecione as contas que quer que a sua aplicação suporte.

       | Tipos de conta suportados | Descrição |
       |-------------------------|-------------|
       | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se não estiver a registar a aplicação num diretório.<br><br>Esta opção mapeia para o inquilino único do Azure AD.<br><br>Esta é a opção predefinida, a menos que esteja a registar a aplicação fora de um diretório. Nos casos em que a aplicação é registada fora de um diretório, a predefinição é contas da Microsoft pessoais e de multi-inquilino do Azure AD. |
       | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser visar todos os clientes comerciais ou pedagógicos.<br><br>Esta opção mapeia para um multi-inquilino único do Azure AD.<br><br>Se registou a aplicação como inquilino único do Azure AD, pode atualizá-la para multi-inquilino do Azure AD e voltar ao inquilino único através do painel **Autenticação**. |
       | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Esta opção mapeia para contas da Microsoft pessoais e de multi-inquilino do Azure AD.<br><br>Se registou a aplicação como conta da Microsoft pessoal e de multi-inquilino do Azure AD, não pode alterar esta definição na IU. Em vez disso, tem de utilizar o editor de manifesto de aplicação para alterar os tipos de conta suportados. |

   - **URI de Redirecionamento (opcional)** - Selecione o tipo de aplicação que está a criar, **Web** ou **Cliente público (móvel e ambiente de trabalho)** e, em seguida, introduza o URI de redirecionamento (ou URL de resposta) para a sua aplicação.
       - Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web.
       - Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico para a aplicação, por exemplo `myapp://auth`.

     Para ver exemplos específicos de aplicações Web ou nativas, veja os nossos [inícios rápidos](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Quando terminar, selecione **Registar**.

    [![Registar uma nova aplicação no portal do Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

O Azure AD atribui um ID de aplicação (cliente) exclusivo à sua aplicação e será encaminhado para a página **Descrição Geral** da sua aplicação. Para adicionar mais capacidades à aplicação, pode selecionar outras opções de configuração, incluindo imagem corporativa, certificados e segredos, permissões de API e muito mais.

[![Página de descrição geral da aplicação recentemente registada](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [permissões e consentimentos](v2-permissions-and-consent.md).
- Para ativar funcionalidades de configuração adicionais no registo da aplicação, como credenciais e permissões, e ativar o início de sessão para os utilizadores de outros inquilinos, veja os seguintes inícios rápidos:
    - [Configurar uma aplicação cliente para aceder a APIs Web](quickstart-configure-app-access-web-apis.md)
    - [Configurar uma aplicação para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
    - [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
- Escolha um [início rápido](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) para criar uma aplicação e adicionar funcionalidades rapidamente, como obter tokens, atualizar tokens, iniciar a sessão de um utilizador, apresentar algumas informações de utilizador e muito mais.
- Saiba mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre eles, consulte [Objetos da aplicação e objetos principais de serviço](app-objects-and-service-principals.md).
- Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
