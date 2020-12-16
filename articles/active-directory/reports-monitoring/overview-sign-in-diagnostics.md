---
title: O que são diagnósticos de inscrição no Azure AD? | Microsoft Docs
description: Fornece uma visão geral geral dos diagnósticos de inscrição em Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: e113753bee5c0a94fbec47a2ea14b98c1779a394
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578156"
---
# <a name="what-are-sign-in-diagnostics-in-azure-ad"></a>O que são diagnósticos de inscrição no Azure AD?

O Azure AD fornece-lhe um modelo de segurança flexível para controlar o que os utilizadores podem fazer com os recursos geridos. O acesso a estes recursos não é apenas controlado por quem é, mas também pela forma como os acede. Normalmente, a flexibilidade vem junto com um certo grau de complexidade devido ao número de opções de configuração que você tem. A complexidade tem o potencial de aumentar o risco de erros.

Como administrador de TI, precisa de uma solução, que lhe dê o nível certo de insights sobre as atividades no seu sistema para que possa facilmente diagnosticar e resolver problemas quando ocorrem. Os diagnósticos de entrada para Azure AD são um exemplo para tal ferramenta. Utilize o diagnóstico para analisar o que aconteceu durante uma sindes de saúde e que ações pode tomar para resolver problemas sem ser obrigado a envolver o suporte da Microsoft.

Este artigo dá-lhe uma visão geral do que esta ferramenta faz e como pode usá-la.


## <a name="requirements"></a>Requisitos

O diagnóstico de inscrição está disponível em todas as edições do Azure AD.<br> Deve ser um administrador global na Azure AD para utilizar esta ferramenta.

## <a name="how-it-works"></a>Como funciona

Em Azure AD, a resposta a uma tentativa de inscrição não está apenas ligada a quem você é, mas também como você acede ao seu inquilino. Por exemplo, como administrador, poderá configurar todos os aspetos do seu inquilino enquanto se inscreveu na sua rede corporativa. No entanto, pode mesmo ser bloqueado quando iniciar sôms na mesma conta a partir de uma rede não fidedqui nem fided quanto à sua. Devido à maior flexibilidade que o sistema tem para responder a uma tentativa de inscrição, pode terminar em cenários em que precisa de tentativas de sessão de resolução de problemas. O diagnóstico de inscrição é uma funcionalidade que analisa dados do sign-in e apresenta mensagens específicas sobre o que aconteceu, e recomendações sobre o que fazer para resolver problemas. O diagnóstico de inscrição para Azure AD foi concebido para permitir o autodiagnóstico de erros de inscrição. O processo de diagnóstico consiste em quatro blocos principais de construção:

![Processo de diagnóstico de inscrição](./media/overview-sign-in-diagnostics/process.png)
 
1. **Defina** a margem para os eventos de inscrição que lhe interessam

2. **Selecione** o evento que pretende rever

3. **Diagnóstico de revisão**

4. **Tomar** ações

 
### <a name="define-sign-ins"></a>Definir ins-ins

O objetivo deste passo é definir a margem para eventos de inscrição que pretende investigar. O seu âmbito de aplicação baseia-se num utilizador ou num identificador (correlationId, requestId) e num intervalo de tempo. Além disso, também pode especificar um nome de aplicação. A Azure AD utiliza as informações de âmbito para localizar os eventos certos para si.  

### <a name="select-sign-in-event"></a>Selecione evento de inscrição 

Com base nos seus critérios de pesquisa, o Azure AD recupera todos os eventos de inscrição correspondentes e apresenta-os numa vista de lista de resumo de autenticação. 

![Resumo da autenticação](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Pode personalizar as colunas apresentadas nesta vista.

### <a name="review-diagnosis"></a>Diagnóstico de revisão

Para o evento de inscrição selecionado, o Azure AD fornece-lhe um resultado de diagnóstico. 

![Resultados de diagnóstico](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
O resultado começa com uma avaliação. A avaliação explica em poucas frases o que aconteceu. A explicação ajuda-o a compreender o comportamento do sistema. 

Como passo seguinte, obtém-se um resumo das políticas de acesso condicional relacionadas que foram aplicadas ao sin-in selecionado. Esta peça é completada por medidas de reparação recomendadas para resolver o seu problema. Como nem sempre é possível resolver problemas sem ajuda adicional, um passo recomendado pode ser abrir um bilhete de apoio. 

### <a name="take-action"></a>Tomar medidas 
Neste momento, deve ter a informação necessária para corrigir o seu problema.


## <a name="scenarios"></a>Cenários

Esta secção fornece-lhe uma visão geral dos cenários de diagnóstico cobertos. São implementados os seguintes cenários: 
 
- Bloqueado por acesso condicional

- Acesso condicional falhado

- MFA do acesso condicional

- MFA de outros requisitos

- MFA Prova necessária

- MFA Proof up necessário, mas tentativa de inscrição do utilizador não é de localização segura

- Sindução bem sucedida


### <a name="blocked-by-conditional-access"></a>Bloqueado por acesso condicional

Este cenário é causado por uma assinatura que foi bloqueada por uma política de acesso condicional.

![Bloquear o acesso](./media/overview-sign-in-diagnostics/block-access.png)

A secção de diagnóstico mostra detalhes sobre o s indicador do utilizador e as políticas aplicadas.


### <a name="failed-conditional-access"></a>Acesso condicional falhado

Este cenário é normalmente o resultado de uma assinatura que falhou porque os requisitos de uma política de acesso condicional não estavam satisfeitos. Alguns exemplos comuns são:

![Exigir controlos](./media/overview-sign-in-diagnostics/require-controls.png)

- Requerem o dispositivo híbrido Azure AD

- Requera uma aplicação de cliente aprovada

- Pedir uma política de proteção de aplicações   


O diagnóstico apresenta detalhes sobre o s indicado pelo utilizador e sobre a política ou políticas que foram aplicadas.


### <a name="mfa-from-conditional-access"></a>MFA do acesso condicional

Este cenário é causado por uma política de acesso condicional que tem a obrigação de iniciar sação usando conjunto de autenticação multi-fator.

![Requerem autenticação de vários fatores](./media/overview-sign-in-diagnostics/require-mfa.png)

A secção de diagnóstico mostra detalhes sobre o s indicador do utilizador e as políticas aplicadas.



### <a name="mfa-from-other-requirements"></a>MFA de outros requisitos

Este cenário ocorre quando a autenticação de vários fatores não foi aplicada por uma política de acesso condicional. Por exemplo, configura a autenticação de vários fatores por utilizador no seu inquilino.


![Requerem autenticação de vários fatores por utilizador](./media/overview-sign-in-diagnostics/mfa-per-user.png)


A intenção deste cenário de diagnóstico é fornecer mais detalhes sobre:

- A origem da interrupção da autenticação multi-factor. 
- O resultado da interação do cliente.

Além disso, esta secção também lhe fornece todos os detalhes sobre a tentativa de início de s.a. 


### <a name="mfa-proof-up-required"></a>MFA comprovativo necessário

Este cenário resulta de inscrições que foram interrompidas por pedidos para configurar a autenticação de vários fatores. Este processo de configuração também é conhecido como "prova".

A prova de autenticação multi-factor ocorre quando um utilizador é obrigado a utilizar a autenticação de vários fatores, mas nunca a configurar antes, ou um administrador configurar o utilizador para exigir a sua configuração.

A intenção deste cenário de diagnóstico é fornecer a introspeção de que a interrupção da autenticação de vários fatores foi a sua configuração e fornecer a recomendação para que o utilizador complete a prova.

### <a name="mfa-proof-up-required-but-user-sign-in-attempt-is-not-from-secure-location"></a>MFA Proof up necessário, mas tentativa de inscrição do utilizador não é de localização segura

Este cenário resulta de inscrições que foram interrompidas por um pedido de criação de autenticação multi-factor, mas a inscrição foi considerada arriscada. 

Autenticação multi-factorEso ocorre quando um utilizador é obrigado a usar a autenticação de vários fatores, mas nunca o configurar antes, ou um administrador configura o utilizador para exigir a sua configuração.

A intenção deste cenário de diagnóstico é fornecer a introspeção de que a interrupção da autenticação de vários fatores foi a sua configuração, fornecer a recomendação para que o utilizador complete a prova, mas para fazê-lo a partir de um local de rede que não pareça arriscado. Por exemplo, se uma rede corporativa for definida como uma tentativa de localização nomeada para fazer a Prova da rede corporativa em vez disso.


### <a name="successful-sign-in"></a>Sindução bem sucedida

Este cenário abrange o sinal de Azure AD sem interrupção do acesso condicional ou da autenticação de vários fatores.

A intenção deste cenário de diagnóstico é fornecer informações sobre o que o utilizador forneceu durante a entrada em causa no caso de existir uma política de Acesso Condicional ou políticas que se esperassem aplicar, ou uma autenticação configurada de vários fatores que se esperasse que interrompesse o início de saúde do utilizador.



## <a name="next-steps"></a>Passos seguintes

* [O que são os relatórios do Azure Active Directory?](overview-reports.md)
* [O que é a monitorização do Azure Active Directory?](overview-monitoring.md)
