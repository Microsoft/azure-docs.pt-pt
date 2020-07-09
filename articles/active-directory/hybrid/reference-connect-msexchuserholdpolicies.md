---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies Microsoft Docs'
description: Este tópico descreve o comportamento do atributo dos atributos msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74213080"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
O documento de referência que se segue descreve estes atributos utilizados pela Exchange e a forma correta de editar as regras de sincronização padrão.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>O que são msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Existem dois tipos de [porões](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) disponíveis para um Servidor de Câmbio: Hold de Litígios e Hold In-Place. Quando o Hold de Litígios está ativado, todas as caixas de correio são colocadas em espera.  Um Hold In-Place é utilizado para preservar apenas os itens que satisfaçam os critérios de uma consulta de pesquisa que definiu utilizando a ferramenta In-Place eDiscovery.

Os atributos MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies permitem que a AD e a Azure AD no local determinem quais os utilizadores que estão sob um porão, dependendo se estão a utilizar no local Exchange ou Exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies flow de sincronização
Por predefinição, a MsExchUserHoldPolcies é sincronizada pela Azure AD Connect diretamente ao atributo msExchUserHoldPolicies no metaverso e, em seguida, ao atributo msExchUserHoldPolices em Azure AD

As tabelas que se seguem descrevem o fluxo:

A caminho do Diretório Ativo:

|Atributo de Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de Sincronização|
|-----|-----|-----|-----|-----|
|Active Directory no local|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|In from AD - User Exchange|

Saída para Azure AD:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de Sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies synchronization flow
Por nuvem predefinidaMExchUserHoldPolicies é sincronizado pela Azure AD Connect diretamente para o atributo cloudMsExchUserHoldPolicies no metaverso. Então, se a MSExchUserHoldPolices não for nula no metaverso, o atributo fluiu para o Ative Directory.

As tabelas que se seguem descrevem o fluxo:

A caminho de Azure AD:

|Atributo de Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de Sincronização|
|-----|-----|-----|-----|-----|
|Active Directory no local|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In from AAD - User Exchange|

Saída para o Ative Directory:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de Sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|SE (NÃO NULO)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informação sobre o comportamento do atributo
O msExchangeUserHoldPolicies é um atributo de autoridade única.  Um único atributo de autoridade pode ser colocado num objeto (neste caso, objeto de utilizador) no diretório no local ou no diretório de nuvens.  As regras do Início da Autoridade ditam que, se o atributo for sincronizado a partir do local, então a Azure AD não será autorizada a atualizar este atributo.

Para permitir que os utilizadores estabeleçam uma política de espera num objeto de utilizador na nuvem, é utilizado o atributo cloudMSExchangeUserHoldPolicies. Este atributo é usado porque a Azure AD não pode definir msExchangeUserHoldPolicies diretamente com base nas regras acima explicadas.  Este atributo sincronizará então para o diretório no local se, o msExchangeUserHoldPolicies não for nulo e substituir o valor atual da MSExchangeUserHoldPolicies.

Em determinadas circunstâncias, por exemplo, se ambas fossem alteradas no local e em Azure ao mesmo tempo, isso poderia causar alguns problemas.  

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
