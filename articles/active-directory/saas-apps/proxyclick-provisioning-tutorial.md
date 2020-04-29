---
title: 'Tutorial: Configure Proxyclick para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer e desfornecer automaticamente contas de utilizador para proxyclick.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063361"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Configure Proxyclick para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados em Proxyclick e Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para proxyclick.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de utilizador em Proxyclick com permissões de Administrador.

## <a name="add-proxyclick-from-the-gallery"></a>Adicione Proxyclick da galeria

Antes de configurar proxyclick para o fornecimento automático de utilizadores com AD Azure, você precisa adicionar Proxyclick da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Proxyclick na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Proxyclick**, **selecione Proxyclick** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuir utilizadores a Proxyclick

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em AD Azure que precisam de acesso ao Proxyclick. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Proxyclick seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir utilizadores a Proxyclick

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Proxyclick para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Proxyclick, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurar o fornecimento automático de utilizadores a Proxyclick 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Proxyclick com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para Proxyclick, seguindo as instruções fornecidas no [tutorial de inscrição única proxyclick](proxyclick-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Proxyclick em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![O link Proxyclick na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Para recuperar o URL do **Inquilino** e o **Token Secreto** da sua conta Proxyclick, siga o passo como descrito no Passo 6.

6. Inscreva-se na consola [Proxyclick Admin](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegar para **Configurações** > **Integrações** > **Navegue no Mercado**.

    ![Definições proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Mercado Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **Azure AD**. Clique em **Instalar agora**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalação proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione **fornecimento de utilizador** e clique em iniciar a **integração**. 

    ![Proxyclick Fornecimento de Utilizador](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A Configuração UI de configuração adequada deve agora aparecer **em** > **definições integrações**. Selecione **Definições** em **Azure AD (Fornecimento de Utilizador)**.

    ![Proxyclick Criar](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Você pode encontrar o URL do **Inquilino** e **O Token Secreto** aqui.

    ![Proxyclick Criar Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se a Proxyclick. Se a ligação falhar, certifique-se de que a sua conta Proxyclick tem permissões de Administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Proxyclick**.

    ![Proxyclick Mappings de utilizador](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Proxyclick na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Proxyclick para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Proxyclick Atributos de utilizador](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de provisionamento de AD Azure para Proxyclick, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja fornecer ao Proxyclick, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Proxyclick.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* Proxyclick requer **que os e-mails** e **o userName** tenham o mesmo valor de origem. Quaisquer atualizações de qualquer atributo modificarão o outro valor.
* Proxyclick não suporta o provisionamento para grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

