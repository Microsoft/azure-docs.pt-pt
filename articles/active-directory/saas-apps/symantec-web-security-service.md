---
title: 'Tutorial: Configure Symantec Web Security Service (WSS) para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Serviço de Segurança Web Symantec (WSS).
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
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063123"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Tutorial: Configure Symantec Web Security Service (WSS) para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Symantec Web Security Service (WSS) e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Symantec Web Security Service (WSS).

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uma conta de utilizador no Symantec Web Security Service (WSS) com permissões de administrador.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Atribuir utilizadores ao Serviço de Segurança Web Symantec (WSS)

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Serviço de Segurança Web Symantec (WSS). Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Serviço de Segurança Web Symantec (WSS) seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Dicas importantes para atribuir utilizadores ao Serviço de Segurança Web Symantec (WSS)

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Serviço de Segurança Web (WSS) da Symantec para testar a configuração automática de fornecimento de utilizadores. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Serviço de Segurança Web Symantec (WSS), deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurar o Serviço de Segurança Web Symantec (WSS) para o fornecimento

Antes de configurar o Symantec Web Security Service (WSS) para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no Serviço de Segurança Web Symantec (WSS).

1. Inscreva-se na sua consola de administração do [Serviço de Segurança Web Symantec](https://portal.threatpulse.com/login.jsp). Navegar para **Soluções** > **Serviço**.

    ![Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Navegar para **manutenção de conta** > **Integrações** > **Nova Integração.**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecione **Utilizadores e Grupos Sincronizados de Terceiros**. 

    ![Serviço de Segurança Web Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Copie o **URL SCIM** e **token**. Estes valores serão inseridos no **campo URL** do Tenant e no campo **Secret Token** no separador de fornecimento da sua aplicação Symantec Web Security Service (WSS) no portal Azure.

    ![Serviço de Segurança Web Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Adicione o Serviço de Segurança Web Symantec (WSS) da galeria

Para configurar o Symantec Web Security Service (WSS) para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Serviço de Segurança Web Symantec (WSS) da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o Serviço de Segurança Web Symantec (WSS) da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o Serviço de **Segurança Web Symantec,** selecione O Serviço de **Segurança Web Symantec** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Serviço de Segurança Web Symantec (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurar o fornecimento automático de utilizadores ao Serviço de Segurança Web Symantec (WSS)

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Serviço de Segurança Web Symantec (WSS) com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal de sessão baseado em SAML para o Symantec Web Security Service (WSS), seguindo as instruções fornecidas no tutorial de inscrição individual do Serviço de [Segurança Web Symantec (WSS).](symantec-tutorial.md) O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Serviço de Segurança Web Symantec (WSS) em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Symantec Web Security Service**.

    ![O link Symantec Web Security Service (WSS) na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, insere os valores **de URL SCIM** e **Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Serviço de Segurança Web Symantec. Se a ligação falhar, certifique-se de que a sua conta symantec Web Security Service (WSS) tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Symantec Web Security Service (WSS)** .

    ![Mapeamento de utilizadores do Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Symantec Web Security Service (WSS) na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Serviço de Segurança Web Symantec (WSS) para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizadores do Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/userattribute.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Symantec Web Security Service**.

    ![Mapeamento de utilizadores do Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Symantec Web Security Service (WSS) na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no Symantec Web Security Service (WSS) para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizadores do Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Serviço de Segurança Web Symantec, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Symantec Web Security Service (WSS) escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo demorará os utilizadores e/ou grupos a fornecer, veja [quanto tempo demorará a fornecer aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Current Status** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Serviço de Segurança Web (WSS) da Symantec. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
