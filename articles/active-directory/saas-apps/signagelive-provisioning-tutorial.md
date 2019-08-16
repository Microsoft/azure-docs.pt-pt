---
title: 'Tutorial: Configurar o Signagelive para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Signagelive.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 7838fd30869629298c5b44cc4b3e5c1e5daa7051
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520000"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Tutorial: Configurar o Signagelive para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Signagelive e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Signagelive.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um locatário do Signagelive](https://signagelive.com/pricing/)
* Uma conta de usuário no Signagelive com permissões de administrador.

## <a name="assigning-users-to-signagelive"></a>Atribuindo usuários ao Signagelive   

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Signagelive. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Signagelive seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Dicas importantes para atribuir usuários ao Signagelive   

* É recomendável que um único usuário do Azure AD seja atribuído ao Signagelive para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Signagelive, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-signagelive--for-provisioning"></a>Configurar o Signagelive para provisionamento

Antes de configurar o Signagelive para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Signagelive.

1.  Entre em contato com o [Signagelive](mailto:development@signagelive.com) para obter o token secreto necessário para configurar o provisionamento do SCIM.


## <a name="add-signagelive-from-the-gallery"></a>Adicionar o Signagelive da Galeria

Para configurar o Signagelive para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Signagelive da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Signagelive da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Signagelive**, selecione **Signagelive** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Signagelive na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Configurando o provisionamento automático de usuário para o Signagelive    

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Signagelive com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
>  Você também pode optar por habilitar o logon único baseado em SAML para o BitaBIZ, seguindo as instruções fornecidas no [tutorial de logon único do Signagelive](Signagelive-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Signagelive no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Signagelive**.

    ![O link do Signagelive na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira ` https://samlapi.signagelive.com/scim/v2` a **URL de locatário**. No campo **token secreto** , insira o valor do **token** de portador fornecido pela equipe de desenvolvimento de engenharia. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Signagelive. Se a conexão falhar, verifique se sua conta do Signagelive tem permissões de administrador ![e tente novamente URL de locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Signagelive**.

    ![Mapeamentos de usuário Signagelive](media/signagelive-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Signagelive na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Signagelive para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do Signagelive](media/signagelive-provisioning-tutorial/userattribute.png)

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory grupo para Signagelive**.

    ![Mapeamentos de usuário Signagelive](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Signagelive na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de grupo no Signagelive para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do Signagelive](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Signagelive, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Signagelive escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Signagelive.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)