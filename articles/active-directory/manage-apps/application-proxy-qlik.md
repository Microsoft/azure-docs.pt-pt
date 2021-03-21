---
title: Azure AD App Proxy e Qlik Sense| Microsoft Docs
description: Ligue o Application Proxy no portal Azure e instale os Conectores para o representante inverso.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259089"
---
# <a name="application-proxy-and-qlik-sense"></a>Aplicação Proxy e Qlik Sense 
A Azure Ative Directory Application Proxy e Qlik Sense uniram-se para garantir que é facilmente capaz de usar o Application Proxy para fornecer acesso remoto para a sua implementação Qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O resto deste cenário pressupõe que fez o seguinte:
 
- [Sentido Qlik](https://community.qlik.com/docs/DOC-19822)configurado. 
- [Instalado um conector Proxy de aplicação](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 
Para publicar o QlikSense, terá de publicar duas aplicações no Azure.  

### <a name="application-1"></a>#1 de aplicação: 
Siga estes passos para publicar a sua aplicação. Para obter uma passagem mais detalhada dos passos 1-8, consulte [as aplicações de publicação utilizando o Proxy da Aplicação AD AD da Azure](application-proxy-add-on-premises-application.md). 


1. Inicie sessão no Portal do Azure como administrador global. 
2. Selecione as aplicações **da Azure Ative Directory**  >  **Enterprise**. 
3. **Selecione Adicione** na parte superior da lâmina. 
4. Selecione **aplicação no local**. 
5. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 
   - **URL interno**: Esta aplicação deve ter um URL interno que é o url QlikSense em si. Por exemplo, **https&#58;//demo.qlikemm.com:4244** 
   - **Método de pré-autenticação**: Diretório Ativo Azure (Recomendado, mas não necessário) 
1. **Selecione Adicione** na parte inferior da lâmina. A sua aplicação é adicionada e o menu de arranque rápido abre. 
2. No menu de arranque rápido, selecione **Atribua um utilizador para testes** e adicione pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tem acesso à aplicação no local. 
3. Selecione **Atribuir** para guardar a atribuição do utilizador do teste. 
4. (Opcional) Na lâmina de gestão de aplicações, selecione Single sign-on. Escolha **a Delegação Condicionada Kerberos** do menu suspenso e preencha os campos necessários com base na sua configuração Qlik. Selecione **Guardar**. 

### <a name="application-2"></a>#2 de aplicação: 
Siga os mesmos passos que para a aplicação #1, com as seguintes exceções: 

**Passo #5**: O URL Interno deve agora ser o URL QlikSense com a porta de autenticação utilizada pela aplicação. O padrão é **de 4244** para HTTPS e **4248** para VERS para ver lançamentos QlikSense antes de abril de 2018. O padrão para lançamentos QlikSense após abril de 2018 é **de 443** para HTTPS e **80** para HTTP.  Ex: **https&#58;//demo.qlik.com:4244**</br></br>
**Passo #10:** Não instale SSO e deixe o **único sinal de insação**
 
 
## <a name="testing"></a>Testar 
A sua aplicação está agora pronta para ser testada. Aceda ao URL externo que usou para publicar qlikSense em #1 de aplicação, e faça login como um utilizador atribuído a ambas as aplicações.  

## <a name="additional-references"></a>Referências adicionais
Para obter mais informações sobre a publicação do Qlik Sense com Application Proxy, consulte os seguintes artigos comunitários da Qlik: 
- [Azure AD com autenticação integrada do Windows usando uma delegação restrita kerberos com Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integração Qlik Sense com Proxy de Aplicação AD AZure](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações com o Proxy da Aplicação](application-proxy-add-on-premises-application.md)
- [Trabalhar com conectores proxy de aplicação](application-proxy-connector-groups.md)

