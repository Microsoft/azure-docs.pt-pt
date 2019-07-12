---
title: 'Tutorial: Configurar 4me para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para 4me automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: e2e7c27d8cfa79bc7a8f8462def4d46e598cb508
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595086"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Tutorial: Configurar 4me para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no 4me e Azure Active Directory (Azure AD) para configurar o Azure AD para automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para 4me.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino 4me](https://www.4me.com/trial/)
* Uma conta de utilizador no 4me com permissões de administrador.

## <a name="add-4me-from-the-gallery"></a>Adicionar 4me a partir da Galeria

Antes de configurar 4me para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar 4me a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar 4me a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **4me**, selecione **4me** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![4me na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Atribuir utilizadores a 4me

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a 4me. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a 4me ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Dicas importantes para atribuir utilizadores a 4me

* Recomenda-se que um único utilizador do Azure AD está atribuído a 4me para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para 4me, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurar o aprovisionamento automático de utilizadores para 4me 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no 4me com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para 4me, seguindo as instruções fornecidas a [4me único início de sessão tutorial](4me-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para 4me no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **4me**.

    ![A ligação de 4me na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Para obter o **URL de inquilino** e **segredo de Token** da sua conta de 4me, siga o passo a passo, conforme descrito no passo 6.

6. Inicie sessão na consola de administração de 4me. Navegue para **definições**.

    ![Definições de 4me](media/4me-provisioning-tutorial/4me01.png)

    Escreva **aplicações** na barra de pesquisa.

    ![aplicações de 4me](media/4me-provisioning-tutorial/4me02.png)

    Abra o **SCIM** lista pendente para obter o Token de segredo e o ponto de extremidade SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se 4me. Se a ligação falhar, certifique-se de que a conta de 4me tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para 4me**.

    ![Mapeamentos de utilizador 4me](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para 4me no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no 4me para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Mapeamentos de utilizador 4me](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para 4me**.

    ![Mapeamentos de utilizador 4me](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para 4me no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no 4me para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Mapeamentos de grupo 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para 4me do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

16. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento 4me escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no 4me do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* 4me tem diferentes URLs de ponto final SCIM para ambientes de teste e produção. A primeira termina com **.qa** enquanto o último termina com **.com**
* os Tokens de segredo de 4me gerado têm uma data de expiração de um mês de geração.
* não suporta a 4me **eliminar** operações

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)