---
title: Integrar aplicativos locais com o Cloud App Security-Azure AD
description: Configure um aplicativo local no Azure Active Directory para trabalhar com Microsoft Cloud App Security (MCAS). Use o Controle de Aplicativos de Acesso Condicional MCAS para monitorar e controlar sessões em tempo real com base em políticas de acesso condicional. Você pode aplicar essas políticas a aplicativos locais que usam o proxy de aplicativo no Azure Active Directory (AD do Azure).
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275498"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configurar o monitoramento de acesso a aplicativos em tempo real com Microsoft Cloud App Security e Azure Active Directory
Configure um aplicativo local no Azure Active Directory (Azure AD) para usar Microsoft Cloud App Security (MCAS) para monitoramento em tempo real. O MCAS usa Controle de Aplicativos de Acesso Condicional para monitorar e controlar sessões em tempo real com base em políticas de acesso condicional. Você pode aplicar essas políticas a aplicativos locais que usam o proxy de aplicativo no Azure Active Directory (AD do Azure).

Aqui estão alguns exemplos dos tipos de políticas que você pode criar com MCAS:

- Bloquear ou proteger o download de documentos confidenciais em dispositivos não gerenciados.
- Monitore quando os usuários de alto risco entram em aplicativos e registram suas ações dentro da sessão. Com essas informações, você pode analisar o comportamento do usuário para determinar como aplicar políticas de sessão.
- Use certificados de cliente ou a conformidade do dispositivo para bloquear o acesso a aplicativos específicos de dispositivos não gerenciados.
- Restrinja as sessões de usuário de redes não corporativas. Você pode conceder acesso restrito a usuários que acessam um aplicativo de fora de sua rede corporativa. Por exemplo, esse acesso restrito pode impedir que o usuário Baixe documentos confidenciais.

Para obter mais informações, consulte [proteger aplicativos com Microsoft Cloud App Security controle de aplicativos de acesso condicional](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Requisitos

Carteira

- Licença do EMS E5 ou 
- Azure Active Directory Premium P1 e MCAS autônomos.

Aplicativo local:

- O aplicativo local deve usar a delegação restrita de Kerberos (KCD)

Configurar o proxy de aplicativo:

- Configure o Azure AD para usar o proxy de aplicativo, incluindo preparar seu ambiente e instalar o conector de proxy de aplicativo. Para obter um tutorial, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure ad](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Adicionar aplicativo local ao Azure AD

Adicione um aplicativo local ao Azure AD. Para obter um início rápido, consulte [Adicionar um aplicativo local ao Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Ao adicionar o aplicativo, certifique-se de definir as duas configurações a seguir na folha **Adicionar seu aplicativo local** :

- **Pré-autenticação**: insira **Azure Active Directory**.
- **Traduzir URLs no corpo do aplicativo**: escolha **Sim**.

Essas duas configurações são necessárias para que o aplicativo funcione com o MCAS.

## <a name="test-the-on-premises-application"></a>Testar o aplicativo local

Depois de adicionar seu aplicativo ao Azure AD, use as etapas em [testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application) para adicionar um usuário para teste e teste o logon. 

## <a name="deploy-conditional-access-app-control"></a>Implantar Controle de Aplicativos de Acesso Condicional

Para configurar seu aplicativo com o controle de aplicativo de acesso condicional, siga as instruções em [implantar controle de aplicativo de acesso condicional para aplicativos do Azure ad](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Controle de Aplicativos de Acesso Condicional de teste

Para testar a implantação de aplicativos do Azure AD com o controle de aplicativo de acesso condicional, siga as instruções em [testar a implantação para aplicativos do Azure ad](/cloud-app-security/proxy-deployment-aad).





