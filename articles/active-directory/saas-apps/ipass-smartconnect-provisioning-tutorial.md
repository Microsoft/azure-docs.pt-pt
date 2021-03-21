---
title: 'Tutorial: Configurar iPass SmartConnect para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores para o iPass SmartConnect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 405a7bc3b653ca7bca026d3318763a4922244e88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093711"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Tutorial: Configurar iPass SmartConnect para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no iPass SmartConnect e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos para iPass SmartConnect.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino iPass SmartConnect.](https://www.ipass.com/buy-ipass/)
* Uma conta de utilizador no iPass SmartConnect com permissões de Administração.

## <a name="assigning-users-to-ipass-smartconnect"></a>Atribuir utilizadores ao iPass SmartConnect

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao iPass SmartConnect. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao iPass SmartConnect seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Dicas importantes para atribuir utilizadores ao iPass SmartConnect

* Recomenda-se que um único utilizador AZure AD seja designado para o iPass SmartConnect para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao iPass SmartConnect, deve selecionar qualquer função específica da aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Configuração iPass SmartConnect para provisionamento

Antes de configurar o iPass SmartConnect para o fornecimento automático de utilizadores com Azure AD, terá de recuperar informações de configuração a partir da consola de administração iPass SmartConnect:

1. Para recuperar o token do portador que é necessário para autenticar contra o seu ponto final do iPass SmartConnect SCIM, consulte a primeira vez que configura o iPass SmartConnect, uma vez que este valor só é fornecido nessa altura. 
2. Se não tiver o token do portador, contacte a [equipa de suporte do iPass SmartConnect](mailto:help@ipass.com) para recuperar um novo.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Adicione iPass SmartConnect da galeria

Para configurar o iPass SmartConnect para fornecimento automático de utilizadores com Azure AD, é necessário adicionar o iPass SmartConnect da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o iPass SmartConnect da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **iPass SmartConnect,** selecione **iPass SmartConnect** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![iPass SmartConnect na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Configurar o fornecimento automático de utilizadores ao iPass SmartConnect 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no iPass SmartConnect com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
>  Pode também optar por ativar o sign-on único baseado em SAML para o iPass SmartConnect, seguindo as instruções fornecidas no [tutorial de assinatura single iPass SmartConnect Single](ipasssmartconnect-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o iPass SmartConnect em AD Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iPass SmartConnect**.

    ![O link iPass SmartConnect na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://openmobile.ipass.com/moservices/scim/v1` na URL do **inquilino.** Introduza o símbolo do portador recuperado mais cedo em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao iPass SmartConnect. Se a ligação falhar, certifique-se de que a sua conta iPass SmartConnect tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para iPass SmartConnect**.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/usermapping.png" alt-text="Screenshot da secção mappings. Sob nome, é visível o Synchronize Azure Ative Directory Users para iPass SmartConnect." border="false":::

9. Reveja os atributos do utilizador que são sincronizados do AD AD do Azure para o iPass SmartConnect na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no iPass SmartConnect para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/userattribute.png" alt-text="Screenshot da página De mapeamentos de atributos. Uma tabela lista os atributos Azure Ative Directory e iPass SmartConnect e a precedência correspondente." border="false":::


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para o iPass SmartConnect, altere o **Estado de Provisionamento** para **Ligar** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja providenciar ao iPass SmartConnect, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no iPass SmartConnect.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O iPass SmartConnect apenas aceita nomes de utilizadores que tenham os seus domínios registados na consola de administração iPass SmartConnect.  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
