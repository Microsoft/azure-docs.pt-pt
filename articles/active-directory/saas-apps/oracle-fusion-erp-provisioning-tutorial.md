---
title: 'Tutorial: Configurar o Oracle Fusion ERP para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desaparamento de contas de utilizador ao ERP da Oracle Fusion.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358444"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Tutorial: Configurar o Oracle Fusion ERP para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Oracle Fusion ERP e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e/ou grupos de fornecimento e/ou grupos da Oracle Fusion ERP.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* Um [inquilino da Oracle Fusion ERP.](https://www.oracle.com/applications/erp/)
* Uma conta de utilizador no Oracle Fusion ERP com permissões de administração.

## <a name="assign-users-to-oracle-fusion-erp"></a>Atribuir utilizadores ao Oracle Fusion ERP 
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Oracle Fusion ERP. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Oracle Fusion ERP seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Dicas importantes para atribuir utilizadores ao Oracle Fusion ERP 

 * Recomenda-se que um único utilizador AZure AD seja atribuído ao Oracle Fusion ERP para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Oracle Fusion ERP, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configurar o ERP de fusão oracle para provisão

Antes de configurar o Oracle Fusion ERP para o fornecimento automático do utilizador com Azure AD, terá de ativar o fornecimento scim no Oracle Fusion ERP.

1. Inscreva-se na consola de administração [ERP da Oracle Fusion](https://cloud.oracle.com/sign-in)

2. Clique no Navegador no canto superior esquerdo. Em **Ferramentas**, selecione **Consola de Segurança**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Screenshot da página Do Navegador na consola de administração Oracle Fusion E R P. As ferramentas e a consola de segurança estão em destaque." border="false":::

3. Navegue para **os Utilizadores**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Screenshot de um painel na consola de administração Oracle Fusion E R P. Destaca-se o item Utilizadores." border="false":::

4. Guarde o nome de utilizador e a palavra-passe para a conta de utilizador administrada que utilizará para iniciar sessão na consola de administração ERP da Oracle Fusion. Estes valores devem ser introduzidos nos campos nome de utilizador e **palavra-passe** do **Administrador** no separador De Provisionamento da sua aplicação ERP oracle Fusion no portal Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Adicione Oracle Fusion ERP da galeria

Para configurar o Oracle Fusion ERP para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Oracle Fusion ERP da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Oracle Fusion ERP da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Oracle Fusion ERP,** selecione **Oracle Fusion ERP** no painel de resultados.

    ![Oracle Fusion ERP na lista de resultados](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configure o fornecimento automático de utilizadores ao ERP da Oracle Fusion 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Oracle Fusion ERP com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o Oracle Fusion ERP seguindo as instruções fornecidas no tutorial de assinatura único do [Oracle Fusion ERP.](oracle-fusion-erp-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final SCIM da Oracle Fusion ERP, consulte a [API REST para funcionalidades comuns na Nuvem de Aplicações oracle.](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para fuze em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Oracle Fusion ERP**.

    ![O link ERP da Oracle Fusion na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` na URL do **inquilino.** Introduza o nome de utilizador e a palavra-passe de administrador recuperados anteriormente nos campos nome de utilizador e **palavra-passe** do **administrador.** Clique na **ligação de teste** entre Azure AD e Oracle Fusion ERP. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Screenshot da secção de credenciais de administração. Um botão de ligação de teste e campos para um Tenant U R L, nome de utilizador de administração e senha de administração são visíveis." border="false":::

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Screenshot da secção mappings. Em Nome, é visível o Synchronize Azure Ative Directory Users para a Oracle Fusion E R P." border="false":::

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Oracle Fusion ERP na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Oracle Fusion ERP para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Screenshot da página De mapeamentos de atributos. Uma tabela lista os atributos Azure Ative Directory e Oracle Fusion E R P e a precedência correspondente." border="false":::

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Oracle Fusion ERP**.

    ![Mapeamentos do grupo ERP da Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Oracle Fusion ERP na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Oracle Fusion ERP para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo ERP da Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Oracle Fusion ERP, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Oracle Fusion ERP, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Oracle Fusion ERP.

    Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Oracle Fusion ERP só suporta a Autenticação Básica para o seu ponto final SCIM.
* O Oracle Fusion ERP não suporta o fornecimento de grupos.
* As funções no Oracle Fusion ERP são mapeadas para grupos em Azure AD. Para atribuir funções aos utilizadores no Oracle Fusion ERP a partir da Azure AD, terá de atribuir os utilizadores aos grupos AD Azure desejados que têm o nome de funções no Oracle Fusion ERP.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
