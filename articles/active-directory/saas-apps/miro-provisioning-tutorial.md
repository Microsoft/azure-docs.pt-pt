---
title: 'Tutorial: Configure Miro para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Miro.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81eecaff-d888-472b-a1c2-0b7b0c9ccd8d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: b7e1907e3fa1eb9d775fb7662445b08d5671e0b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063418"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Tutorial: Configure Miro para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo Miro e Azure (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos à Miro.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino Miro](https://miro.com/pricing/)
* Uma conta de utilizador em Miro com permissões de Administração.

## <a name="assigning-users-to-miro"></a>Atribuir utilizadores à Miro

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Miro. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Miro seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Dicas importantes para atribuir utilizadores à Miro

* Recomenda-se que um único utilizador AZure AD seja designado à Miro para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Miro, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-miro-for-provisioning"></a>Configurar Miro para provisionamento

1.  Para recuperar a necessária equipa de apoio **da Miro em Contacto Secreto** em support@miro.com . Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Miro no portal Azure.

## <a name="add-miro-from-the-gallery"></a>Adicione Miro da galeria

Antes de configurar o Miro para o fornecimento automático de utilizadores com Azure AD, tem de adicionar o Miro da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Miro da galeria de aplicações AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Miro,** selecione **Miro** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Miro na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>Configurar o fornecimento automático de utilizadores ao Miro 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Miro com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o Miro, seguindo as instruções fornecidas no [tutorial de inscrição única Miro .](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-tutorial) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Miro, consulte [isto.](https://help.miro.com/hc/en-us/articles/360036777814)

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Miro em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Miro.**

    ![O link Miro na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://miro.com/api/v1/scim` na URL do **inquilino.** Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao Miro. Se a ligação falhar, certifique-se de que a sua conta Miro tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Miro**.

    ![Mapeamentos de utilizadores Miro](media/miro-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Miro na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Miro para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador Miro](media/miro-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Miro**.

    ![Mapeamentos do Grupo Miro](media/miro-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de AD AD a Miro na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em Miro para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações. Desvermarcar **criar** e **eliminar** em **Ações de Objeto-Alvo,** uma vez que a API Miro SCIM não suporta a criação e eliminação de grupos.

    ![Atributos do Grupo Miro](media/miro-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Miro, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Miro, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Miro.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O ponto final SCIM da Miro não permite **criar** e **eliminar** operações em grupos. Só suporta a operação **de atualização** do grupo.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

