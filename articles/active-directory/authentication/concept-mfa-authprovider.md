---
title: Quando e como utilizar um fornecedor do multi-factor Auth do Azure? - Azure Active Directory
description: Quando deve utilizar um fornecedor de autenticação com a MFA do Azure?
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
ms.openlocfilehash: dfababeae15ee18a140042d9a6ca10be40e41339
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310647"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando utilizar um fornecedor de autenticação do multi-factor do Azure

A verificação de dois passos está disponível por predefinição para os administradores globais que tenham utilizadores do Azure Active Directory e do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](howto-mfa-mfasettings.md), deverá comprar a versão completa do Multi-Factor Authentication (MFA) do Azure.

Um fornecedor do multi-factor Auth do Azure é utilizado para tirar partido das funcionalidades fornecidas pelo Azure multi-factor Authentication para os utilizadores quem **não têm licenças**.

Se tiver licenças que abrangem todos os utilizadores na sua organização, em seguida, não terá um fornecedor do multi-factor Auth do Azure. Crie um fornecedor de autenticação do multi-factor do Azure apenas se também tem de fornecer a verificação de dois passos para alguns usuários que não têm licenças.

> [!NOTE]
> Em vigor a partir de 1 de Setembro de 2018 novos auth fornecedores já não podem ser criados. Fornecedores de autenticação existentes podem continuar a ser utilizado e atualizados. Multi-factor authentication irá continuar a estar disponível como uma funcionalidade no licenças do Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Avisos relacionados com o SDK de MFA do Azure

Tenha em atenção que o SDK foi preterido e só irá continuar a funcionar até 14 de Novembro de 2018. Após esse tempo, as chamadas para o SDK irão falhar.

## <a name="what-is-an-mfa-provider"></a>O que é um Fornecedor de MFA?

Existem dois tipos de fornecedores de autenticação, e a diferença em torno de como a sua subscrição do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas num mês relativamente ao seu inquilino. Esta é a melhor opção no caso de existirem vários utilizadores que são ocasionalmente autenticados. A opção por utilizador calcula o número de indivíduos no seu inquilino que executam a verificação de dois passos num mês. Esta é a melhor opção caso existam alguns utilizadores com licenças, mas tem de alargar a MFA a mais utilizadores para além dos limites da sua licença.

## <a name="manage-your-mfa-provider"></a>Gerir o Fornecedor de MFA

Não pode alterar o modelo de utilização (por utilizador ativado ou por autenticação) após a criação de um fornecedor do MFA.

Se tiver adquirido licenças suficientes para cobrir todos os utilizadores que estão ativados para a MFA, pode eliminar completamente o fornecedor do MFA.

Se o fornecedor do MFA não estiver associado a um inquilino do Azure AD ou associar o novo fornecedor do MFA a um inquilino diferente do Azure AD, as definições do utilizador e as opções de configuração não são transferidas. Além disso, os servidores de MFA do Azure existentes têm de ser reativados com credenciais de ativação geradas através do fornecedor de MFA. Reativar os servidores MFA para associá-los para o fornecedor do MFA não afeta chamadas telefónicas e de autenticação de mensagem de texto, mas as notificações da aplicação móvel deixam de funcionar para todos os utilizadores até que reativem a aplicação móvel.

## <a name="next-steps"></a>Passos Seguintes

[Configurar as Definições do Multi-Factor Authentication](howto-mfa-mfasettings.md)
