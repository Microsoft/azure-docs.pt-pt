---
title: 'Tutorial: Configurar o Zscaler ZSCloud para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o Zscaler ZSCloud automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: caf306360d3a9f044ed7f786aea616d3f5e0bda6
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344835"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler ZSCloud para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler ZSCloud e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos para Zscaler ZSCloud automaticamente.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../active-directory-saas-app-provisioning.md).

> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

*   Um inquilino do Azure AD
*   Um inquilino do Zscaler ZSCloud
*   Uma conta de utilizador no Zscaler ZSCloud com permissões de administrador

> [!NOTE]
> A integração de aprovisionamento do Azure AD depende da API de SCIM do Zscaler ZSCloud, que está disponível para programadores do Zscaler ZSCloud para contas com o pacote da empresa.

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Adicionando Zscaler ZSCloud da Galeria
Antes de configurar o Zscaler ZSCloud para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Zscaler ZSCloud a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Zscaler ZSCloud a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]

3. Para adicionar Zscaler ZSCloud, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Zscaler ZSCloud**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/appsearch.png)

5. No painel de resultados, selecione **Zscaler ZSCloud**e, em seguida, clique nas **Add** botão para adicionar o Zscaler ZSCloud à sua lista de aplicações SaaS.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/appsearchresults.png)

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/appcreation.png)

## <a name="assigning-users-to-zscaler-zscloud"></a>Atribuir utilizadores a Zscaler ZSCloud

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Zscaler ZSCloud. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Zscaler ZSCloud ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Dicas importantes para atribuir utilizadores a Zscaler ZSCloud

*   Recomenda-se que um único utilizador do Azure AD está atribuído a Zscaler ZSCloud para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para Zscaler ZSCloud, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-zscloud"></a>Configurar o aprovisionamento automático de utilizadores para Zscaler ZSCloud

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Zscaler ZSCloud com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Zscaler ZSCloud, seguindo as instruções fornecidas no [Zscaler ZSCloud único início de sessão tutorial](zscaler-zsCloud-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-zscloud-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Zscaler ZSCloud no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione o Zscaler ZSCloud da sua lista de aplicações SaaS.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/appinstancesearch.png)

3. Selecione o **aprovisionamento** separador.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** da sua conta do Zscaler ZSCloud conforme descrito no passo 6.

6. Para obter o **URL de inquilino** e **segredo de Token**, navegue até **administração > definições de autenticação** na interface de utilizador do portal do Zscaler ZSCloud e clique em  **SAML** sob **tipo de autenticação**. 

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Clique em **configurar SAML** para abrir **configuração SAML** opções. 

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)
    
    Selecione **aprovisionamento Enable SCIM-Based** para obter **URL de Base** e **Token de portador**, em seguida, guarde as definições. Cópia a **URL de Base** ao **URL de inquilino** e **Token de portador** para **segredo de Token** no portal do Azure.

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Zscaler ZSCloud. Se a ligação falhar, certifique-se de que sua conta do Zscaler ZSCloud tem permissões de administrador e tente novamente.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)
    
8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para Zscaler ZSCloud**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Zscaler ZSCloud no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zscaler ZSCloud para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar Azure grupos do Active Directory para Zscaler ZSCloud**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Zscaler ZSCloud no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zscaler ZSCloud para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](./../active-directory-saas-scoping-filters.md).

15. Para ativar o Azure AD para Zscaler ZSCloud do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Zscaler ZSCloud escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Zscaler ZSCloud aprovisionamento](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Zscaler ZSCloud do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
