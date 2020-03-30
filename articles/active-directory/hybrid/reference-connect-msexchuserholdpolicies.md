---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies / Microsoft Docs'
description: Este tópico descreve comportamento de atributo dos atributos msExchUserHoldPolicies e cloudMsExchUserHoldPolicies atributos
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213080"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
O seguinte documento de referência descreve estes atributos utilizados pela Exchange e a forma adequada de editar as regras de sincronização predefinidas.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>O que são msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Existem dois tipos de [detenção](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) disponíveis para um Servidor de Câmbio: Hold de Contencioso e Hold In-Place. Quando o Litigation Hold estiver ativado, todos os artigos são colocados em espera.  Um In-Place Hold é usado para preservar apenas os itens que cumprem os critérios de uma consulta de pesquisa que definiu utilizando a ferramenta In-Place eDiscovery.

Os atributos MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies permitem que a AD e a AD Azure determinem quais os utilizadores que estão sob um porão, dependendo se estão a usar o Exchange ou o Exchange on-line no local.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies fluxo de sincronização
Por padrão, msExchUserHoldPolcies é sincronizado por Azure AD Connect diretamente ao atributo msExchUserHoldPolicies no metaverso e, em seguida, ao atributo msExchUserHoldPolices em Azure AD

As tabelas que se seguem descrevem o fluxo:

Entrada a partir do diretório ativo no local:

|Atributo de Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory no local|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|Em AD - Permuta de Utilizadores|

Saída para Azure AD:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Fora para AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies fluxo de sincronização
Por predefinição cloudMsExchUserHoldPolicies é sincronizado por Azure AD Connect diretamente para o atributo cloudMsExchUserHoldPolicies no metaverso. Em seguida, se msExchUserHoldPolices não for nulo no metaverso, o atributo fluiu para o Diretório Ativo.

As tabelas que se seguem descrevem o fluxo:

Entrada a partir de Azure AD:

|Atributo de Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory no local|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|Em AAD - Permuta de Utilizadores|

Saída para o diretório ativo no local:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|SE (NÃO NULO)|msExchUserHoldPolicies|Fora para AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informação sobre o comportamento do atributo
O msExchangeUserHoldPolicies é um atributo de autoridade única.  Um único atributo de autoridade pode ser definido sobre um objeto (neste caso, objeto de utilizador) no diretório no local ou no diretório da nuvem.  As regras de início de autoridade ditam que, se o atributo for sincronizado a partir do local, então a Azure AD não será autorizada a atualizar este atributo.

Para permitir que os utilizadores estabeleçam uma política de espera sobre um objeto de utilizador na nuvem, o atributo cloudMSExchangeUserHoldPolicies é usado. Este atributo é usado porque o Azure AD não pode definir msExchangeUserHoldPolicies diretamente com base nas regras acima explicadas.  Este atributo irá então sincronizar de volta ao diretório no local se, o msExchangeUserHoldPolicies não for nulo e substituir o valor atual das MsExchangeUserHoldPolicies.

Em determinadas circunstâncias, por exemplo, se ambos fossem alterados no local e em Azure ao mesmo tempo, isso poderia causar alguns problemas.  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
