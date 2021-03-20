---
title: Evitar a aceleração automática de inscrição no Azure AD utilizando a política home realm Discovery
description: Saiba como prevenir domain_hint auto-aceleração para iDPs federados.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589383"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Desative a aceleração automática para um IDP federado durante o início de sação do utilizador com a política home realm Discovery

[Home Realm Discovery Policy](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) oferece aos administradores várias formas de controlar como e onde os seus utilizadores autenticam. A `domainHintPolicy` secção da política de HRD é usada para ajudar a migrar utilizadores federados para cloud credenciais geridas como o [FIDO,](../authentication/howto-authentication-passwordless-security-key.md)garantindo que visitam sempre a página de inscrição AD Azure e não são automaticamente acelerados para um IDP federado por causa de dicas de domínio.

Esta política é necessária em situações em que as aplicações que um administrador não pode controlar ou atualizar adicionar dicas de domínio durante a entrada.  Por exemplo, `outlook.com/contoso.com` envia o utilizador para uma página de login com o parâmetro `&domain_hint=contoso.com` anexado, de modo a acelerar automaticamente o utilizador diretamente para o IDP federado para o `contoso.com` domínio. Os utilizadores com credenciais geridas enviadas para um IDP federado não podem iniciar sedus usando as suas credenciais geridas, reduzindo a segurança e frustrando os utilizadores com experiências de inscrição aleatórias. Os administradores que estão a lançar credenciais geridas [também devem criar esta política](#suggested-use-within-a-tenant) para garantir que os utilizadores possam utilizar sempre as suas credenciais geridas.

## <a name="domainhintpolicy-details"></a>Detalhes da Política de Domínio

A secção DomainHintPolicy da política de HRD é um objeto JSON, que permite que um administrador opte por certos domínios e aplicações a partir da utilização de sugestões de domínio.  Funcionalmente, isto diz à página de login AZure para se comportar como se um `domain_hint` parâmetro no pedido de login não estivesse presente.

### <a name="the-respect-and-ignore-policy-sections"></a>As secções políticas de Respeito e Ignorar

|Section | Significado | Valores |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Se esta dica de domínio for enviada no pedido, ignore-a. |Matriz de endereços de domínio (por `contoso.com` exemplo). Também apoia `all_domains`|
|`RespectDomainHintForDomains`| Se esta dica de domínio for enviada no pedido, respeite-a mesmo `IgnoreDomainHintForApps` que indique que a aplicação no pedido não deve acelerar automaticamente. Isto é usado para retardar o lançamento de sugestões de domínio depreciativas dentro da sua rede – pode indicar que alguns domínios ainda devem ser acelerados. | Matriz de endereços de domínio (por `contoso.com` exemplo). Também apoia `all_domains`|
|`IgnoreDomainHintForApps`| Se um pedido desta aplicação vier com uma dica de domínio, ignore-a. | Matriz de IDs de aplicação (GUIDs). Também apoia `all_apps`|
|`RespectDomainHintForApps` |Se um pedido desta aplicação vier com uma sugestão de domínio, respeite-a mesmo `IgnoreDomainHintForDomains` que inclua esse domínio. Usado para garantir que algumas aplicações continuam a funcionar se descobrir que quebram sem pistas de domínio. | Matriz de IDs de aplicação (GUIDs). Também apoia `all_apps`|

### <a name="policy-evaluation"></a>Avaliação de políticas

A lógica DomainHintPolicy funciona em cada pedido de entrada que contém uma sugestão de domínio e acelera com base em duas peças de dados no pedido – o domínio na dica de domínio, e o ID do cliente (a app). Em resumo - "Respeito" por um domínio ou aplicação tem precedência sobre uma instrução para "Ignorar" uma dica de domínio para um determinado domínio ou aplicação.

1. Na ausência de qualquer política de sugestão de domínio, ou se nenhuma das 4 secções referenciar a aplicação ou sugestão de domínio mencionada, [o resto da política de HRD será avaliado](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Se uma (ou ambas) de `RespectDomainHintForApps` ou `RespectDomainHintForDomains` seção incluir a app ou o domínio no pedido, então o utilizador será automaticamente acelerado para o IDP federado conforme solicitado.
1. Se uma (ou ambas) `IgnoreDomainHintsForApps` ou `IgnoreDomainHintsForDomains` referências da app ou do domínio sugerirem no pedido, e não forem referenciadas pelas secções "Respeito", então o pedido não será acelerado automaticamente, e o utilizador permanecerá na página de login AZure AD para fornecer um nome de utilizador.

Uma vez que um utilizador tenha introduzido um nome de utilizador na página de login, pode utilizar as suas credenciais geridas, se tiver algum registo.  Se optarem por não utilizar uma credencial gerida, ou não tiverem nenhuma registada, serão levados para o seu IDP federado para entrada de credenciais, como de costume.

## <a name="suggested-use-within-a-tenant"></a>Uso sugerido dentro de um inquilino

Os administradores de domínios federados devem criar esta secção da política de RHD num plano em quatro fases. O objetivo deste plano é, eventualmente, ter todos os utilizadores de um inquilino a ter a oportunidade de usar as suas credenciais geridas independentemente do domínio ou aplicação, salvar as aplicações que têm dependências difíceis de `domain_hint` uso.  Este plano ajuda os administradores a encontrar essas aplicações, isentando-as da nova política, e continuando a fazer a mudança para o resto do inquilino.

1. Escolha um domínio para inicialmente lançar esta alteração para fora.  Este será o seu domínio de teste, por isso escolha um que possa ser mais recetivo às alterações no UX (ou seja, ver uma página de login diferente).  Isto ignorará todas as dicas de domínio de todas as aplicações que utilizam este nome de domínio. [Desafine](#configuring-policy-through-graph-explorer) esta política na sua política de HRD padrão do inquilino:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Recolha feedback dos utilizadores do domínio de teste. Recolher detalhes para aplicações que se partiram como resultado desta mudança - eles têm uma dependência do uso de sugestões de domínio, e devem ser atualizados. Por enquanto, adicione-os à `RespectDomainHintForApps` secção:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Continue a expandir o lançamento da política para novos domínios, recolhendo mais feedback.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Complete o seu lançamento - direcione todos os domínios, isentando os que devem continuar a ser acelerados:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Após o passo 4 estar completo, todos os utilizadores, exceto os que `guestHandlingDomain.com` estão em , podem iniciar sação na página de início de súpido AD, mesmo quando as indicações de domínio causariam uma aceleração automática a um IDP federado.  A exceção a isso é se a aplicação que solicita o início de sing é uma das isentas - para essas aplicações, todas as dicas de domínio ainda serão aceites.

## <a name="configuring-policy-through-graph-explorer"></a>Política de configuração através do Graph Explorer

Desagram a [política de HRD](/graph/api/resources/homeRealmDiscoveryPolicy) como de costume, utilizando o Microsoft Graph.  

1. Conceder a permissão de Configuração de Aplicações.ReadWrite.ApplicationConfiguration no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. Use o URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Publique a nova política neste URL, ou PATCH para `/policies/homerealmdiscoveryPolicies/{policyID}` ela se escrever excessivamente uma existente.

Conteúdo sonoro ou PATCH:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Certifique-se de que utiliza cortes para escapar à secção JSON quando utilizar o `Definition` Gráfico.  

`isOrganizationDefault` deve ser verdade, mas o nome e a definição do display podem mudar.

## <a name="next-steps"></a>Passos seguintes

* [Ativar o sing-in da chave de segurança sem palavras-passe](../authentication/howto-authentication-passwordless-security-key.md)
* [Ativar o acesso sem palavras-passe com a aplicação Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)