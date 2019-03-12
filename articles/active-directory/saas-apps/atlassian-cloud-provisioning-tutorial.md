---
title: 'Tutorial: Configurar a Cloud da Atlassian para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para a Cloud de Atlassian automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant
ms.openlocfilehash: ca9a569d28e42baafeabc15f49fb7f5206566730
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57572543"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: Configurar a Cloud da Atlassian para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas na Cloud de Atlassian e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos para a Cloud da Atlassian automaticamente. 

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

*   Um inquilino do Azure AD
*   [Um inquilino de nuvem da Atlassian](https://www.atlassian.com/licensing/cloud)
*   Uma conta de utilizador na Cloud da Atlassian com permissões de administrador.

> [!NOTE]
> Conta com o Azure AD, integração de aprovisionamento a **Atlassian Cloud SCIM API**, que está disponível para as equipes de Atlassian Cloud.

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionar Atlassian Cloud a partir da Galeria
Antes de configurar a Cloud da Atlassian para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Atlassian Cloud a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Atlassian Cloud a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]
    
3. Para adicionar Atlassian Cloud, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Atlassian Cloud**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/app-search.png)

5. No painel de resultados, selecione **Atlassian Cloud**e, em seguida, clique nas **Add** botão para adicionar Atlassian Cloud à sua lista de aplicações SaaS.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/app-create.png)

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/app-instance.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuir utilizadores a nuvem de Atlassian

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso à nuvem da Atlassian. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para Atlassian Cloud ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir utilizadores a nuvem de Atlassian

*   Recomenda-se que um único utilizador do Azure AD é atribuído à nuvem da Atlassian para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Quando atribuir um utilizador a nuvem de Atlassian, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurar o aprovisionamento automático de utilizadores para a Cloud de Atlassian 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos na Cloud da Atlassian com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para a nuvem da Atlassian, seguindo as instruções fornecidas no [Atlassian Cloud único início de sessão tutorial](atlassian-cloud-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para a nuvem da Atlassian no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione a nuvem de Atlassian da sua lista de aplicações SaaS.
 
    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/application-instance-search.png)

3. Selecione o **aprovisionamento** separador.
    
    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** da conta de sua nuvem da Atlassian. Exemplos destes valores são:

    *   Na **URL de inquilino** campo, preencha o ponto de extremidade de inquilino específico, receberá da Atlassian, conforme descrito no passo 6. Por exemplo: **https://api.atlassian.com/scim/directory/{directoryId}**

    *   Na **segredo de Token** campo, preencha o token SECRETO, conforme descrito no passo 6.

6. Navegue para [Gestor de organização da Atlassian](https://admin.atlassian.com) **> aprovisionamento de utilizadores** e clique em **criar um Token**. Copiar o **URL de base do diretório** e **Token de portador** para o **URL de inquilino** e **segredo de Token** campos, respetivamente.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian aprovisionamento de Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    
    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar à Cloud da Atlassian. Se a ligação falhar, certifique-se de que sua conta na Cloud da Atlassian tem permissões de administrador e tente novamente.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)
    
8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para Atlassian Cloud**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para a nuvem da Atlassian no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na Cloud da Atlassian para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar Azure grupos do Active Directory para a Cloud de Atlassian**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para a nuvem da Atlassian no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos na Cloud da Atlassian para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para a nuvem da Atlassian do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento na Cloud da Atlassian escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento de Cloud da Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)


Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD na Cloud da Atlassian do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Atlassian Cloud permite o aprovisionamento de utilizadores apenas a partir [domínios verificados](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Nuvem de Atlassian não suporta atualmente muda de grupo. Isso significa que todas as alterações para o displayName de um grupo no Azure AD não vão ser atualizadas e refletidas na Cloud da Atlassian.
* O valor do **correio** só é preenchido o atributo de utilizador no Azure AD Se o utilizador tiver uma caixa de correio do Exchange do Microsoft. Se o utilizador não tiver uma, é recomendado para mapear um atributo pretendido diferente para o **e-mails** atributo na Cloud da Atlassian.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
