---
title: 'Tutorial: Configurar o GitHub para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para o GitHub.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31f10ba0c04ccbd9f52b95c43fea7cc551fe64ee
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888020"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Configurar o GitHub para o aprovisionamento automático de utilizadores


O objetivo deste tutorial é mostrar a os passos que necessários para executar no GitHub e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para o GitHub. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory
*   Uma organização do GitHub criada no [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de faturação do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
*   Uma conta de utilizador no GitHub com permissões de administrador para a organização

> [!NOTE]
> Conta com o Azure AD, integração de aprovisionamento a [API do GitHub SCIM](https://developer.github.com/v3/scim/), que está disponível para [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) os clientes que têm o [plano de faturação do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) .

## <a name="assigning-users-to-github"></a>Atribuir utilizadores a GitHub

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado. 

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação do GitHub. Depois de decidir, pode atribuir estes utilizadores à sua aplicação do GitHub ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir utilizadores a GitHub

*   Recomenda-se que um único utilizador do Azure AD é atribuído ao GitHub para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para o GitHub, tem de selecionar um a **utilizador** função, ou outro válido específico do aplicativo função (se disponível) na caixa de diálogo atribuição. O **acesso predefinido** função não funciona para o aprovisionamento e estes utilizadores são ignorados.


## <a name="configuring-user-provisioning-to-github"></a>Configurar o aprovisionamento para o GitHub 

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do GitHub e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no GitHub com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativada baseado em SAML início de sessão único para o GitHub, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores conta para o GitHub no Azure AD


1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado o GitHub para início de sessão único, procure a sua instância do GitHub com o campo de pesquisa. Caso contrário, selecione **Add** e procure **GitHub** na Galeria de aplicações. Selecione o GitHub resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do GitHub, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento do GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. Esta operação é aberta uma caixa de diálogo de autorização do GitHub numa nova janela do browser. 

6. Na nova janela, inicie sessão no GitHub com a sua conta de administrador. Na caixa de diálogo resultante autorização, selecione a equipa do GitHub que pretende ativar o aprovisionamento para e, em seguida, selecione **autorizar**. Depois de concluído, regresse ao portal do Azure para concluir a configuração de aprovisionamento.

    ![Caixa de diálogo de autorização](./media/github-provisioning-tutorial/GitHub2.png)

7. No portal do Azure, de entrada **URL de inquilino** e clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação do GitHub. Se a ligação falhar, certifique-se de sua conta do GitHub com permissões de administrador e **URl de inquilino** é introduzido corretamente, em seguida, repita o passo de "Autorizar" (pode constituem **URL de inquilino** através da regra: `https://api.github.com/scim/v2/organizations/<Organization_name>` , pode encontrar suas organizações na sua conta do GitHub: **As definições** > **organizações**).

    ![Caixa de diálogo de autorização](./media/github-provisioning-tutorial/GitHub3.png)

8. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

9. Clique em **Guardar**. 

10. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para o GitHub**.

11. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o GitHub. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no GitHub para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para ativar o Azure AD para o GitHub do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

13. Clique em **Guardar**. 

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao GitHub na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
