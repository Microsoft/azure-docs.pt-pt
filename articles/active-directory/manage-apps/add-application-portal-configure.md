---
title: 'Quickstart: Configure propriedades para uma aplicação no seu inquilino Azure Ative (Azure AD)'
description: Este quickstart utiliza o portal Azure para configurar uma aplicação que foi registada com o seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: a1a99e9f02a25f5e1d57ea485930a4f26149b53f
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808410"
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

    ![Screenshot do ecrã Propriedades que mostra propriedades de aplicações editáveis.](media/add-application-portal/edit-properties.png)

3. Aproveite um momento para entender as opções disponíveis para configurar:
    - **Habilitado para os utilizadores fazerem o s.a.?** determina se os utilizadores designados para a aplicação podem iniciar sessão.
    - **Atribuição de utilizadores necessária?** determina se os utilizadores que não estão atribuídos à aplicação podem iniciar sessão.
    - **Visível para os utilizadores?** determina se os utilizadores atribuídos a uma aplicação podem vê-la no painel de [acesso](https://myapps.microsoft.com) e no lançador de aplicações office 365. (Consulte o menu de waffles no canto superior esquerdo de um site do Office 365 ou microsoft 365.)
4. Use as seguintes tabelas para ajudá-lo a escolher as melhores opções para as suas necessidades.

   - Comportamento para utilizadores *atribuídos*:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores atribuídos | Experiência para utilizadores atribuídos |
       |---|---|---|---|---|
       | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores atribuídos podem iniciar sessão? | Os utilizadores atribuídos podem ver a aplicação?* |
       | Sim | Sim | Sim | Sim | Sim  |
       | Sim | Sim | Não  | Sim | Não   |
       | Sim | Não  | Sim | Sim | Sim  |
       | Sim | Não  | Não  | Sim | Não   |
       | Não  | Sim | Sim | Não  | Não   |
       | Não  | Sim | Não  | Não  | Não   |
       | Não  | Não  | Sim | Não  | Não   |
       | Não  | Não  | Não  | Não  | Não   |

   - Comportamento para utilizadores *não atribuídos*:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores não atribuídos | Experiência para utilizadores não atribuídos |
       |---|---|---|---|---|
       | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | Sim | Sim | Sim | Não  | Não   |
       | Sim | Sim | Não  | Não  | Não   |
       | Sim | Não  | Sim | Sim | Não   |
       | Sim | Não  | Não  | Sim | Não   |
       | Não  | Sim | Sim | Não  | Não   |
       | Não  | Sim | Não  | Não  | Não   |
       | Não  | Não  | Sim | Não  | Não   |
       | Não  | Não  | Não  | Não  | Não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

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
> Pode automatizar a gestão de aplicações utilizando a API do Gráfico, ver [Gestão de aplicações do Automamate com a Microsoft Graph API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar com a série quickstart, então considere apagar a app para limpar o seu inquilino de teste. A eliminação da aplicação é abrangida pelo último quickstart desta série, ver [Delete uma aplicação](delete-application-portal.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a configurar um único sº de sôs-on para uma aplicação.
> [!div class="nextstepaction"]
> [Configurar o início de sessão único](add-application-portal-setup-sso.md)
