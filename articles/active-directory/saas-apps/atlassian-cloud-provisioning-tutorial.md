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
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059356"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: configurar o Atlassian Cloud para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Atlassian Cloud e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Atlassian Cloud.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino da Nuvem Atlassian](https://www.atlassian.com/licensing/cloud)
* Uma conta de usuário no Atlassian Cloud com permissões de administrador.

> [!NOTE]
> A integração de fornecimento de AD Azure baseia-se na **Atlassian Cloud SCIM API,** que está disponível para equipas Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud da Galeria

Antes de configurar o Atlassian Cloud para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Nuvem Atlassian a partir da galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **a Nuvem Atlassian,** selecione **Atlassian Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuindo usuários ao Atlassian Cloud

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Atlassian Cloud. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Atlassian Cloud seguindo estas instruções:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir usuários ao Atlassian Cloud

* É recomendável que um único usuário do Azure AD seja atribuído ao Atlassian Cloud para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Atlassian Cloud, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurando o provisionamento automático de usuário para o Atlassian Cloud 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Atlassian Cloud com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para a Nuvem Atlassian, seguindo as instruções fornecidas no tutorial de [inscrição individual da Nuvem Atlassian](atlassian-cloud-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Atlassian Cloud no Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Atlassian Cloud**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Atlassian Cloud**.

    ![O link do Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navegue para [Atlassian Organization Manager](https://admin.atlassian.com) **> selecione o org > diretório**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Clique em **Provisionamento de Utilizador** e clique em **Criar um diretório**. Copie o URL base do **Diretório** e **o Token bearer** para os campos URL do **Inquilino** e **DoKen Secreto,** respectivamente.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Sob a secção **de Credenciais de Administrador,** insere o **URL** do Inquilino e o **Token Secreto** da sua conta atlassian Cloud. Exemplos desses valores são:

   * No campo URL do **Inquilino,** preencha o ponto final específico do inquilino que recebe do Atlassian, conforme descrito no Passo 6. Por exemplo: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * No campo **Secreto token,** povoe o símbolo secreto como descrito no Passo 6.

8. Ao povoar os campos mostrados no Passo 7, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à Cloud Atlassian. Se a conexão falhar, verifique se sua conta do Atlassian Cloud tem permissões de administrador e tente novamente.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Clique em **Guardar**.

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Reveja os atributos do utilizador que são sincronizados de Azure AD para Atlassian Cloud na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Atlassian Cloud para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Reveja os atributos do grupo que são sincronizados de Azure AD para Atlassian Cloud na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Atlassian Cloud para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Para ativar o serviço de provisionamento de AD Azure para a Cloud Atlassian, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Defina os utilizadores e/ou grupos que gostaria de fornecer à Cloud Atlassian, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure na Nuvem Atlassian.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* A Atlassian Cloud permite o fornecimento de utilizadores apenas a partir de [domínios verificados](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* A nuvem Atlassian não dá suporte a renomeações de grupo hoje. Isso significa que qualquer alteração no displayName de um grupo no Azure AD não será atualizada e refletida no Atlassian Cloud.
* O valor do atributo do utilizador de **correio** em Azure AD só é povoado se o utilizador tiver uma Caixa de Correio seletiva Microsoft Exchange. Se o utilizador não tiver um, recomenda-se mapear um atributo diferente desejado aos **e-mails atribuídos** na Nuvem Atlassian.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png