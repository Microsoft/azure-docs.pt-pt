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
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037004"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de aplicações e Qlik Sense 
O Azure Active Directory para o Proxy de aplicações e Qlik Sense estabeleceram uma parceria em conjunto para garantir que facilmente pode utilizar o Proxy de aplicações para fornecer acesso remoto para a sua implementação Qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O resto deste cenário pressupõe que feito o seguinte:
 
- Sentido [Qlik](https://community.qlik.com/docs/DOC-19822)configurado. 
- [Instalou um conector proxy de aplicação](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 
Para publicar QlikSense, terá de publicar as duas aplicações no Azure.  

### <a name="application-1"></a>Aplicação #1: 
Siga estes passos para publicar a aplicação. Para uma passagem mais detalhada dos passos 1-8, consulte [as aplicações da Publicação utilizando o Proxy de Aplicação AD Azure](application-proxy-add-on-premises-application.md). 


1. Inicie sessão no portal do Azure como um administrador global. 
2. Selecione **aplicações**de Diretório Ativo > Enterprise **do Azure.** 
3. **Selecione Adicionar** na parte superior da lâmina. 
4. Selecione **aplicação No local**. 
5. Preencha os campos obrigatórios com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 
   - **URL Interno**: Esta aplicação deve ter um URL interno que é o url qlikSense em si. Por exemplo, **&#58;https /demo.qlikemm.com:4244** 
   - **Método de pré-autenticação**: Diretório Ativo Azure (Recomendado mas não necessário) 
1. **Selecione Adicionar** na parte inferior da lâmina. Seu aplicativo é adicionado e abre o menu de início rápido. 
2. No menu de arranque rápido, **selecione Atribuir um utilizador para teste**, e adicione pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tenha acesso à aplicação no local. 
3. **Selecione Atribuir** para salvar a atribuição do utilizador do teste. 
4. (Opcional) No painel de gestão da aplicação, selecione o início de sessão único. Escolha a **Delegação Limitada Kerberos** do menu suspenso e preencha os campos necessários com base na sua configuração Qlik. Selecione **Guardar**. 

### <a name="application-2"></a>Aplicação #2: 
Siga os passos como para a aplicação de n. º 1, com as seguintes exceções: 

**Passo #5**: O URL Interno deve agora ser o URL QlikSense com a porta de autenticação utilizada pela aplicação. O padrão é **de 4244** para HTTPS, e **4248** para HTTP para lançamentos QlikSense antes de abril de 2018. O padrão para lançamentos qlikSense após abril de 2018 é **de 443** para HTTPS e **80** para HTTP.  Ex: **&#58;https //demo.qlik.com:4244**</br></br>
**Passo #10:** Não instale sSO e deixe o **único sinal de desativado**
 
 
## <a name="testing"></a>Testar 
Está agora pronto para testar seu aplicativo. O URL externo que utilizou para publicar QlikSense na aplicação de n. º 1 e o início de sessão como um utilizador atribuído a ambas as aplicações de acesso.  

## <a name="additional-references"></a>Referências adicionais
Para obter mais informações sobre a publicação de Qlik Sense com procuração de aplicação, consulte os seguintes artigos da Comunidade Qlik: 
- [Azure AD com autenticação integrada do Windows usando uma delegação limitada kerberos com Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integração qlik Sense com procuração de aplicação da AD Azure](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações com procuração de aplicação](application-proxy-add-on-premises-application.md)
- [Trabalhar com conectores proxy de aplicação](application-proxy-connector-groups.md)

