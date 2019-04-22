---
title: Proxy de aplicações do Azure Active Directory e o Tableau | Documentos da Microsoft
description: Saiba como utilizar o Proxy de aplicações do Azure Active Directory (Azure AD) para fornecer acesso remoto para a sua implementação Tableau.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d80381815524658d91067ee880eba47c394058
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783558"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy de aplicações do Azure Active Directory e o Tableau 

Proxy de aplicação do Active Directory do Azure e o Tableau estabeleceram uma parceria para garantir que facilmente pode utilizar o Proxy de aplicações para fornecer acesso remoto para a sua implementação Tableau. Este artigo explica como configurar este cenário.  

## <a name="prerequisites"></a>Pré-requisitos 

O cenário neste artigo parte do princípio de que tem:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) configurado. 

- Uma [conector de Proxy de aplicações](application-proxy-add-on-premises-application.md) instalado. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Ativar o Proxy da aplicação para Tableau 

Proxy de aplicações suporta o OAuth 2.0 fluxo de concessão, que é necessário para a Tableau a funcionar corretamente. Isso significa que já não existem quaisquer passos especiais necessários para ativar esta aplicação, que não seja de configurá-la ao seguir os passos de publicação abaixo.


## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 

Para publicar o Tableau, terá de publicar uma aplicação no Portal do Azure.

Para:

- Obter instruções de etapas detalhadas 1-8, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md). 
- Obter informações sobre como localizar valores de Tableau para os campos de Proxy de aplicações, consulte a documentação de Tableau.  

**Para publicar a aplicação**: 


1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador da aplicação. 

2. Selecione **do Azure Active Directory > aplicações empresariais**. 

3. Selecione **adicionar** na parte superior do painel. 

4. Selecione **aplicação no local**. 

5. Preencha os campos obrigatórios com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 

    - **URL interno**: Esta aplicação deve ter um URL interno que é o URL de Tableau em si. Por exemplo, `https://adventure-works.tableau.com`. 

    - **Método de pré-autenticação**: O Azure Active Directory (recomendado mas não obrigatório). 

6. Selecione **adicionar** na parte superior do painel. Seu aplicativo é adicionado e abre o menu de início rápido. 

7. No menu de início rápido, selecione **atribuir um utilizador para fins de teste**, e adicionar pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tenha acesso à aplicação no local. 

8. Selecione **atribuir** para guardar a atribuição de utilizador de teste. 

9. (Opcional) Na página de gestão de aplicações, selecione **início de sessão único**. Escolher **a autenticação integrada do Windows** no menu pendente e preencha os campos obrigatórios com base na sua configuração de Tableau. Selecione **Guardar**. 

 

## <a name="testing"></a>Testes 

Está agora pronto para testar seu aplicativo. Aceder ao URL externo que utilizou para publicar o Tableau, e inicie sessão como um utilizador atribuído a ambas as aplicações.



## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Proxy de aplicações do Azure AD, consulte [como fornecer acesso remoto seguro a aplicações no local](application-proxy.md).

