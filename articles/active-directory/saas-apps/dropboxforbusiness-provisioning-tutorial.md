---
title: 'Tutorial: Configurar o Dropbox para negócios para fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores ao Dropbox para Negócios.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938771"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox para o Negócio para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dropbox para Business and Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desa provisionamento automaticamente de utilizadores e/ou grupos ao Dropbox para negócios.

> [!IMPORTANT]
> A Microsoft e o Dropbox vão depreciar a antiga integração dropbox eficaz 04/01/2021. Para evitar perturbações no serviço, recomendamos migrar para a nova integração dropbox que suporta grupos. Para migrar para a nova integração dropbox, adicione e configuure um novo exemplo de Dropbox for Provisioning no seu inquilino Azure AD usando os passos abaixo. Uma vez configurado a nova integração dropbox, desative o Provisioning na antiga integração dropbox para evitar conflitos de provisionamento. Para obter etapas mais detalhadas sobre a migração para a nova integração dropbox, consulte [Update to the new mais recente aplicação Dropbox for Business utilizando Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um Dropbox para inquilino de negócios](https://www.dropbox.com/business/pricing)
* Uma conta de utilizador no Dropbox para Negócios com permissões de Administração.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Adicionar Dropbox para Negócios da galeria

Antes de configurar o Dropbox for Business para fornecimento automático de utilizadores com Azure AD, tem de adicionar o Dropbox for Business da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Dropbox para Negócios na galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Dropbox para Negócios,** selecione **Dropbox para Negócios** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Dropbox para Negócios na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuir utilizadores ao Dropbox para Negócios

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Dropbox para negócios. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Dropbox para Negócios seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir utilizadores ao Dropbox para negócios

* Recomenda-se que um único utilizador Azure AD seja designado para o Dropbox para o Negócios para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Dropbox para Negócios, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurar o fornecimento automático de utilizadores ao Dropbox para negócios 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Dropbox para negócios com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sinal de sing baseado em SAML para o Dropbox para negócios, seguindo as instruções fornecidas no tutorial de assinatura única do [Dropbox para negócios.](dropboxforbusiness-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Dropbox for Business em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dropbox para Negócios**.

    ![O link Dropbox para Negócios na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administrador**, clique em **Autorizar**. Abre um diálogo de login dropbox para Negócios numa nova janela do navegador.

    ![Aprovisionamento ](common/provisioning-oauth.png)

6. No **Drop-in para o Dropbox para o Negócios ligar-se ao diálogo Azure AD, inscreva-se** no seu Dropbox para inquilino de negócios e verifique a sua identidade.

    ![Dropbox para entrada de negócios](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Ao completar os passos 5 e 6, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Dropbox para Negócios. Se a ligação falhar, certifique-se de que a sua conta Dropbox para Negócios tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-oauth.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Dropbox**.

    ![Mapeamentos de utilizadores de caixa de gota](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Reveja os atributos do utilizador que são sincronizados de AD AD a Dropbox na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Dropbox para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador da caixa de lançamento](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Dropbox**.

    ![Mapeamentos do grupo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Dropbox na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Dropbox para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de Ad Azure para o Dropbox, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que deseja providenciar ao Dropbox, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Dropbox.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector
 
* O Dropbox não suporta a suspensão de utilizadores convidados. Se um utilizador convidado for suspenso, esse utilizador será eliminado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

