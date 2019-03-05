---
title: 'Tutorial: Configurar Velpic para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Velpic automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 059ce3a23a9bdacfb978ccad775c7da853772e3f
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344829"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurar Velpic para aprovisionamento automático de utilizadores


O objetivo deste tutorial é mostrar a os passos que necessários para executar no Velpic e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para Velpic automaticamente. 

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active Directory
*   Um inquilino Velpic com o [plano Enterprise](https://www.velpic.com/pricing.html) ou melhor ativado 
*   Uma conta de utilizador no Velpic com permissões de administrador 

## <a name="assigning-users-to-velpic"></a>Atribuir utilizadores a Velpic

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, serão sincronizados apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD. 

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Velpic. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Velpic ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir utilizadores a Velpic

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Velpic para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para Velpic, tem de selecionar um a **utilizador** função, ou outro válido específico do aplicativo função (se disponível) na caixa de diálogo atribuição. Tenha em atenção que o **acesso predefinido** função não funciona para o aprovisionamento e estes utilizadores vão ser ignorados.


## <a name="configuring-user-provisioning-to-velpic"></a>Configurar o aprovisionamento para Velpic 

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do Velpic e configurar o serviço de aprovisionamento para criar, atualizar e desativar atribuídos a contas de utilizador no Velpic com base no utilizador e a atribuição de grupo no Azure AD.

>[!TIP]
>Também pode optar por ativada baseado em SAML início de sessão único para Velpic, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.


### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta para Velpic no Azure AD:

1.  Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado Velpic para início de sessão único, procure a sua instância do Velpic usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Velpic** na Galeria de aplicações. Selecione Velpic resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3.  Selecione a sua instância de Velpic, em seguida, selecione o **aprovisionamento** separador.

4.  Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5.  Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino e o segredo de Token** de Velpic. ( Pode encontrar estes valores na sua conta de Velpic: **Gerir** > **integração** > **Plug-in** > **SCIM**)

    ![Valores de autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de Velpic. Se a ligação falhar, certifique-se de que a conta de Velpic tem permissões de administrador e tente novamente o passo 5.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação abaixo.

8. Clique em **Guardar**. 

9. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para Velpic**.

10. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que serão sincronizados do Azure AD para Velpic. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para corresponder as contas de utilizador no Velpic para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD para Velpic do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **Guardar**. 

Isso iniciará a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Velpic na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial demora mais tempo a executar que sincroniza subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para relatórios de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)