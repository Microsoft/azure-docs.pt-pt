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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956157"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Configure propriedades para uma aplicação no seu inquilino Azure Ative (Azure AD)

No início rápido anterior, adicionou um pedido ao seu inquilino AZure AD. Quando adiciona uma aplicação, está a informar o seu inquilino Azure AD de que é o fornecedor de identidade da app. Agora vai configurar algumas das propriedades da aplicação.
 
## <a name="prerequisites"></a>Pré-requisitos

Para configurar as propriedades de uma aplicação no seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- (Opcional: Conclusão de Visualização das [suas apps).](view-applications-portal.md)
- (Opcional: Conclusão de [Adicionar uma aplicação).](add-application-portal.md)

>[!IMPORTANT]
>Recomendamos a utilização de um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="configure-app-properties"></a>Configure propriedades de aplicativos

Quando terminar de adicionar uma candidatura ao seu inquilino Azure AD, é imediatamente apresentada a página geral para o mesmo. Se está a configurar uma aplicação que já foi adicionada, então olhe para o primeiro quickstart, ele o acompanha através da visualização das aplicações adicionadas ao seu inquilino. 

Para editar as propriedades da aplicação:

1. No portal AD Azure, selecione **as aplicações Enterprise** e, em seguida, encontre e selecione a aplicação que pretende configurar.
2. Na secção Gerir, selecione **Propriedades** para abrir o painel de propriedades para edição.
    ![Mostra o ecrã propriedades e propriedades de aplicativos editáveis](media/add-application-portal/edit-properties.png)
3. Aproveite um momento para entender as opções disponíveis para configurar.
    - **Habilitado para os utilizadores fazerem o s.a.?** determina se os utilizadores designados para a aplicação podem iniciar sessão.
    - **Atribuição de utilizadores necessária?** determina se os utilizadores que não estão atribuídos à aplicação podem iniciar sessão.
    - **Visível para os utilizadores?** determina se os utilizadores atribuídos a uma aplicação podem vê-lo no painel de acesso ( e no https://myapps.microsoft.com) lançador de aplicações O365 (o menu de waffles no topo esquerdo de um website do Office 365 ou microsoft 365).
4. Use as seguintes tabelas para ajudá-lo a escolher as melhores opções para as suas necessidades.

   - Comportamento para utilizadores **atribuídos**:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores atribuídos | Experiência para utilizadores atribuídos |
       |---|---|---|---|---|
       | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores atribuídos podem iniciar sessão? | Os utilizadores atribuídos podem ver a aplicação?* |
       | sim | sim | sim | sim | sim  |
       | sim | sim | não  | sim | não   |
       | sim | não  | sim | sim | sim  |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

   - Comportamento para utilizadores **não atribuídos**:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores não atribuídos | Experiência para utilizadores não atribuídos |
       |---|---|---|---|---|
       | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

## <a name="use-a-custom-logo"></a>Utilizar um logótipo personalizado

Para utilizar um logótipo personalizado:

1. Crie um logótipo com 215 por 215 píxeis e guarde-o no formato PNG.
2. No portal AD Azure, selecione **as aplicações Enterprise** e, em seguida, encontre e selecione a aplicação que pretende configurar.
3. Na secção Gerir, selecione **Propriedades** para abrir o painel de propriedades para edição. 
4. Selecione o ícone para carregar o logotipo.
5. Quando terminar, **selecione Save**. 
    ![Mostra como alterar o logótipo da página Propriedades da aplicação](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A miniatura exibida neste painel **de propriedades** não atualiza imediatamente. Pode fechar e reabrir as propriedades para ver o ícone atualizado.

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste as propriedades de uma aplicação, podes continuar a configurar uma única inscrição.

- [Configurar o início de sessão único](add-application-portal-setup-sso.md)
- [Eliminar uma aplicação](delete-application-portal.md)