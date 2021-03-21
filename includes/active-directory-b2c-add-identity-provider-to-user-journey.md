---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674242"
---
## <a name="add-a-user-journey"></a>Adicione uma viagem de utilizador 

Neste momento, o fornecedor de identidade foi criado, mas ainda não está disponível em nenhuma das páginas de inscrição. Se não tiver a sua própria viagem personalizada de utilizador, crie uma duplicação de uma viagem de utilizador de modelo existente, caso contrário continue até ao passo seguinte. 

1. Abra o ficheiro *TrustFrameworkBase.xml* do pacote de arranque.
1. Encontre e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"` .
1. Abra a *TrustFrameworkExtensions.xml* e encontre o elemento **UserJourneys.** Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que copiou em criança do elemento **UserJourneys.**
1. Mude o nome da id da viagem de utilizador. Por exemplo, `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Adicione o fornecedor de identidade a uma viagem de utilizador 

Agora que tem uma viagem de utilizador, adicione o novo fornecedor de identidade à viagem do utilizador. Primeiro adicione um botão de início de sôr-in e, em seguida, ligue o botão a uma ação. A ação é o perfil técnico que criaste anteriormente.

1. Encontre o elemento do passo de orquestração que `Type="CombinedSignInAndSignUp"` inclui, ou `Type="ClaimsProviderSelection"` na viagem do utilizador. Normalmente é o primeiro passo de orquestração. O elemento **ClaimsProviderSelections** contém uma lista de fornecedores de identidade com os que um utilizador pode iniciar súm. A ordem dos elementos controla a ordem dos botões de inscrição apresentados ao utilizador. Adicione um elemento **XML de Seleção de Reivindicações.** Desaprote o valor do **TargetClaimsExchangeId** para um nome amigável.

1. No próximo passo de orquestração, adicione um elemento **ClaimsExchange.** Defina o **Id** para o valor da troca de reclamações alvo Id. Atualize o valor do **TechnicalProfileReferenceD** para o ID do perfil técnico que criou anteriormente.

O seguinte XML demonstra os dois primeiros passos de orquestração de uma viagem de utilizador com o fornecedor de identidade: