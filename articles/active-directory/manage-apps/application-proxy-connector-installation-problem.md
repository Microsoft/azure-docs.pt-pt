---
title: Problema ao instalar o conector de agente de Proxy de aplicações | Documentos da Microsoft
description: Como resolver problemas que pode se depara ao instalar o conector de agente de Proxy de aplicações
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c82bba6ccb1eaa1933176362e34b8c3e30c37f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783637"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o Conector do Agente do Proxy de Aplicações

Conector do Proxy de aplicações do Microsoft AAD é um componente de domínio interno que utiliza as ligações de saída para estabelecer a conectividade do ponto final disponíveis na cloud para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas com problemas gerais com a instalação do conector

Quando a instalação de um conector falhar, a causa raiz é normalmente uma das seguintes áreas:

1.  **Conectividade** – para concluir uma instalação com êxito, as necessidades de conector novo para se registrar e estabelecer as propriedades de fidedignidade futuras. Isso é feito ao ligar ao serviço de nuvem de Proxy de aplicações do AAD.

2.  **Estabelecimento de confiança** – o novo conector cria um certificado autoassinado e registra para o serviço em nuvem.

3.  **Autenticação do administrador de** – durante a instalação, o utilizador tem de fornecer credenciais de administrador para concluir a instalação de conector.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço de Proxy de aplicações na Cloud e a página de Login da Microsoft

**Objetivo:** Certifique-se de que o computador do conector pode ligar-se para o ponto final do registo de Proxy de aplicações do AAD, bem como a página de início de sessão da Microsoft.

1.  Abra um browser e aceda à página web seguinte: <https://aadap-portcheck.connectorporttest.msappproxy.net> e certifique-se de que a conectividade com data Centers de E.U.A. Central e E.U.A. leste, com as portas 80 e 443 está a funcionar.

2.  Se qualquer uma dessas portas não for bem sucedida (não tem uma marca de verificação verde), certifique-se de que tem o proxy de Firewall ou back-end \*. msappproxy.net com as portas 80 e 443 definido corretamente.

3.  Abra um browser (separador à parte) e aceda à página web seguinte: <https://login.microsoftonline.com>, certifique-se de que pode iniciar sessão nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Certifique-se de que os componentes de máquina e de back-end de suporte para o certificado de confiança do Proxy de aplicações

**Objetivo:** Certifique-se de que a máquina de conector, o proxy de back-end e o firewall podem suportar o certificado criado pelo conector para confiança futura.

>[!NOTE]
>O conector tenta criar um certificado de SHA512 que é suportado pelo TLS1.2. Se a máquina ou o firewall back-end e o proxy não suportar TLS1.2, a instalação falhar.
>
>

**Para resolver o problema:**

1.  Certifique-se de que a máquina suporta TLS1.2 – versões de todos os Windows após 2012 R2 devem dar suporte a TLS 1.2. Se a sua máquina de conector encontra-se de uma versão do 2012 R2 ou antes, certifique-se de que o KBs seguintes são instalados na máquina: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contacte o administrador de rede e peça para verificar que o proxy de back-end e de firewall não bloqueia SHA512 tráfego de saída.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se o administrador é utilizado para instalar o conector

**Objetivo:** Certifique-se de que o utilizador que tenta instalar o conector é um administrador com as credenciais corretas. Atualmente, o utilizador tem de ser, pelo menos, um administrador da aplicação para a instalação com êxito.

**Para verificar que as credenciais estão corretas:**

Ligar ao <https://login.microsoftonline.com> e utilizar as mesmas credenciais. Certifique-se de que o início de sessão é efetuada com êxito. Pode verificar a função de utilizador ao aceder **do Azure Active Directory**  - &gt; **utilizadores e grupos**  - &gt; **todos os utilizadores**. 

Selecione a sua conta de utilizador, em seguida, "função de diretório" no menu resultante. Certifique-se de que a função selecionada é "Administrador da aplicação". Se não conseguir aceder a qualquer uma das páginas ao longo destes passos, não tem a função necessária.

## <a name="next-steps"></a>Passos Seguintes
[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)
