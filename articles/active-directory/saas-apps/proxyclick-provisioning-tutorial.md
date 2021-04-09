---
title: 'Tutorial: Configurar Proxyclick para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desaprovisionar contas de utilizadores à Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359175"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Configurar Proxyclick para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Proxyclick e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desprovisionamento de utilizadores e/ou grupos à Proxyclick.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino Proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de utilizador em Proxyclick com permissões de Administração.

## <a name="add-proxyclick-from-the-gallery"></a>Adicione Proxyclick da galeria

Antes de configurar o Proxyclick para o fornecimento automático de utilizadores com Azure AD, tem de adicionar Proxyclick da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Proxyclick da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Proxyclick,** selecione **Proxyclick** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuir utilizadores ao Proxyclick

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Proxyclick. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Proxyclick seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir utilizadores ao Proxyclick

* Recomenda-se que um único utilizador AZure AD seja designado para o Proxyclick para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Proxyclick, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurar o fornecimento automático do utilizador à Proxyclick 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Proxyclick com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para Proxyclick, seguindo as instruções fornecidas no [tutorial de inscrição única proxyclick](proxyclick-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Proxyclick em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![O link Proxyclick na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Para recuperar o **URL do inquilino** e o **Token Secreto** da sua conta Proxyclick, siga a passagem como descrito no Passo 6.

6. Inscreva-se na sua [Consola de Administração Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegar para **Configurações**  >  **Integrações**  >  **Navegue no Mercado**.

    ![Configurações proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **Azure Ad**. Clique **em Instalar agora**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalação Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione **o Provisionamento do Utilizador** e clique na **integração iniciar**. 

    ![Provisão de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A configuração adequada da configuração UI deve agora aparecer em **Configurações**  >  **Integrações**. Selecione **Definições** em **AZure AD (Provisionamento do utilizador)**.

    ![Criar Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Você pode encontrar a **URL do inquilino** e o Segredo **Token** aqui.

    ![Proxyclick Criar Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que a Azure AD pode ligar-se ao Proxyclick. Se a ligação falhar, certifique-se de que a sua conta Proxyclick tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Proxyclick**.

    ![Mapeamentos de proxyclick do utilizador](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Proxyclick na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Proxyclick para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos de utilizador proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de prestação de Ad Azure para Proxyclick, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja providenciar à Proxyclick, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Proxyclick.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Proxyclick requer **e-mails** e **nome de utilizador** para ter o mesmo valor de origem. Quaisquer atualizações a qualquer um dos atributos modificarão o outro valor.
* A Proxyclick não apoia o provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

