---
title: 'Tutorial: Configurar Directory federado para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador ao diretório federado automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840592"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configurar Directory federado para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no diretório de Federado e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar os utilizadores e/ou grupos para o diretório de federado automaticamente.

> [!NOTE]
>  Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [A federado](https://www.federated.directory/pricing).
* Uma conta de utilizador no diretório federadas com permissões de administrador.

## <a name="assign-users-to-federated-directory"></a>Atribuir utilizadores ao diretório federado
O Azure Active Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso ao diretório federado. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Directory federado ao seguir as instruções aqui:

 * [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir utilizadores a Directory federado
 * Recomenda-se que um único utilizador do Azure AD é atribuído ao diretório federado para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao diretório federado, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com a função de acesso predefinida são excluídos desde o aprovisionamento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurar o diretório federado para o aprovisionamento

Antes de configurar o diretório federado para aprovisionamento automático de utilizadores com o Azure AD, terá de ativar o aprovisionamento de SCIM no diretório federado.

1. Inicie sessão no seu [federado a consola de administração de diretório](https://federated.directory/of)

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navegue para **diretórios > diretórios de usuário** e selecione o seu inquilino. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Para gerar um token de portador permanente, navegue até ao **chaves de diretório > Criar nova chave.** 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated01.png)

4. Crie uma chave de diretório. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copiar o **Token de acesso** valor. Este valor será introduzido na **segredo de Token** campo no separador aprovisionamento do seu aplicativo federada diretório no portal do Azure. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicionar diretório federado a partir da Galeria

Para configurar o diretório federado para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Directory federado a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar diretório federado a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Directory federado**, selecione **federado Directory** no painel de resultados.

    ![Diretório federado na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado em baixo num browser separado. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Clique em **iniciar sessão**.

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Como o diretório federada é uma aplicação de OpenIDConnect, optar por iniciar sessão no diretório federadas com a conta de trabalho da Microsoft.
    
    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Após uma autenticação com êxito, aceite a solicitação de consentimento para a página de consentimento. O aplicativo, em seguida, irá ser automaticamente adicionado ao seu inquilino e será redirecionado para a sua conta do federado.

    ![diretório federado adicionar SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurar o aprovisionamento automático de utilizadores ao diretório federado 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no diretório federadas com base no utilizador e/ou atribuições de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o diretório federado no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Directory federado**.

    ![O link de Active federado na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada `https://api.federated.directory/v2/` no URL de inquilino. Introduza o valor que obtidos e guardado anteriormente do Directory federado na **segredo de Token**. Clique em **Testar ligação** para garantir que o Azure AD pode ligar ao diretório federado. Se a ligação falhar, certifique-se de que sua conta do federada tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory utilizadores ao diretório federado**.

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o diretório federado no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no diretório federado para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o Azure AD para o diretório federado do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

14. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento do federado escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no diretório federado do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)