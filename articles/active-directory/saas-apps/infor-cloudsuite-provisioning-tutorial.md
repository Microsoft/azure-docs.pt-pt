---
title: 'Tutorial: Configure Infor CloudSuite para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Infor CloudSuite.
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
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057552"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Infor CloudSuite e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de utilizador no Infor CloudSuite com permissões de administrador.

## <a name="assigning-users-to-infor-cloudsuite"></a>Atribuir utilizadores ao Infor CloudSuite

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Infor CloudSuite. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Infor CloudSuite seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir utilizadores ao Infor CloudSuite

* Recomenda-se que um único utilizador da AD Azure seja designado para o Infor CloudSuite para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Infor CloudSuite, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar infor CloudSuite para provisionamento

1. Inscreva-se na consola [Infor CloudSuite Admin](https://www.infor.com/customer-center). Clique no ícone do utilizador e, em seguida, navegue para **a gestão**do utilizador .

    ![Consola infor CloudSuite Admin](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone do menu no canto superior esquerdo do ecrã. Clique em **Gerir**.

    ![Infor CloudSuite Adicionar SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegar para **contas SCIM**.

    ![Conta Infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um utilizador de administração clicando no ícone plus. Forneça uma **palavra-passe SCIM** e escreva a mesma palavra-passe em **'Confirmar Palavra-passe'.** Clique no ícone da pasta para guardar a palavra-passe. Em seguida, verá um **Identificador de Utilizador** gerado para o utilizador administrativo.

    ![Utilizador infor CloudSuite Admin](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Palavra-passe infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identificador Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Para gerar o token do portador, copie o **Identificador** de Utilizador e a **Palavra-passe SCIM**. Colá-los em bloco de notas++ separados por um cólon. Codificar o valor de cadeia navegando até **plugins > ferramentas MIME > Basic64 Encode**. 

    ![Identificador Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copie o símbolo do portador. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação Infor CloudSuite no portal Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicione Infor CloudSuite da galeria

Antes de configurar o Infor CloudSuite para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar infor CloudSuite da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Infor CloudSuite na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **infor CloudSuite,** selecione **Infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurar o fornecimento automático de utilizadores ao Infor CloudSuite 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Infor CloudSuite com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para infor CloudSuite, seguindo as instruções fornecidas no [tutorial infor CloudSuite Single](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Infor CloudSuite, consulte [isto](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para infor CloudSuite em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Infor CloudSuite**.

    ![O link Infor CloudSuite na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor simbólico do portador recuperado anteriormente em **Secret Token**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Infor CloudSuite. Se a ligação falhar, certifique-se de que a sua conta Infor CloudSuite tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Infor CloudSuite**.

    ![Mapeamento de utilizadores infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Infor CloudSuite na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Infor CloudSuite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Infor CloudSuite**.

    ![Mapeamento do grupo Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Infor CloudSuite na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Infor CloudSuite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para Infor CloudSuite, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Infor CloudSuite, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Infor CloudSuite.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)