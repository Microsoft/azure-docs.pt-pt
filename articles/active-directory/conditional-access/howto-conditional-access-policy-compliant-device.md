---
title: Acesso Condicional - Exigir dispositivos conformes - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para exigir dispositivos conformes
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295206"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso Condicional: Exigir dispositivos conformes

As organizações que implementaram o Microsoft Intune podem utilizar as informações devolvidas dos seus dispositivos para identificar dispositivos que satisfaçam os requisitos de conformidade, tais como:

* Exigindo um PIN para desbloquear
* Requerendo encriptação do dispositivo
* Exigindo uma versão mínima ou máxima do sistema operativo
* Exigir um dispositivo não é preso ou enraizado

Esta informação de conformidade de política é transmitida à Azure AD onde o Acesso Condicional pode tomar decisões para conceder ou bloquear o acesso aos recursos. Mais informações sobre as políticas de conformidade do dispositivo podem ser encontradas no artigo, [definir regras sobre dispositivos para permitir o acesso aos recursos na sua organização usando intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de Acesso Condicional para exigir que os dispositivos de acesso aos recursos sejam marcados como conformes com as políticas de conformidade intune da sua organização.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo do Azure.
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou de vidro de emergência da sua organização. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações** > cloud**Inclua**, selecione **todas as aplicações em nuvem**.
   1. Se tiver de excluir aplicações específicas da sua apólice, pode escolhê-las no separador **Excluir** em aplicações de **nuvem excluídas e** escolher **Select**.
   1. Selecione **Done** (Concluído).
1. Em **condições,** > **as aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim,** e selecionar **Feito**.
1. Sob **controlos** > de acesso**Grant**, selecione Exigir que o dispositivo seja marcado **como conforme**.
   1. Selecione **Selecionar**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar para ativar a sua política.

> [!NOTE]
> Pode inscrever os seus novos dispositivos para Intune mesmo que selecione **o dispositivo Exigir que o dispositivo seja marcado como conforme** para todos os **utilizadores** e todas **as aplicações em nuvem** utilizando os passos acima. **Exigir que o dispositivo seja marcado como** controlo conforme não bloqueia a inscrição de Intune. 

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores web de terceiros A AD AD identifica o dispositivo utilizando um certificado de cliente que é aprovisionado quando o dispositivo está registado na Azure AD. Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador final deve selecionar este certificado antes de poder continuar a utilizar o navegador.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade de dispositivos funcionam com o Microsoft Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
