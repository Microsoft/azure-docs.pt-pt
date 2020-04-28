---
title: Integrar aplicações no local com Cloud App Security - Azure AD
description: Configure uma aplicação no local no Azure Ative Directory para trabalhar com o Microsoft Cloud App Security (MCAS). Utilize o McAS Conditional Access App Control para monitorizar e controlar sessões em tempo real com base nas políticas de Acesso Condicional. Pode aplicar estas políticas a aplicações no local que utilizam o Proxy de Aplicação em Diretório Ativo Azure (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275498"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configure a monitorização do acesso a aplicações em tempo real com o Microsoft Cloud App Security e o Diretório Ativo do Azure
Configure uma aplicação no local no Azure Ative Directory (Azure AD) para utilizar o Microsoft Cloud App Security (MCAS) para monitorização em tempo real. O MCAS utiliza o Controlo de Aplicações de Acesso Condicional para monitorizar e controlar sessões em tempo real com base nas políticas de Acesso Condicional. Pode aplicar estas políticas a aplicações no local que utilizam o Proxy de Aplicação em Diretório Ativo Azure (Azure AD).

Aqui estão alguns exemplos dos tipos de políticas que pode criar com o MCAS:

- Bloqueie ou proteja o download de documentos sensíveis em dispositivos não geridos.
- Monitorize quando os utilizadores de alto risco acedem às aplicações e, em seguida, inicieo o registo das suas ações a partir da sessão. Com esta informação, pode analisar o comportamento do utilizador para determinar como aplicar as políticas de sessão.
- Utilize certificados de cliente ou conformidade do dispositivo para bloquear o acesso a aplicações específicas de dispositivos não geridos.
- Restringir as sessões de utilizadores de redes não empresariais. Pode dar acesso restrito aos utilizadores que acedam a uma aplicação de fora da sua rede corporativa. Por exemplo, este acesso restrito pode impedir o utilizador de descarregar documentos sensíveis.

Para mais informações, consulte [Proteja as aplicações com](/cloud-app-security/proxy-intro-aad)o Microsoft Cloud App Security Conditional Access App Control .

## <a name="requirements"></a>Requisitos

Licença:

- Licença EMS E5, ou 
- Azure Ative Directory Premium P1 e MCAS Autónomos.

Pedido no local:

- O pedido no local deve utilizar a Delegação Limitada kerberos (KCD)

Configure Procuração de aplicação:

- Configure o AD Azure para utilizar o Proxy da Aplicação, incluindo preparar o seu ambiente e instalar o conector Proxy de Aplicação. Para um tutorial, consulte [Adicionar um pedido no local para acesso remoto através de Application Proxy em Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Adicionar aplicação no local à Azure AD

Adicione uma aplicação no local ao Azure AD. Para um arranque rápido, consulte [Adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Ao adicionar a aplicação, **certifique-se** de definir as seguintes duas definições na lâmina de aplicação Adicionar no local:

- **Pré-Autenticação**: Insira **o Diretório Ativo Azure**.
- **Traduzir URLs no Corpo**de Aplicação : Escolha **Sim**.

Estas duas definições são necessárias para que a aplicação funcione com o MCAS.

## <a name="test-the-on-premises-application"></a>Testar o pedido no local

Depois de adicionar a sua aplicação ao Azure AD, utilize os passos em [Teste da aplicação](application-proxy-add-on-premises-application.md#test-the-application) para adicionar um utilizador para testes e testar o sinal. 

## <a name="deploy-conditional-access-app-control"></a>Implementar controlo de aplicações de acesso condicional

Para configurar a sua aplicação com o Controlo de Aplicações de Acesso Condicional, siga as instruções no Controlo de Aplicações de [Acesso Condicional para aplicações Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Teste controlo de aplicações de acesso condicional

Para testar a implementação de aplicações Azure AD com controlo de aplicações de acesso condicional, siga as instruções em [Teste da implementação de aplicações Azure AD](/cloud-app-security/proxy-deployment-aad).





