---
title: 'Tutorial: configurar o Brivo OnAir Identity Connector para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Brivo OnAir Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: c2db09d48f9efc22134f930b367ef16eda62ac6f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174515"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Tutorial: configurar o Brivo OnAir Identity Connector para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Brivo OnAir Identity Connector e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no conector de identidade Brivo OnAir.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do conector de identidade do Brivo OnAir](https://www.brivo.com/quote)
* Uma conta de usuário no conector de identidade Brivo OnAir com permissões de administrador sênior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Atribuindo usuários ao conector de identidade Brivo OnAir

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao conector de identidade Brivo OnAir. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Brivo OnAir Identity Connector seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Dicas importantes para atribuir usuários ao conector de identidade Brivo OnAir

* É recomendável que um único usuário do Azure AD seja atribuído ao Brivo OnAir Identity Connector para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Brivo OnAir Identity Connector, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Configurar o conector de identidade do OnAir Brivo para provisionamento

1.  Entre no console do [administrador do conector de identidade do Brivo OnAir](https://acs.brivo.com/login/). Navegue até **conta > configurações da conta**.

    ![Console de administração do conector de identidade do Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Clique na guia **Azure ad** . Na página de detalhes do **Azure ad** , insira novamente a senha da sua conta de administrador sênior. Clique em **Enviar**.

    ![Brivo OnAir conector de identidade do Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.  Clique no botão **copiar token** e salve o **token secreto**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo Brivo OnAir Identity Connector no portal do Azure.

    ![Token do conector de identidade do Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Adicionar o Brivo OnAir Identity Connector da Galeria

Antes de configurar o Brivo OnAir Identity Connector para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Brivo OnAir Identity Connector da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Brivo OnAir Identity Connector da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Brivo OnAir Identity Connector**, selecione **Brivo OnAir Identity Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Brivo OnAir Identity Connector na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurando o provisionamento automático de usuário para o conector de identidade Brivo OnAir 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Brivo OnAir Identity Connector com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o conector de identidade Brivo OnAir no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Brivo OnAir Identity Connector**.

    ![O link do conector de identidade do Brivo OnAir na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://scim.brivo.com/ActiveDirectory/v2/` na **URL do locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao conector de identidade Brivo OnAir. Se a conexão falhar, verifique se sua conta do conector de identidade Brivo OnAir tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o Brivo OnAir Identity Connector**.

    ![Mapeamentos de usuário do conector de identidade do Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Examine os atributos de usuário que são sincronizados do Azure AD para o conector de identidade do Brivo OnAir na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no conector de identidade Brivo OnAir para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do conector de identidade do Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Brivo OnAir Identity Connector**.

    ![Mapeamentos de grupo do conector de identidade Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o conector de identidade do Brivo OnAir na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no conector de identidade Brivo OnAir para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de grupo do conector de identidade Brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o conector de identidade do Brivo OnAir, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Brivo OnAir Identity Connector escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no conector de identidade do Brivo OnAir.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

