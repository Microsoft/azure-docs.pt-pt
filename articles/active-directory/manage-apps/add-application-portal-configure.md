---
title: 'Quickstart: Configure propriedades para uma aplicação no seu inquilino Azure Ative (Azure AD)'
description: Este quickstart utiliza o portal Azure para configurar uma aplicação que foi registada com o seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: e83caba2d2a5435c5cdac02c6f63094dc079c43c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258613"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Configure propriedades para uma aplicação no seu inquilino Azure Ative (Azure AD)

No quickstart anterior, adicionou um pedido ao seu inquilino Azure Ative Directory (Azure AD). Quando você adiciona uma aplicação, você está deixando o seu inquilino Azure AD saber que é o fornecedor de identidade para a app. Agora vai configurar algumas das propriedades da aplicação.
 
## <a name="prerequisites"></a>Pré-requisitos

Para configurar as propriedades de uma aplicação no seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Opcional: Conclusão de [Ver as suas apps.](view-applications-portal.md)
- Opcional: Conclusão de [Adicionar uma aplicação.](add-application-portal.md)

>[!IMPORTANT]
>Utilize um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="configure-app-properties"></a>Configure propriedades de aplicativos

Quando terminar de adicionar uma aplicação ao seu inquilino Azure AD, a página geral aparece. Se está a configurar uma aplicação que já foi adicionada, olhe para o primeiro quickstart. Acompanha-o a ver as candidaturas adicionadas ao seu inquilino. 

Para editar as propriedades da aplicação:

1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende configurar.
2. Na secção **Gerir,** selecione **Propriedades** para abrir o painel **propriedades** para edição.
3. Aproveite um momento para entender as opções disponíveis. As opções disponíveis dependerão da forma como a aplicação é integrada com o Azure AD. Por exemplo, uma aplicação que utilize um único sinal baseado em SAML terá campos como URL de *acesso ao utilizador,* enquanto uma aplicação que utiliza um único sinal baseado no OIDC não o fará. Note também que as aplicações adicionadas através **do Azure Ative Directory > Os registos da App** são por padrão aplicações baseadas em OIDC. Enquanto as aplicações adicionadas através do **Azure Ative Directory > aplicações enterprise** podem usar qualquer uma de uma série de normas únicas de inscrição. Todas as aplicações terão campos para configurar quando uma aplicação aparece e pode ser usada. Estes campos são:
    - **Habilitado para os utilizadores fazerem o s.a.?** determina se os utilizadores designados para a aplicação podem iniciar sessão.
    - **Atribuição de utilizadores necessária?** determina se os utilizadores que não estão atribuídos à aplicação podem iniciar sessão.
    - **Visível para os utilizadores?** determina se os utilizadores atribuídos a uma aplicação podem vê-la nas [My Apps](https://myapps.microsoft.com) e no launcher da aplicação Microsoft 365. (Consulte o menu de waffles no canto superior esquerdo de um website da Microsoft 365.)
    
    > [!TIP]
    > A atribuição de utilizadores acontece na secção de Navegação dos **Utilizadores e grupos.**

    As três opções podem ser alternadas independentemente uma da outra e o comportamento resultante nem sempre é óbvio. Aqui está uma mesa que pode ajudar:
    
    | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Comportamento para utilizadores que tenham sido ou não designados para a app. |
    |---|---|---|---|
    | Yes | Yes | Yes | Os utilizadores designados podem ver a aplicação e iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |
    | Yes | Yes | No  | As utilizações atribuídas não podem ver a aplicação, mas podem iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |
    | Yes | No  | Yes | Os utilizadores designados podem ver a aplicação e iniciar sessão.<br>Os utilizadores não atribuídos não conseguem ver a aplicação, mas podem iniciar sedu. |
    | Yes | No  | No  | Os utilizadores designados não podem ver a aplicação, mas podem iniciar sessão.<br>Os utilizadores não atribuídos não conseguem ver a aplicação, mas podem iniciar sedu. |
    | No  | Yes | Yes | Os utilizadores designados não podem ver a aplicação e não podem iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |
    | No  | Yes | No  | Os utilizadores designados não podem ver a aplicação e não podem iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |
    | No  | No  | Yes | Os utilizadores designados não podem ver a aplicação e não podem iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |
    | No  | No  | No  | Os utilizadores designados não podem ver a aplicação e não podem iniciar sessão.<br>Os utilizadores não atribuídos não podem ver a aplicação e não podem iniciar seduca. |

4. Quando terminar, **selecione Save**.

## <a name="use-a-custom-logo"></a>Utilizar um logótipo personalizado

Para utilizar um logótipo personalizado:

1. Crie um logótipo que seja de 215 por 215 pixels e guarde-o em formato .png.
2. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende configurar.
3. Na secção **Gerir,** selecione **Propriedades** para abrir o painel **propriedades** para edição. 
4. Selecione o ícone para carregar o logotipo.
5. Quando terminar, **selecione Save**.

    ![Screenshot do ecrã Propriedades que mostra como alterar o logotipo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A miniatura exibida neste painel **de propriedades** não atualiza imediatamente. Pode fechar e reabrir o painel **de propriedades** para ver o ícone atualizado.


> [!TIP]
> Pode automatizar a gestão de aplicações utilizando a API do Gráfico, ver [Gestão de aplicações do Automamate com a Microsoft Graph API](/graph/application-saml-sso-configure-api).

## <a name="add-notes"></a>Adicionar notas

Pode utilizar o campo de notas para adicionar qualquer informação que seja relevante para a gestão da aplicação em Azure AD. Notas é um campo de texto gratuito com um tamanho máximo de 1024 caracteres.

1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende configurar.
2. Na secção **Gerir,** selecione **Propriedades** para abrir o painel **propriedades** para edição.
3. Atualize o campo Notas, **selecione Guardar**.

    ![Screenshot do ecrã Propriedades que mostra como alterar as notas](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar com a série quickstart, então considere apagar a app para limpar o seu inquilino de teste. A eliminação da aplicação é abrangida pelo último quickstart desta série, ver [Delete uma aplicação](delete-application-portal.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a atribuir utilizadores à app.
> [!div class="nextstepaction"]
> [Atribuir utilizadores a uma aplicação](add-application-portal-assign-users.md)