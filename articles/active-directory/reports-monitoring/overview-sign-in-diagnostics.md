---
title: O que é o Diagnóstico de Inscrição em Azure AD? | Microsoft Docs
description: Fornece uma visão geral geral do Diagnóstico de Inscrição em Azure AD.
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
ms.openlocfilehash: e85de1edd94a0430a4b28b332d9e43b967afba76
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608923"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>O que é o Diagnóstico de Inscrição em Azure AD?

O Azure AD fornece-lhe um modelo de segurança flexível para controlar o que os utilizadores podem fazer com os recursos geridos. O acesso a estes recursos não é apenas controlado por **quem** é, mas também pela **forma como** os acede. Normalmente, a flexibilidade vem junto com um certo grau de complexidade devido ao número de opções de configuração que você tem. A complexidade tem o potencial de aumentar o risco de erros.

Como administrador de TI, precisa de uma solução que lhe dê o nível certo de insights sobre as atividades no seu sistema para que possa facilmente diagnosticar e resolver problemas quando ocorrem. O Diagnóstico de Inscrição para Azure AD é um exemplo para tal solução. Utilize o diagnóstico para analisar o que aconteceu durante uma sindes de saúde e que ações pode tomar para resolver problemas sem ser obrigado a envolver o suporte da Microsoft.

Este artigo dá-lhe uma visão geral do que a solução faz e como pode usá-la.


## <a name="requirements"></a>Requisitos

O Sign-in Diagnostics está disponível em todas as edições do Azure AD.<br> Deve ser um administrador global na Azure AD para usá-lo.

## <a name="how-it-works"></a>Como funciona

Em Azure AD, a resposta a uma tentativa de inscrição está ligada a **quem** você é e **como** você acede ao seu inquilino. Por exemplo, como administrador, normalmente pode configurar todos os aspetos do seu inquilino quando iniciar seduca na sua rede corporativa. No entanto, pode até ser bloqueado quando iniciar sedutada com a mesma conta de uma rede não fidedqui nem fided quanto a isso.
 
Devido à maior flexibilidade do sistema para responder a uma tentativa de inscrição, pode terminar em cenários em que precisa de resolução de problemas. O diagnóstico de inscrição é uma característica que:

- Analisa dados de insusous. 

- Mostra o que aconteceu e recomendações sobre como resolver problemas. 

O Diagnóstico de Inscrição para Azure AD foi concebido para permitir o autodiagnóstico de erros de inscrição. Para completar o processo de diagnóstico, é necessário:

![Processo de diagnóstico de inscrição](./media/overview-sign-in-diagnostics/process.png)
 
1. **Defina** o âmbito dos eventos de inscrição que lhe interessam

2. **Selecione** o sinal de in que pretende rever

3. **Reveja** o resultado do diagnóstico

4. **Tomar** ações

 
### <a name="define-scope"></a>Definir âmbito

O objetivo deste passo é definir a margem para os sign-ins que pretende investigar. O seu âmbito de aplicação baseia-se num utilizador ou num identificador (correlationId, requestId) e num intervalo de tempo. Para reduzir ainda mais o âmbito, também pode especificar um nome de aplicação. A Azure AD utiliza as informações de âmbito para localizar os eventos certos para si.  

### <a name="select-sign-in"></a>Selecione iniciar s-in  

Com base nos seus critérios de pesquisa, o Azure AD recupera todos os sign-ins correspondentes e apresenta-os numa vista de lista de resumo de autenticação. 

![Resumo da autenticação](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Pode personalizar as colunas apresentadas nesta vista.

### <a name="review-diagnostic"></a>Revisão de diagnóstico 

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

Este cenário baseia-se numa inscrição que foi bloqueada por uma política de acesso condicional.

![Bloquear o acesso](./media/overview-sign-in-diagnostics/block-access.png)

A secção de diagnóstico deste cenário mostra detalhes sobre o início de saúde do utilizador e as políticas aplicadas.


### <a name="failed-conditional-access"></a>Acesso condicional falhado

Este cenário é normalmente o resultado de uma assinatura que falhou porque os requisitos de uma política de acesso condicional não estavam satisfeitos. Alguns exemplos comuns são:

![Exigir controlos](./media/overview-sign-in-diagnostics/require-controls.png)

- Requerem o dispositivo híbrido Azure AD

- Requera uma aplicação de cliente aprovada

- Pedir uma política de proteção de aplicações   


A secção de diagnóstico deste cenário mostra detalhes sobre o início de saúde do utilizador e as políticas aplicadas.


### <a name="mfa-from-conditional-access"></a>MFA do acesso condicional

Este cenário baseia-se numa política de acesso condicional que tem a obrigação de iniciar sação utilizando um conjunto de autenticação de vários fatores.

![Requerem autenticação de vários fatores](./media/overview-sign-in-diagnostics/require-mfa.png)

A secção de diagnóstico deste cenário mostra detalhes sobre o início de saúde do utilizador e as políticas aplicadas.



### <a name="mfa-from-other-requirements"></a>MFA de outros requisitos

Este cenário baseia-se num requisito de autenticação de vários fatores que não foi aplicado por uma política de acesso condicional. Por exemplo, a autenticação de vários fatores por utilizador.


![Requerem autenticação de vários fatores por utilizador](./media/overview-sign-in-diagnostics/mfa-per-user.png)


A intenção deste cenário de diagnóstico é fornecer mais detalhes sobre:

- A origem da interrupção da autenticação multi-factor. 
- O resultado da interação do cliente.

Além disso, esta secção também lhe fornece todos os detalhes sobre a tentativa de início de s.a. 


### <a name="mfa-proof-up-required"></a>MFA comprovativo necessário

Este cenário baseia-se em inscrições que foram interrompidas por pedidos de configuração da autenticação de vários fatores. Esta configuração também é conhecida como "prova".

A prova de autenticação multi-factor ocorre quando um utilizador é obrigado a utilizar a autenticação de vários fatores, mas ainda não a configura, ou um administrador configura o utilizador para o configurar.

A intenção deste cenário de diagnóstico é fornecer a introspeção de que a interrupção da autenticação de vários fatores foi a sua configuração e fornecer a recomendação para que o utilizador complete a prova.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>MFA comprovativo exigido de um sinal de risco

Este cenário resulta de inscrições que foram interrompidas por um pedido de configuração da autenticação de vários fatores a partir de uma sinalização de risco. 

A intenção deste cenário de diagnóstico é fornecer a introspeção de que a interrupção da autenticação de vários fatores foi a sua configuração, fornecer a recomendação para que o utilizador complete a prova, mas para fazê-lo a partir de um local de rede que não pareça arriscado. Por exemplo, se uma rede corporativa for definida como uma tentativa de localização nomeada para fazer a Prova da rede corporativa em vez disso.


### <a name="successful-sign-in"></a>Sindução bem sucedida

Este cenário baseia-se em inscrições que não foram interrompidas por acesso condicional ou autenticação de vários fatores.

A intenção deste cenário de diagnóstico é fornecer informações sobre o que o utilizador forneceu durante a entrada em causa no caso de existir uma política de Acesso Condicional ou políticas que se esperassem aplicar, ou uma autenticação configurada de vários fatores que se esperasse que interrompesse o início de saúde do utilizador.



## <a name="next-steps"></a>Passos seguintes

* [O que são os relatórios do Azure Active Directory?](overview-reports.md)
* [O que é a monitorização do Azure Active Directory?](overview-monitoring.md)
