---
title: Como proibir o uso de palavras-passe no Azure AD
description: Banir senhas fracas de sua envirionment com passwrords do Azure AD banido dinamicamente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef8f66cda7e80ffdafefbbac5de24d45f01401af
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286935"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurar a lista de palavras-passe banidas personalizado

Muitas organizações consideram que aos utilizadores criarem palavras-passe utilizando palavras locais comuns, como uma instituição de ensino, equipe de esporte ou pessoas famosas, deixando-as fáceis de adivinhar. Lista de palavras-passe banidas personalizado da Microsoft permite que as organizações de acrescentar cadeias de caracteres para avaliar e bloquear, além de global banida a lista de palavra-passe, quando os utilizadores e administradores tentam alterar ou repor uma palavra-passe.

## <a name="add-to-the-custom-list"></a>Adicionar à lista personalizada

Configurar a lista de palavras-passe banidas personalizado requer uma licença do Azure Active Directory Premium P1 ou P2. Para obter mais informações sobre o licenciamento do Azure Active Directory, consulte a [Azure Active Directory, página de preços](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory**, **métodos de autenticação**, em seguida, **proteção por senha**.
1. Definir a opção **lista personalizada de impor**ao **Sim**.
1. Acrescentar cadeias de caracteres para o **personalizado banida lista de palavra-passe**, uma cadeia de caracteres por linha
   * A lista de palavras-passe banidas personalizado pode conter até 1000 palavras.
   * A lista de palavras-passe banidas personalizado diferencia maiúsculas de minúsculas.
   * A lista de palavras-passe banidas personalizado considera a substituição de caracteres comuns.
      * Exemplo: "o" e "0" ou "a" e "\@"
   * O comprimento da cadeia mínimo é de quatro caracteres e o máximo é de 16 carateres.
1. Quando adicionar todas as cadeias de caracteres, clique em **guardar**.

> [!NOTE]
> Pode demorar várias horas para atualizações para a lista de palavras-passe banidas personalizadas a serem aplicadas.

![Modificar a lista de palavras-passe banidas personalizadas em métodos de autenticação no portal do Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Como funciona

Sempre que um utilizador ou administrador repõe ou uma palavra-passe do Azure AD, é alterado, flui através de listas de palavra-passe banidas para confirmar que não está numa lista. Esta verificação está incluída nas palavras-passe definiu ou alterou a utilizar o Azure AD.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um utilizador tenta repor uma palavra-passe para algo que deve ser excluído, verão a seguinte mensagem de erro:

Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que faz com que a palavra-passe guessable facilmente. Tente novamente com uma palavra-passe diferente.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral conceptual das listas de palavra-passe banidas](concept-password-ban-bad.md)

[Descrição geral conceptual de proteção de palavra-passe do Azure AD](concept-password-ban-bad-on-premises.md)

[Ativar a integração no local com as listas de palavra-passe banidas](howto-password-ban-bad-on-premises.md)
