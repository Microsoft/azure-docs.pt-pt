---
title: 'Tutorial: Configurar GoToMeeting para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058268"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configurar GoToMeeting para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar no GoToMeeting e na AD AZure para provisões automáticas e desabilitadas contas de utilizadores de Azure AD a GoToMeeting.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma assinatura ativada por GoToMeeting.
*   Uma conta de utilizador em GoToMeeting com permissões de Administração da Equipa.

## <a name="assigning-users-to-gotomeeting"></a>Atribuir utilizadores ao GoToMeeting

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em AZure AD que representam os utilizadores que precisam de acesso à sua aplicação GoToMeeting. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação GoToMeeting seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Dicas importantes para atribuir utilizadores ao GoToMeeting

*   Recomenda-se que um único utilizador AD Azure seja designado para o GoToMeeting para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao GoToMeeting, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da GoToMeeting que fornece a API e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas no GoToMeeting com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sign-on baseado em SAML para o GoToMeeting, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o provisionamento automático da conta de utilizador:

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurar o GoToMeeting para um único sign-on, procure a sua instância de GoToMeeting utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **GoToMeeting** na galeria de aplicações. Selecione GoToMeeting a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de GoToMeeting e, em seguida, selecione o **separador Provisioning.**

1. Desa ajuste o modo **de provisionamento** para **automático**. 

    ![provisionamento](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Na secção Credenciais de Administração, execute os seguintes passos:
   
    a. Na caixa de texto **GoToMeeting Admin User Name,** digite o nome de utilizador de um administrador.

    b. Na caixa de texto **goToMeeting Admin Password,** a palavra-passe do administrador.

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação GoToMeeting. Se a ligação falhar, certifique-se de que a sua conta GoToMeeting tem permissões de Administração da Equipa e tente novamente o passo **"Admin Credentials".**

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação.

1. Clique **em Guardar.**

1. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to GoToMeeting.**

1. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de AZure AD a GoToMeeting. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no GoToMeeting para operações de atualização. Selecione o botão Guardar para escoar quaisquer alterações.

1. Para ativar o serviço de prestação de AD Azure para o GoToMeeting, altere o **Estado de Provisionamento** para **On** na secção Definições

1. Clique **em Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao GoToMeeting na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para os registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação GoToMeeting.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


