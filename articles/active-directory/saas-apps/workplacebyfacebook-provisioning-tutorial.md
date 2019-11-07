---
title: 'Tutorial: configurar o Workplace by Facebook para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee091d1c8f0f477354f6bb422d041278ec5668e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574253"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: configurar o Workplace by Facebook para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no workplace by Facebook e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Workplace by Facebook.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Workplace by Facebook, você precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma assinatura habilitada para logon único do workplace by Facebook

> [!NOTE]
> Para testar as etapas neste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, você deve seguir estas recomendações:

- Não use seu ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Atribuindo usuários ao Workplace by Facebook

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo local de trabalho por Facebook. Depois de decidir, você pode atribuir esses usuários ao aplicativo Workplace by Facebook seguindo estas instruções:

[Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Dicas importantes para atribuir usuários ao Workplace by Facebook

*   É recomendável que um único usuário do Azure AD seja atribuído ao Workplace by Facebook para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

*   Ao atribuir um usuário ao Workplace by Facebook, você deve selecionar uma função de usuário válida. A função de "acesso padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Habilitar provisionamento de usuário

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do workplace by Facebook e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no workplace by Facebook com base no usuário e no grupo atribuição no Azure AD.

>[!Tip]
>Você também pode optar por habilitar o logon único baseado em SAML para o Workplace by Facebook, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Para configurar o provisionamento de conta de usuário para o Workplace by Facebook no Azure AD:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no workplace by Facebook.

O Azure AD dá suporte à capacidade de sincronizar automaticamente os detalhes da conta de usuários atribuídos ao Workplace by Facebook. Essa sincronização automática permite que o Workplace by Facebook obtenha os dados de que precisa para autorizar os usuários para acesso, antes que eles tentem entrar pela primeira vez. Ele também desprovisiona os usuários do workplace by Facebook quando o acesso foi revogado no Azure AD.

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **aplicativos empresariais** > **todos os aplicativos** .

2. Se você já tiver configurado o Workplace by Facebook para logon único, pesquise por sua instância do workplace by Facebook usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Workplace by Facebook** na Galeria de aplicativos. Selecione local de trabalho por Facebook nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do workplace by Facebook e, em seguida, selecione a guia **provisionamento** .

4. Defina o **modo de provisionamento** como **automático**. 

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Na seção **credenciais de administrador** , insira o token de acesso do administrador do seu local de trabalho por Facebook e defina o valor da URL do locatário como `https://www.facebook.com/scim/v1/`. Consulte estas [instruções](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) sobre como criar um token de acesso para o local de trabalho. 

6. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao aplicativo Workplace by Facebook. Se a conexão falhar, verifique se a conta do seu local de trabalho por Facebook tem permissões de administrador de equipe.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção.

8. Clique em **salvar.**

9. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários no workplace pelo Facebook.**

10. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o Workplace by Facebook. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no workplace by Facebook para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD para o Workplace by Facebook, altere o **status de provisionamento** para **ativado** na seção **configurações**

12. Clique em **salvar.**

Para obter mais informações sobre como configurar o provisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foi sincronizada com o Workplace by Facebook.

> [!NOTE]
> Estamos trabalhando junto com a equipe do workplace by Facebook para garantir que o aplicativo do Azure AD seja aprovado e atenda às suas novas diretrizes. O local de trabalho dos prazos do Facebook é 16 de dezembro e esperamos atendê-lo. Nenhum trabalho é esperado para os clientes nesse momento. Em 28 de fevereiro de 2020, os clientes precisarão fazer a transição para a nova integração. Postaremos aqui assim que o caminho de migração estiver disponível.    

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar logon único](workplacebyfacebook-tutorial.md)
