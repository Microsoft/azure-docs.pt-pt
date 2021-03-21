---
title: 'Tutorial: Configure MerchLogix para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359702"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configure MerchLogix para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na MerchLogix e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos à MerchLogix.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* Um inquilino da MerchLogix
* Um contacto técnico na MerchLogix que pode fornecer o URL de ponta SCIM e o símbolo secreto necessário para o fornecimento do utilizador

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionar MerchLogix da galeria

Antes de configurar a MerchLogix para o fornecimento automático de utilizadores com Azure AD, precisa de adicionar a MerchLogix da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar MerchLogix da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.** 

    ![O botão Azure Ative Directory][1]

2. Navegar para **aplicações da Enterprise**  >  **Todas as aplicações.**

    ![Secção de aplicações da Enterprise][2]

3. Para adicionar MerchLogix, clique no botão **de aplicação Novo** na parte superior do diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **MerchLogix.**

5. No painel de resultados, selecione **MerchLogix** e, em seguida, clique no botão **Adicionar** para adicionar MerchLogix à sua lista de aplicações SaaS.

    ![Screenshot da secção Adicionar da secção de galé com a caixa de texto de nome Inserida chamada.][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuir utilizadores à MerchLogix

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados. 

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à MerchLogix. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à MerchLogix seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir utilizadores à MerchLogix

* Recomenda-se que um único utilizador AZure AD seja designado à MerchLogix para testar a sua configuração inicial de provisionamento automático do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde assim que os testes forem bem sucedidos.

* Ao atribuir um utilizador à MerchLogix, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurar o fornecimento automático de utilizadores à MerchLogix 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na MerchLogix com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para a MerchLogix, seguindo as instruções fornecidas no tutorial de assinatura único da [MerchLogix.](merchlogix-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a MerchLogix em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para **aplicações Azure Ative Directory > Enterprise > Todas as aplicações**.

2. Selecione MerchLogix da sua lista de aplicações SaaS.

3. Selecione o separador **Aprovisionamento**.

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da secção MerchLogix - Prisioning com a opção provisionamento chamada, o modo de provisionamento definido para Automático, e a opção de Ligação de Teste chamada.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Sob a secção **de Credenciais de Administração:**

    * No campo URL do **inquilino,** insira o URL de ponto final SCIM fornecido pelo seu contacto técnico MerchLogix.

    * No campo **Secret Token,** insira o símbolo secreto fornecido pelo seu contacto técnico merchLogix.

6. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que a Azure AD pode ligar-se à MerchLogix. Se a ligação falhar, certifique-se de que a sua conta MerchLogix tem permissões de Administração e tente novamente.

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para MerchLogix**.

10. Reveja os atributos do utilizador que são sincronizados de AD AD a MerchLogix na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na MerchLogix para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

11. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para MerchLogix**.

12. Reveja os atributos do grupo que são sincronizados de AD AD a MerchLogix na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos na MerchLogix para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

13. Para ativar o serviço de fornecimento de Ad Azure para a MerchLogix, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

14. Quando estiver pronto para aprovisionar, clique em **Guardar**.

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure na MerchLogix.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
