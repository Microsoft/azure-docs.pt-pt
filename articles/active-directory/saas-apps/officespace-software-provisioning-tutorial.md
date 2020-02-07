---
title: 'Tutorial: Configure OfficeSpace Software para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao OfficeSpace Software.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063429"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Tutorial: Configure OfficeSpace Software para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no OfficeSpace Software e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o OfficeSpace Software.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um [inquilino de Software OfficeSpace](https://www.officespacesoftware.com/)
* Uma conta de utilizador no OfficeSpace Software com permissões de Administrador.

## <a name="assigning-users-to-officespace-software"></a>Atribuir utilizadores ao OfficeSpace Software

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao OfficeSpace Software. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao OfficeSpace Software seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Dicas importantes para atribuir utilizadores ao OfficeSpace Software

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao OfficeSpace Software para testar a configuração automática de fornecimento de utilizadores. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao OfficeSpace Software, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-officespace-software-for-provisioning"></a>Configurar o OfficeSpace Software para o provisionamento

1. Inscreva-se na consola [officeSpace Software Admin](https://support.officespacesoftware.com/hc). Navegar para **Definições e Conectores**.

    ![Consola de administradora de software OfficeSpace](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navegue para **Sincronização de Diretório > SCIM**.

    ![Software OfficeSpace Adicionar SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Copiar o Símbolo de **Autenticação SCIM**. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação OfficeSpace Software no portal Azure.

    ![OfficeSpace Software Criar Token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Adicione software OfficeSpace da galeria

Antes de configurar o OfficeSpace Software para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Software OfficeSpace da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar software OfficeSpace da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **OfficeSpace Software,** selecione **OfficeSpace Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Software OfficeSpace na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Configurar o fornecimento automático de utilizadores ao OfficeSpace Software 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no OfficeSpace Software com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para o OfficeSpace Software seguindo as instruções fornecidas no [tutorial de inscrição individual](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial)do OfficeSpace Software . O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o OfficeSpace Software em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OfficeSpace Software**.

    ![O link officeSpace Software na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** input `https://<subdomain>.officespacesoftware.com/api/scim/v2/` formato URL no **URL do Inquilino**. Por exemplo, `https://contoso.officespacesoftware.com/api/scim/v2/`. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Software OfficeSpace. Se a ligação falhar, certifique-se de que a sua conta de Software OfficeSpace tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to OfficeSpace Software**.

    ![Mapeamento de utilizadores de software officeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para OfficeSpace Software na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no OfficeSpace Software para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de software officespace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para o OfficeSpace Software, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja fornecer ao OfficeSpace Software, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure no OfficeSpace Software.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

