---
title: 'Tutorial: Configure Cisco Webex para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores à Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058523"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: Configure Cisco Webex para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Cisco Webex e azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores à Cisco Webex.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino da Cisco Webex.](https://www.webex.com/pricing/index.html)
* Uma conta de utilizador na Cisco Webex com permissões de administrador.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco WebEx da Galeria

Antes de configurar a Cisco Webex para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar a Cisco Webex da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar a Cisco Webex à galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **cisco Webex**, selecione Cisco **Webex** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Cisco Webex na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Atribuir utilizadores à Cisco Webex

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores do Azure AD que precisam de acesso ao Cisco Webex. Uma vez decidido, pode atribuir estes utilizadores à Cisco Webex seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Dicas importantes para atribuir utilizadores à Cisco Webex

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Cisco Webex para testar a configuração automática de fornecimento de utilizadores. Os utilizadores adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Cisco Webex, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurar o fornecimento automático de utilizadores à Cisco Webex

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar os utilizadores na Cisco Webex com base nas atribuições dos utilizadores em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Cisco Webex em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Cisco Webex**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cisco Webex**.

    ![O link Cisco Webex na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Cisco Webex Provisioning](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Cisco Webex Provisioning](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insere o URL do **Inquilino**e **o Token Secreto** da sua conta Cisco Webex.

    ![Cisco Webex Provisioning](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  No campo URL do **Arrendatário,** insira um valor sob a forma de `https://api.ciscoweb.com/v1/scim/[OrgId]`. Para obter `[OrgId]`, inscreva-se no seu [Cisco Webex Control Hub](https://admin.webex.com/login). Clique no nome da sua organização no canto inferior esquerdo e copie o valor do ID da **Organização**. 

    * Para obter o valor do **Token Secreto,** navegue para este [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). A partir do sinal webex na página que aparece, inscreva-se com a conta de administração da Cisco Webex completa para a sua organização. Uma página de erro aparece dizendo que o site não pode ser alcançado, mas isto é normal.

        ![Cisco Webex Provisioning](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Copie o valor do token do portador gerado a partir do URL como realçado abaixo. Este token é válido por 365 dias.
        
        ![Cisco Webex Provisioning](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à Cisco Webex. Se a ligação falhar, certifique-se de que a sua conta Cisco Webex tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Cisco Webex**.

    ![Cisco Webex Provisioning](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Cisco Webex na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Cisco Webex para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Cisco Webex Provisioning](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para a Cisco Webex, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer à Cisco Webex, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Cisco Webex.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* A Cisco Webex está atualmente na fase de Testes de Campo Precoce (EFT) da Cisco. Para mais informações, contacte a [equipa de apoio da Cisco.](https://www.webex.co.in/support/support-overview.html) 
* Para obter mais informações sobre a configuração do Cisco Webex, consulte a documentação da Cisco [aqui](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)