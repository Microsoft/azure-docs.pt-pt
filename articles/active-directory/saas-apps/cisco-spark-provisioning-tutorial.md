---
title: 'Tutorial: Configurar o Cisco Spark para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador ao Cisco Spark automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd7145be5f82a90acced0f02e35f6c8880dbcad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120402"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cisco Spark para aprovisionamento automático de utilizadores


O objetivo deste tutorial é demonstrar as etapas a serem executadas no Cisco Spark e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores ao Cisco Spark.


> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

*   Um inquilino do Azure AD
*   Um inquilino de Cisco Spark
*   Uma conta de utilizador no Spark da Cisco com permissões de administrador


> [!NOTE]
> O Azure AD aprovisionamento integração depende a [Cisco Spark Webservice](https://developer.webex.com/getting-started.html), que está disponível para as equipes de Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Adicionar Cisco Spark a partir da Galeria
Antes de configurar o Cisco Spark para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar Cisco Spark a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Cisco Spark a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]

3. Para adicionar Cisco Spark, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Cisco Spark**.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. No painel de resultados, selecione **Cisco Spark**e, em seguida, clique nas **Add** botão para adicionar o Cisco Spark à sua lista de aplicações SaaS.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Atribuir utilizadores a Cisco Spark

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, deve decidir quais os utilizadores no Azure AD precisam de aceder ao Cisco Spark. Depois de decidir, pode atribuir estes utilizadores a Cisco Spark ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Dicas importantes para atribuir utilizadores a Cisco Spark

*   Recomenda-se que um único utilizador do Azure AD é atribuído ao Cisco Spark para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Cisco Spark, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Configurar o aprovisionamento automático de utilizadores ao Cisco Spark

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores no Spark Cisco, com base em atribuições de utilizador no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o Cisco Spark no Azure AD:


1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione Cisco Spark da sua lista de aplicações SaaS.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Selecione o **aprovisionamento** separador.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino**, e **segredo de Token** da conta do Spark Cisco.

    *   Na **URL de inquilino** campo, preencher o URL da API Cisco Spark SCIM para o seu inquilino, que assume a forma de `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, onde `[Tenant ID]` é uma cadeia de caracteres de alfanumérica, conforme descrito no passo 6.

    *   Na **segredo de Token** campo, preencha o segredo de Token, conforme descrito no passo 6.

1. O **ID do inquilino** e **segredo de Token** para o Spark Cisco a conta pode ser encontrada ao iniciar sessão no [site de desenvolvedor do Cisco Spark](https://developer.webex.com/) com a sua conta de administrador. Uma vez conectado -
   * Vá para o [página Introdução](https://developer.webex.com/getting-started.html)
   * Desloque para baixo para o [secção autenticação](https://developer.webex.com/getting-started.html#authentication)
     ![Cisco Spark o Token de autenticação](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
   * A cadeia de alfanumérica na caixa é sua **segredo de Token**. Copie este token para a área de transferência
   * Vá para o [página de obter minhas próprias detalhes](https://developer.webex.com/endpoint-people-me-get.html)
       * Certifique-se de que modo de teste está ON
       * Escreva a palavra "Bearer" seguido de um espaço e, em seguida, cole o Token do segredo no campo de autorização ![Cisco Spark o Token de autenticação](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Clique em executar
   * O texto de resposta para a direita, o **ID de inquilino** aparece como "orgId":

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

1. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar ao Cisco Spark. Se a ligação falhar, certifique-se de que a conta de Cisco Spark tem permissões de administrador e tente novamente.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users ao Cisco Spark**.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Spark no Cisco a **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Cisco Spark para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o Azure AD para o Cisco Spark do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento Cisco Spark escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Spark Cisco aprovisionamento](./media/cisco-spark-provisioning-tutorial/Save.png)


Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Cisco Spark do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Cisco Spark está atualmente em fase de teste de campo antecipada (TEF) da Cisco. Para obter mais informações, contacte [equipa de suporte do Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
