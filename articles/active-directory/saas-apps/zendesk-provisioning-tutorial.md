---
title: 'Tutorial: Configurar o zendesk para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f9d819533b97a126a324ab867b7185fd6415847
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851970"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o zendesk para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no zendesk e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos no zendesk.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um inquilino do Azure AD.
* Um locatário do zendesk com o plano profissional ou melhor habilitado.
* Uma conta de usuário no zendesk com permissões de administrador.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicionar o zendesk do Azure Marketplace

Antes de configurar o zendesk para o provisionamento automático de usuário com o Azure AD, adicione o zendesk do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o zendesk do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone de Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **zendesk** e selecione **zendesk** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir usuários ao zendesk

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao zendesk. Para atribuir esses usuários ou grupos ao zendesk, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir usuários ao zendesk

* Hoje, as funções do zendesk são preenchidas de forma automática e dinâmica na interface do usuário do portal do Azure. Antes de atribuir funções do zendesk a usuários, verifique se uma sincronização inicial foi concluída no zendesk para recuperar as funções mais recentes em seu locatário do zendesk.

* Recomendamos que você atribua um único usuário do Azure AD ao zendesk para testar a configuração inicial de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais posteriormente depois que os testes forem bem-sucedidos.

* Quando você atribuir um usuário ao zendesk, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurar o provisionamento automático de usuário para o zendesk 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD. Use-o para criar, atualizar e desabilitar usuários ou grupos no zendesk com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o logon único baseado em SAML para o zendesk. Siga as instruções no [tutorial de logon único do zendesk](zendesk-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o zendesk no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos** > empresariais**todos os aplicativos** > **zendesk**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **zendesk**.

    ![O link do zendesk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Provisionamento do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Na seção **credenciais de administrador** , insira o nome de usuário do administrador, o token secreto e o domínio da sua conta do zendesk. Exemplos desses valores são:

   * Na caixa **nome de usuário administrador** , preencha o nome de usuário da conta de administrador em seu locatário do zendesk. Um exemplo é admin@contoso.com.

   * Na caixa **token secreto** , preencha o token secreto, conforme descrito na etapa 6.

   * Na caixa **domínio** , preencha o subdomínio de seu locatário do zendesk. Por exemplo, para uma conta com uma URL de locatário `https://my-tenant.zendesk.com`do, seu subdomínio é **meu locatário**.

6. O token secreto para sua conta do zendesk está localizado em**configurações**da**API** > de **Administração** > . Verifique se o **acesso ao token** está definido como **habilitado**.

    ![Configurações de administrador do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token secreto do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas na etapa 5, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao zendesk. Se a conexão falhar, verifique se sua conta do zendesk tem permissões de administrador e tente novamente.

    ![Conexão de teste do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na caixa **email de notificação** , insira o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o zendesk**.

    ![Sincronização de usuário do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o zendesk na seção mapeamentos de **atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no zendesk para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de usuário correspondentes ao zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o zendesk**.

    ![Sincronização de grupo do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o zendesk na seção mapeamentos de **atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no zendesk para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos do grupo de correspondência do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o zendesk, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Status de provisionamento do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os usuários ou grupos que você deseja provisionar no zendesk. Na seção **configurações** , selecione os valores desejados no **escopo**.

    ![Escopo do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Salvamento do zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD seja executado. 

Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no zendesk.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O zendesk dá suporte ao uso de grupos somente para usuários com funções de **agente** . Para obter mais informações, consulte a [documentação do zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um usuário ou grupo, o serviço de provisionamento automático de usuário do Azure AD também atribui o **agente**de função padrão. Somente os agentes podem ser atribuídos a uma função personalizada. Para obter mais informações, consulte a [documentação da API do zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
