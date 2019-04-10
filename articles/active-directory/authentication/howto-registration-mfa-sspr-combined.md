---
title: Introdução ao registo combinado para o Azure AD SSPR e o multi-factor Authentication (pré-visualização) - Azure Active Directory
description: Ativar combinados AD multi-factor Authentication do Azure e o registo (pré-visualização) de reposição de palavra-passe self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280575"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registo de informações de segurança para ativar combinado (pré-visualização)

Antes de ativar a nova experiência, consulte o artigo [combinado de registo de informações de segurança (pré-visualização)](concept-registration-mfa-sspr-combined.md) para garantir que compreende a funcionalidade e os efeitos desta funcionalidade.

![Experiência aprimorada de registo de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registo de informações de segurança combinada para o Azure multi-factor Authentication e reposição de palavra-passe self-service do Azure Active Directory (Azure AD) é uma funcionalidade de pré-visualização pública do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Conclua estes passos para ativar o registo combinado:

1. Inicie sessão no portal do Azure como um administrador de utilizador ou administrador global.
2. Aceda a **do Azure Active Directory** > **definições de utilizador** > **gerir as definições para as funcionalidades de pré-visualização do painel de acesso**.
3. Sob **os utilizadores podem utilizar funcionalidades para registar e gerir informações de segurança de pré-visualização - atualizar**, optar por ativar para um **selecionados** grupo de utilizadores ou para **todos os** utilizadores.

   ![Ativar a experiência de pré-visualização de informações de segurança combinado para todos os utilizadores](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partir de Março de 2019, as opções de chamada telefónica não estará disponíveis para o multi-factor Authentication e os utilizadores SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. As opções de chamada telefónica continuarão a estar disponíveis para os utilizadores pagos inquilinos do Azure AD.

> [!NOTE]
> Depois de ativar o registo de combinado, os utilizadores que registar ou confirmar o número de telefone ou aplicação móvel através da nova experiência pode usá-los a para o multi-factor Authentication e SSPR, se esses métodos estão ativados no multi-factor Authentication e SSPR políticas. Se, em seguida, desativa essa experiência, os utilizadores que vão para o registo SSPR anterior página em `https:/aka.ms/ssprsetup` terão de efetuar a autenticação multifator para poderem aceder a página.

Se tiver configurado o Site para a lista de atribuições de zona do Internet Explorer, os seguintes sites precisam ser na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Passos Seguintes

[Métodos disponíveis para o multi-factor Authentication e SSPR](concept-authentication-methods.md)

[Configurar a reposição personalizada de palavra-passe](howto-sspr-deployment.md)

[Configurar o Multi-Factor Authentication do Azure](howto-mfa-getstarted.md)

[Resolução de problemas de combinar o registo de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)