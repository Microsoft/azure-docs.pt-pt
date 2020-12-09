---
title: 'Tutorial: Configure Playvox para fornecimento automático de utilizadores através da utilização do Azure Ative Directory Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores do Azure AD para a Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862484"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Tutorial: Configurar playvox para fornecimento automático de utilizadores

Este tutorial descreve os passos a seguir tanto no Playvox como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desescvisões utilizadores ou grupos para a [Playvox](https://www.playvox.com) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e para perguntas frequentes, consulte [o fornecimento e desprovisionamento de utilizadores da Automamate para aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Crie utilizadores na Playvox.
> * Remova os utilizadores no Playvox quando já não precisam de acesso.
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Playvox.

## <a name="prerequisites"></a>Pré-requisitos

O cenário neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. Por exemplo, uma conta pode ter o Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global.
* Uma conta de utilizador na [Playvox](https://www.playvox.com) com permissões super administradas.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1: Planeie a sua implantação de provisionamento

1. Saiba [como funciona o serviço de prestação de serviços.](../app-provisioning/user-provisioning.md)

2. Determinar quem estará [no âmbito do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Determine quais os dados a [mapear entre Azure AD e Playvox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Passo 2: Configurar a Playvox para apoiar o provisionamento utilizando a Azure AD

1. Inicie sessão na consola de administração Playvox e vá para **Definições > Teclas API**.

2. Selecione **Criar tecla API**.

    ![Screenshot parcial mostrando a localização do botão Chave API Criar na interface do utilizador Playvox.](media/playvox-provisioning-tutorial/create.png)

3. Introduza um nome significativo para a tecla API e, em seguida, **selecione Save**. Depois de gerada a tecla API, selecione **Close**.

4. Na tecla API que criou, selecione o ícone **Detalhes.**

    ![Screenshot parcial mostrando a localização do ícone Details, que é uma lupa, na interface do utilizador Playvox.](media/playvox-provisioning-tutorial/api.png)

5. Copie e guarde o valor **BASE64 KEY.** Mais tarde, no portal Azure, irá introduzir este valor na caixa de texto **Secret Token** no **separador Provisioning** da sua aplicação Playvox.

    ![Screenshot da caixa de mensagens Da Chave API detalhes, com o valor BASE64 KEY realçado.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Passo 3: Adicione playvox da galeria de aplicações AZure AD

Para começar a gerir o fornecimento à Playvox, adicione a Playvox ao seu inquilino AZure AD da galeria de aplicações. Para saber mais, consulte [Quickstart: Adicione uma aplicação ao seu inquilino Azure Ative Directory (Azure AD).](../manage-apps/add-application-portal.md)

Se já configurar o Playvox para um único sign-on (SSO), pode utilizar a mesma aplicação. No entanto, recomendamos que crie uma aplicação separada ao testar a integração inicialmente.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento

Utiliza o serviço de prestação de Ad Azure para o âmbito de aplicação de quem será a provisionado, quer na atribuição à aplicação, quer em atributos do utilizador ou grupo. Para ver quem será aprovisionado na sua app com base na atribuição, consulte gerir a [atribuição do utilizador para uma aplicação no Azure Ative Directory](../manage-apps/assign-user-or-group-access-portal.md) para aprender a atribuir utilizadores ou grupos à aplicação. Para o âmbito de aplicação de quem será provisionado com base apenas em atributos do utilizador ou grupo, utilize um filtro de escotagem, conforme descrito no [provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Lembre-se destes pontos:

* Ao atribuir os utilizadores à Playvox, tem de selecionar outra função que não o Acesso Predefinido. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se o Acesso Predefinido for a única função disponível na aplicação, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções.

* Comece pequeno. Teste com um pequeno conjunto de utilizadores ou grupos antes de rolar para todos. Quando o âmbito de provisionamento é baseado em utilizadores ou grupos atribuídos, pode controlar o tamanho do conjunto atribuindo apenas um ou dois utilizadores ou grupos à aplicação. Ao o âmbito de provisionamento inclui todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para limitar o tamanho do seu conjunto de teste.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Passo 5: Configurar o fornecimento automático do utilizador à Playvox

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos, com base em atribuições de utilizador ou grupo em Azure AD.

Para configurar o fornecimento automático de utilizadores para a Playvox em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![Screenshot parcial do portal Azure, com aplicações da Enterprise e todos os itens de aplicações em destaque](common/enterprise-applications.png)

2. Na lista de aplicações, procure e selecione **Playvox**.

    ![Screenshot parcial da lista de aplicações, com a caixa de pesquisa de aplicação em destaque.](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot parcial mostrando o item do menu de Provisioning.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot parcial do separador Provisioning, mostrando a opção Automática selecionada na caixa de lista de down-down do modo de provisionamento.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administrador,** insira o URL **do seu Inquilino Playvox** como:

    `https://{tenant}.playvox.com/scim/v1`

    Insira o **Token Secreto** que copiou mais cedo no passo 2. Em seguida, selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se à Playvox. Se a ligação falhar, certifique-se de que a sua conta Playvox tem permissões de Administração e tente novamente.

    ![Screenshot parcial mostrando a secção de Credenciais de Administrador, incluindo URL de inquilino e caixas de texto Secret Token, e com a ligação de ligação de teste realçada.](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa de texto **do Email de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que receberá as notificações de erro de provisionamento. Em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Screenshot parcial mostrando a caixa de texto de Email de Notificação e a caixa de verificação de notificação de e-mail.](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Playvox**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Playvox na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Playvox para operações de atualização. Se optar por alterar o [atributo alvo correspondente,](../app-provisioning/customize-application-attributes.md)certifique-se de que a API Playvox suporta utilizadores filtrantes com base nesse atributo. **Selecione Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |active|Booleano|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|Cadeia|
   |externalId|String|

10. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento AZure AD para a Playvox, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Screenshot parcial da secção Definições, mostrando o estado de provisionamento definido para On.](common/provisioning-toggle-on.png)

12. Ainda em **Definições,** defina os utilizadores ou grupos para provisão à Playvox, escolhendo os valores que pretende no **Âmbito**.

    ![Screenshot parcial da secção Definições, mostrando a caixa de lista de drop-down scope.](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, **selecione Save**.

    ![Screenshot parcial mostrando opções de Guardar e Descartar.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial demora mais do que os ciclos posteriores. Os ciclos posteriores ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZURE AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6: Monitorize a sua implantação

Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
* Verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
* Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Para saber mais sobre estados de quarentena, consulte [o provisionamento da aplicação em estado de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)