---
title: 'Tutorial: Configure Diretório Federado para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizadores ao Diretório Federado.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057949"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configure O Diretório Federado para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Federado e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos ao Diretório Federado.

> [!NOTE]
>  Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um Diretório Federado.](https://www.federated.directory/pricing)
* Uma conta de utilizador no Diretório Federado com permissões de Administrador.

## <a name="assign-users-to-federated-directory"></a>Atribuir utilizadores ao Diretório Federado
Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Diretório Federado. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Diretório Federado seguindo as instruções aqui:

 * [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir utilizadores ao Diretório Federado
 * Recomenda-se que um único utilizador da AD Azure seja atribuído ao Diretório Federado para testar a configuração automática de fornecimento de utilizadores. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Diretório Federado, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Criação de Diretório Federado para o provisionamento

Antes de configurar o Diretório Federado para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM em Diretório Federado.

1. Inscreva-se na sua Consola de Administrador de [Diretório Federado](https://federated.directory/of)

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navegue para **Diretórios > Diretórios** de utilizadores e selecione o seu inquilino. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Para gerar um token permanente do portador, navegue para **O Diretório Keys > Create New Key.** 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated01.png)

4. Crie uma chave de diretório. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copie o valor do Token de **Acesso.** Este valor será inscrito no campo **Secret Token** no separador de provisionamento da sua aplicação de Diretório Federado no portal Azure. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicione o Diretório Federado da galeria

Para configurar o Diretório Federado para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Diretório Federado da galeria de aplicações Azure AD à sua lista de aplicações geridas do SaaS.

**Para adicionar o Diretório Federado da galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Diretório Federado**, selecione **Diretório Federado** no painel de resultados.

    ![Diretório Federado na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado abaixo num navegador separado. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Clique em **iniciar sessão**.

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Como o Diretório Federado é uma aplicação OpenIDConnect, opte por iniciar sessão no Diretório Federado utilizando a sua conta de trabalho da Microsoft.
    
    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. O pedido será adicionado automaticamente ao seu inquilino e será redirecionado para a sua conta de Diretório Federado.

    ![diretório federado Adicionar SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurar o fornecimento automático de utilizadores ao Diretório Federado 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Diretório Federado com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Diretório Federado em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Diretório Federado**.

    ![O link do Diretório Federado na lista de Candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://api.federated.directory/v2/` no URL do Inquilino. Insera o valor que recuperou e salvou mais cedo do Diretório Federado em **Token Secreto.** Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Diretório Federado. Se a ligação falhar, certifique-se de que a sua conta de Diretório Federado tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Federated Directy**.

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Diretório Federado na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder às contas de utilizador em Diretório Federado para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de Anúncios Azure para o Diretório Federado, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Diretório Federado, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure no Diretório Federado.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
