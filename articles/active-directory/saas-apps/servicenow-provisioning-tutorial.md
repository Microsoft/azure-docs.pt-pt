---
title: 'Tutorial: Configurar o ServiceNow para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967172"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Tutorial: Configurar o ServiceNow para o provisionamento automático de usuário com o Azure Active Directory

O objetivo deste tutorial é mostrar as etapas que você precisa executar no ServiceNow e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ServiceNow.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ServiceNow, você precisa dos seguintes itens:

- Uma subscrição do Azure
- Para o ServiceNow, uma instância ou locatário do ServiceNow, versão Calgary ou superior
- Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinque ou superior

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="assigning-users-to-servicenow"></a>Atribuindo usuários ao ServiceNow

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo ServiceNow. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo ServiceNow seguindo estas instruções: [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   É recomendável que um único usuário do Azure AD seja atribuído ao ServiceNow para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.
>*   Ao atribuir um usuário ao ServiceNow, você deve selecionar uma função de usuário válida. A função de "acesso padrão" não funciona para provisionamento.
>*   Para obter mais informações sobre como criar e configurar funções no Azure AD, consulte este [link](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

## <a name="enable-automated-user-provisioning"></a>Habilitar provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do ServiceNow e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no ServiceNow com base na atribuição de usuário e de grupo no Azure AD.

> [!TIP]
>Você também pode optar por habilitar o logon único baseado em SAML para o ServiceNow, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

1. Se você já tiver configurado o ServiceNow para logon único, pesquise sua instância do ServiceNow usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **ServiceNow** na Galeria de aplicativos. Selecione ServiceNow nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do ServiceNow e, em seguida, selecione a guia **provisionamento** .

1. Defina o modo de **provisionamento** como **automático**. 

    ![a aprovisionar](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Na seção credenciais de administrador, execute as seguintes etapas:
   
    a. Na caixa de texto **nome da instância do servicenow** , digite o nome da instância do servicenow.

    b. Na caixa de texto **nome de usuário do administrador do ServiceNow** , digite o nome de usuário de um administrador.

    c. Na caixa de texto senha do administrador do **ServiceNow** , a senha do administrador.

1. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo ServiceNow. Se a conexão falhar, verifique se sua conta do ServiceNow tem permissões de administrador de equipe e tente a etapa **"credenciais de administrador"** novamente.

1. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção.

1. Clique em **salvar.**

1. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para o ServiceNow.**

1. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o ServiceNow. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no ServiceNow para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD para o ServiceNow, altere o **status de provisionamento** para **ativado** na seção Configurações

1. Clique em **salvar.**

Ele inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao ServiceNow na seção usuários e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo ServiceNow.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar logon único](servicenow-tutorial.md)


