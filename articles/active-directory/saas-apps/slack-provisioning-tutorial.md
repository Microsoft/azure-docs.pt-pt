---
title: 'Tutorial: Fornecimento de utilizadores para Slack - Azure AD'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador à Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062834"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configure a folga para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em Slack e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Slack.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino Slack com o [plano Plus](https://aadsyncfabric.slack.com/pricing) ou melhor habilitado
* Uma conta de utilizador em Slack com permissões team admin

Nota: A integração de fornecimento de AD Azure baseia-se na [Slack SCIM API,](https://api.slack.com/scim)que está disponível para equipas Slack no plano Plus ou melhor.

## <a name="assigning-users-to-slack"></a>Atribuir utilizadores à Slack

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação em Azure AD serão sincronizados.

Antes de configurar e ativar o serviço de provisionamento, terá de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação Slack. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação Slack seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Dicas importantes para atribuir utilizadores à Slack

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Slack para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Slack, deve selecionar a função **Utilizador** ou "Grupo" no diálogo de atribuição. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="configuring-user-provisioning-to-slack"></a>Configurar o fornecimento de utilizadores à Slack 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Slack que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Slack com base na atribuição de utilizador e grupo em Azure AD.

**Dica:** Também pode optar por ativar o Single Sign-On baseado em SAML para slack, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Slack em Azure AD:

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

2. Se já configurou o Slack para um único sinal, procure a sua instância de Slack utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **Por Slack** na galeria de aplicações. Selecione A folga dos resultados da pesquisa e adicione-a à sua lista de aplicações.

3. Selecione a sua instância de Slack e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

   ![Fornecimento de folga](./media/slack-provisioning-tutorial/slack1.png)

5. Na secção **credenciais de administrador,** clique **em Autorizar**. Isto abre um diálogo de autorização Slack numa nova janela do navegador.

6. Na nova janela, assine em Slack usando a sua conta Team Admin. no diálogo de autorização resultante, selecione a equipa Slack que pretende ativar o provisionamento e, em seguida, **selecione Autorizar**. Uma vez concluído, volte ao portal Azure para completar a configuração de provisionamento.

    ![Diálogo de Autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Slack. Se a ligação falhar, certifique-se de que a sua conta Slack tem permissões de Team Admin e tente novamente o passo "Autorizar".

8. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação abaixo.

9. Clique em **Guardar**.

10. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Slack**.

11. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que serão sincronizados de Azure AD para Slack. Note que os atributos selecionados como propriedades **correspondentes** serão usados para combinar as contas de utilizador em Slack para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

12. Para ativar o serviço de provisionamento de AD Azure para a Folga, altere o Estado de **Provisionamento** para **On** na secção **Definições**

13. Clique em **Guardar**.

Isto iniciará a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Slack na secção Utilizadores e Grupos. Note que a sincronização inicial demorará mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 10 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos relatórios de atividadede provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcional] Configurar o fornecimento de objetos de grupo à Slack

Opcionalmente, pode ativar o fornecimento de objetos de grupo de Azure AD a Slack. Isto é diferente dos "grupos de atribuição de utilizadores", na medida em que o objeto de grupo real para além dos seus membros será replicado de Azure AD para Slack. Por exemplo, se tiver um grupo chamado "My Group" em Azure AD, um grupo idêntico chamado "My Group" será criado dentro de Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Para permitir o fornecimento de objetos de grupo:

1. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Groups to Slack**.

2. Na lâmina de mapeamento do atributo, coloque ativado a Sim.

3. Na secção **De Mapeamentos de Atributos,** reveja os atributos do grupo que serão sincronizados de Azure AD a Slack. Note que os atributos selecionados como propriedades **correspondentes** serão usados para combinar os grupos em Slack para operações de atualização. 

4. Clique em **Guardar**.

Isto resulta em quaisquer objetos de grupo atribuídos à Slack na secção **Utilizadores e Grupos** sendo totalmente sincronizados de Azure AD para Slack. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Slack.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* Ao configurar o atributo de nome de **exibição** de Slack, esteja atento aos seguintes comportamentos:

  * Os valores não são inteiramente únicos (por exemplo, 2 utilizadores podem ter o mesmo nome de exibição)

  * Suporta personagens não ingleses, espaços, capitalização. 
  
  * A pontuação permitida inclui períodos, sublinhados, hífenes, apóstrofos, parênteses (por exemplo **, ( [ { ] ) )** e separadores (por **exemplo, / ;**
  
  * Apenas atualizações se estas duas definições estiverem configuradas no local de trabalho/organização da Slack - A sincronização de **perfis está ativada** e os Utilizadores não podem alterar o seu nome de **exibição**.
  
* O atributo de nome de **utilizador** da Slack tem de ter menos de 21 caracteres e ter um valor único.

* A folga só permite combinar com os atributos **userName** e **email**.  

## <a name="additional-resources"></a>Recursos Adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
