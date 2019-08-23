---
title: 'Tutorial: Configurar o Workgrid para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Workgrid.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: c714fa736375e36d68a528dfea82c2c4b7ef448f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69908215"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Tutorial: Configurar o Workgrid para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Workgrid e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Workgrid.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um locatário do Workgrid](https://www.workgrid.com/)
* Uma conta de usuário no Workgrid com permissões de administrador.

## <a name="assigning-users-to-workgrid"></a>Atribuindo usuários ao Workgrid 

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Workgrid. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Workgrid seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Dicas importantes para atribuir usuários ao Workgrid 

* É recomendável que um único usuário do Azure AD seja atribuído ao Workgrid para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Workgrid, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-workgrid-for-provisioning"></a>Configurar o Workgrid para provisionamento

Antes de configurar o Workgrid para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Workgrid.

1. Faça logon no Workgrid. Navegue até **usuários > provisionamento de usuário**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. Em **API de gerenciamento de contas**, clique em **criar credenciais**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Copie o **ponto de extremidade scim** e os valores de **token de acesso** . Eles serão inseridos no campo **URL do locatário** e **token secreto** na guia provisionamento do aplicativo Workgrid no portal do Azure.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Adicionar o Workgrid da Galeria

Para configurar o Workgrid para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Workgrid da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workgrid da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Workgrid**, selecione **Workgrid** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workgrid na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configurando o provisionamento automático de usuário para o Workgrid  

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Workgrid com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Workgrid, seguindo as instruções fornecidas no [tutorial de logon único do Workgrid](Workgrid-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Workgrid no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workgrid**.

    ![O link do Workgrid na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira o **ponto de extremidade scim** e os valores de **token de acesso** recuperados anteriormente na **URL do locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Workgrid. Se a conexão falhar, verifique se sua conta do Workgrid tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Workgrid**.

    ![Mapeamentos de usuário Workgrid](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Workgrid na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Workgrid para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do Workgrid](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Workgrid**

    ![Mapeamentos de usuário Workgrid](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Examine os atributos de grupo que são sincronizados do Azure AD para o Workgrid na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Workgrid para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de usuário Workgrid](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o Workgrid, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você deseja provisionar para o Workgrid escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo levará para o provisionamento de usuários e/ou grupos, consulte [quanto tempo levará para provisionar usuários](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Workgrid. Para obter mais informações, consulte [verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
