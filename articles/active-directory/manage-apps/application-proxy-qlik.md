---
title: Proxy de aplicações do Azure AD e Qlik Sense | Documentos da Microsoft
description: Ativar o Proxy de aplicações no portal do Azure e instalar os conectores para o proxy inverso.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bf71274d8eb1039254cafe48a34587199be724
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263511"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de aplicações e Qlik Sense 
O Azure Active Directory para o Proxy de aplicações e Qlik Sense estabeleceram uma parceria em conjunto para garantir que facilmente pode utilizar o Proxy de aplicações para fornecer acesso remoto para a sua implementação Qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O resto deste cenário pressupõe que feito o seguinte:
 
- Configurado [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Instalar um conector de Proxy de aplicações](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 
Para publicar QlikSense, terá de publicar as duas aplicações no Azure.  

### <a name="application-1"></a>Aplicação #1: 
Siga estes passos para publicar a aplicação. Para um mais detalhadas passo a passo dos passos 1 a 8, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md). 


1. Inicie sessão no portal do Azure como um administrador global. 
2. Selecione **do Azure Active Directory** > **aplicações empresariais**. 
3. Selecione **adicionar** na parte superior do painel. 
4. Selecione **aplicação no local**. 
5. Preencha os campos obrigatórios com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 
   - **URL interno**: esta aplicação deve ter um URL interno que é o URL de QlikSense em si. Por exemplo, **https&#58;//demo.qlikemm.com:4244** 
   - **Método de pré-autenticação**: Azure Active Directory (recomendado mas não obrigatório) 
1. Selecione **adicionar** na parte inferior do painel. Seu aplicativo é adicionado e abre o menu de início rápido. 
2. No menu de início rápido, selecione **atribuir um utilizador para fins de teste**, e adicionar pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tenha acesso à aplicação no local. 
3. Selecione **atribuir** para guardar a atribuição de utilizador de teste. 
4. (Opcional) No painel de gestão da aplicação, selecione o início de sessão único. Escolher **delegação restrita de Kerberos** no menu pendente e preencha os campos obrigatórios com base na sua configuração de Qlik. Selecione **Guardar**. 

### <a name="application-2"></a>Aplicação #2: 
Siga os passos como para a aplicação de n. º 1, com as seguintes exceções: 

**Passo 5 de #** : O URL interno agora deve ser o URL de QlikSense com a porta de autenticação utilizada pela aplicação. O padrão é **4244** para HTTPS e **4248** para versões de http para QlikSense anteriores a 2018 de abril. O padrão para versões QlikSense depois de abril de 2018 é **443** para HTTPS e **80** para http.  Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Passo #10:** não configurar o SSO e deixe o **início de sessão único desativado**
 
 
## <a name="testing"></a>Testes 
Está agora pronto para testar seu aplicativo. O URL externo que utilizou para publicar QlikSense na aplicação de n. º 1 e o início de sessão como um utilizador atribuído a ambas as aplicações de acesso.  

## <a name="additional-references"></a>Referências adicionais
Para obter mais informações sobre como publicar o Qlik Sense Sense com o proxy de aplicativo, consulte os artigos da Comunidade Qlik Sense a seguir: 
- [Azure AD com autenticação integrada do Windows usando uma delegação restrita de Kerberos com sensor Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integração do Qlik Sense Sense com o Azure Proxy de Aplicativo do AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md)
- [Trabalhar com conetores da Proxy da aplicação](application-proxy-connector-groups.md)

