---
title: 'Tutorial: Configure RFPIO para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador para o RFPIO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 54419db4-47d5-4fb4-ab74-7b0b28afb11b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 6ae423305b39c1335b5db1cd893d5f817be1929b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060869"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Tutorial: Configure rfPIO para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no RFPIO e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o RFPIO.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino RFPIO.](https://www.rfpio.com/product/)
* Uma conta de utilizador no RFPIO com permissões de administrador.

## <a name="assigning-users-to-rfpio"></a>Atribuir utilizadores ao RFPIO

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao RFPIO. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao RFPIO seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Dicas importantes para atribuir utilizadores ao RFPIO

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao RFPIO para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao RFPIO, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-rfpio-for-provisioning"></a>Configurar rfPIO para o provisionamento

Antes de configurar o RFPIO para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no RFPIO.

1.  Inscreva-se na consola rfpio Admin. Na parte inferior à esquerda da consola de administração, clique em **Tenant**.

    ![Consola de administrador rfpio](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Clique em **Configurações de Organização**.
    
    ![Administrador RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Navegue para a**SEGURANÇA DA** >  **GESTÃO** > DO UTILIZADOR**SCIM**.

    ![RFPIO Adicionar SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Certifique-se de que o **fornecimento de utilizador automático** está ativado. Clique na **GERAÇÃO SCIM API TOKEN**.

    ![RFPIO Criar Token](media/rfpio-provisioning-tutorial/generate.png)

5.  Guarde o **Token SCIM API,** uma vez que esta ficha não será exibida novamente para fins de segurança. Este valor será inserido no campo **Secret Token** no separador de provisionamento da sua aplicação RFPIO no portal Azure.

    ![RFPIO Criar Token](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Adicione RFPIO da galeria

Para configurar o RFPIO para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar RFPIO da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar RFPIO na galeria de aplicações azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **RFPIO,** selecione **RFPIO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Configurar o fornecimento automático de utilizadores ao RFPIO 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em RFPIO com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para O RFPIO, seguindo as instruções fornecidas no [tutorial de inscrição individual RFPIO](rfpio-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para RFPIO em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RFPIO**.

    ![O link RFPIO na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Um valor `https://Azure-test1.rfpio.com/rfpserver/scim/v2`de exemplo é . Insere o valor **SCIM API Token** recuperado anteriormente em **Secret Token**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao RFPIO. Se a ligação falhar, certifique-se de que a sua conta RFPIO tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to RFPIO**.

    ![Mapeamento de utilizadores RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para RFPIO na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar as contas de utilizador no RFPIO para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o RFPIO, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao RFPIO, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no RFPIO.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O RFPIO não apoia os grupos que atuam atualmente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
