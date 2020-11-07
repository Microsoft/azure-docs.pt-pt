---
title: 'Tutorial: Configurar Infor CloudSuite para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores ao Infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a62afa9469caa886d86814036017427c0cc0d193
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357441"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configurar Infor CloudSuite para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Infor CloudSuite e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos para o Infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino Infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de utilizador no Infor CloudSuite com permissões de Administrador.

## <a name="assigning-users-to-infor-cloudsuite"></a>Atribuir utilizadores ao Infor CloudSuite

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Infor CloudSuite. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Infor CloudSuite seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir utilizadores ao Infor CloudSuite

* Recomenda-se que um único utilizador AZure AD seja designado para o Infor CloudSuite para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Infor CloudSuite, tem de selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar o Infor CloudSuite para o provisionamento

1. Inscreva-se na sua [Consola de Administração Infor CloudSuite](https://www.infor.com/customer-center). Clique no ícone do utilizador e, em seguida, navegue para a **gestão do utilizador**.

    ![Consola infor CloudSuite Admin](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone do menu no canto superior esquerdo do ecrã. Clique em **Manage**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegue para as **contas SCIM**.

    ![Conta Infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um utilizador administrativo clicando no ícone plus. Forneça uma **palavra-passe SCIM** e escreva a mesma palavra-passe em **Confirmar Password**. Clique no ícone da pasta para guardar a palavra-passe. Em seguida, verá um **Identificador de Utilizador** gerado para o utilizador administrativo.

    ![Utilizador infor CloudSuite Admin](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Senha Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Screenshot da consola de administração Infor CloudSuite mostrando uma linha de mesa realçada. Esta linha contém um identificador de utilizador, senhas e uma hora marcada." border="false":::

5. Para gerar o token do portador, copie o **Identificador de Utilizador** e **a Palavra-passe SCIM**. Cole-as em bloco de notas++ separadas por um cólon. Codificar o valor da cadeia navegando para **plugins > ferramentas MIME > Basic64 Encode**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Screenshot de um documento Notepad++. No menu Plugins, destacam-se as ferramentas MIME. No menu de ferramentas MIME, destaca-se o código base64." border="false":::

3.  Copie o símbolo do portador. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Infor CloudSuite no portal Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicione Infor CloudSuite da galeria

Antes de configurar o Infor CloudSuite para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar Infor CloudSuite da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Infor CloudSuite na galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Infor CloudSuite** , selecione **Infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurar o fornecimento automático de utilizadores ao Infor CloudSuite 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Infor CloudSuite com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o Infor CloudSuite, seguindo as instruções fornecidas no [tutorial de inscrição única infor CloudSuite.](./infor-cloud-suite-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Para saber mais sobre o ponto final scim do Infor CloudSuite, consulte [isto](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Infor CloudSuite em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Infor CloudSuite**.

    ![O link Infor CloudSuite na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` na URL do **inquilino.** Insira o valor simbólico do portador recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao Infor CloudSuite. Se a ligação falhar, certifique-se de que a sua conta Infor CloudSuite tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Infor CloudSuite na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Infor CloudSuite para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos de utilizador Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Infor CloudSuite**.

    ![Mapeamentos do grupo Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de AZure AD a Infor CloudSuite na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Infor CloudSuite para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Infor CloudSuite, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Infor CloudSuite, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Infor CloudSuite.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)