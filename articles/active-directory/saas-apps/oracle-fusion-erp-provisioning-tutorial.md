---
title: 'Tutorial: Configure Oráculo Fusion ERP para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Oracle Fusion ERP.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061204"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Tutorial: Configure Oráculo Fusion ERP para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Oracle Fusion ERP e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o ERP da Oracle Fusion.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [Os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* Um [inquilino ERP da Oracle Fusion.](https://www.oracle.com/applications/erp/)
* Uma conta de utilizador no ErP oracle Fusion com permissões de administrador.

## <a name="assign-users-to-oracle-fusion-erp"></a>Atribuir utilizadores ao ERP de Fusão Oracle 
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao ERP de fusão oracle. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao ERP oracle Fusion seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Dicas importantes para atribuir utilizadores ao ERP de Fusão Oracle 

 * Recomenda-se que um único utilizador da AD Azure seja atribuído ao ERP oracle Fusion para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao ERP oracle Fusion, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configurar o ERP oracle fusion para o fornecimento

Antes de configurar o Oracle Fusion ERP para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no ErP oracle Fusion.

1. Inscreva-se na consola [de administração DOMO Fusion ERP](https://cloud.oracle.com/sign-in)

2. Clique no Navegador no canto superior esquerdo. Em **ferramentas,** selecione Consola de **Segurança**.

    ![Oracle Fusion ERP Adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navegar para **utilizadores**.
    
    ![Oracle Fusion ERP Adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Guarde o nome de utilizador e a palavra-passe para a conta de utilizador administrador que utilizará para iniciar sessão na consola de administração DORácula Fusion ERP. Estes valores devem ser inseridos nos campos **'Nome** de utilizador e **palavra-passe'** do Administrador no separador de fornecimento da sua aplicação ERP Oracle Fusion no portal Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Adicione o ERP oracle fusion da galeria

Para configurar o ErP oracle Fusion para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o ErP oracle fusion da galeria de aplicações Azure AD à sua lista de aplicações geridas saaS.

**Para adicionar o ErP oracle fusion da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o ERP oracle **Fusion,** selecione **ORP** de Fusão Oracle no painel de resultados.

    ![ErP da Oracle Fusion na lista de resultados](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configure o fornecimento automático de utilizadores ao ERP de Fusão Oracle 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no ERP de fusão oracle com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o ERP de fusão oracle, seguindo as instruções fornecidas no tutorial de inscrição individual do [Oracle Fusion ERP Single](oracle-fusion-erp-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Oracle Fusion ERP, consulte a [REST API para características comuns na nuvem](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)de aplicações oracle .

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para fuze em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Oráculo Fusion ERP**.

    ![O link ERP da Oracle Fusion na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Introduza o nome de utilizador administrativo e a palavra-passe recuperada anteriormente nos campos De utilizador e **palavra-passe** **do Administrador.** Clique na **ligação de teste** entre o Azure AD e o Oracle Fusion ERP. 

    ![Oracle Fusion ERP Adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Oracle Fusion ERP**.

    ![Oracle Fusion ERP Adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Oráculo Fusion ERP na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no ERP de fusão oracle para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Oracle Fusion ERP Adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Oracle Fusion ERP**.

    ![Mapeamentos do grupo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Oráculo Fusion ERP na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no ERP de fusão oracle para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o ERP de Fusão Oracle, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer ao ERP oracle Fusion, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no ERP da Oracle Fusion.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O ERP oracle Fusion apenas suporta a autenticação básica para o seu ponto final SCIM.
* O ERP oracle Fusion não apoia o fornecimento de grupos.
* As funções no ErP oracle Fusion são mapeadas para grupos em Azure AD. Para atribuir funções aos utilizadores no Oracle Fusion ERP da Azure AD, terá de atribuir utilizadores aos grupos AD Azure desejados que têm o nome de funções no ErP da Oracle Fusion.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
