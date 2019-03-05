---
title: 'Tutorial: Configurar MerchLogix para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para MerchLogix automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe36969661ae1b729601681c02f79e777b2f8cab
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344927"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configurar MerchLogix para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no MerchLogix e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para MerchLogix. 

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

*   Um inquilino do Azure AD
*   Um inquilino MerchLogix
*   Um contacto técnico em MerchLogix que pode fornecer o URL de ponto final SCIM e o token secreto necessárias para o aprovisionamento de utilizadores

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionando MerchLogix da Galeria
Antes de configurar MerchLogix para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar MerchLogix a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar MerchLogix a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]
    
3. Para adicionar MerchLogix, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix**e, em seguida, clique nas **Add** botão para adicionar MerchLogix à sua lista de aplicações SaaS.

    ![Aprovisionamento MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuir utilizadores a MerchLogix

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a MerchLogix. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a MerchLogix ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir utilizadores a MerchLogix

*    Recomenda-se que um único utilizador do Azure AD está atribuído a MerchLogix para testar sua inicial configuração de aprovisionamento de utilizadores automática. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde quando os testes forem bem-sucedidos.

*   Ao atribuir um utilizador para MerchLogix, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurar o aprovisionamento automático de utilizadores para MerchLogix 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no MerchLogix com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para MerchLogix, seguindo as instruções fornecidas no [MerchLogix único início de sessão tutorial](merchlogix-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para MerchLogix no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione MerchLogix da sua lista de aplicações SaaS.

3. Selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Sob o **credenciais de administrador** secção:

    *   Na **URL de inquilino** campo, introduza o URL de ponto final SCIM fornecido pelo seu contacto técnico MerchLogix.

    *   Na **segredo de Token** , insira o token secreto fornecido pelo seu contacto técnico MerchLogix.

6. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se MerchLogix. Se a ligação falhar, certifique-se de que a conta de MerchLogix tem permissões de administrador e tente novamente.

    
7. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

8. Clique em **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para MerchLogix**.

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para MerchLogix no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no MerchLogix para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

11. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para MerchLogix**.

12. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para MerchLogix no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no MerchLogix para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

13. Para ativar o Azure AD para MerchLogix do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

14. Quando estiver pronto para aprovisionar, clique em **guardar**.


Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no MerchLogix do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
