---
title: Integrar aplicativos no local com Cloud App Security - Azure AD
description: Configure uma aplicação no local no Azure Ative Directory para trabalhar com a Microsoft Cloud App Security (MCAS). Utilize o Controlo de Aplicações de Acesso Condicional MCAS para monitorizar e controlar sessões em tempo real com base em políticas de Acesso Condicional. Pode aplicar estas políticas a aplicações no local que utilizam o Application Proxy in Azure Ative Directory (Azure AD).
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 889168782acabaafa4200a8ebd097e431a68ede3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764575"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configure a monitorização do acesso a aplicações em tempo real com a Microsoft Cloud App Security e o Azure Ative Directory
Configure uma aplicação no local no Azure Ative Directory (Azure AD) para utilizar o Microsoft Cloud App Security (MCAS) para monitorização em tempo real. O MCAS utiliza o Controlo de Aplicações de Acesso Condicional para monitorizar e controlar sessões em tempo real com base em políticas de Acesso Condicional. Pode aplicar estas políticas a aplicações no local que utilizam o Application Proxy in Azure Ative Directory (Azure AD).

Aqui estão alguns exemplos dos tipos de políticas que pode criar com o MCAS:

- Bloqueie ou proteja o descarregamento de documentos sensíveis em dispositivos não geridos.
- Monitorize quando os utilizadores de alto risco se inscrevem nas aplicações e, em seguida, registar as suas ações a partir da sessão. Com esta informação, pode analisar o comportamento do utilizador para determinar como aplicar políticas de sessão.
- Utilize certificados de cliente ou conformidade do dispositivo para bloquear o acesso a aplicações específicas de dispositivos não geridos.
- Restringir as sessões de utilizadores de redes não corporativas. Pode dar acesso restrito aos utilizadores que acedam a uma aplicação de fora da sua rede corporativa. Por exemplo, este acesso restrito pode impedir o utilizador de descarregar documentos sensíveis.

Para obter mais informações, consulte [as aplicações Protect com o Microsoft Cloud App Security Conditional Access App Control](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Requisitos

Licença:

- Licença EMS E5, ou 
- Azure Ative Directory Premium P1 e MCAS Autónomo.

Aplicação no local:

- O pedido no local deve utilizar a Delegação Restrita Kerberos (KCD)

Configure aplicação Proxy:

- Configure a Ad AD para utilizar o Application Proxy, incluindo preparar o seu ambiente e instalar o conector Application Proxy. Para obter um tutorial, consulte [adicionar uma aplicação no local para acesso remoto através da Aplicação Proxy em Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Adicionar aplicação no local à Azure AD

Adicione uma aplicação no local à Azure AD. Para um arranque rápido, consulte [adicionar uma aplicação no local ao Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) Ao adicionar a aplicação, certifique-se de definir as duas seguintes definições na lâmina de aplicação Adicionar a sua lâmina **de aplicação no local:**

- **Pré Autenticação**: **Insira o Diretório Ativo Azure**.
- **Traduzir URLs no Corpo de Aplicação:** Escolha **Sim**.

Estas duas definições são necessárias para que a aplicação funcione com o MCAS.

## <a name="test-the-on-premises-application"></a>Testar a aplicação no local

Depois de adicionar a sua aplicação ao Azure AD, utilize os passos no [Teste da aplicação](application-proxy-add-on-premises-application.md#test-the-application) para adicionar um utilizador para testes e teste o sinal. 

## <a name="deploy-conditional-access-app-control"></a>Implementar controlo de aplicativos de acesso condicional

Para configurar a sua aplicação com o Controlo de Aplicações de Acesso Condicional, siga as instruções no [Controlo de Aplicações de Acesso Condicional para aplicações AD Azure](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Teste Controlo de Aplicativos de Acesso Condicional

Para testar a implementação de aplicações AD Azure com Controlo de Aplicações de Acesso Condicional, siga as instruções em [Testar a implementação para aplicações AD Azure](/cloud-app-security/proxy-deployment-aad).





