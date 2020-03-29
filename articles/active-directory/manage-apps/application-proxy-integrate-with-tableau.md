---
title: Aplicação de Diretório Ativo Azure Proxy e Tableau Microsoft Docs
description: Saiba como utilizar o Proxy de Aplicação de Aplicação do Diretório Ativo Azure (Azure AD) para fornecer acesso remoto à sua implementação em Tableau.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783853"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Procuração de candidatura de diretório ativo Azure e Tableau 

A Procuração de Aplicação de Diretório Ativo Azure e a Tableau associaram-se para garantir que é facilmente capaz de utilizar o Application Proxy para fornecer acesso remoto à sua implementação tableau. Este artigo explica como configurar este cenário.  

## <a name="prerequisites"></a>Pré-requisitos 

O cenário neste artigo pressupõe que tem:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) configurado. 

- Instalado um [conector Proxy de Aplicação.](application-proxy-add-on-premises-application.md) 

 
## <a name="enabling-application-proxy-for-tableau"></a>Habilitar a procuração de candidatura para tableau 

Application Proxy suporta o OAuth 2.0 Grant Flow, que é necessário para tableau funcionar corretamente. Isto significa que já não existem quaisquer passos especiais necessários para ativar esta aplicação, a não ser configurá-la seguindo os passos de publicação abaixo.


## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 

Para publicar tableau, precisa de publicar uma aplicação no Portal Azure.

Para:

- Instruções detalhadas dos passos 1-8, ver [Publicar aplicações utilizando procuração de aplicação AD Azure](application-proxy-add-on-premises-application.md). 
- Informações sobre como encontrar valores de Tableau para os campos proxy da app, consulte a documentação do Tableau.  

**Para publicar a sua aplicação:** 


1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de candidatura. 

2. Selecione aplicações de **Diretório Ativo > Enterprise do Azure.** 

3. **Selecione Adicionar** na parte superior da lâmina. 

4. Selecione **aplicação No local**. 

5. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 

    - **URL Interno**: Esta aplicação deve ter um URL interno que seja o URL tableau em si. Por exemplo, `https://adventure-works.tableau.com`. 

    - **Método de pré-autenticação**: Diretório Ativo Azure (recomendado mas não necessário). 

6. **Selecione Adicionar** na parte superior da lâmina. A sua aplicação é adicionada e o menu de início rápido abre. 

7. No menu de arranque rápido, **selecione Atribuir um utilizador para teste**, e adicione pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tem acesso ao pedido no local. 

8. **Selecione Atribuir** para salvar a atribuição do utilizador do teste. 

9. (Opcional) Na página de gestão de aplicações, selecione **Single sign-on**. Escolha a **Autenticação Integrada** do Windows a partir do menu suspenso e preencha os campos necessários com base na configuração do Tableau. Selecione **Guardar**. 

 

## <a name="testing"></a>Testar 

A sua candidatura está pronta para ser testálizada. Aceda ao URL externo utilizado para publicar Tableau e faça login como utilizador atribuído a ambas as aplicações.



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Procurador de Aplicação AD Azure, consulte [como fornecer acesso remoto seguro a aplicações no local](application-proxy.md).

