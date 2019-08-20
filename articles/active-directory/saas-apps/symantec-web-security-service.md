---
title: 'Tutorial: Configurar o Symantec WSS (Web Security Service) para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Symantec WSS (Web Security Service).
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
ms.openlocfilehash: cfdf5487bcd4c33a21fca593fef829c58d2d099d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576202"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Tutorial: Configurar o Symantec WSS (Web Security Service) para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Symantec WSS (Web Security Service) e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Symantec Web Security Serviço (WSS).

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Symantec WSS (Web Security Service)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uma conta de usuário no Symantec WSS (Web Security Service) com permissões de administrador.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Atribuindo usuários ao Symantec WSS (Web Security Service)

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Symantec WSS (Web Security Service). Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Symantec WSS (Web Security Service) seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Dicas importantes para atribuir usuários ao Symantec WSS (Web Security Service)

* É recomendável que um único usuário do Azure AD seja atribuído ao Symantec WSS (Web Security Service) para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Symantec WSS (Web Security Service), você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurar o Symantec WSS (Web Security Service) para provisionamento

Antes de configurar o Symantec WSS (Web Security Service) para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Symantec WSS (Web Security Service).

1. Entre no console de [Administração do Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Navegue até o**serviço**de **soluções** > .

    ![Symantec WSS (Web Security Service)](media/symantec-web-security-service/service.png)

2. Navegue para > integraçãode > manutenção de conta**nova integração**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecione **usuários de terceiros & a sincronização de grupos**. 

    ![Serviço de segurança na Web da Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Copie a **URL** e o **token**do SCIM. Esses valores serão inseridos no campo **URL do locatário** e **token secreto** na guia provisionamento do seu aplicativo Symantec WSS (Web Security Service) no portal do Azure.

    ![Serviço de segurança na Web da Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Adicionar o Symantec WSS (Web Security Service) por meio da Galeria

Para configurar o Symantec WSS (Web Security Service) para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o Symantec WSS (Web Security Service) da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Symantec WSS (Web Security Service) da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Symantec Web Security Service**, selecione **Symantec Web Security Service** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![O Symantec WSS (Web Security Service) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurando o provisionamento automático de usuário para o Symantec WSS (Web Security Service)

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Symantec WSS (Web Security Service) com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Symantec WSS (Web Security Service), seguindo as instruções fornecidas no [tutorial de logon único do Symantec WSS (Web Security Service)](symantec-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Symantec WSS (Web Security Service) no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Symantec Web Security Service**.

    ![O link do Symantec WSS (Web Security Service) na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira a **URL scim** e os valores de **token** recuperados anteriormente na **URL do locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Symantec Web Security Service. Se a conexão falhar, verifique se sua conta do Symantec WSS (Web Security Service) tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o Symantec WSS (Web Security Service)** .

    ![Mapeamentos de usuário do Symantec WSS (Web Security Service)](media/symantec-web-security-service/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Symantec WSS (Web Security Service) na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Symantec WSS (Web Security Service) para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de usuário do Symantec WSS (Web Security Service)](media/symantec-web-security-service/userattribute.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o Symantec Web Security Service**.

    ![Mapeamentos de usuário do Symantec WSS (Web Security Service)](media/symantec-web-security-service/groupmapping.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Symantec WSS (Web Security Service) na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Symantec WSS (Web Security Service) para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de usuário do Symantec WSS (Web Security Service)](media/symantec-web-security-service/groupattribute.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Symantec Web Security Service, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Symantec WSS (Web Security Service) escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo levará para o provisionamento de usuários e/ou grupos, consulte [quanto tempo levará para provisionar usuários](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Symantec WSS (Web Security Service). Para obter mais informações, consulte [verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
