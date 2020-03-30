---
title: 'Tutorial: Fornecimento de utilizadores para Pingboard - Azure AD'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Pingboard.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061280"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configure pingboard para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa seguir para permitir o fornecimento automático e o desprovisionamento das contas de utilizador de Azure Ative Directory (Azure AD) para Pingboard.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino da AD Azure
* Uma conta [pro](https://pingboard.com/pricing) de inquilino pingboard
* Uma conta de utilizador em Pingboard com permissões de administração

> [!NOTE]
> A integração do fornecimento de AD Azure baseia-se na [API do Pingboard,](https://pingboard.docs.apiary.io/#)que está disponível para a sua conta.

## <a name="assign-users-to-pingboard"></a>Atribuir utilizadores ao Pingboard

A Azure AD utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores afetados a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de provisionamento, deve decidir quais os utilizadores do Azure AD que precisam de acesso à sua aplicação Pingboard. Em seguida, pode atribuir estes utilizadores à sua aplicação Pingboard seguindo as instruções aqui:

[Atribuir um utilizador a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir utilizadores ao Pingboard

Recomendamos que atribua um único utilizador da AD Azure ao Pingboard para testar a configuração de provisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-pingboard"></a>Configure o fornecimento de utilizadores ao Pingboard 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador pingboard que aprovisiona API. Configura também o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas no Pingboard que se baseiam em atribuições de utilizadores em Azure AD.

> [!TIP]
> Para ativar o único sinal baseado em SAML para o Pingboard, siga as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal instável pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador ao Pingboard em Azure AD

1. No [portal Azure,](https://portal.azure.com)navegue até à secção de**aplicações** >  **azure Ative Directory** > Enterprise**Apps Todas as aplicações.**

1. Se já configurar o Pingboard para uma única inscrição, procure a sua instância de Pingboard utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **pingboard** na galeria de aplicações. Selecione **Pingboard** a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Pingboard e, em seguida, selecione o separador **Provisioning.**

1. Definir **o modo de provisionamento** para **automático**.

    ![Provisionamento de pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. No âmbito da secção **de Credenciais de Administrador,** utilize os seguintes passos:

    a. No URL do `https://your_domain.pingboard.com/scim/v2` **Arrendatário,** introduza , e substitua "your_domain" pelo seu domínio real.

    b. Inscreva-se no [Pingboard](https://pingboard.com/) utilizando a sua conta de administração.

    c. Selecione **Add-Ons** > **Integrações** > **Azure Ative Directory**.

    d. Vá ao **separador Configure** e selecione **Ativar o fornecimento do utilizador a partir do Azure**.

    e. Copie o símbolo em **Token OAuth Bearer**e insira-o em **Secret Token**.

1. No portal Azure, selecione **Test Connection** para testar o Azure AD pode ligar-se à sua aplicação Pingboard. Se a ligação falhar, teste que a sua conta Pingboard tem permissões de administração e tente novamente o passo de Ligação de **Teste.**

1. Insira o endereço de e-mail de uma pessoa ou grupo que deseja receber notificações de erro no Email de **Notificação**. Selecione a caixa de verificação por baixo.

1. Selecione **Guardar**.

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Pingboard**.

1. Na secção **DeMapeamentos** de Atributos, reveja os atributos do utilizador a serem sincronizados de Azure AD a Pingboard. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Pingboard para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações. Para mais informações, consulte [Personalizar o fornecimento de mapeamentos de atributos.](../app-provisioning/customize-application-attributes.md)

1. Para ativar o serviço de provisionamento de AD Azure para o Pingboard, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

1. Selecione **Guardar** para iniciar a sincronização inicial dos utilizadores atribuídos ao Pingboard.

A sincronização inicial demora mais tempo a ser executada do que a seguir sincronizações, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Utilize a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividade de provisionamento. Os registos descrevem todas as ações tomadas pelo serviço de provisionamento na sua aplicação Pingboard.

Para obter mais informações sobre como ler os registos de fornecimento de AD Azure, consulte [relatório sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](pingboard-tutorial.md)
