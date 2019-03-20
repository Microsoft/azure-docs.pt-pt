---
title: 'Tutorial: Configurar o Zendesk para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador ao Zendesk automaticamente.
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
ms.date: 01/31/2018
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e05e5e430760d16d5d494a6f27ef985aafc487
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097996"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zendesk e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar os utilizadores e/ou grupos ao Zendesk. 

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

*   Um inquilino do Azure AD
*   Um inquilino de Zendesk com o [Enterprise](https://www.zendesk.com/product/pricing/) planear ou melhor ativada 
*   Uma conta de utilizador no Zendesk com permissões de administrador 

> [!NOTE]
> Conta com o Azure AD, integração de aprovisionamento a [API do Rest de Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), que está disponível para as equipes do Zendesk no plano de Enterprise ou melhor.

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar Zendesk a partir da Galeria
Antes de configurar o Zendesk para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Zendesk a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Zendesk a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]
    
3. Para adicionar o Zendesk, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Zendesk**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. No painel de resultados, selecione **Zendesk**e, em seguida, clique nas **Add** botão para adicionar o Zendesk à sua lista de aplicações SaaS.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Atribuir utilizadores a Zendesk

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de aceder ao Zendesk. Depois de decidir, pode atribuir estes utilizadores e/ou grupos ao Zendesk, seguindo as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir utilizadores a Zendesk

*    Funções de Zendesk são automaticamente e dinamicamente preenchidas no portal do Azure da interface do Usuário hoje mesmo. Antes de atribuir funções de Zendesk para os utilizadores, certifique-se de que uma sincronização inicial foi concluída em relação a Zendesk para obter as funções mais recente no seu inquilino do Zendesk.

*    Recomenda-se que um único utilizador do Azure AD é atribuído ao Zendesk para testar sua inicial configuração de aprovisionamento de utilizadores automática. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde quando os testes forem bem-sucedidos.
  
*   Recomenda-se que um único utilizador do Azure AD é atribuído ao Zendesk para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Zendesk, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configurar o aprovisionamento automático de utilizadores ao Zendesk 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Zendesk com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Zendesk, seguindo as instruções fornecidas no [Zendesk único início de sessão tutorial](zendesk-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Zendesk no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione o Zendesk da sua lista de aplicações SaaS.
 
    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Selecione o **aprovisionamento** separador.
    
    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sob o **credenciais de administrador** secção, de entrada a **nome de utilizador administrador**, **segredo de Token**, e **domínio** da conta do Zendesk. Exemplos destes valores são:

   * Na **nome de utilizador administrador** campo, preencher o nome de utilizador de conta de administrador do seu inquilino do Zendesk. Exemplo: admin@contoso.com.

   * Na **segredo de Token** campo, preencha o token SECRETO, conforme descrito no passo 6.

   * Na **domínio** campo, preencher o subdomínio do seu inquilino do Zendesk.
     Exemplo: Uma conta com um URL de inquilino do https://my-tenant.zendesk.com, o subdomínio seria **meu inquilino**.

6. O **segredo de Token** para o Zendesk conta está localizada na **administração > API > definições**.
   Certifique-se de que **Token de acesso** está definida como **ativado**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar ao Zendesk. Se a ligação falhar, certifique-se de que sua conta do Zendesk com permissões de administrador e tente novamente.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory ao Zendesk**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD ao Zendesk no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zendesk para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory ao ZenDesk**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD ao Zendesk no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zendesk para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para Zendesk do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definir a utilizadores e/ou grupos que pretende aprovisionar ao Zendesk ao selecionar os valores pretendidos na **âmbito** no **definições** secção.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Zendesk do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector
* Zendesk oferece suporte a utilização de grupos para os utilizadores com apenas as funções do agente. Para obter mais informações, consulte [documentação do Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).
* Quando uma função personalizada é atribuída a um utilizador e/ou grupo, o utilizador automático do Azure AD do serviço de aprovisionamento também atribuirá a função predefinida **agente**. Apenas **agentes** pode ser atribuída uma função personalizada. Para obter mais informações, consulte este [documentação da API do Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
