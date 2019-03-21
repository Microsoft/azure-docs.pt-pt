---
title: Operações de proteção de palavra-passe do AD do Azure e relatórios
description: Operações de pós-implementação de proteção de palavra-passe do AD do Azure e relatórios
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac91a70465d79b235ae43681a1375d47e7691efe
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285807"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Procedimentos operacionais de proteção de palavra-passe do AD do Azure

Depois de concluir o [instalação de proteção de palavra-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md) no local, existem alguns itens que devem ser configurados no portal do Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurar a lista de palavras-passe banidas personalizado

Siga as instruções no artigo [configurar a lista de palavras-passe banidas personalizado](howto-password-ban-bad-configure.md) para obter passos para personalizar a lista de palavra-passe banidas para a sua organização.

## <a name="enable-password-protection"></a>Ativar a proteção de palavra-passe

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory**, **métodos de autenticação**, em seguida, **proteção por senha**.
1. Definir **ativar a proteção de palavra-passe no Active Directory do Windows Server** para **Sim**
1. Conforme mencionado na [guia de implementação](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), é recomendado para definir inicialmente o **modo** para **auditoria**
   * Quando estiver familiarizado com a funcionalidade, pode mudar a **modo** para **imposto**
1. Clicar em **Guardar**

![Ativar a componentes de proteção de palavra-passe do Azure AD no portal do Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modo de Auditoria

Modo de auditoria destina-se como uma forma de executar o software num modo "what if". Cada serviço do agente DC avalia uma senha de entrada, de acordo com a política atualmente ativa. Se a política atual estiver configurada para estar no modo de auditoria, as palavras-passe "bad" resultam em mensagens de registo de eventos, mas são aceites. Esta é a única diferença entre o modo de auditoria e impor; todas as outras operações, execute o mesmo.

> [!NOTE]
> A Microsoft recomenda que a implantação inicial e teste sempre começam no modo de auditoria. Eventos no registo de eventos, em seguida, devem ser monitorizados para tentar prever se todos os processos operacionais existentes iriam ser seja afetados quando o modo de imposição está ativado.

## <a name="enforce-mode"></a>Modo de imposição

Impor o modo destina-se como configuração final. Como no modo de auditoria acima, cada serviço do agente DC avalia entradas palavras-passe, de acordo com a política atualmente ativa. Se o modo de imposição está ativado no entanto, uma palavra-passe que é considerada não segura, de acordo com a política será rejeitada.

Quando uma palavra-passe é rejeitada no modo imposição pelo agente do Azure AD palavra-passe DC de proteção, o impacto visível visto por um usuário final é idêntico ao que seria vêem se a palavra-passe foi rejeitada pela imposição de complexidade de palavra-passe tradicionais no local. Por exemplo, um utilizador poderá ver a seguinte mensagem de erro tradicional no ecrã de palavra-passe do Windows logon\change:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Esta mensagem é apenas um exemplo de vários resultados possíveis. A mensagem de erro específicas pode variar consoante o cenário que está a tentar definir uma palavra-passe não segura ou software real.

Os utilizadores finais afetados poderá ter de trabalhar com sua equipe de TI para compreender os requisitos novos e mais pode escolher as palavras-passe seguras.

## <a name="enable-mode"></a>Ativar o modo

Esta definição normalmente deve ser deixada no estado predefinido ativado (Sim). Configurar esta definição para desativado (não), fará com que todas implementados agentes do Azure AD palavra-passe DC de proteção entrar num modo inativo em que todas as senhas são aceites como-é, e não existem atividades de validação será executada nenhuma forma (por exemplo, nem mesmo os eventos de auditoria será emitido).

## <a name="next-steps"></a>Passos Seguintes

[Monitorização para a proteção de palavras-passe do AD do Azure](howto-password-ban-bad-on-premises-monitor.md)
