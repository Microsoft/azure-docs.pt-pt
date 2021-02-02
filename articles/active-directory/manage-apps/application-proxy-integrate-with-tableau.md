---
title: Azure Ative Directory Application Proxy e Tableau | Microsoft Docs
description: Saiba como utilizar o Azure Ative Directory (Azure AD) Application Proxy para fornecer acesso remoto para a sua implementação do Tableau.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258630"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Ative Directory Application Proxy e Tableau 

A Azure Ative Directory Application Proxy e Tableau têm uma parceria para garantir que é facilmente capaz de usar o Application Proxy para fornecer acesso remoto para a sua implementação do Tableau. Este artigo explica como configurar este cenário.  

## <a name="prerequisites"></a>Pré-requisitos 

O cenário neste artigo pressupõe que tem:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) configurado. 

- Instalado [um conector Proxy de aplicação.](application-proxy-add-on-premises-application.md) 

 
## <a name="enabling-application-proxy-for-tableau"></a>Ativar proxy de aplicação para Tableau 

O Application Proxy suporta o Fluxo de Subvenção OAuth 2.0, que é necessário para que o Tableau funcione corretamente. Isto significa que já não existem quaisquer passos especiais necessários para permitir esta aplicação, além de a configurar seguindo os passos de publicação abaixo.


## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 

Para publicar Tableau, tem de publicar uma aplicação no Portal Azure.

Para:

- Instruções detalhadas dos passos 1-8, consulte [as aplicações de publicação utilizando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 
- Informações sobre como encontrar valores tableau para os campos de Procuração de Aplicações, consulte a documentação do Tableau.  

**Para publicar a sua aplicação:** 


1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicação. 

2. Selecione **Azure Ative Directory > Aplicações Enterprise**. 

3. **Selecione Adicione** na parte superior da lâmina. 

4. Selecione **aplicação no local**. 

5. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 

    - **URL interno**: Esta aplicação deve ter um URL interno que é o URL do tableau em si. Por exemplo, `https://adventure-works.tableau.com`. 

    - **Método de pré-autenticação**: Azure Ative Directory (recomendado, mas não necessário). 

6. **Selecione Adicione** na parte superior da lâmina. A sua aplicação é adicionada e o menu de arranque rápido abre. 

7. No menu de arranque rápido, selecione **Atribua um utilizador para testes** e adicione pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tem acesso à aplicação no local. 

8. Selecione **Atribuir** para guardar a atribuição do utilizador do teste. 

9. (Opcional) Na página de gestão de aplicações, selecione **Single sign-on**. Escolha **a autenticação integrada** do Windows a partir do menu suspenso e preencha os campos necessários com base na configuração do Tableau. Selecione **Guardar**. 

 

## <a name="testing"></a>Testar 

A sua aplicação está agora pronta para ser testada. Aceda ao URL externo que usou para publicar Tableau e faça login como utilizador atribuído a ambas as aplicações.



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure AD Application Proxy, consulte [Como fornecer acesso remoto seguro a aplicações no local.](application-proxy.md)

