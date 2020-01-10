---
title: Operações e relatórios de proteção por senha-Azure Active Directory
description: Operações de pós-implantação e relatórios de proteção de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8602a9c5b69c47c2f663dab461394ef5e1a9bed
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762863"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Procedimentos operacionais de proteção por senha do Azure AD

Depois de concluir a [instalação da proteção de senha do Azure ad](howto-password-ban-bad-on-premises-deploy.md) local, há alguns itens que devem ser configurados no portal do Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurar a lista personalizada de senhas banidas

Siga as orientações no artigo [Configurando a lista de senhas excluídas personalizadas](howto-password-ban-bad-configure.md) para obter as etapas para personalizar a lista de senhas banidas para sua organização.

## <a name="enable-password-protection"></a>Habilitar proteção por senha

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory**, **segurança**, métodos de**autenticação**e proteção por **senha**.
1. Defina **habilitar proteção por senha no Windows Server Active Directory** como **Sim**
1. Conforme mencionado no [Guia de implantação](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), é recomendável definir inicialmente o **modo** como **auditoria**
   * Depois de se sentir confortável com o recurso, você pode alternar o **modo** para **imposto**
1. Clicar em **Guardar**

![Habilitando componentes de proteção de senha do Azure AD no portal do Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modo de auditoria

O modo de auditoria é projetado como uma maneira de executar o software em um modo "e se". Cada serviço de agente de DC avalia uma senha de entrada de acordo com a política ativa no momento. Se a política atual estiver configurada para estar no modo de auditoria, as senhas "incorretas" resultarão em mensagens do log de eventos, mas serão aceitas. Essa é a única diferença entre o modo de auditoria e de aplicação; todas as outras operações são executadas da mesma.

> [!NOTE]
> A Microsoft recomenda que a implantação inicial e o teste sempre sejam iniciados no modo de auditoria. Os eventos no log de eventos devem ser monitorados para tentar antecipar se os processos operacionais existentes seriam incomodados quando o modo imposição for habilitado.

## <a name="enforce-mode"></a>Impor modo

O modo impor é destinado à configuração final. Como no modo de auditoria acima, cada serviço de agente de DC avalia as senhas de entrada de acordo com a política ativa no momento. No entanto, se o modo impor for habilitado, uma senha considerada não segura de acordo com a política será rejeitada.

Quando uma senha é rejeitada no modo impor pelo agente DC da proteção de senha do Azure AD, o impacto visível visto por um usuário final é idêntico ao que eles veriam se sua senha fosse rejeitada pela imposição de complexidade de senha local tradicional. Por exemplo, um usuário pode ver a seguinte mensagem de erro tradicional na tela de senha do Windows logon\change:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Essa mensagem é apenas um exemplo de vários resultados possíveis. A mensagem de erro específica pode variar dependendo do software real ou do cenário que está tentando definir uma senha não segura.

Os usuários finais afetados podem precisar trabalhar com a equipe de ti para entender os novos requisitos e ser mais capazes de escolher senhas seguras.

> [!NOTE]
> A proteção por senha do Azure AD não tem controle sobre a mensagem de erro específica exibida pelo computador cliente quando uma senha fraca é rejeitada.

## <a name="enable-mode"></a>Modo de habilitação

Essa configuração deve ser deixada em seu estado padrão habilitado (Sim). Definir essa configuração como desabilitada (não) fará com que todos os agentes de DC da proteção de senha do Azure AD implantados entrem em um modo inativo em que todas as senhas sejam aceitas como estão, e nenhuma atividade de validação será executada de qualquer forma (por exemplo, nem mesmo eventos de auditoria será emitido).

## <a name="next-steps"></a>Passos seguintes

[Monitoramento da proteção de senha do Azure AD](howto-password-ban-bad-on-premises-monitor.md)
