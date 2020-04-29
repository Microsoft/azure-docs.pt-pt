---
title: 'Tutorial: Configure A autenticação de identidade da plataforma da nuvem SAP para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores à Autenticação de Identidade da Plataforma Cloud SAP.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060482"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Tutorial: Configure A autenticação de identidade da plataforma da nuvem SAP para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Autenticação de Identidade da Plataforma De Nuvem SAP e no Diretório Ativo Azure (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para autenticação de identidade da plataforma De cloud SAP.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de autenticação de identidade da plataforma de nuvem SAP](https://cloudplatform.sap.com/pricing.html)
* Uma conta de utilizador na Autenticação de Identidade da Plataforma SAP Cloud com permissões de Administrador.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Atribuir utilizadores à autenticação de identidade da plataforma SAP Cloud

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Autenticação de Identidade da Plataforma Cloud SAP. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Autenticação de Identidade da Plataforma De Nuvem SAP seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Dicas importantes para atribuir utilizadores à Autenticação de Identidade da Plataforma SAP Cloud

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Autenticação de Identidade da Plataforma Cloud SAP para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Autenticação de Identidade da Plataforma SAP Cloud, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configuração Da Autenticação de Identidade da Plataforma De Nuvem SAP para fornecimento

1. Inscreva-se na consola de autenticação de identidade da [plataforma SAP Cloud](https://sapmsftintegration.accounts.ondemand.com/admin). Navegue para **utilizadores & Autorizações > Administradores.**

    ![Consola de autenticação de identidade de plataforma de nuvem SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Crie um Utilizador De Administrador e selecione o utilizador.  

3.  Em autorizações de configuração, ligue o botão de alternância contra **gerir utilizadores** e **gerir grupos**.

    ![Autenticação de identidade da plataforma sap cloud adicionar SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Receberá um e-mail para ativar a sua conta e definirá uma senha para o Serviço de Autenticação de Identidade da **Plataforma Cloud SAP**.

4.  Copie o ID do **utilizador** e a **palavra-passe**. Estes valores serão introduzidos nos campos de nome de utilizador e palavra-passe do Administrador, respectivamente, no separador provisionamento da sua aplicação de autenticação de identidade de plataforma de nuvem SAP no portal Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicione a autenticação de identidade da plataforma de nuvem SAP na galeria

Antes de configurar a autenticação de identidade da plataforma SAP Cloud para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar a Autenticação de Identidade da Plataforma Cloud SAP da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar autenticação de identidade da plataforma SAP Cloud na galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza a autenticação de identidade da **plataforma SAP Cloud,** selecione A autenticação de identidade da **plataforma da Plataforma SAP Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Autenticação de identidade da plataforma SAP Cloud na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurar o fornecimento automático de utilizadores à autenticação de identidade da plataforma SAP Cloud 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Autenticação de Identidade da Plataforma Cloud SAP com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para autenticação de identidade da plataforma de nuvem SAP, seguindo as instruções fornecidas no tutorial único de autenticação de identidade da [plataforma de nuvem SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para autenticação de identidade da plataforma SAP Cloud em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP Cloud Platform Identity Authentication**.

    ![O link de autenticação de identidade da plataforma SAP Cloud na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://<tenantID>.accounts.ondemand.com/service/scim ` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera os valores de ID do **utilizador** e **palavra-passe** recuperados anteriormente no Nome de **Utilizador da Admin** e **na Palavra-Passe do Administrador,** respectivamente. Clique em **ligação** de teste para garantir que o Azure AD pode ligar-se à autenticação de identidade da plataforma da nuvem SAP. Se a ligação falhar, certifique-se de que a sua conta de Autenticação de Identidade da Plataforma De Nuvem SAP tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para a autenticação de identidade**da plataforma da nuvem SAP .

    ![Mapeamento de utilizador de autenticação de identidade da plataforma sap cloud](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para SAP Cloud Platform Identity Authentication na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador na Autenticação de Identidade da Plataforma SAP Cloud para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador de autenticação de identidade da plataforma sap cloud](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para autenticação de identidade da plataforma de nuvem SAP, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer à Autenticação de Identidade da Plataforma De Nuvem SAP, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Autenticação de Identidade da Plataforma De Nuvem SAP.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O ponto final do SCIM da Plataforma Cloud SCIM requer que certos atributos sejam de formato específico. Pode saber mais sobre estes atributos e o seu formato específico [aqui.](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

