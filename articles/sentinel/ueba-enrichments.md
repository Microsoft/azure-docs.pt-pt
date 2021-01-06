---
title: Referência de enriquecimentos Azure Sentinel UEBA Microsoft Docs
description: Este artigo exibe os enriquecimentos de entidades gerados pela análise de comportamento da entidade de Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901790"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Referência de enriquecimentos Azure Sentinel UEBA

Estas tabelas listam e descrevem enriquecimentos de entidades que podem ser usados para focar e afiar a sua investigação de incidentes de segurança.

As duas primeiras tabelas, **insights do utilizador** e insights do **dispositivo,** contêm informações de entidades de Fontes de Ative Directory / Azure AD e Microsoft Threat Intelligence.

<a name="baseline-explained"></a>O resto das tabelas, em **tabelas de insights de atividade,** contêm informações de entidades baseadas nos perfis comportamentais construídos pela entidade de Azure Sentinel analítica de comportamento. As atividades são analisadas com base que é compilada dinamicamente cada vez que é usada. Cada atividade tem o seu período de retrocesso definido a partir do qual esta linha de base dinâmica é derivada. Este período é especificado na coluna [**Baseline**](#activity-insights-tables) nesta tabela.

> [!NOTE] 
> O campo **de nomes de enriquecimento** nas três tabelas exibe duas linhas de informação. O primeiro, em **negrito,** é o "nome amigável" do enriquecimento. O segundo *(em itálico e parênteses)* é o nome de campo do enriquecimento armazenado na [**tabela Behavior Analytics**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tabela de insights do utilizador

| Nome do enriquecimento | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Nome da exibição da conta**<br>*(Nome de Contas)* | O nome de exibição da conta do utilizador. | Administrador, Hayden Cook |
| **Domínio da conta**<br>*(AccountDomain)* | O nome de domínio da conta do utilizador. |  |
| **ID de objeto de conta**<br>*(ContaObjectID)* | O iD do objeto de conta do utilizador. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Raio de explosão**<br>*(BlastRadius)* | O raio de explosão é calculado com base em vários fatores: a posição do utilizador na árvore org e as funções e permissões do Azure Ative Directory do utilizador. | Baixo, Médio, Alto |
| **É uma conta dormente**<br>*(IsDormantAccount)* | A conta não foi usada nos últimos 180 dias. | Verdade, Falso |
| **É administrador local**<br>*(IsLocalAdmin)* | A conta tem privilégios de administrador local. | Verdade, Falso |
| **É uma nova conta**<br>*(IsNewAccount)* | A conta foi criada nos últimos 30 dias. | Verdade, Falso |
| **Nas instalações SID**<br>*(OnPremisesSID)* | O SID no local do utilizador relacionado com a ação. | S-1-5-21-1112946627-1321165628-243734228-1103 |
|

## <a name="device-insights-table"></a>Tabela de insights do dispositivo

| Nome do enriquecimento | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Browser**<br>*(Browser)* | O navegador usado na ação. | Edge |
| **Família de dispositivos**<br>*(DispositivoFamily)* | A família do dispositivo usado na ação. | Windows |
| **Tipo de Dispositivo**<br>*(DeviceType)* | O tipo de dispositivo cliente usado na ação | Ambiente de trabalho |
| **ISP**<br>*(ISP)* | O fornecedor de serviços de internet usado na ação. |  |
| **Sistema operativo**<br>*(Sistema Operativo)* | O sistema operativo utilizado na ação. | Windows 10 |
| **Descrição do indicador de informação de ameaça**<br>*(Descrição do Indicator ThreatIntel)* | Descrição do indicador de ameaça observado resolvido a partir do endereço IP utilizado na ação. | Host é membro da botnet: azorult |
| **Tipo de indicador de informação de ameaça**<br>*(ThreatIntelIndicatorType)* | O tipo de indicador de ameaça resolvido a partir do endereço IP utilizado na ação. | Botnet, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, Malware, Phishing, Proxy, PUA, Watchlist |
| **Agente utilizador**<br>*(UserAgent)* | O agente utilizador utilizado na ação. | Microsoft Azure Graph Client Library 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **Família de agente de utilizadores**<br>*(UserAgentFamily)* | A família do agente utilizador usado na ação. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tabelas de insights de atividade

#### <a name="action-performed"></a>Ação realizada

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Primeira vez que o utilizador realizou ação**<br>*(FirstTimeUserPerformedAction)* | 180 | A ação foi realizada pela primeira vez pelo utilizador. | Verdade, Falso |
| **Ação invulgarmente realizada pelo utilizador**<br>*(ActionUncommonlyPerformedByUser)* | 10 | A ação não é geralmente realizada pelo utilizador. | Verdade, Falso |
| **Ação invulgarmente realizada entre pares**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | A ação não é geralmente realizada entre os pares do utilizador. | Verdade, Falso |
| **Primeira ação realizada em inquilino**<br>*(FirstTimeActionPerformedInTenant)* | 180 | A ação foi realizada pela primeira vez por qualquer um da organização. | Verdade, Falso |
| **Ação invulgarmente realizada no arrendatário**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | A ação não é comumente realizada na organização. | Verdade, Falso |
|

#### <a name="app-used"></a>Aplicativo usado

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Aplicação usada pela primeira vez pelo utilizador**<br>*(FirstTimeUserUsedApp)* | 180 | A aplicação foi utilizada pela primeira vez pelo utilizador. | Verdade, Falso |
| **App raramente utilizada pelo utilizador**<br>*(AppUncommonlyUsedByUser)* | 10 | A aplicação não é comumente utilizada pelo utilizador. | Verdade, Falso |
| **App raramente usada entre pares**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | A aplicação não é comumente utilizada entre os pares do utilizador. | Verdade, Falso |
| **App da primeira vez observada no inquilino**<br>*(FirstTimeAppObservedInTenant)* | 180 | A aplicação foi observada pela primeira vez na organização. | Verdade, Falso |
| **App raramente usada no inquilino**<br>*(AppUncommonlyUsedInTenant)* | 180 | A aplicação não é comumente usada na organização. | Verdade, Falso |
| 

#### <a name="browser-used"></a>Navegador usado

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Primeiro utilizador conectado via browser**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | O navegador foi observado pela primeira vez pelo utilizador. | Verdade, Falso |
| **Navegador invulgarmente utilizado pelo utilizador**<br>*(BrowserUncommonlyUsedByUser)* | 10 | O navegador não é comumente utilizado pelo utilizador. | Verdade, Falso |
| **Navegador invulgarmente usado entre pares**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | O navegador não é comumente usado entre os pares do utilizador. | Verdade, Falso |
| **Navegador da primeira vez observado no inquilino**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | O navegador foi observado pela primeira vez na organização. | Verdade, Falso |
| **Navegador invulgarmente usado no inquilino**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | O navegador não é comumente usado na organização. | Verdade, Falso |
| 

#### <a name="country-connected-from"></a>País ligado a partir de

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Utilizador pela primeira vez ligado a partir do país**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | A localização geo, tal como resolvida a partir do endereço IP, foi ligada pela primeira vez pelo utilizador. | Verdade, Falso |
| **País invulgarmente ligado pelo utilizador**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | A localização geo, tal como resolvida a partir do endereço IP, não é normalmente ligada pelo utilizador. | Verdade, Falso |
| **País invulgarmente ligado entre os pares**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | A localização geo, tal como resolvida a partir do endereço IP, não está normalmente ligada entre os pares do utilizador. | Verdade, Falso |
| **Primeira ligação do país observada no inquilino**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | O país foi ligado pela primeira vez por alguém da organização. | Verdade, Falso |
| **País invulgarmente ligado a partir de inquilino**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | A localização geo, tal como resolvida a partir do endereço IP, não está normalmente ligada à organização. | Verdade, Falso |
| 

#### <a name="device-used-to-connect"></a>Dispositivo utilizado para ligar

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Utilizador pela primeira vez ligado a partir do dispositivo**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | O dispositivo de origem foi ligado pela primeira vez pelo utilizador. | Verdade, Falso |
| **Dispositivo invulgarmente utilizado pelo utilizador**<br>*(DeviceUncommonlyUsedByUser)* | 10 | O aparelho não é normalmente utilizado pelo utilizador. | Verdade, Falso |
| **Dispositivo invulgarmente utilizado entre pares**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | O dispositivo não é normalmente utilizado entre os pares do utilizador. | Verdade, Falso |
| **Dispositivo pela primeira vez observado no inquilino**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | O dispositivo foi observado pela primeira vez na organização. | Verdade, Falso |
| **Dispositivo invulgarmente utilizado no inquilino**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | O dispositivo não é comumente usado na organização. | Verdade, Falso |
| 

#### <a name="other-device-related"></a>Outros dispositivos relacionados

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Primeiro utilizador iniciou sessão no dispositivo**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | O dispositivo de destino foi ligado pela primeira vez pelo utilizador. | Verdade, Falso |
| **Família de dispositivos raramente usada no inquilino**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | A família do dispositivo não é comumente usada na organização. | Verdade, Falso |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Fornecedor de Serviços de Internet usado para ligar

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Utilizador pela primeira vez conectado via ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | O ISP foi observado pela primeira vez pelo utilizador. | Verdade, Falso |
| **ISP raramente utilizado pelo utilizador**<br>*(ISPUncommonlyUsedByUser)* | 10 | O ISP não é normalmente utilizado pelo utilizador. | Verdade, Falso |
| **ISP raramente usado entre pares**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | O ISP não é comumente utilizado entre os pares do utilizador. | Verdade, Falso |
| **Primeira ligação via ISP no inquilino**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | O ISP foi observado pela primeira vez na organização. | Verdade, Falso |
| **ISP raramente usado no inquilino**<br>*(ISPUncommonlyUsedInTenant)* | 30 | O ISP não é comumente usado na organização. | Verdade, Falso |
| 

#### <a name="resource-accessed"></a>Recurso acedido

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **Recurso de acesso do utilizador pela primeira vez**<br>*(FirstTimeUserAccessedResource)* | 180 | O recurso foi acedido pela primeira vez pelo utilizador. | Verdade, Falso |
| **Recurso invulgarmente acedido pelo utilizador**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | O recurso não é habitualmente acedido pelo utilizador. | Verdade, Falso |
| **Recurso invulgarmente acedido entre pares**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | O recurso não é habitualmente acedido entre os pares do utilizador. | Verdade, Falso |
| **Primeiro recurso acedido em inquilino**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | O recurso foi acedido pela primeira vez por qualquer pessoa da organização. | Verdade, Falso |
| **Recurso invulgarmente acedido no inquilino**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | O recurso não é comumente acedido na organização. | Verdade, Falso |
| 

#### <a name="miscellaneous"></a>Diversos

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Description | Valor da amostra |
| --- | --- | --- | --- |
| **A última vez que o utilizador realizou ação**<br>*(LastTimeUserPerformedAction)* | 180 | A última vez que o utilizador realizou a mesma ação. | <Timestamp> |
| **Ações semelhantes não foram realizadas no passado.**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | O utilizador não realizou qualquer ação no mesmo fornecedor de recursos. | Verdade, Falso |
| **Localização IP de origem**<br>*(Localização SourceIP)* | *N/D* | O país resolveu a partir da origem ip da ação. | [Surrey, Inglaterra] |
| **Volume incomum de operações**<br>*(UncommonHighVolumeOfOperations)* | 7 | Um utilizador realizou uma explosão de operações semelhantes dentro do mesmo fornecedor | Verdade, Falso |
| **Número incomum de falhas de acesso condicional AZure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Um número incomum de utilizadores não autenticou devido ao acesso condicional | Verdade, Falso |
| **Número incomum de dispositivos adicionados**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Um utilizador adicionou um número incomum de dispositivos. | Verdade, Falso |
| **Número incomum de dispositivos eliminados**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Um utilizador apagou um número incomum de dispositivos. | Verdade, Falso |
| **Número incomum de utilizadores adicionados ao grupo**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Um utilizador adicionou um número incomum de utilizadores a um grupo. | Verdade, Falso |
|