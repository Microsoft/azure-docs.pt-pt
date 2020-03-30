---
title: Problema de instalação do Conector de Agente proxy da aplicação [ Microsoft Docs
description: Como resolver problemas que pode enfrentar ao instalar o Conector de Agente proxy de aplicação
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
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049135"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o Conector do Agente do Proxy de Aplicações

O Conector proxy proxy da aplicação Microsoft AAD é um componente interno de domínio que utiliza ligações de saída para estabelecer a conectividade desde o ponto final disponível da nuvem até ao domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problemas gerais com instalação de conector

Quando a instalação de um conector falha, a causa da raiz é geralmente uma das seguintes áreas:

1.  **Conectividade** – para completar uma instalação bem sucedida, o novo conector precisa de registar e estabelecer futuras propriedades fiducieis. Isto é feito ligando-se ao serviço de nuvem proxy de aplicação aAD.

2.  **Trust Establishment** – o novo conector cria um certificado auto-assinado e regista-se no serviço de nuvem.

3.  **Autenticação do administrador** – durante a instalação, o utilizador deve fornecer credenciais de administração para completar a instalação do Conector.

> [!NOTE]
> Os registos de instalação do Conector podem ser encontrados na pasta %TEMP% e podem ajudar a fornecer informações adicionais sobre o que está a causar uma falha de instalação.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço Proxy de Aplicação de Aplicação cloud e página de Login da Microsoft

**Objetivo:** Verifique se a máquina de conector pode ligar-se ao ponto final de registo proxy da aplicação AAD, bem como à página de login da Microsoft.

1.  No servidor do conector, efetue um teste de porta utilizando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.

2.  Se alguma dessas portas não for bem sucedida, verifique se o Firewall ou o proxy backend têm acesso aos domínios e portas necessários, [Prepare o ambiente no local.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)

3.  Abra um browser (separador) e vá `https://login.microsoftonline.com`para a seguinte página web: , certifique-se de que pode iniciar sessão nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verificar suporte de componentes de máquina e backend para a confiança da procuração de aplicação cert

**Objetivo:** Verifique se a máquina do conector, o proxy de backend e a firewall podem suportar o certificado criado pelo conector para futura confiança.

>[!NOTE]
>O conector tenta criar um certificado SHA512 que é suportado por TLS1.2. Se a máquina ou a firewall e o proxy não suportarem o TLS1.2, a instalação falha.
>
>

**Para resolver a questão:**

1.  Verifique se a máquina suporta TLS1.2 – Todas as versões Windows após 2012 R2 devem suportar TLS 1.2. Se a sua máquina de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados na máquina:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contacte o administrador da sua rede e peça para verificar se o proxy e firewall de backend não bloqueiam o SHA512 para tráfego de saída.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se a administração é usada para instalar o conector

**Objetivo:** Verifique se o utilizador que tenta instalar o conector é um administrador com credenciais corretas. Atualmente, o utilizador deve ser pelo menos um administrador de aplicação para que a instalação tenha sucesso.

**Para verificar se as credenciais estão corretas:**

Ligue-se `https://login.microsoftonline.com` e use as mesmas credenciais. Certifique-se de que o login tem sucesso. Pode verificar a função do utilizador indo para utilizadores de **diretório sonorizadores**  - &gt; ativos do Azure **e grupos**  - &gt; **todos os utilizadores**. 

Selecione a sua conta de utilizador e, em seguida, "Role de Direção" no menu resultante. Verifique se a função selecionada é "Administrador de Aplicação". Se não conseguir aceder a nenhuma das páginas ao longo destes passos, não tem o papel necessário.

## <a name="next-steps"></a>Passos seguintes
[Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
