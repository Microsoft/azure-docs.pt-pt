---
title: 'Tutorial: Fornecimento de utilizadores para MilEyes - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 7958ef9195c9dcef65fab70fbf48edf2021ff94b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355827"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configurar MilEyes para o provisionamento automático do utilizador

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em ThousandEyes e AD Azure para provisões automáticas e desavisagem de contas de utilizadores de Azure AD a ThousandEyes. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino milEyes com o [plano Standard](https://www.thousandeyes.com/pricing) ou melhor habilitado 
* Uma conta de utilizador em ThousandEyes com permissões de Administração 

> [!NOTE]
> A integração de provisionamento da AZure AD depende da [MilEyes SCIM API,](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)que está disponível para equipas mileyes no plano Standard ou melhor.

## <a name="assigning-users-to-thousandeyes"></a>Atribuir utilizadores a ThousandEyes

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em AZure AD que representam os utilizadores que precisam de acesso à sua aplicação MilEyes. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação MilEyes seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir utilizadores a MilEyes

* Recomenda-se que um único utilizador AD Azure seja designado para a ThousandEyes para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a ThousandEyes, deve selecionar a função **Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **de Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurar o fornecimento de utilizadores ao ThousandEyes 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da ThousandEyes que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em ThousandEyes com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar Sign-On individuais baseadas em SAML para MilEyes, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador à ThousandEyes em Azure AD

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurado MilEyes para um único sign-on, procure o seu exemplo de ThousandEyes usando o campo de pesquisa. Caso contrário, **selecione Add** e procure **mileyes** na galeria de aplicações. Selecione ThousandEyes dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de ThousandEyes e, em seguida, selecione o **separador Provisioning.**

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![O screenshot mostra o separador Provisioning for ThousandEyes com o Modo de Provisionamento selecionado para o Modo de Provisionamento.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Na secção **Credenciais Admin,**  insira o Token do Portador da **OAuth** gerado pela conta da ThousandEyes (pode encontrar e ou gerar um símbolo na sua secção **de Perfil da** conta MilEyes).

    ![A screenshot mostra onde encontrar o link 'Definições de Conta' para o Grupo de Contas Correntes.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação MilEyes. Se a ligação falhar, certifique-se de que a sua conta MilEyes tem permissões de Administração e tente novamente o passo 5.

7. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo **de e-mail de notificação** e verifique a caixa de verificação "Envie uma notificação de e-mail quando ocorrer uma falha."

8. Clique em **Guardar**.

9. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to ThousandEyes**.

10. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador que são sincronizados de AZure AD a ThousandEyes. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em ThousandEyes para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

11. Para ativar o serviço de prestação de Ad Azure para ThousandEyes, altere o **Estado de Provisionamento** para **On** na secção **Definições**

12. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos a MilEyes na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para os registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
