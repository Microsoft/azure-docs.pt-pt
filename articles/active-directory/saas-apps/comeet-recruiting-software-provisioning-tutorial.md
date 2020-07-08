---
title: 'Tutorial: Configurar o Software de Recrutamento de Comeets para o provisionamento automático do utilizador com o Azure Ative Directory [ Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desa provisionar contas de utilizadores ao Comeet Recruiting Software.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058342"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutorial: Configurar o Software de Recrutamento de Comeets para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Comeet Recruiting Software e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao Software de Recrutamento de Comeet.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de software de recrutamento de comeet](https://www.comeet.co/)
* Uma conta de utilizador em Comeet Recruiting Software com permissões de administração.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Adicionar software de recrutamento comeet da galeria

Antes de configurar o Software de Recrutamento de Comeets para o fornecimento automático de utilizadores com Azure AD, tem de adicionar o Software de Recrutamento comeet da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Software de Recrutamento Comeet da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Software de Recrutamento Comeet,** selecione **o Comeet Recruiting Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Software de Recrutamento de Comeet na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Atribuir utilizadores ao Software de Recrutamento de Comeet

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em AZure AD que precisam de acesso ao Software de Recrutamento de Comeet. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Software de Recrutamento de Comeet seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Dicas importantes para atribuir utilizadores ao Software de Recrutamento de Comeet

* Recomenda-se que um único utilizador AD Azure seja designado para o Comeet Recruiting Software para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Software de Recrutamento Comeet, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurar o fornecimento automático de utilizadores ao Software de Recrutamento de Comeet 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Comeet Recruiting Software com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sign-on baseado em SAML para o Software de Recrutamento de Comeet, seguindo as instruções fornecidas no tutorial de inscrição única do [Comeet Recruiting Software.](comeetrecruitingsoftware-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Software de Recrutamento de Comeet em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Comeet Recruiting Software**.

    ![O link comeet recruiting software na lista de aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do **inquilino** e **o Token Secreto** da conta do seu Comeet Recruiting Software, conforme descrito no Passo 6.

6. Na [consola de administração do Comeet Recruiting Software](https://app.comeet.co/), navegue para **Comeet > Definições > Autenticação > Microsoft Azure**, e copie o **Token Secreto para** o valor da sua empresa para o campo Secret **Token** em Azure AD.

    ![Fornecimento de software de recrutamento de comeet](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que a Azure AD pode ligar-se ao Software de Recrutamento de Comeet. Se a ligação falhar, certifique-se de que a sua conta de Software de Recrutamento de Comeet tem permissões de administração e tente novamente.

    ![Token](common/provisioning-testconnection-token.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Comeet**.

    ![Mapeamentos de utilizadores de software de recrutamento de comeet](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados do AD AD AD a Comeet Recruiting Software na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Comeet Recruiting Software para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo de Software de Recrutamento de Comeet](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento Azure AD para o Software de Recrutamento de Comeet, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Comeet Recruiting Software, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Comeet Recruiting Software.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Comeet Recruiting Software não suporta atualmente grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

