---
title: Procurador de Aplicação Azure AD e Qlik Sense Microsoft Docs
description: Ligue o Proxy de Aplicação no portal Azure e instale os Conectores para o proxy inverso.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037004"
---
# <a name="application-proxy-and-qlik-sense"></a>Procuração de aplicação e Qlik Sense 
A Procuração de Aplicação de Diretório Ativo Azure e a Qlik Sense uniram-se para garantir que é facilmente capaz de utilizar o Application Proxy para fornecer acesso remoto à sua implementação qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O restante deste cenário pressupõe que fez o seguinte:
 
- Sentido [Qlik](https://community.qlik.com/docs/DOC-19822)configurado. 
- [Instalou um conector proxy de aplicação](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publique as suas aplicações no Azure 
Para publicar o QlikSense, terá de publicar duas aplicações no Azure.  

### <a name="application-1"></a>#1 de aplicação: 
Siga estes passos para publicar a sua aplicação. Para uma passagem mais detalhada dos passos 1-8, consulte [as aplicações da Publicação utilizando o Proxy de Aplicação AD Azure](application-proxy-add-on-premises-application.md). 


1. Inicie sessão no Portal do Azure como administrador global. 
2. Selecione**aplicações** **Azure Ative Directory** > Enterprise . 
3. **Selecione Adicionar** na parte superior da lâmina. 
4. Selecione **aplicação No local**. 
5. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições: 
   - **URL Interno**: Esta aplicação deve ter um URL interno que é o url qlikSense em si. Por exemplo, **https&#58;//demo.qlikemm.com:4244** 
   - **Método de pré-autenticação**: Diretório Ativo Azure (Recomendado mas não necessário) 
1. **Selecione Adicionar** na parte inferior da lâmina. A sua aplicação é adicionada e o menu de início rápido abre. 
2. No menu de arranque rápido, **selecione Atribuir um utilizador para teste**, e adicione pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tem acesso ao pedido no local. 
3. **Selecione Atribuir** para salvar a atribuição do utilizador do teste. 
4. (Opcional) Na lâmina de gestão da aplicação, selecione single sign-on. Escolha a **Delegação Limitada Kerberos** do menu suspenso e preencha os campos necessários com base na sua configuração Qlik. Selecione **Guardar**. 

### <a name="application-2"></a>#2 de aplicação: 
Siga os mesmos passos que para a Aplicação #1, com as seguintes exceções: 

**Passo #5**: O URL Interno deve agora ser o URL QlikSense com a porta de autenticação utilizada pela aplicação. O padrão é **de 4244** para HTTPS, e **4248** para HTTP para lançamentos QlikSense antes de abril de 2018. O padrão para lançamentos qlikSense após abril de 2018 é **de 443** para HTTPS e **80** para HTTP.  Ex: **https&#58;//demo.qlik.com:4244**</br></br>
**Passo #10:** Não instale sSO e deixe o **único sinal de desativado**
 
 
## <a name="testing"></a>Testar 
A sua candidatura está pronta para ser testálizada. Aceda ao URL externo utilizado para publicar o QlikSense na aplicação #1 e faça login como utilizador atribuído a ambas as aplicações.  

## <a name="additional-references"></a>Referências adicionais
Para obter mais informações sobre a publicação de Qlik Sense com procuração de aplicação, consulte os seguintes artigos da Comunidade Qlik: 
- [Azure AD com autenticação integrada do Windows usando uma delegação limitada kerberos com Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integração qlik Sense com procuração de aplicação da AD Azure](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações com o Proxy da Aplicação](application-proxy-add-on-premises-application.md)
- [Trabalhar com conectores proxy de aplicação](application-proxy-connector-groups.md)

