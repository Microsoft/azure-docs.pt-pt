---
title: 'Tutorial: Fornecimento de utilizadores - LinkedIn Sales Navigator, Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao LinkedIn Sales Navigator.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 458b527194c1123e266bd6abedf25de18e0cee09
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359362"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Tutorial: Configurar LinkedIn Sales Navigator para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar no LinkedIn Sales Navigator e AZure AD para fornecimento e desavisionamento automática de contas de utilizadores do Azure AD ao LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino do LinkedIn Sales Navigator 
* Uma conta de administrador no LinkedIn Sales Navigator com acesso ao LinkedIn Account Center

> [!NOTE]
> O Azure Ative Directory integra-se com o LinkedIn Sales Navigator utilizando o protocolo [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Atribuir utilizadores ao LinkedIn Sales Navigator

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD serão sincronizados.

Antes de configurar e ativar o serviço de fornecimento, terá de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao LinkedIn Sales Navigator. Uma vez decididos, pode atribuir estes utilizadores ao LinkedIn Sales Navigator seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Dicas importantes para atribuir utilizadores ao LinkedIn Sales Navigator

* Recomenda-se que um único utilizador Azure AD seja designado ao LinkedIn Sales Navigator para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao LinkedIn Sales Navigator, tem de selecionar a função **Utilizador** no diálogo de atribuição. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Configurar o fornecimento de utilizadores ao LinkedIn Sales Navigator

Esta secção guia-o através da ligação do seu AZure AD à conta de utilizador SCIM do LinkedIn Sales Navigator que fornece API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas no LinkedIn Sales Navigator com base na atribuição de utilizadores e grupos no AZure AD.

> [!TIP]
> Pode também optar por ativar Sign-On única baseada em SAML para o LinkedIn Sales Navigator, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador ao LinkedIn Sales Navigator em Azure AD:

O primeiro passo é recuperar o seu token de acesso LinkedIn. Se for administrador da Enterprise, pode auto-providenciar um token de acesso. No seu centro de conta, vá a **Definições &gt; Globais de Definições** e abra o painel **de configuração SCIM.**

> [!NOTE]
> Se estiver a aceder diretamente ao centro de conta e não através de um link, pode alcançá-lo utilizando os seguintes passos.

1. Inscreva-se no Centro de Contas.

2. Selecione **&gt; Definições de administração de admin** .

3. Clique em **Integrações Avançadas** na barra lateral esquerda. Está direcionado para o centro de contas.

4. Clique **+ Adicione a nova configuração SCIM** e siga o procedimento preenchendo cada campo.

    > [!NOTE]
    > Quando as licenças de autoassign não estão ativadas, significa que apenas os dados do utilizador são sincronizados.

    ![A screenshot mostra as definições globais do LinkedIn Account Center.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Quando a atribuição de autolicense está ativada, é necessário observar a instância de aplicação e o tipo de licença. As licenças são atribuídas por um primeiro a chegar, primeiro servir base até que todas as licenças sejam tomadas.

    ![A screenshot mostra a página de configuração S C I M.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Clique **em Gerar ficha**. Deve ver o seu ecrã de token de acesso sob o campo **'Token'.**

6. Guarde o seu token de acesso à sua prancheta ou computador antes de sair da página.

7. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com), e navegue na secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

8. Se já configurar o LinkedIn Sales Navigator para um único sign-on, procure a sua instância do LinkedIn Sales Navigator utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure o **LinkedIn Sales Navigator** na galeria de aplicações. Selecione LinkedIn Sales Navigator a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

9. Selecione a sua instância de LinkedIn Sales Navigator e, em seguida, selecione o **separador Provisioning.**

10. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![A screenshot mostra a página de Provisionamento Do LinkedIn Elevate.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Preencha os seguintes campos sob **as credenciais de administração:**

    * No campo URL do **inquilino,** insira https://developer.linkedin.com .

    * No campo **Secret Token,** insira o token de acesso gerado no passo 1 e clique em **Test Connection** .

    * Deverá ver uma notificação de sucesso no lado superior do seu portal.

12. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação abaixo.

13. Clique em **Guardar**.

14. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador e do grupo que serão sincronizados do AZure AD ao LinkedIn Sales Navigator. Note que os atributos selecionados como propriedades **de correspondência** serão utilizados para combinar com as contas e grupos de utilizador no LinkedIn Sales Navigator para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

    ![A screenshot mostra mapeamentos, incluindo mapeamentos de atributos.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Para permitir o serviço de prestação de Azure AD para o LinkedIn Sales Navigator, altere o **Estado de Provisionamento** para **On** na secção **Definições**

16. Clique em **Guardar**.

Isto iniciará a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao LinkedIn Sales Navigator na secção Utilizadores e Grupos. Note que a sincronização inicial demorará mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço estiver em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o provisionamento de registos de atividades, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação LinkedIn Sales Navigator.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
