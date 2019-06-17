---
title: Configurar um SSO de um clique à sua aplicação da Galeria de aplicações do Azure AD | Documentos da Microsoft
description: Passos para configurar o SSO de clique de um à sua aplicação a partir da Galeria de aplicações do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057849"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Um recurso de clique SSO para aplicações de galeria do Azure AD

 Neste tutorial, saiba como executar um SSO clique para todas as aplicações SAML que fornecem a interface do Usuário para a configuração de SSO.

## <a name="introduction-to-one-click-sso"></a>Introdução a um só clique SSO

Um recurso de clique SSO é introduzido para configurar o início de sessão único para aplicações de galeria do Azure AD que suportam o protocolo SAML. Na página de configuração do SSO do Azure AD, nós fornecemos esta opção para permitir que os nossos clientes configurar automaticamente os metadados do Azure AD no lado do aplicativo. O objetivo é ajudar os clientes a configurar o SSO rapidamente com um esforço manual mínimo. 

## <a name="advantages-of-the-one-click-sso"></a>Vantagens do clique SSO

- Configuração rápida do SSO das aplicações de galeria em que os clientes têm de fazer a configuração manual no lado do aplicativo.
- Mais eficiente e precisa forma de configuração.
- Nenhuma comunicação do parceiro ou suporte necessário para a configuração, como o aplicativo fornece a interface do Usuário para a configuração de SAML.

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição ativa do aplicativo com credenciais de administrador que pretende configurar com OneClick SSO.
- **Minha extensão do browser aplicações Secure início de sessão** da Microsoft instalado no browser. Se desejar saber mais sobre esta extensão, consulte este [link](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Detalhes do passo a passo uma funcionalidade de SSO de clique

1. Adicione a aplicação a partir da Galeria de aplicações do Azure AD.

2. Clique no início de sessão único.

3. Clique em Ativar início de sessão único.

4. Preencha os valores de configuração obrigatório na secção de configuração básica de SAML.

    > [!NOTE] 
    > Se a aplicação tem de configuração de declarações personalizadas, configure-os antes de executar OneClick SSO.

5. Se a funcionalidade de SSO de clique de um é implementada para qualquer aplicação da galeria, verá seguinte ecrã. Se o **segura de aplicações meu início de sessão da extensão de browser** é ainda não estiver instalado, terá de clicar em **instalar a extensão** opção.

    ![Instalar a extensão de browser seguro de aplicações My início de sessão](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Depois de adicionar a extensão para o navegador, clique em **nome da aplicação de configuração** que irá redirecioná-lo para o portal de administração do aplicativo. Tem de iniciar sessão como administrador para obter no aplicativo.

    ![Configurar o nome da aplicação](./media/one-click-sso-tutorial/setup-sso.png)

7. A extensão do browser agora irá configurar automaticamente o aplicativo para. Ele primeiro solicita a confirmação se quiser continuar. Clique em **Sim**.

    ![A guardar o automaticamente preenchido dados](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Se precisar de qualquer aplicativo extra nagivation ou passos, verá mensagens apropriadas, que lhe pede para executar as etapas. 

8. Depois da configuração estiver concluída, clique em **Ok** para guardar as alterações.

    ![Salvar os dados de preenchida automaticamente](./media/one-click-sso-tutorial/save-data.png)

9. É apresentada uma mensagem de pop-up de confirmação com êxito e as definições de SSO com êxito estão configuradas. Em seguida, pode testar a aplicação.

    ![SSO configurado](./media/one-click-sso-tutorial/sso-configured.png)

10. Depois da configuração foi concluída com êxito, o aplicativo será terminado e são devolvidos ao portal do Azure.

11. Pode clicar no botão de teste para testar o início de sessão único.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [O que é a extensão de browser seguro de aplicações My início de sessão](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 