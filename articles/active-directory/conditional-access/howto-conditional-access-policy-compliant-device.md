---
title: Acesso Condicional - Requer dispositivos compatíveis - Azure Ative Directory
description: Crie uma política de acesso condicional personalizado para exigir dispositivos compatíveis
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049133"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso Condicional: Requer dispositivos compatíveis

As organizações que implementaram o Microsoft Intune podem utilizar as informações devolvidas dos seus dispositivos para identificar dispositivos que satisfaçam os requisitos de conformidade, tais como:

* Exigindo um PIN para desbloquear
* Exigindo encriptação do dispositivo
* Que requer uma versão do sistema operativo mínimo ou máximo
* Exigir um dispositivo não é quebrado ou enraizado

Esta informação de conformidade da política é reencaminhada para a Azure AD onde o Acesso Condicional pode tomar decisões para conceder ou bloquear o acesso aos recursos. Mais informações sobre as políticas de conformidade do dispositivo podem ser encontradas no artigo, [definir regras em dispositivos para permitir o acesso aos recursos na sua organização usando o Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As seguintes medidas ajudarão a criar uma política de Acesso Condicional para exigir que os dispositivos que acedam aos recursos sejam marcados como conformes com as políticas de conformidade intune da sua organização.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**.
   1. Se tiver de excluir aplicações específicas da sua política, pode selecioná-las no separador **Excluir** em **Aplicações de nuvem excluídas** e escolher **Select**.
   1. Selecione **Concluído**.
1. **Em Condições As**  >  **aplicações do Cliente (Pré-visualização)**  >  **Selecione as aplicações do cliente a que esta política se aplicará,** deixe todas as predefinições selecionadas e selecione **Feito**.
1. Sob **controlos de acesso**  >  **Grant**, **selecione Exigir que o dispositivo seja marcado como conforme**.
   1. Selecione **Selecionar**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

> [!NOTE]
> Pode inscrever os seus novos dispositivos no Intune mesmo que selecione **O dispositivo Requerer que seja marcado como conforme** para todos os **utilizadores** e **todas as aplicações em nuvem** utilizando os passos acima. **Exigir que o dispositivo seja marcado como** controlo compatível não bloqueia a inscrição do Intune. 

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores web de terceiros, a Azure AD identifica o dispositivo usando um certificado de cliente que é a provisionado quando o dispositivo está registado no Azure AD. Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador final deve selecionar este certificado antes de poder continuar a utilizar o navegador.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade de dispositivos funcionam com o Microsoft Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
