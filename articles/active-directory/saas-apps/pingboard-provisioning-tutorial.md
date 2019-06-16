---
title: 'Tutorial: Configurar Pingboard para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Pingboard automaticamente.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f7e2fabc86374f7fe055303d056ae8e00f33389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964381"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configurar Pingboard para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que tem de seguir para ativar automática provisionamento e desprovisionamento de contas de utilizador do Azure Active Directory (Azure AD) para Pingboard.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure AD
* Um inquilino Pingboard [conta do Pro](https://pingboard.com/pricing)
* Uma conta de utilizador no Pingboard com permissões de administrador

> [!NOTE]
> Aprovisionamento de integração do Azure AD depende de [Pingboard API](https://pingboard.docs.apiary.io/#), que está disponível para a sua conta.

## <a name="assign-users-to-pingboard"></a>Atribuir utilizadores a Pingboard

Azure AD utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores atribuídos a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores no Azure AD precisam de acesso à sua aplicação Pingboard. Em seguida, pode atribuir estes utilizadores à sua aplicação Pingboard ao seguir as instruções aqui:

[Atribuir um utilizador a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir utilizadores a Pingboard

Recomendamos que atribua um único utilizador do Azure AD para Pingboard para testar a configuração de aprovisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-pingboard"></a>Configurar o aprovisionamento do utilizador Pingboard 

Esta secção orienta-o ao longo da ligação do Azure AD para a conta de utilizador de Pingboard API de aprovisionamento. Também é configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Pingboard que se baseiam em atribuições de utilizador no Azure AD.

> [!TIP]
> Para ativar baseado em SAML início de sessão único para Pingboard, siga as instruções fornecidas a [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementam uma à outra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta para Pingboard no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

1. Se já tiver configurado o Pingboard para início de sessão único, procure na sua instância do Pingboard, utilizando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Pingboard** na Galeria de aplicações. Selecione **Pingboard** resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância do Pingboard e, em seguida, selecione o **aprovisionamento** separador.

1. Definir **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Sob o **credenciais de administrador** secção, utilize os seguintes passos:

    a. Na **URL de inquilino**, introduza `https://your_domain.pingboard.com/scim/v2`e substitua "your_domain" com o seu domínio real.

    b. Inicie sessão no [Pingboard](https://pingboard.com/) usando sua conta de administrador.

    c. Selecione **suplementos** > **integrações** > **o Azure Active Directory**.

    d. Vá para o **configurar** separador e selecione **ativar o aprovisionamento de utilizadores do Azure**.

    e. Copie o token no **Token de portador do OAuth**e introduza-a na **segredo de Token**.

1. No portal do Azure, selecione **Testar ligação** testar o Azure AD, pode ligar à sua aplicação de Pingboard. Se a ligação falhar, o teste que a conta de Pingboard tem permissões de administrador e tente a **Testar ligação** passo novamente.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que pretende receber notificações de erro de aprovisionamento na **notificação por E-Mail**. Selecione a caixa de verificação abaixo.

1. Selecione **Guardar**.

1. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Pingboard**.

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador a ser sincronizados a partir do Azure AD para Pingboard. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Pingboard para operações de atualização. Selecione **guardar** para consolidar as alterações. Para obter mais informações, consulte [mapeamentos de atributos de aprovisionamento de utilizadores de personalizar](../manage-apps/customize-application-attributes.md).

1. Para ativar o Azure AD para Pingboard, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

1. Selecione **guardar** para iniciar a sincronização inicial de utilizadores atribuídos a Pingboard.

A sincronização inicial demora mais tempo a executar a seguintes sincronizações, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Utilize o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades de aprovisionamento. Os registos de descrevem todas as ações que o serviço de aprovisionamento na sua aplicação Pingboard.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [um relatório sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configure single sign-on](pingboard-tutorial.md) (Configurar o início de sessão único)
