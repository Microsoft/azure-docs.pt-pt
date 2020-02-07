---
title: 'Tutorial: Configure Enviado para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Enviado.
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
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058030"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Tutorial: Configure enviado para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Enviado e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos ao Enviado.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino enviado](https://envoy.com/pricing/)
* Uma conta de utilizador no Enviado com permissões de administrador.

## <a name="add-envoy-from-the-gallery"></a>Adicione enviado da galeria

Antes de configurar o Enviado para o fornecimento automático de utilizadores com a AD Azure, você precisa adicionar o enviado da galeria de aplicações Azure AD à sua lista de aplicações geridas saaS.

**Para adicionar enviado da galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Enviado**, selecione **Enviado** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Enviado na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Atribuir utilizadores ao Enviado

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Enviado. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Enviado seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Dicas importantes para atribuir utilizadores ao Enviado

* Recomenda-se que um único utilizador da AD Azure seja designado para o Enviado para testar a configuração automática de fornecimento de utilizadores. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Enviado, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Configurar o fornecimento automático de utilizadores ao enviado 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento da AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Enviado com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para enviado, seguindo as instruções fornecidas no tutorial de [inscrição única](envoy-tutorial.md)do Enviado. O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para enviado em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Enviado**.

    ![O link do enviado na lista de candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://app.envoy.com/scim/v2` no **URL do Arrendatário**. Para recuperar o **Símbolo Secreto** da sua conta de enviado, siga a passagem como descrito no Passo 6.

6. Inscreva-se na sua [Consola De Enviado Admin](https://dashboard.envoy.com/login). Clique em **Integrações**.

    ![Integrações do Enviado](media/envoy-provisioning-tutorial/envoy01.png)

    Clique em **Instalar** para a integração do **Microsoft Azure SCIM**.

    ![Instalação do enviado](media/envoy-provisioning-tutorial/envoy02.png)

    Clique em **Guardar** para **Sincronizar todos os utilizadores**. 

    ![Enviado Salvar](media/envoy-provisioning-tutorial/envoy03.png)

    Recupere o Símbolo Secreto povoado.
    
    ![Enviado OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Enviado. Se a ligação falhar, certifique-se de que a sua conta de enviado tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Envoy**.
    
    ![Atributos do Utilizador enviado](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Enviado na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder às contas de utilizador no Enviado para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Utilizador enviado](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Envoy**.

    ![Mapeamento de utilizador enviado](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para enviado na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Enviado para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizador enviado](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento de AD Azure para enviado, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que gostaria de fornecer ao Enviado, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Enviado.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

