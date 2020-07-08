---
title: 'Tutorial: Configurar o OfficeSpace Software para o fornecimento automático de utilizadores com o Azure Ative Directory Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desa provisionar contas de utilizadores ao OfficeSpace Software.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063429"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Tutorial: Configurar software OfficeSpace para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no OfficeSpace Software e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao OfficeSpace Software.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* Um [inquilino de Software OfficeSpace](https://www.officespacesoftware.com/)
* Uma conta de utilizador no OfficeSpace Software com permissões de Administração.

## <a name="assigning-users-to-officespace-software"></a>Atribuir utilizadores ao OfficeSpace Software

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Software OfficeSpace. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao OfficeSpace Software seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Dicas importantes para atribuir utilizadores ao OfficeSpace Software

* Recomenda-se que um único utilizador AZure AD seja designado para o OfficeSpace Software para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao OfficeSpace Software, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-officespace-software-for-provisioning"></a>Configurar o Software OfficeSpace para provisionamento

1. Inscreva-se na sua [Consola de Administração de Software OfficeSpace](https://support.officespacesoftware.com/hc). Navegue para **Configurações > Conectores**.

    ![Consola de administração de software OfficeSpace](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navegue para a **sincronização do diretório > SCIM**.

    ![Software OfficeSpace Adicionar SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Copie o **token de autenticação SCIM**. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação OfficeSpace Software no portal Azure.

    ![Software OfficeSpace Criar Token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Adicionar Software OfficeSpace da galeria

Antes de configurar o OfficeSpace Software para o fornecimento automático de utilizadores com Azure AD, tem de adicionar software OfficeSpace da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar software OfficeSpace da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Software OfficeSpace,** selecione **OfficeSpace Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Software OfficeSpace na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Configurar o fornecimento automático de utilizadores ao OfficeSpace Software 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Software OfficeSpace com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o Software OfficeSpace seguindo as instruções fornecidas no [tutorial de assinatura único do Software OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o OfficeSpace Software em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OfficeSpace Software**.

    ![A ligação de Software OfficeSpace na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** inserir `https://<subdomain>.officespacesoftware.com/api/scim/v2/` o formato URL em URL do **inquilino.** Por exemplo, `https://contoso.officespacesoftware.com/api/scim/v2/`. Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao Software OfficeSpace. Se a ligação falhar, certifique-se de que a sua conta de Software OfficeSpace tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to OfficeSpace Software**.

    ![Mapeamentos de utilizadores de software officeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados do AD AD do Azure para o OfficeSpace Software na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no OfficeSpace Software para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador do software officeSpace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento Azure AD para software OfficeSpace, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja prestar ao OfficeSpace Software, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no OfficeSpace Software.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

