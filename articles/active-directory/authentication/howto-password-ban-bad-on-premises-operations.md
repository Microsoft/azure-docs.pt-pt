---
title: Ativar no local a Proteção de Passwords AD Azure
description: Saiba como ativar a Proteção de Passwords AD Azure para um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ee0f3d89d48b23db48e3bf4b78203b09fbcbdbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652623"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Ativar no local a Proteção de Palavras-passe de Diretório Ativo Azure

Os utilizadores geralmente criam senhas que usam palavras locais comuns, como uma escola, equipe de esportes ou pessoa famosa. Estas palavras-passe são fáceis de adivinhar, e fracas contra ataques baseados no dicionário. Para impor senhas fortes na sua organização, a Azure Ative Directory (Azure AD) Password Protection fornece uma lista de senhas proibidas global e personalizada. Um pedido de alteração de senha falha se houver uma correspondência nesta lista de senhas proibidas.

Para proteger o ambiente de Serviços de Domínio ativo do Diretório Ativo (AD DS) no local, pode instalar e configurar a Proteção de Passwords Azure AD para trabalhar com o seu em prem DC. Este artigo mostra-lhe como ativar a Proteção de Passwords Azure AD para o seu ambiente no local.

Para obter mais informações sobre como a Proteção de Passwords Azure AD funciona num ambiente no local, consulte como impor a Proteção de [Passwords AD Azure para](concept-password-ban-bad-on-premises.md)o Diretório Ativo do Servidor do Windows .

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra-lhe como ativar a Proteção de Passwords Azure AD para o seu ambiente no local. Antes de completar este artigo, [instale e registe o serviço de proxy de proteção de senhas Azure AD e agentes DC](howto-password-ban-bad-on-premises-deploy.md) no seu ambiente AD DS no local.

## <a name="enable-on-premises-password-protection"></a>Ativar a proteção de senhas no local

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue pelos**métodos** > de autenticação de > **segurança** > do **Diretório Ativo Azure****.**
1. Detete a opção para ativar a proteção de **passwords no Diretório Ativo do Servidor do Windows** para *Sim*.

    Quando esta definição está definida para *Nº ,* todos os agentes de proteção de senhas Azure AD implantados entram num modo quiescente onde todas as palavras-passe são aceites como está. Não são realizadas atividades de validação e não são gerados eventos de auditoria.

1. Recomenda-se inicialmente definir o **Modo** de *Auditoria*. Depois de se sentir confortável com a funcionalidade e o impacto nos utilizadores da sua organização, pode mudar o **Modo** para *'Enforced*' . Para mais informações, consulte a seguinte secção sobre os [modos de funcionamento](#modes-of-operation).
1. Quando estiver pronto, selecione **Guardar**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modos de funcionamento

Quando ativa a Proteção de Palavras-passe AD Azure, pode utilizar o modo de *auditoria* ou impor o modo de *aplicação.* Recomendamos que a implementação e teste iniciais comecem sempre em modo de auditoria. As entradas no registo do evento devem então ser monitorizadas para antecipar se quaisquer processos operacionais existentes seriam perturbados assim que o modo *Enforce* estiver ativado.

### <a name="audit-mode"></a>Modo de auditoria

*O* modo de auditoria destina-se a executar o software num modo "e se". Cada serviço de agente de proteção de senhas Azure AD avalia uma senha de entrada de acordo com a política atualmente ativa.

Se a política atual estiver configurada para estar em modo de auditoria, as palavras-passe "más" resultam em mensagens de registo de eventos, mas são processadas e atualizadas. Este comportamento é a única diferença entre a auditoria e o modo de aplicação. Todas as outras operações são iguais.

### <a name="enforced-mode"></a>Modo Forçado

*O* modo forçado destina-se a configurar a configuração final. Tal como quando em modo de auditoria, cada serviço de agente de proteção de senhas AD do Azure avalia as palavras-passe de entrada de acordo com a política atualmente ativa. No entanto, quando o modo forçado é ativado, uma palavra-passe considerada insegura de acordo com a política é rejeitada.

Quando uma palavra-passe é rejeitada no modo forçado pelo agente DC de proteção de senhas do Azure AD, um utilizador final vê um erro semelhante ao que veria se a sua palavra-passe foi rejeitada pela aplicação tradicional da complexidade da palavra-passe no local. Por exemplo, um utilizador pode ver a seguinte mensagem de erro tradicional no início do Windows ou alterar o ecrã de palavra-passe:

*"Incapaz de atualizar a senha. O valor previsto para a nova senha não satisfaz o comprimento, complexidade ou requisitos de história do domínio."*

Esta mensagem é apenas um exemplo de vários resultados possíveis. A mensagem de erro específica pode variar dependendo do software ou cenário real que está a tentar definir uma palavra-passe insegura.

Os utilizadores finais afetados podem ter de trabalhar com o seu pessoal de TI para compreender os novos requisitos e escolher senhas seguras.

> [!NOTE]
> A Proteção de Palavras-passe Azure AD não tem controlo sobre a mensagem de erro específica exibida pela máquina cliente quando uma palavra-passe fraca é rejeitada.

## <a name="next-steps"></a>Passos seguintes

Para personalizar a lista de palavras-passe proibida para a sua organização, consulte Configurar a lista de [senhas proibida selada](tutorial-configure-custom-password-protection.md)por Código de Acesso AD .

Para monitorizar os eventos on-prem, consulte [Monitorização on-prem Azure Password Protection](howto-password-ban-bad-on-premises-monitor.md).
