---
title: 'Tutorial: Configurar o Zscaler três para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o Azure Active Directory para provisionar e desprovisionar contas de utilizador com o Zscaler três automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 93efb44668a3a0b189e96c1b1212766dee075ef3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672849"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler três para aprovisionamento automático de utilizadores

Neste tutorial, irá aprender como configurar o Azure Active Directory (Azure AD) para automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para Zscaler três.

> [!NOTE]
> Este tutorial descreve um conector que baseia-se o serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes no qual esse serviço faz e como ele funciona e respostas para perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../active-directory-saas-app-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os termos do Azure gerais de utilização de funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino do Zscaler três.
* Uma conta de utilizador no Zscaler três com permissões de administrador.

> [!NOTE]
> A integração de aprovisionamento do Azure AD depende da API de SCIM do Zscaler ZSCloud, que está disponível para contas empresariais.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler três da Galeria

Antes de configurar o Zscaler três para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Zscaler três da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Aceda a **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**:

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

![Selecionar novo aplicativo](common/add-new-app.png)

Na caixa de pesquisa, introduza **Zscaler três**. Selecione **Zscaler três** nos resultados e, em seguida, selecione **Add**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Atribuir utilizadores a três de Zscaler

Utilizadores do Azure AD tem de ser concedido acesso às aplicações selecionadas, antes de poderem utilizá-los. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos no Azure AD precisam de acesso a Zscaler três. Depois de decidir que, pode atribuir estes utilizadores e grupos a Zscaler três ao seguir as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Dicas importantes para atribuir utilizadores a três de Zscaler

* Recomendamos que primeiro de atribuir um único utilizador do Azure AD para Zscaler três para testar o configuração de aprovisionamento automático de utilizadores. Pode atribuir mais utilizadores e grupos mais tarde.

* Quando atribui um utilizador a Zscaler três, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o aprovisionamento automático de utilizadores

Esta secção orienta-o através dos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar a utilizadores e grupos no Zscaler três com base no utilizador e as atribuições de grupo no Azure AD.

> [!TIP]
> Pode também querer ativar baseado em SAML início de sessão único para Zscaler três. Se, siga as instruções no [Zscaler três único início de sessão tutorial](zscaler-three-tutorial.md). Início de sessão único pode ser configurado de forma independente de aprovisionamento automático de utilizadores, mas os dois recursos complementam uma à outra.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais** > **todos os aplicativos** > **Zscaler três**:

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler três**:

    ![Lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador:

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Definir o **modo de aprovisionamento** ao **automática**:

    ![Definir o modo de aprovisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Na **credenciais de administrador** , digite o **URL de inquilino** e **segredo de Token** da sua conta Zscaler três, conforme descrito no passo seguinte.

6. Para obter o **URL de inquilino** e **segredo de Token**, aceda a **administração** > **definições de autenticação** no Zscaler Três portal e selecione **SAML** sob **tipo de autenticação**:

    ![Definições de autenticação do Zscaler três](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Selecione **configurar SAML** para abrir o **configurar SAML** janela:

    ![Configurar a janela SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Selecione **aprovisionamento Enable SCIM-Based** e copie a **URL de Base** e **Token de portador**e, em seguida, guarde as definições. No portal do Azure, cole o **URL de Base de** para o **URL de inquilino** caixa e o **Token de portador** no **segredo de Token** caixa.

7. Depois de introduzir os valores a **URL de inquilino** e **segredo de Token** caixas, selecionadas **Testar ligação** para se certificar de que o Azure AD pode ligar-se para Zscaler três. Se a ligação falhar, certifique-se de que a sua conta do Zscaler três com permissões de administrador e tente novamente.

    ![Testar a ligação](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Na **notificação por E-Mail** , introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento. Selecione **enviar uma notificação por e-mail quando ocorre uma falha**:

    ![Configurar notificação por e-mail](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para ZscalerThree**:

    ![Sincronizar utilizadores do Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Rever os atributos de utilizador que são sincronizados a partir do Azure AD Zscaler três para o **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zscaler três para operações de atualização. Selecione **guardar** para consolidar as alterações.

    ![Mapeamentos de atributos](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Na **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para ZscalerThree**:

    ![Sincronizar grupos do Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Rever os atributos de grupo que são sincronizados a partir do Azure AD Zscaler três para o **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zscaler três para operações de atualização. Selecione **guardar** para consolidar as alterações.

    ![Mapeamentos de atributos](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, consulte as instruções no [tutorial de filtro de Scoping](./../active-directory-saas-scoping-filters.md).

15. Para ativar o Azure AD para Zscaler três do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção:

    ![Estado de aprovisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento Zscaler três escolhendo os valores pretendidos sob **âmbito** no **definições** secção:

    ![Valores de âmbito](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, selecione **guardar**:

    ![Selecionar guardar](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Esta operação é iniciada a sincronização inicial de todos os utilizadores e grupos definidos em **âmbito** no **definições** secção. A sincronização inicial demora mais de sincronizações subsequentes, o que ocorrer sobre cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode monitorizar o progresso no **detalhes de sincronização** secção. Também pode seguir links para um relatório de atividade aprovisionamento, que descreve todas as ações executadas pelo Azure AD no Zscaler três do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
