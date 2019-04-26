---
title: Integrar aplicações no local com o Cloud App Security – o Azure Active Directory | Documentos da Microsoft
description: Configure uma aplicação no local no Azure Active Directory para trabalhar com o Microsoft Cloud App Security (MCAS). Utilizar o controlo de aplicação de acesso condicional do MCAS para monitorizar e sessões de controlo em tempo real com base nas políticas de acesso condicional. Pode aplicar estas políticas para aplicações no local que utilizam o Proxy de aplicações no Azure Active Directory (Azure AD).
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21c197dace3088ceb89270b7e3e8a1b91e9c8ebc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292814"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configurar a monitorização de acesso de aplicações em tempo real com o Microsoft Cloud App Security e Azure Active Directory
Configure uma aplicação no local no Azure Active Directory (Azure AD) para utilizar a segurança de aplicação na Cloud da Microsoft (MCAS) para a monitorização em tempo real. MCAS utiliza o controlo de aplicação de acesso condicional para monitorizar e sessões de controlo em tempo real com base nas políticas de acesso condicional. Pode aplicar estas políticas para aplicações no local que utilizam o Proxy de aplicações no Azure Active Directory (Azure AD).

Aqui estão alguns exemplos dos tipos de políticas que pode criar com MCAS:

- Bloquear ou proteger a transferência de documentos confidenciais em dispositivos não geridos.
- Monitor de quando os utilizadores de alto risco iniciar sessão aplicações e, em seguida, inicie sessão suas ações de dentro da sessão. Com essas informações, pode analisar o comportamento do utilizador para determinar como aplicar as políticas de sessão.
- Utilizar certificados de cliente ou de conformidade do dispositivo para bloquear o acesso a aplicações específicas de dispositivos não geridos.
- Restringir as sessões de utilizador a partir de redes não empresariais. Pode dar acesso restrito para os utilizadores que acedem um aplicativo de fora da sua rede empresarial. Por exemplo, este acesso restrito pode impedir o utilizador de transferir documentos confidenciais.

Para obter mais informações, consulte [proteger aplicações com o Microsoft Cloud App Security aplicação controlo de acesso condicional](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Requisitos

Licença:

- Licença de EMS E5, ou 
- O Azure Active Directory Premium P1 e MCAS autónomo.

Aplicação no local:

- A aplicação no local tem de utilizar a delegação restrita de Kerberos (KCD)

Configure o Proxy de aplicações:

- Configure o Azure AD para utilizar o Proxy de aplicações, incluindo preparar o ambiente e instalar o conector do Proxy de aplicações. Para obter um tutorial, veja [adicionar uma aplicações no local para acesso remoto através do Proxy de aplicações no Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Adicionar a aplicação no local para o Azure AD

Adicione uma aplicação no local para o Azure AD. Para um guia de introdução, consulte [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Ao adicionar a aplicação, certifique-se de que defina as duas definições seguintes no **adicionar a sua aplicação no local** painel:

- **Pré-autenticação**: Enter **Azure Active Directory**.
- **Traduzir URLs no corpo do aplicativo**: Escolher **Sim**.

Essas duas definições são necessárias para a aplicação funcione com MCAS.

## <a name="test-the-on-premises-application"></a>Testar a aplicação no local

Depois de adicionar a sua aplicação para o Azure AD, utilize os passos em [testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application) para adicionar um utilizador para fins de teste e testar o início de sessão. 

## <a name="deploy-conditional-access-app-control"></a>Implementar o controlo de aplicação de acesso condicional

Para configurar a sua aplicação com o controlo de aplicação de acesso condicional, siga as instruções em [implementar o controlo de aplicação de acesso condicional para aplicações do Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Controlo de aplicação de acesso condicional de teste

Para testar a implementação de aplicações do Azure AD com o controlo de aplicação de acesso condicional, siga as instruções em [testar a implementação para aplicações do Azure AD](/cloud-app-security/proxy-deployment-aad).





