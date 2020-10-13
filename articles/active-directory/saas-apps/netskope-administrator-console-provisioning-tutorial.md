---
title: 'Tutorial: Configurar consola de administrador netskope para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para fornecimento automático e desavisionamento de contas de utilizadores para a Consola administradora netskope.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: b4ac2308eae3466dbb9d68895bca4a4de30fcebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91305011"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Tutorial: Configurar consola de administrador netskope para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Consola administrador de Netskope e no Diretório Ativo Azure (Ad) para configurar a Azure AD para fornecimento automático e desa provisionamento de utilizadores e/ou grupos para a Consola administrador de Netskope.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de consola administrador de Netskope](https://www.netskope.com/)
* Uma conta de utilizador na Consola de Administrador netskope com permissões de administrador.

## <a name="assigning-users-to-netskope-administrator-console"></a>Atribuir utilizadores à Consola de Administrador netskope

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em AZure AD que precisam de acesso à Consola de Administrador netskope. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Consola de Administrador netskope seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Dicas importantes para atribuir utilizadores à Consola de Administrador netskope

* Recomenda-se que um único utilizador AD Azure seja designado para a Consola de Administrador netskope para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Consola de Administrador netskope, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurar a Consola de Administrador netskope para provisionamento

1. Inscreva-se na consola [de administração de netskope.](https://netskope.goskope.com/) Navegue para **as definições de > em casa**.

    ![Consola administradora de Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navegar para **ferramentas**. No menu **Ferramentas,** navegue para **Ferramentas de Diretório > INTEGRAÇÃO SCIM**.

    ![Ferramentas de consola de administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Consola de administrador de Netskope Adicionar SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Desloque-se para baixo e clique no botão **Adicionar Token.** Na caixa de diálogo **'Adicionar OAuth Client Name'** fornece-se um **NOME CLIENTE** e clica no botão **Guardar.**

    ![Consola de administrador de Netskope adicionar token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nome CLient da consola do administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copie o URL do **servidor SCIM** e o **TOKEN**. Estes valores serão introduzidos nos campos URL e Secret Token, respectivamente, no separador Provisioning da sua aplicação de Consola de Administrador netskope no portal Azure.

    ![Consola de administrador de Netskope Criar Token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Adicione consola de administrador netskope da galeria

Antes de configurar a Consola de Administrador netskope para o fornecimento automático do utilizador com Azure AD, tem de adicionar a Consola de Administrador netskope da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Consola de Administrador netskope da galeria de aplicações AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira a **Consola de Administrador netskope**, selecione **consola de administrador netskope** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Consola de administrador de Netskope na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configurar o fornecimento automático do utilizador à Consola de Administrador netskope 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Consola de Administrador netskope com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para a consola de administrador netskope, seguindo as instruções fornecidas no tutorial de assinatura único da [consola de administrador netskope.](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final scim da Netskope Administrator Console, consulte [isto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para a consola de administrador netskope em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **consola de administrador netskope**.

    ![O link da consola do administrador netskope na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o valor **URL do SERVIDOR SCIM** recuperado anteriormente no **URL do inquilino**. Insira o valor **TOKEN** recuperado anteriormente em **Secret Token**. Clique em **'Test Connection'** para garantir que o Azure AD pode ligar-se à Consola de Administrador netskope. Se a ligação falhar, certifique-se de que a sua conta de Consola de Administrador netskope tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para a Consola de Administrador netskope**.

    ![Mapeamentos de utilizador de consola de administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Ad AD AD para Netskope Administrator Console na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na Consola de Administrador netskope para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador do utilizador da consola do administrador netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Netskope Administrator Console**.

    ![Mapeamentos do grupo de consolas do administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Netskope Administrator Console na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos na Consola de Administrador netskope para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo de consola de administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de AD Azure para a Consola de Administrador netskope, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Consola de Administrador netskope, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure na Consola de Administrador de Netskope.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

