---
title: 'Tutorial: Configurar RFPIO para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao RFPIO.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77060869"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Tutorial: Configurar RFPIO para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no RFPIO e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao RFPIO.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
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

    ![RFPIO Criar Ficha](media/rfpio-provisioning-tutorial/generate.png)

5.  Guarde o **Token API da SCIM,** uma vez que este token não será novamente exibido por razões de segurança. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação RFPIO no portal Azure.

    ![RFPIO Criar Ficha](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Adicione RFPIO da galeria

Para configurar o RFPIO para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar RFPIO da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar RFPIO da galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **RFPIO,** selecione **RFPIO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Configurar o fornecimento automático de utilizadores ao RFPIO 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores e/ou grupos em RFPIO com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o RFPIO, seguindo as instruções fornecidas no [tutorial de inscrição única RFPIO](rfpio-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o RFPIO em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **RFPIO**.

    ![O link RFPIO na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` na URL do **inquilino.** Um valor de exemplo `https://Azure-test1.rfpio.com/rfpserver/scim/v2` é. Insira o valor **token da API SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao RFPIO. Se a ligação falhar, certifique-se de que a sua conta RFPIO tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para RFPIO**.

    ![Mapeamentos de utilizadores RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a RFPIO na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no RFPIO para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o RFPIO, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao RFPIO, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AZure no RFPIO.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* A RFPIO não apoia atualmente o provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
