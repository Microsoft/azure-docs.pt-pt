---
title: 'Tutorial: Configure iPass SmartConnect para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizador para o iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057542"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Tutorial: Configure iPass SmartConnect para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no iPass SmartConnect e Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o iPass SmartConnect.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um iPass SmartConnect inquilino.](https://www.ipass.com/buy-ipass/)
* Uma conta de utilizador no iPass SmartConnect com permissões de Administrador.

## <a name="assigning-users-to-ipass-smartconnect"></a>Atribuir utilizadores ao iPass SmartConnect

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao iPass SmartConnect. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao iPass SmartConnect seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Dicas importantes para atribuir utilizadores ao iPass SmartConnect

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao iPass SmartConnect para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao iPass SmartConnect, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Configuração iPass SmartConnect para provisionamento

Antes de configurar o iPass SmartConnect para o fornecimento automático de utilizadores com o Azure AD, terá de recuperar informações de configuração da consola de administração do iPass SmartConnect:

1. Para recuperar o token do portador que é necessário para autenticar contra o seu ponto final do IPass SmartConnect SCIM, consulte a primeira vez que configura o iPass SmartConnect, uma vez que este valor só é fornecido nessa altura. 
2. Se não tiver o token do portador, contacte a equipa de [suporte do iPass SmartConnect](mailto:help@ipass.com) para recuperar um novo.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Adicione iPass SmartConnect a partir da galeria

Para configurar o iPass SmartConnect para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar o iPass SmartConnect da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o iPass SmartConnect a partir da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **iPass SmartConnect,** selecione **o iPass SmartConnect** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![iPass SmartConnect na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Configurar o fornecimento automático de utilizadores para o iPass SmartConnect 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no iPass SmartConnect com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
>  Também pode optar por ativar um único sinal baseado em SAML para o BitaBIZ, seguindo as instruções fornecidas no tutorial de [inscrição single do iPass SmartConnect](ipasssmartconnect-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o iPass SmartConnect em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iPass SmartConnect**.

    ![O link iPass SmartConnect na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://openmobile.ipass.com/moservices/scim/v1` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Introduza o símbolo do portador recuperado anteriormente em **Secret Token.** Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao iPass SmartConnect. Se a ligação falhar, certifique-se de que a sua conta iPass SmartConnect tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para iPass SmartConnect**.

    ![Mapeamento de utilizador iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para iPass SmartConnect na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no iPass SmartConnect para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizador iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para iPass SmartConnect, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao iPass SmartConnect, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no iPass SmartConnect.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O iPass SmartConnect apenas aceita nomes de utilizadores que tenham os seus domínios registados na consola de administração iPass SmartConnect.  

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
