---
title: 'Tutorial: configurar o infor CloudSuite para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408719"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: configurar infor CloudSuite para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no infor CloudSuite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de usuário no infor CloudSuite com permissões de administrador.

## <a name="assigning-users-to-infor-cloudsuite"></a>Atribuindo usuários ao infor CloudSuite

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao infor CloudSuite. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao infor CloudSuite seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir usuários ao infor CloudSuite

* É recomendável que um único usuário do Azure AD seja atribuído a infor CloudSuite para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao infor CloudSuite, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar o infor CloudSuite para provisionamento

1. Entre no console de [Administração do infor CloudSuite](https://www.infor.com/customer-center). Clique no ícone de usuário e navegue até **Gerenciamento de usuário**.

    ![Console de administração do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone de menu no canto superior esquerdo da tela. Clique em **gerenciar**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegue até **contas do scim**.

    ![Conta do infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um usuário administrador clicando no ícone de adição. Forneça uma **senha do scim** e digite a mesma senha em **Confirmar senha**. Clique no ícone de pasta para salvar a senha. Em seguida, você verá um **identificador de usuário** gerado para o usuário administrador.

    ![Usuário administrador do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite senha](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identificador de CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Para gerar o token de portador, copie o **identificador de usuário** e a **senha scim**. Cole-os no bloco de notas + + separados por dois-pontos. Codifique o valor da cadeia de caracteres navegando até **plug-ins > ferramentas MIME > codificação Basic64**. 

    ![Identificador de CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copie o token de portador. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo infor CloudSuite no portal do Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicionar o infor CloudSuite da Galeria

Antes de configurar o infor CloudSuite para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o infor CloudSuite da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o infor CloudSuite da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **infor CloudSuite**, selecione **infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurando o provisionamento automático de usuário para infor CloudSuite 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no infor CloudSuite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para infor CloudSuite, seguindo as instruções fornecidas no tutorial de [logon único do infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do infor CloudSuite, confira [isso](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para infor CloudSuite no Azure AD:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **infor CloudSuite**.

    ![O link infor CloudSuite na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` na **URL do locatário**. Insira o valor do token de portador recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao infor CloudSuite. Se a conexão falhar, verifique se sua conta do infor CloudSuite tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para infor CloudSuite**.

    ![Mapeamentos de usuário do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para infor CloudSuite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no infor CloudSuite para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para infor CloudSuite**.

    ![Mapeamentos de grupo infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para infor CloudSuite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em infor CloudSuite para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Infor atributos de grupo CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para infor CloudSuite, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para infor CloudSuite escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no infor CloudSuite.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)