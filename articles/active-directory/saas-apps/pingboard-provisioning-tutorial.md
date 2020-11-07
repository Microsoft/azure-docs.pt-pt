---
title: 'Tutorial: Fornecimento de utilizadores para pingboard - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357900"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configure Pingboard para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de seguir para permitir o fornecimento automático e desavisionamento de contas de utilizador de Azure Ative Directory (Azure AD) ao Pingboard.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino da AD AZure
* Uma conta [Pro](https://pingboard.com/pricing) de inquilino pingboard
* Uma conta de utilizador em Pingboard com permissões de administração

> [!NOTE]
> A integração de provisionamento a Azure AD depende da [API pingboard,](https://pingboard.docs.apiary.io/#)que está disponível para a sua conta.

## <a name="assign-users-to-pingboard"></a>Atribuir utilizadores ao Pingboard

A Azure AD utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores afetados a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores em Azure AD que precisam de acesso à sua aplicação Pingboard. Em seguida, pode atribuir estes utilizadores à sua aplicação Pingboard seguindo as instruções aqui:

[Atribuir um utilizador a uma aplicação da empresa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir utilizadores ao Pingboard

Recomendamos que atribua um único utilizador Azure AD ao Pingboard para testar a configuração de provisionamento. Utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-pingboard"></a>Configure o fornecimento de utilizadores ao Pingboard 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador pingboard que fornece API. Também configura o serviço de fornecimento para criar, atualizar e desativar contas de utilizadores atribuídas no Pingboard que se baseiam em atribuições de utilizadores em Azure AD.

> [!TIP]
> Para ativar o único sinal de inscrição baseado em SAML para o Pingboard, siga as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador ao Pingboard em Azure AD

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory**  >  **Enterprise Apps** All  >  **applications.**

1. Se já configurar o Pingboard para um único sinal, procure o seu exemplo de Pingboard utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **pingboard** na galeria de aplicações. Selecione **Pingboard** a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Pingboard e, em seguida, selecione o **separador Provisioning.**

1. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Provisão de pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Na secção **Credenciais de Administração,** utilize os seguintes passos:

    a. No **URL do inquilino,** insira `https://your_domain.pingboard.com/scim/v2` e substitua "your_domain" pelo seu domínio real.

    b. Inscreva-se no [Pingboard](https://pingboard.com/) utilizando a sua conta de administração.

    c. Selecione **Add-Ons**  >  **Integrações adicionais**  >  **Azure Ative Directory**.

    d. Vá ao **separador Configurar** e selecione **Ative o fornecimento do utilizador a partir de Azure**.

    e. Copie o símbolo em **OAuth Bearer Token,** e insira-o em **Secret Token**.

1. No portal Azure, selecione **Test Connection** para testar Azure AD pode ligar-se à sua aplicação Pingboard. Se a ligação falhar, teste se a sua conta de Pingboard tem permissões de administração e tente novamente o passo **de Ligação** de Teste.

1. Insira o endereço de e-mail de uma pessoa ou grupo que pretende receber notificações de erro de provisionamento no **E-mail de Notificação**. Selecione a caixa de verificação por baixo.

1. Selecione **Save** (Guardar).

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Pingboard**.

1. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador para serem sincronizados de Azure AD a Pingboard. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Pingboard para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações. Para obter mais informações, consulte [Personalizar os mapeamentos de atributos de fornecimento do utilizador.](../app-provisioning/customize-application-attributes.md)

1. Para ativar o serviço de prestação de AD Azure para o Pingboard, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

1. **Selecione Guardar** para iniciar a sincronização inicial dos utilizadores atribuídos ao Pingboard.

A sincronização inicial demora mais tempo a ser executada do que a seguir sincronizações, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço estiver em funcionamento. Utilize a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para os registos de atividade de provisionamento. Os registos descrevem todas as ações tomadas pelo serviço de fornecimento na sua aplicação Pingboard.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatório sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](pingboard-tutorial.md)
