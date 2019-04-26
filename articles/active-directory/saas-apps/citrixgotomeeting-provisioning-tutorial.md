---
title: 'Tutorial: Configurar o GoToMeeting para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e3145d0faaa3aecb90b582b3b6ef0063572ff43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430790"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configurar o GoToMeeting para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no GoToMeeting e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para o GoToMeeting automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um GoToMeeting início de sessão único ativado na subscrição.
*   Uma conta de utilizador no GoToMeeting com permissões de administrador da equipa.

## <a name="assigning-users-to-gotomeeting"></a>Atribuir utilizadores a GoToMeeting

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação GoToMeeting. Depois de decidir, pode atribuir estes utilizadores à sua aplicação GoToMeeting ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Dicas importantes para atribuir utilizadores a GoToMeeting

*   Recomenda-se que um único utilizador do Azure AD é atribuído a GoToMeeting para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para o GoToMeeting, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do GoToMeeting e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no GoToMeeting com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativada baseado em SAML início de sessão único para o GoToMeeting, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador automáticas:

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

1. Se já tiver configurado o GoToMeeting para início de sessão único, procure a sua instância do GoToMeeting usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **GoToMeeting** na Galeria de aplicações. Selecione o GoToMeeting resultados da pesquisa e adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância do GoToMeeting, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **aprovisionamento** modo **automática**. 

    ![a aprovisionar](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Na secção de credenciais de administrador, execute os seguintes passos:
   
    a. Na **nome de utilizador de administrador do GoToMeeting** caixa de texto, escreva o nome de utilizador do administrador.

    b. Na **palavra-passe de administrador do GoToMeeting** caixa de texto, a senha do administrador.

1. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de GoToMeeting. Se a ligação falhar, certifique-se de que a conta de GoToMeeting tem permissões de administrador da equipa e tente a **"Credenciais de administrador"** passo novamente.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

1. Clique em **guardar.**

1. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para GoToMeeting.**

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o GoToMeeting. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no GoToMeeting para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para o GoToMeeting do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

1. Clique em **guardar.**

Ele começa a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a GoToMeeting, na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação GoToMeeting de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


