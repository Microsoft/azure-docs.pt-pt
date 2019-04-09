---
title: 'Tutorial: Configurar Asana para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Asana.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: a763b2516f88e8c92efc321db50dc15881f54c9b
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057851"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configurar Asana para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no Asana e Azure Active Directory (Azure AD) para aprovisionar e desaprovisionar contas de utilizador do Azure AD para Asana automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure AD
* Um inquilino do Asana com um [Enterprise](https://www.asana.com/pricing) planear ou melhor ativada
* Uma conta de utilizador no Asana com permissões de administrador

> [!NOTE]
> Aprovisionamento de integração do Azure AD depende de [Asana API](https://asana.com/developers/api-reference/users), que está disponível para Asana.

## <a name="assign-users-to-asana"></a>Atribuir utilizadores a Asana

Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores no Azure AD precisam de acesso à sua aplicação do Asana. Em seguida, pode atribuir estes utilizadores à sua aplicação do Asana ao seguir as instruções aqui:

[Atribuir um utilizador a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir utilizadores a Asana

Recomendamos que atribua um único utilizador do Azure AD para Asana para testar a configuração de aprovisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-asana"></a>Configurar o aprovisionamento do utilizador Asana

Esta secção orienta-o ao longo da ligação do Azure AD para a conta de utilizador de Asana API de aprovisionamento. Também é configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Asana com base em atribuições de utilizador no Azure AD.

> [!TIP]
> Para ativar baseado em SAML início de sessão único para Asana, siga as instruções fornecidas a [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementam uma à outra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta para Asana no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

1. Se já tiver configurado o Asana para início de sessão único, procure na sua instância do Asana, utilizando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Asana** na Galeria de aplicações. Selecione **Asana** resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância do Asana e, em seguida, selecione o **aprovisionamento** separador.

1. Definir **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento do Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Sob o **credenciais de administrador** secção, siga estas instruções para gerar o token e introduza-a na **segredo de Token**:

    a. Inicie sessão no [Asana](https://app.asana.com) usando sua conta de administrador.

    b. Selecione a fotografia de perfil na barra superior e selecione as definições atuais do nome da organização.

    c. Vá para o **contas de serviço** separador.

    d. Selecione **adicionar conta de serviço**.

    e. Atualização **Name** e **sobre** e a fotografia de perfil, conforme necessário. Copie o token no **Token**e selecione-a na **guardar alterações**.

1. No portal do Azure, selecione **Testar ligação** para garantir que as do Azure AD pode ligar-se à sua aplicação do Asana. Se a ligação falhar, certifique-se de que a sua conta do Asana com permissões de administrador e tente a **Testar ligação** passo novamente.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que pretende receber notificações de erro de aprovisionamento na **notificação por E-Mail**. Selecione a caixa de verificação abaixo.

1. Selecione **Guardar**.

1. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Asana**.

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador a ser sincronizados a partir do Azure AD para Asana. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Asana para operações de atualização. Selecione **guardar** para consolidar as alterações. Para obter mais informações, consulte [personalizar mapeamentos de atributos do utilizador aprovisionar](../manage-apps/customize-application-attributes.md).

1. Para ativar o Azure AD para Asana, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

1. Selecione **Guardar**.

Agora é iniciada a sincronização inicial para todos os utilizadores atribuídos a Asana no **utilizadores** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Utilize o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades de aprovisionamento. Os registos de auditoria descrevem todas as ações executadas pelo serviço de aprovisionamento na sua aplicação do Asana.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [um relatório sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar o início de sessão único](asana-tutorial.md)
