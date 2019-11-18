---
title: 'Tutorial: configurar o SpaceIQ para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SpaceIQ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 207c8214-6304-4687-afc6-61562f0041a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 37c73b72abfe1fefa59aca719a11524c7d080186
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134869"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Tutorial: configurar o SpaceIQ para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SpaceIQ e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no SpaceIQ.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do SpaceIQ](https://spaceiq.com/)
* Uma conta de usuário no SpaceIQ com permissões de administrador.

## <a name="assigning-users-to-spaceiq"></a>Atribuindo usuários ao SpaceIQ

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao SpaceIQ. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao SpaceIQ seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Dicas importantes para atribuir usuários ao SpaceIQ

* É recomendável que um único usuário do Azure AD seja atribuído ao SpaceIQ para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao SpaceIQ, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-spaceiq-for-provisioning"></a>Configurar o SpaceIQ para provisionamento

1. Entre no console do [administrador do SpaceIQ](https://main.spaceiq.com/login/). Navegue até **configurações** selecionando-a no menu suspenso no canto superior direito da tela.

    ![Console de administração do SpaceIQ](media/spaceiq-provisioning-tutorial/admin.png)

2.  Na página **configurações** , selecione **integrações**de terceiros.

    ![SpaceIQ adicionar SCIM](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Navegue até o **provisionamento e** a guia SSO. pesquise o bloco do **Azure** . Clique em **Ativar**.

    ![Provisionamento e SSO do SpaceIQ](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ ativar o Azure ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Copie o **token de portador scim**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo SpaceIQ no portal do Azure. Clique em **Ativar**

    ![SpaceIQ criar token](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Adicionar o SpaceIQ da Galeria

Antes de configurar o SpaceIQ para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o SpaceIQ da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SpaceIQ da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **SpaceIQ**, selecione **SpaceIQ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SpaceIQ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Configurando o provisionamento automático de usuário para o SpaceIQ 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no SpaceIQ com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o SpaceIQ, seguindo as instruções fornecidas no [tutorial de logon único do SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para SpaceIQ no Azure AD:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SpaceIQ**.

    ![O link do SpaceIQ na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://api.spaceiq.com/scim` na **URL do locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao SpaceIQ. Se a conexão falhar, verifique se sua conta do SpaceIQ tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para SpaceIQ**.

    ![Mapeamentos de usuário SpaceIQ](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o SpaceIQ na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no SpaceIQ para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do SpaceIQ](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o SpaceIQ, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para o SpaceIQ escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no SpaceIQ.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)