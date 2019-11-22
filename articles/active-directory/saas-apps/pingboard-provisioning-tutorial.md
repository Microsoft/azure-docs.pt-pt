---
title: 'Tutorial: provisionamento de usuário para Pingboard-Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Pingboard.
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
ms.openlocfilehash: 81988f2643fd9acb911e6f70765cedbb4786f14c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278261"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: configurar o Pingboard para o provisionamento automático de usuário

A finalidade deste tutorial é mostrar as etapas que você precisa seguir para habilitar o provisionamento automático e desprovisionamento de contas de usuário do Azure Active Directory (Azure AD) para o Pingboard.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure AD
* Uma [conta pro](https://pingboard.com/pricing) locatário do Pingboard
* Uma conta de usuário no Pingboard com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do Pingboard](https://pingboard.docs.apiary.io/#), que está disponível para sua conta.

## <a name="assign-users-to-pingboard"></a>Atribuir usuários ao Pingboard

O Azure AD usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários atribuídos a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários no Azure AD precisam de acesso ao seu aplicativo Pingboard. Em seguida, você pode atribuir esses usuários ao seu aplicativo Pingboard seguindo estas instruções:

[Atribuir um usuário a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir usuários ao Pingboard

Recomendamos que você atribua um único usuário do Azure AD ao Pingboard para testar a configuração de provisionamento. Usuários adicionais podem ser atribuídos posteriormente.

## <a name="configure-user-provisioning-to-pingboard"></a>Configurar o provisionamento de usuário para o Pingboard 

Esta seção orienta você pela conexão do Azure AD à API de provisionamento de conta de usuário do Pingboard. Você também configura o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Pingboard que se baseiam em atribuições de usuário no Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o Pingboard, siga as instruções fornecidas na [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para o Pingboard no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **aplicativos empresariais** > **todos os aplicativos** .

1. Se você já tiver configurado o Pingboard para logon único, pesquise sua instância do Pingboard usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Pingboard** na Galeria de aplicativos. Selecione **Pingboard** nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do Pingboard e, em seguida, selecione a guia **provisionamento** .

1. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Na seção **credenciais de administrador** , use as seguintes etapas:

    a. Em **URL do locatário**, insira `https://your_domain.pingboard.com/scim/v2`e substitua "your_domain" por seu domínio real.

    b. Entre no [Pingboard](https://pingboard.com/) usando sua conta de administrador.

    c. Selecione **complementos** > **integrações** > **Azure Active Directory**.

    d. Vá para a guia **Configurar** e selecione **habilitar provisionamento de usuário do Azure**.

    e. Copie o token no **token de portador OAuth**e insira-o no **token secreto**.

1. No portal do Azure, selecione **testar conexão** para testar o Azure AD pode se conectar ao seu aplicativo Pingboard. Se a conexão falhar, teste se sua conta do Pingboard tem permissões de administrador e tente a etapa **testar conexão** novamente.

1. Insira o endereço de email de uma pessoa ou grupo que você deseja receber notificações de erro de provisionamento no **email de notificação**. Marque a caixa de seleção abaixo.

1. Selecione **Guardar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Pingboard**.

1. Na seção **mapeamentos de atributo** , examine os atributos de usuário a serem sincronizados do Azure ad para o Pingboard. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Pingboard para operações de atualização. Selecione **salvar** para confirmar as alterações. Para obter mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento de usuário](../manage-apps/customize-application-attributes.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Pingboard, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

1. Selecione **salvar** para iniciar a sincronização inicial de usuários atribuídos ao Pingboard.

A sincronização inicial demora mais para ser executada do que as sincronizações a seguir, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **detalhes de sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento. Os logs descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Pingboard.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatório sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configure single sign-on](pingboard-tutorial.md) (Configurar o início de sessão único)
