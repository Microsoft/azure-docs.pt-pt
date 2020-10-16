---
title: 'Tutorial: Configurar RFPIO para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao RFPIO.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 98f92a6b0169c573b97788c7ffaf76255796cfa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278945"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Tutorial: Configurar RFPIO para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no RFPIO e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao RFPIO.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino rfpio.](https://www.rfpio.com/product/)
* Uma conta de utilizador em RFPIO com permissões de administração.

## <a name="assigning-users-to-rfpio"></a>Atribuir utilizadores ao RFPIO

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao RFPIO. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao RFPIO seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Dicas importantes para atribuir utilizadores ao RFPIO

* Recomenda-se que um único utilizador AZure AD seja designado para o RFPIO para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao RFPIO, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-rfpio-for-provisioning"></a>Configuração RFPIO para provisionamento

Antes de configurar o RFPIO para o fornecimento automático do utilizador com a AZure AD, terá de permitir o fornecimento scim no RFPIO.

1.  Inscreva-se na sua Consola de Administração RFPIO. Na parte inferior esquerda da consola de administração, clique no **Tenant**.

    ![Consola de Administração RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Clique em **Configurações de Organização**.
    
    ![Administrador RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Navegar para a **segurança da gestão**do utilizador  >  **SECURITY**  >  **SCIM**.

    ![RFPIO Adicionar SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Certifique-se de que **o Provisionamento do Utilizador Automático** está ativado. Clique em **GENERATE SCIM API TOKEN**.

    ![Screenshot da secção S C I M com a opção GENERATE S C I M A P I TOKEN chamada.](media/rfpio-provisioning-tutorial/generate.png)

5.  Guarde o **Token API da SCIM,** uma vez que este token não será novamente exibido por razões de segurança. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação RFPIO no portal Azure.

    ![Screenshot da secção S C I M com a caixa de diálogo de aviso que aparece depois de selecionar SUBMIT.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Adicione RFPIO da galeria

Para configurar o RFPIO para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar RFPIO da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar RFPIO da galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **RFPIO,** selecione **RFPIO** no painel de resultados e, em seguida, clique no botão    **Adicionar** para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Configurar o fornecimento automático de utilizadores ao RFPIO 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores e/ou grupos em RFPIO com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o RFPIO, seguindo as instruções fornecidas no [tutorial de inscrição única RFPIO](rfpio-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o RFPIO em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **RFPIO**.

    ![O link RFPIO na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` na URL do **inquilino.** Um valor de exemplo `https://Azure-test1.rfpio.com/rfpserver/scim/v2` é. Insira o valor **token da API SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao RFPIO. Se a ligação falhar, certifique-se de que a sua conta RFPIO tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para RFPIO**.

    ![Mapeamentos de utilizadores RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a RFPIO na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no RFPIO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o RFPIO, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao RFPIO, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AZure no RFPIO.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* A RFPIO não apoia atualmente o provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
