---
title: 'Tutorial: Configure smartFile para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizador ao SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060240"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Tutorial: Configure o SmartFile para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no SmartFile e no Azure Ative Directory (Azure AD) para configurar o Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o SmartFile.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino SmartFile.](https://www.SmartFile.com/pricing/)
* Uma conta de utilizador no SmartFile com permissões de Administrador.

## <a name="assigning-users-to-smartfile"></a>Atribuir utilizadores ao SmartFile

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao SmartFile. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao SmartFile seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Dicas importantes para atribuir utilizadores ao SmartFile

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao SmartFile para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao SmartFile, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-smartfile-for-provisioning"></a>Configurar smartFile para provisionamento

Antes de configurar o SmartFile para o fornecimento automático de utilizadores com o Azure AD, terá de ativar o fornecimento de SCIM no SmartFile e recolher detalhes adicionais necessários.

1. Inscreva-se na consola de administrador smartFile. Navegue para o canto superior direito da consola de administrador SmartFile. Selecione **Chave do Produto**.

    ![Consola de administrador smartFile](media/smartfile-provisioning-tutorial/login.png)

2. Para gerar um símbolo do portador, copie a Chave do **Produto** e **a Palavra-Passe**do Produto . Colá-los num bloco de notas com um cólon entre eles.
    
     ![Smartfile Adicionar SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![Smartfile Adicionar SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Adicione smartFile na galeria

Para configurar o SmartFile para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o SmartFile da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o SmartFile na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **SmartFile,** selecione **o SmartFile** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![SmartFile na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurar o fornecimento automático de utilizadores ao SmartFile 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no SmartFile com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o SmartFile, seguindo as instruções fornecidas no tutorial de [assinatura single do SmartFile](SmartFile-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o SmartFile em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SmartFile**.

    ![O link SmartFile na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5.  No âmbito da secção de `https://<SmartFile sitename>.smartfile.com/ftp/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Um exemplo seria. `https://demo1test.smartfile.com/ftp/scim` Introduza o valor **do token bearer** (ProductKey:ProductPassword) que recuperou anteriormente em **Ficha Secreta**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao SmartFile. Se a ligação falhar, certifique-se de que a sua conta SmartFile tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to SmartFile**.

    ![Mapeamento de utilizador smartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para SmartFile na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar as contas de utilizador no SmartFile para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador smartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para SmartFile**.

    ![Mapeamentos de grupo SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para SmartFile na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no SmartFile para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o SmartFile, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao SmartFile, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no SmartFile.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitações do conector

* O SmartFile apenas suporta eliminações duras. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

 [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
