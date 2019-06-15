---
title: Introdução ao registo combinado para o Azure AD SSPR e o multi-factor Authentication (pré-visualização) - Azure Active Directory
description: Ativar combinados AD multi-factor Authentication do Azure e o registo (pré-visualização) de reposição de palavra-passe self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d006bd36cc8f8c84fb13bae43702a3e472f8876a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113298"
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
> Depois de ativar o registo de combinado, os utilizadores que registar ou confirmar o número de telefone ou aplicação móvel através da nova experiência pode usá-los a para o multi-factor Authentication e SSPR, se esses métodos estão ativados no multi-factor Authentication e SSPR políticas. Se, em seguida, desativa essa experiência, os utilizadores que vão para o registo SSPR anterior página em `https://aka.ms/ssprsetup` terão de efetuar a autenticação multifator para poderem aceder a página.

Se tiver configurado o Site para a lista de atribuições de zona do Internet Explorer, os seguintes sites precisam ser na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para o registo combinado

Proteger quando e como registar-se os utilizadores do Azure multi-factor Authentication e reposição de palavra-passe self-service agora é possível com ações do usuário em política de acesso condicional. Esta funcionalidade de pré-visualização está disponível para organizações que tem ativado o [combinados pré-visualização do registo](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações em que eles querem que os utilizadores para se registar para SSPR a partir de uma localização central como uma localização de rede fidedigna e o Azure multi-factor Authentication durante a integração de RH. Para obter mais informações sobre a criação de locais confiáveis no acesso condicional, consulte o artigo [o que é a condição de localização no Azure Active Directory condicional acesso?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local confiável

A seguinte política se aplica a todos os utilizadores selecionados, o que tentarem registar-se com a experiência de registo combinado e bloqueia o acesso, a menos que se está a ligar de um local marcado como rede fidedigna.

![Criar uma política de acesso condicional para controlar o registo de informações de segurança](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Na **portal do Azure**, navegue até **Azure Active Directory** > **acesso condicional**
1. Selecione **Nova política**
1. No nome, introduza um nome para esta política. Por exemplo, **combinados registo de informações de segurança em redes fidedignas**
1. Sob **atribuições**, clique em **utilizadores e grupos**e selecione os utilizadores e grupos aos quais pretende aplicar a esta política

   > [!WARNING]
   > Os usuários devem ser habilitados para o [combinados pré-visualização do registo](../authentication/howto-registration-mfa-sspr-combined.md).

1. Sob **aplicações ou ações da Cloud**, selecione **ações do usuário**, verifique **registe informações de segurança (pré-visualização)**
1. Sob **condições** > **localizações**
   1. Configurar **Sim**
   1. Incluir **qualquer localização**
   1. Excluir **todas as localizações fidedignas**
   1. Clique em **feito** no painel de localizações
   1. Clique em **feito** no painel de condições
1. Sob **controlos de acesso** > **concessão**
   1. Clique em **bloquear o acesso**
   1. Em seguida, clique em **selecione**
1. Definir **ativar política** para **no**
1. Em seguida, clique em **Create**

## <a name="next-steps"></a>Passos Seguintes

[Métodos disponíveis para o multi-factor Authentication e SSPR](concept-authentication-methods.md)

[Configurar a reposição de palavra-passe self-service](howto-sspr-deployment.md)

[Configurar a autenticação Multifator do Azure](howto-mfa-getstarted.md)

[Resolução de problemas de combinar o registo de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)

[O que é a condição de localização no Azure Active Directory condicional acesso?](../conditional-access/location-condition.md)
