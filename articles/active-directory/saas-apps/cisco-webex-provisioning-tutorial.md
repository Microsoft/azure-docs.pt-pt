---
title: 'Tutorial: configurar o Cisco WebEx para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Cisco WebEx.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f4089d690602aa761b1942bd3d538e2ef305aa19
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570370"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: configurar o Cisco WebEx para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Cisco WebEx e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente os usuários para o Cisco WebEx.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um locatário Cisco WebEx](https://www.webex.com/pricing/index.html).
* Uma conta de usuário no Cisco WebEx com permissões de administrador.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco WebEx da Galeria

Antes de configurar o Cisco WebEx para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o Cisco WebEx da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco WebEx da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco WebEx**, selecione **Cisco WebEx** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Cisco WebEx na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Atribuindo usuários ao Cisco WebEx

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários no Azure AD precisam de acesso ao Cisco WebEx. Depois de decidir, você pode atribuir esses usuários ao Cisco WebEx seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Dicas importantes para atribuir usuários ao Cisco WebEx

* É recomendável que um único usuário do Azure AD seja atribuído ao Cisco WebEx para testar a configuração automática de provisionamento de usuário. Usuários adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Cisco WebEx, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurando o provisionamento automático de usuário para o Cisco WebEx

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários no Cisco WebEx com base em atribuições de usuário no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Cisco WebEx no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Cisco WebEx**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cisco WebEx**.

    ![O link do Cisco WebEx na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Provisionamento do Cisco WebEx](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do Cisco WebEx](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário**e o **token secreto** da sua conta do Cisco WebEx.

    ![Provisionamento do Cisco WebEx](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  No campo **URL do locatário** , insira um valor na forma de `https://api.ciscoweb.com/v1/scim/[OrgId]`. Para obter `[OrgId]`, entre no [Hub de controle do Cisco WebEx](https://admin.webex.com/login). Clique no nome da sua organização na parte inferior esquerda e copie o valor da **ID da organização**. 

    * Para obter o valor do **token secreto**, navegue até essa [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na página de entrada do WebEx que aparece, entre com a conta de administrador completa do Cisco WebEx para sua organização. Uma página de erro aparece dizendo que o site não pode ser acessado, mas isso é normal.

        ![Provisionamento do Cisco WebEx](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Copie o valor do token de portador gerado da URL, conforme realçado abaixo. Esse token é válido por 365 dias.
        
        ![Provisionamento do Cisco WebEx](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Cisco WebEx. Se a conexão falhar, verifique se sua conta do Cisco WebEx tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o Cisco WebEx**.

    ![Provisionamento do Cisco WebEx](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Cisco WebEx na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Cisco WebEx para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento do Cisco WebEx](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Cisco WebEx, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Cisco WebEx escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Cisco WebEx.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Cisco WebEx está atualmente na fase de TEF (teste de campo inicial) da Cisco. Para obter mais informações, entre em contato com a [equipe de suporte da Cisco](https://www.webex.co.in/support/support-overview.html). 
* Para obter mais informações sobre a configuração do Cisco WebEx, consulte a documentação da Cisco [aqui](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)