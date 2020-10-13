---
title: Ativar no local Azure AD Password Protection
description: Saiba como ativar a proteção de senha ad Ad Azure para um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d72aa65f18896ebbecf651da32558059ba782cc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968400"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Ativar no local Azure Ative Directory Password Protection

Os utilizadores muitas vezes criam senhas que usam palavras locais comuns, como uma escola, uma equipa desportiva ou uma pessoa famosa. Estas palavras-passe são fáceis de adivinhar e fracas contra ataques baseados em dicionários. Para impor senhas fortes na sua organização, a Azure Ative Directory (Azure AD) Password Protection fornece uma lista de palavras-passe proibidas global e personalizada. Um pedido de alteração de palavra-passe falha se houver uma correspondência nesta lista de senhas proibidas.

Para proteger o ambiente de Serviços de Domínio de Diretório Ativo (AD DS) no local, pode instalar e configurar a Proteção de Passwords AD Ad Azure para trabalhar com o seu DC on-prem. Este artigo mostra-lhe como ativar a Proteção de PasswordS AZure AD para o seu ambiente no local.

Para obter mais informações sobre como a Proteção de Passwords AD AZure funciona num ambiente no local, consulte como impor a proteção de [senha azure AD para o Windows Server Ative Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra-lhe como ativar a Proteção de PasswordS AZure AD para o seu ambiente no local. Antes de concluir este artigo, [instale e registe o serviço de procuração de senhas Azure AD e agentes dc](howto-password-ban-bad-on-premises-deploy.md) no seu ambiente de DS AD no local.

## <a name="enable-on-premises-password-protection"></a>Permitir a proteção de senhas no local

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a **Azure Ative Directory**  >  **Security**  >  **Authentication Methods**Password  >  **protection**.
1. Desafine a opção de ativar a **proteção de palavras-passe no Windows Server Ative Directory** para *Sim*.

    Quando esta definição é definida como *Nº*, todos os agentes de proteção de passwords Azure AD entram num modo quiescente onde todas as palavras-passe são aceites como está. Não são realizadas atividades de validação e os eventos de auditoria não são gerados.

1. Recomenda-se inicialmente definir o **Modo** de *Auditoria.* Depois de se sentir confortável com a funcionalidade e o impacto nos utilizadores da sua organização, pode mudar o **Modo** para *Aplicado*. Para obter mais informações, consulte a seguinte secção sobre [os modos de funcionamento](#modes-of-operation).
1. Quando estiver pronto, **selecione Guardar**.

    [![Ativar a proteção de senhas no local ao abrigo de Métodos de Autenticação no portal Azure](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modos de funcionamento

Quando ativar no local Azure AD Password Protection, pode utilizar o modo *de auditoria* ou o modo *de execução.* Recomendamos que a implementação e os testes iniciais comecem sempre no modo de auditoria. As entradas no registo de eventos devem então ser monitorizadas para antecipar se quaisquer processos operacionais existentes serão perturbados assim que o modo *Enforce* estiver ativado.

### <a name="audit-mode"></a>Modo de auditoria

*O* modo de auditoria destina-se a executar o software num modo "e se". Cada serviço de agente de proteção de passwords Azure AD avalia uma palavra-passe de acordo com a política atualmente ativa.

Se a política atual estiver configurada para estar em modo de auditoria, as palavras-passe "más" resultam em mensagens de registo de eventos, mas são processadas e atualizadas. Este comportamento é a única diferença entre o modo de auditoria e de execução. Todas as outras operações são iguais.

### <a name="enforced-mode"></a>Modo Forçado

*O* modo forçado destina-se à configuração final. Como quando em modo de auditoria, cada serviço de agente de proteção de senhas Azure AD password avalia as palavras-passe de entrada de acordo com a política atualmente ativa. No entanto, quando o modo forçado é ativado, uma palavra-passe considerada insegura de acordo com a política é rejeitada.

Quando uma palavra-passe é rejeitada em modo imposto pelo agente Azure AD Password Protection DC, um utilizador final vê um erro semelhante ao que veria se a sua palavra-passe foi rejeitada pela tradicional aplicação da complexidade da palavra-passe no local. Por exemplo, um utilizador pode ver a seguinte mensagem de erro tradicional no início de sé do Windows ou alterar o ecrã de palavra-passe:

*"Incapaz de atualizar a senha. O valor previsto para a nova palavra-passe não corresponde aos requisitos de comprimento, complexidade ou histórico do domínio."*

Esta mensagem é apenas um exemplo de vários resultados possíveis. A mensagem de erro específica pode variar dependendo do software ou cenário real que está a tentar definir uma palavra-passe insegura.

Os utilizadores finais afetados podem ter de trabalhar com o seu pessoal de TI para compreender os novos requisitos e escolher senhas seguras.

> [!NOTE]
> O Azure AD Password Protection não tem controlo sobre a mensagem de erro específica exibida pela máquina do cliente quando uma palavra-passe fraca é rejeitada.

## <a name="next-steps"></a>Passos seguintes

Para personalizar a lista de palavras-passe proibidas para a sua organização, consulte [a lista de senhas proibidas a Azure AD Password Protection](tutorial-configure-custom-password-protection.md).

To monitor on-prem events, see [Monitoring on-prem Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
