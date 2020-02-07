---
title: 'Tutorial: Configure 4me para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador para 4me.
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
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059172"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Tutorial: Configure 4me para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no 4me e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos a 4me.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino 4me](https://www.4me.com/trial/)
* Uma conta de utilizador em 4me com permissões admin.

## <a name="add-4me-from-the-gallery"></a>Adicione 4me da galeria

Antes de configurar o 4me para o fornecimento automático de utilizadores com a AD Azure, precisa adicionar 4me da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar 4me da galeria de aplicações azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **4me**, selecione **4me** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![4me na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Atribuir utilizadores a 4me

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso a 4me. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos a 4me seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Dicas importantes para atribuir utilizadores a 4me

* Recomenda-se que um único utilizador da AD Azure seja atribuído a 4me para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador a 4me, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurar o fornecimento automático de utilizadores para 4me 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em 4me com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para 4me, seguindo as instruções fornecidas no tutorial de [inscrição individual 4me](4me-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para 4me em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **4me**.

    ![O link 4me na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Para recuperar o URL do **Inquilino** e o **Símbolo Secreto** da sua conta 4me, siga a passagem como descrito no Passo 6.

6. Inscreva-se na sua Consola 4me Admin. Navegar para **Definições**.

    ![4me Definições](media/4me-provisioning-tutorial/4me01.png)

    Digite **aplicativos** na barra de pesquisa.

    ![4me apps](media/4me-provisioning-tutorial/4me02.png)

    Abra a entrega do **SCIM** para recuperar o Token Secreto e o ponto final do SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se a 4me. Se a ligação falhar, certifique-se de que a sua conta 4me tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to 4me**.

    ![Mapeamento de 4me user](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Reveja os atributos do utilizador que são sincronizados de Azure AD a 4me na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador em 4me para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de 4me user](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to 4me**.

    ![Mapeamento de 4me user](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Reveja os atributos do grupo que são sincronizados de Azure AD a 4me na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em 4me para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamentos de grupo 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento de AD Azure por 4me, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que deseja fornecer a 4me, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no 4me.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* 4me tem diferentes URLs de ponto final SCIM para ambientes de ensaio e produção. O primeiro termina com **.qa** enquanto este último termina com **.com**
* 4me gerado Sinos Secretos têm uma data de validade de um mês a partir da geração.
* 4me não suporta operações **DELETE**

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)