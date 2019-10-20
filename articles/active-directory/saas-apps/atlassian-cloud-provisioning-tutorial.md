---
title: 'Tutorial: configurar a nuvem do Atlassian para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Atlassian Cloud.
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
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "68561588"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: configurar o Atlassian Cloud para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Atlassian Cloud e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Atlassian Cloud.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de nuvem do Atlassian](https://www.atlassian.com/licensing/cloud)
* Uma conta de usuário no Atlassian Cloud com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do **Atlassian Cloud scim**, que está disponível para as equipes de nuvem do Atlassian.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud da Galeria

Antes de configurar o Atlassian Cloud para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuindo usuários ao Atlassian Cloud

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Atlassian Cloud. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Atlassian Cloud seguindo estas instruções:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir usuários ao Atlassian Cloud

* É recomendável que um único usuário do Azure AD seja atribuído ao Atlassian Cloud para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Atlassian Cloud, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurando o provisionamento automático de usuário para o Atlassian Cloud 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Atlassian Cloud com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Atlassian Cloud, seguindo as instruções fornecidas no [tutorial de logon único do Atlassian Cloud](atlassian-cloud-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Atlassian Cloud no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Atlassian Cloud**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![O link do Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** da conta da nuvem do Atlassian. Exemplos desses valores são:

   * No campo **URL do locatário** , preencha o ponto de extremidade do locatário específico que você recebe do Atlassian, conforme descrito na etapa 6. Por exemplo: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * No campo **token secreto** , preencha o token secreto conforme descrito na etapa 6.

6. Navegue até [Atlassian Organization Manager](https://admin.atlassian.com) **> provisionamento de usuário** e clique em **criar um token**. Copie a **URL base do diretório** e o **token de portador** para os campos **URL do locatário** e **token secreto** , respectivamente.

    ![Atlassian provisionamento de nuvem ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian provisionamento de nuvem ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Atlassian Cloud. Se a conexão falhar, verifique se sua conta do Atlassian Cloud tem permissões de administrador e tente novamente.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Atlassian Cloud na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Atlassian Cloud para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Atlassian Cloud na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Atlassian Cloud para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Atlassian Cloud, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para o Atlassian Cloud escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Atlassian Cloud.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Atlassian Cloud permite o provisionamento de usuários somente de [domínios verificados](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* A nuvem Atlassian não dá suporte a renomeações de grupo hoje. Isso significa que qualquer alteração no displayName de um grupo no Azure AD não será atualizada e refletida no Atlassian Cloud.
* O valor do atributo de usuário de **email** no Azure ad só será populado se o usuário tiver uma caixa de correio do Microsoft Exchange. Se o usuário não tiver um, é recomendável mapear um atributo desejado diferente para o atributo **emails** no Atlassian Cloud.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
