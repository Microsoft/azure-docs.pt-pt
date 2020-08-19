---
title: 'Tutorial: Configurar a autenticação de identidade da plataforma de nuvem SAP para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para fornecimento e desaconsevisionamento automática de contas de utilizadores à Autenticação de Identidade da Plataforma CLOUD SAP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c330d8000825b2e0ccbdab8c5c1cdefa4d3d45c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543379"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Tutorial: Configurar a autenticação de identidade da plataforma de nuvem SAP para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Autenticação de Identidade da Plataforma SAP Cloud e no Diretório Ativo Azure (AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para a Autenticação de Identidade da Plataforma DE Nuvem SAP.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de autenticação de identidade da plataforma de nuvem SAP](https://cloudplatform.sap.com/pricing.html)
* Uma conta de utilizador na Autenticação de Identidade da Plataforma CLOUD SAP com permissões de Administração.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Atribuir utilizadores à Autenticação de Identidade da Plataforma cloud SAP

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deverá decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Autenticação de Identidade da Plataforma de Nuvem SAP. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Autenticação de Identidade da Plataforma de Nuvem SAP seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Dicas importantes para atribuir utilizadores à Autenticação de Identidade da Plataforma cloud SAP

* Recomenda-se que um único utilizador Azure AD seja designado para a Autenticação de Identidade da Plataforma DE Nuvem SAP para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Autenticação de Identidade da Plataforma CLOUD SAP, tem de selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configuração Instalação Instalação Cloud Platform I autenticação para provisionamento

1. Inscreva-se na sua [consola de autenticação de identidade](https://sapmsftintegration.accounts.ondemand.com/admin)da plataforma SAP Cloud. Navegue para **utilizadores & Autorizações > Administradores**.

    ![Consola de autenticação de identidade da plataforma de nuvem SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Prima o botão **+Adicionar** no painel do lado esquerdo para adicionar um novo administrador à lista. Escolha **o Sistema de Adição** e introduza o nome do sistema.   

> [!NOTE]
> O utilizador do administrador na autenticação de identidade da plataforma de nuvem SAP deve ser do tipo **Sistema**. A criação de um utilizador administrador normal pode levar a erros *não autorizados* durante o provisionamento.   

3.  Ao abrigo de Autorizações de Configuração, ligue o botão de alternação contra **gerir os utilizadores** e **gerir grupos.**

    ![Autenticação de identidade da plataforma de nuvem SAP Adiciona SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Receberá um e-mail para ativar a sua conta e definirá uma palavra-passe para o Serviço de **Autenticação de Identidade da Plataforma SAP Cloud.**

4.  Copie o **ID do utilizador** e **a palavra-passe.** Estes valores serão introduzidos nos campos Nome de Utilizador e Password Admin, respectivamente, no separador De Provisionamento da sua aplicação de Autenticação de Identidade da Plataforma SAP Cloud no portal Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicione a autenticação de identidade da plataforma de nuvem SAP da galeria

Antes de configurar a Autenticação de Identidade da Plataforma CLOUD SAP para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar a Autenticação de Identidade da Plataforma DE Nuvem SAP da galeria de aplicações AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Autenticação de Identidade da Plataforma CLOUD SAP da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **a Autenticação de Identidade da Plataforma cloud SAP,** selecione **a Autenticação de Identidade da Plataforma DE Nuvem SAP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Autenticação de identidade da plataforma de nuvem SAP na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurar o fornecimento automático de utilizadores à autenticação de identidade da plataforma de nuvem SAP 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Autenticação de Identidade da Plataforma de Nuvem SAP com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sinal de autenticação baseado em SAML para a autenticação de identidade da plataforma SAP Cloud, seguindo as instruções fornecidas no [tutorial de autenticação de identidade única da plataforma SAP Cloud.](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Autenticação de Identidade da Plataforma de Nuvem SAP em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **A Autenticação de Identidade da Plataforma de Nuvem SAP**.

    ![O link de autenticação de identidade da plataforma de nuvem SAP na lista de aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://<tenantID>.accounts.ondemand.com/service/scim ` na URL do **inquilino.** Insira os valores **de ID** do Utilizador e **password** recuperados anteriormente no **Nome de Utilizador Admin** e na **Palavra-Passe Admin,** respectivamente. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se à Autenticação de Identidade da Plataforma de Nuvem SAP. Se a ligação falhar, certifique-se de que a sua conta de autenticação de identidade da plataforma SAP Cloud tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **mapeamentos,** selecione **Synchronize Azure Ative Directory Users to SAP Cloud Platform Authentication**.

    ![Mapeamentos de autenticação de identidade da plataforma de nuvem SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Reveja os atributos do utilizador que são sincronizados do AD AD a SAP Cloud Platform Authentication na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador na Autenticação de Identidade da Plataforma DE Nuvem SAP para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador de autenticação de identidade da plataforma de nuvem SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para a Autenticação de Identidade da Plataforma DE Nuvem SAP, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Autenticação de Identidade da Plataforma DA Nuvem SAP, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AZure na Autenticação de Identidade da Plataforma DE Nuvem SAP.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O ponto final scim da Plataforma de Nuvem SAP cloud requer certos atributos para ser de formato específico. Pode saber mais sobre estes atributos e o seu formato específico [aqui.](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

