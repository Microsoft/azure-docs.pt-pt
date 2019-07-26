---
title: Quando e como usar um provedor de autenticação multifator do Azure? -Azure Active Directory
description: Quando você deve usar um provedor de autenticação com o Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399fccf9aaaeb9e252527e80a6549ee286bb1898
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369363"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usar um provedor de autenticação multifator do Azure

A verificação de dois passos está disponível por predefinição para os administradores globais que tenham utilizadores do Azure Active Directory e do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](howto-mfa-mfasettings.md), deverá comprar a versão completa do Multi-Factor Authentication (MFA) do Azure.

Um provedor de autenticação multifator do Azure é usado para aproveitar os recursos fornecidos pela autenticação multifator do Azure para usuários que **não têm licenças**.

> [!NOTE]
> A partir de 1º de setembro, 2018 novos provedores de autenticação podem não ser mais criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados, mas a migração não é mais possível. A autenticação multifator continuará disponível como um recurso em licenças Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>ADVERTÊNCIAS relacionadas ao SDK do Azure MFA

Observe que o SDK foi preterido e só continuará a funcionar até 14 de novembro de 2018. Após esse tempo, as chamadas para o SDK irão falhar.

## <a name="what-is-an-mfa-provider"></a>O que é um Fornecedor de MFA?

Há dois tipos de provedores de autenticação, e a distinção está em relação a como sua assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas num mês relativamente ao seu inquilino. Esta é a melhor opção no caso de existirem vários utilizadores que são ocasionalmente autenticados. A opção por utilizador calcula o número de indivíduos no seu inquilino que executam a verificação de dois passos num mês. Esta é a melhor opção caso existam alguns utilizadores com licenças, mas tem de alargar a MFA a mais utilizadores para além dos limites da sua licença.

## <a name="manage-your-mfa-provider"></a>Gerir o Fornecedor de MFA

Não pode alterar o modelo de utilização (por utilizador ativado ou por autenticação) após a criação de um fornecedor do MFA.

Se você comprou licenças suficientes para cobrir todos os usuários habilitados para MFA, você pode excluir o provedor MFA completamente.

Se o fornecedor do MFA não estiver associado a um inquilino do Azure AD ou associar o novo fornecedor do MFA a um inquilino diferente do Azure AD, as definições do utilizador e as opções de configuração não são transferidas. Além disso, os servidores do Azure MFA existentes precisam ser reativados usando as credenciais de ativação geradas por meio do provedor de MFA. A reativação dos servidores MFA para vinculá-los ao provedor MFA não afeta a chamada telefônica e a autenticação de mensagens de texto, mas as notificações de aplicativo móvel param de funcionar para todos os usuários até que eles reativem o aplicativo móvel.

### <a name="removing-an-authentication-provider"></a>Removendo um provedor de autenticação

> [!CAUTION]
> Não há nenhuma confirmação ao excluir um provedor de autenticação. Selecionar **excluir** é um processo permanente.

Provedores de autenticação podem ser encontrados no **portal do Azure** > **Azure Active Directory** > **provedores**de**MFA** > . Clique em provedores listados para ver detalhes e configurações associadas a esse provedor.

Antes de remover um provedor de autenticação, anote todas as configurações personalizadas definidas em seu provedor. Decida quais configurações precisam ser migradas para as configurações gerais do MFA de seu provedor e conclua a migração dessas configurações. 

Os servidores do Azure MFA vinculados aos provedores precisarão ser reativados usando as credenciais geradas em **portal do Azure** > **Azure Active Directory** > **configurações do servidor** **MFA** > . Antes de reativar, os seguintes arquivos devem ser excluídos do `\Program Files\Multi-Factor Authentication Server\Data\` diretório nos servidores do Azure MFA em seu ambiente:

- cacerts
- certificado
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Excluir um provedor de autenticação da portal do Azure](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Depois de confirmar que todas as configurações foram migradas, você pode navegar até o **portal do Azure** > **Azure Active Directory** > **provedores** de**MFA** > e selecionar as reticências **...** e selecione **excluir**.

> [!WARNING]
> A exclusão de um provedor de autenticação excluirá todas as informações de relatório associadas a esse provedor. Talvez você queira salvar os relatórios de atividade antes de excluir seu provedor.

> [!NOTE]
> Os usuários com versões mais antigas do aplicativo Microsoft Authenticator e do servidor MFA do Azure podem precisar registrar novamente seu aplicativo.

## <a name="next-steps"></a>Passos Seguintes

[Configurar as Definições do Multi-Factor Authentication](howto-mfa-mfasettings.md)
