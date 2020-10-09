---
title: 'Tutorial: Configurar a Cisco Webex para fornecimento automático de utilizadores com diretório Azure Ative Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Cisco Webex.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 069c8a8e2a595248afe45bbb90de877b3b6fc87d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849350"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: Configurar cisco Webex para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Cisco Webex e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento de utilizadores automaticamente à Cisco Webex.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino da Cisco Webex.](https://www.webex.com/pricing/index.html)
* Uma conta de utilizador na Cisco Webex com permissões de Administração.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionar Cisco Webex da galeria

Antes de configurar o Cisco Webex para o fornecimento automático de utilizadores com Azure AD, tem de adicionar a Cisco Webex da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar cisco Webex da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Cisco Webex,** selecione **Cisco Webex** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Cisco Webex na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Atribuir utilizadores à Cisco Webex

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores em Azure AD que precisam de acesso à Cisco Webex. Uma vez decididos, pode atribuir estes utilizadores à Cisco Webex seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Dicas importantes para atribuir utilizadores à Cisco Webex

* Recomenda-se que um único utilizador Azure AD seja atribuído à Cisco Webex para testar a configuração automática de provisionamento do utilizador. Utilizadores adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Cisco Webex, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurar o fornecimento automático de utilizadores à Cisco Webex

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores na Cisco Webex com base em atribuições de utilizadores em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para cisco Webex em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Cisco Webex**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cisco Webex**.

    ![O link Webex cisco na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    :::image type="content" source="common/provisioning.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

5. Sob a secção **de Credenciais de Administrador,** insira o URL do **Inquilino**e **o Token Secreto** da sua conta Cisco Webex.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

6.  No campo URL do **inquilino,** insira um valor na forma de `https://api.ciscospark.com/v1/scim/[OrgId]` . Para `[OrgId]` obter, inscreva-se no seu [Cisco Webex Control Hub](https://admin.webex.com/login). Clique no nome da sua organização no canto inferior esquerdo e copie o valor do ID da **Organização.** 

    * Para obter o valor de **Secret Token,** navegue para este [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). A partir do sinal webex na página que aparece, inscreva-se com a conta de administração cisco completa para a sua organização. Uma página de erro aparece dizendo que o site não pode ser alcançado, mas isto é normal.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::
 
    * Copie o valor do símbolo do portador gerado a partir do URL como realçado abaixo. Este token é válido por 365 dias.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à Cisco Webex. Se a ligação falhar, certifique-se de que a sua conta Cisco Webex tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)
   
8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Cisco Webex**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Cisco Webex na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na Cisco Webex para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Screenshot de um menu no portal Azure. Em Gestão, Provisioning é destacado." border="false":::

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de Ad Azure para a Cisco Webex, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de providenciar à Cisco Webex, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AZure na Cisco Webex.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* A Cisco Webex está atualmente na fase de Testes de Campo Precoce (EFT) da Cisco. Para mais informações, contacte a [equipa de apoio da Cisco.](https://www.webex.co.in/support/support-overview.html) 
* Para obter mais informações sobre a configuração da Cisco Webex, consulte a documentação da Cisco [aqui.](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
