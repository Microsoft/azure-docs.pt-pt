---
title: Qual é o diagnóstico de inscrição para o Azure Ative Directory?
description: Fornece uma visão geral geral do diagnóstico de inscrição no Diretório Ativo Azure.
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
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572294"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Qual é o diagnóstico de inscrição no Azure AD?

O Azure Ative Directory (Azure AD) fornece-lhe um modelo de segurança flexível para controlar o que os utilizadores podem fazer com recursos geridos. O acesso a estes recursos é controlado não só por *quem* são, mas também pela *forma como* os acedem. Normalmente, um modelo flexível vem com um certo grau de complexidade devido ao número de opções de configuração que você tem. A complexidade tem o potencial de aumentar o risco de erros.

Como administrador de TI, precisa de uma solução que lhe dê uma visão das atividades do seu sistema. Esta visibilidade pode permitir diagnosticar e resolver problemas quando ocorrem. O diagnóstico de inscrição para Azure AD é um exemplo de tal solução. Pode utilizar o diagnóstico para analisar o que aconteceu durante uma tentativa de iniciar sing-in e obter recomendações para resolver problemas sem precisar de envolver o suporte da Microsoft.

Este artigo dá-lhe uma visão geral do que a solução faz e como pode usá-la.

## <a name="requirements"></a>Requisitos

O diagnóstico de inscrição está disponível em todas as edições do Azure AD.

Deve ser um administrador global na Azure AD para usá-lo.

## <a name="how-it-works"></a>Como funciona

Em Azure AD, a resposta a uma tentativa de inscrição está ligada a *quem* assina e *como* acedem ao inquilino. Por exemplo, um administrador pode tipicamente configurar todos os aspetos do inquilino quando eles assinam a partir da rede corporativa. Mas o mesmo utilizador pode estar bloqueado quando iniciar sôms com a mesma conta a partir de uma rede não fidedquirida.

Devido à maior flexibilidade do sistema para responder a uma tentativa de inscrição, pode terminar em cenários em que precisa de resolução de problemas. O diagnóstico de inscrição é uma característica que:

- Analisa dados de eventos de inscrição.

- Mostra o que aconteceu.

- Fornece recomendações para como resolver problemas.

O diagnóstico de inscrição para Azure AD foi concebido para permitir o autodiagnóstico de erros de inscrição. Para completar o processo de diagnóstico, é necessário:

![Diagrama mostrando o diagnóstico de inscrição.](./media/overview-sign-in-diagnostics/process.png)

1. Defina o âmbito dos eventos de inscrição que lhe interessam.

2. Selecione o s-in que pretende rever.

3. Reveja os resultados do diagnóstico.

4. Tome medidas.

### <a name="define-scope"></a>Definir âmbito

O objetivo deste passo é definir o âmbito dos eventos de inscrição para investigar. O seu âmbito de aplicação baseia-se num utilizador ou num identificador (correlationId, requestId) e num intervalo de tempo. Para reduzir ainda mais o âmbito, pode especificar um nome de aplicação. A Azure AD utiliza as informações de âmbito para localizar os eventos certos para si.  

### <a name="select-sign-in"></a>Selecione iniciar s-in  

Com base nos seus critérios de pesquisa, o Azure AD recupera todos os eventos de inscrição correspondentes e apresenta-os numa vista de lista de resumo de autenticação.

![Imagem parcial mostrando a secção de resumo da autenticação.](./media/overview-sign-in-diagnostics/authentication-summary.png)

Pode personalizar as colunas apresentadas nesta vista.

### <a name="review-diagnostic"></a>Revisão de diagnóstico

Para o evento de inscrição selecionado, o Azure AD fornece-lhe resultados de diagnóstico.

![Imagem parcial mostrando a secção de resultados de diagnóstico.](./media/overview-sign-in-diagnostics/diagnostics-results.png)

Estes resultados começam com uma avaliação, que explica o que aconteceu em algumas frases. A explicação ajuda-o a compreender o comportamento do sistema.

Em seguida, obtém-se um resumo das políticas de acesso condicional relacionadas que foram aplicadas ao evento de inscrição selecionado. Os resultados do diagnóstico também incluem medidas de reparação recomendadas para resolver o seu problema. Como nem sempre é possível resolver problemas sem mais ajuda, um passo recomendado pode ser abrir um bilhete de apoio.

### <a name="take-action"></a>Tomar medidas

Neste momento, deve ter a informação necessária para corrigir o seu problema.

## <a name="scenarios"></a>Cenários

Os seguintes cenários são abrangidos pelo diagnóstico de inscrição:

- Bloqueado por acesso condicional

- Acesso condicional falhado

- Autenticação multifactor (MFA) a partir de acesso condicional

- MFA de outros requisitos

- MFA comprovativo necessário

- Prova de MFA necessária (local de inscrição arriscada)

- Sindução bem sucedida

### <a name="blocked-by-conditional-access"></a>Bloqueado por acesso condicional

Neste cenário, uma tentativa de inscrição foi bloqueada por uma política de acesso condicional.

![Screenshot mostrando a configuração de acesso com acesso ao Bloco selecionado.](./media/overview-sign-in-diagnostics/block-access.png)

A secção de diagnóstico deste cenário mostra detalhes sobre o evento de inscrição do utilizador e as políticas aplicadas.

### <a name="failed-conditional-access"></a>Acesso condicional falhado

Este cenário é normalmente o resultado de uma tentativa de inscrição que falhou porque os requisitos de uma política de acesso condicional não estavam satisfeitos. Alguns exemplos comuns são:

![Screenshot mostrando configuração de acesso com exemplos de política comum e acesso grant selecionado.](./media/overview-sign-in-diagnostics/require-controls.png)

- Requerem o dispositivo híbrido Azure AD

- Requera uma aplicação de cliente aprovada

- Pedir uma política de proteção de aplicações

A secção de diagnóstico deste cenário mostra detalhes sobre a tentativa de inscrição do utilizador e as políticas aplicadas.

### <a name="mfa-from-conditional-access"></a>MFA do acesso condicional

Neste cenário, uma política de acesso condicional tem a obrigação de assinar usando o conjunto de autenticação multifactor.

![Screenshot mostrando a configuração de acesso com requerer a autenticação multifactor selecionada.](./media/overview-sign-in-diagnostics/require-mfa.png)

A secção de diagnóstico deste cenário mostra detalhes sobre a tentativa de inscrição do utilizador e as políticas aplicadas.

### <a name="mfa-from-other-requirements"></a>MFA de outros requisitos

Neste cenário, um requisito de autenticação multifactor não foi aplicado por uma política de acesso condicional. Por exemplo, a autenticação multifactor por utilizador.

![Screenshot mostrando autenticação multifactor por configuração do utilizador.](./media/overview-sign-in-diagnostics/mfa-per-user.png)

A intenção deste cenário de diagnóstico é fornecer mais detalhes sobre:

- A origem da interrupção da autenticação multifactor
- O resultado da interação do cliente

Também pode ver todos os detalhes da tentativa de iniciar sposição do utilizador.

### <a name="mfa-proof-up-required"></a>MFA comprovativo necessário

Neste cenário, as tentativas de inscrição foram interrompidas por pedidos de criação de autenticação multifactor. Esta configuração também é conhecida como prova.

A prova de autenticação multifactor ocorre quando um utilizador é obrigado a utilizar a autenticação multifactor, mas ainda não a configura, ou um administrador exigiu que o utilizador o configurasse.

A intenção deste cenário de diagnóstico é revelar que a interrupção da autenticação multifactor se deveu à falta de configuração do utilizador. A solução recomendada é que o utilizador complete a prova.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Prova de MFA necessária (local de inscrição arriscada)

Neste cenário, as tentativas de inscrição foram interrompidas por um pedido de criação de autenticação multifactor a partir de um local de inscrição arriscado.

A intenção deste cenário de diagnóstico é revelar que a interrupção da autenticação multifactor se deveu à falta de configuração do utilizador. A solução recomendada é que o utilizador complete a prova, especificamente a partir de uma localização de rede que não pareça arriscada.

Por exemplo, se uma rede corporativa for definida como um local nomeado, o utilizador deve tentar fazer a prova a partir da rede corporativa.

### <a name="successful-sign-in"></a>Sindução bem sucedida

Neste cenário, os eventos de inscrição não foram interrompidos por acesso condicional ou autenticação multifactor.

Este cenário de diagnóstico fornece detalhes sobre eventos de inscrição do utilizador que se esperava que fossem interrompidos devido a políticas de acesso condicional ou autenticação multifactor.

## <a name="next-steps"></a>Passos seguintes

- [O que são os relatórios do Azure Active Directory?](overview-reports.md)
- [O que é a monitorização do Azure Active Directory?](overview-monitoring.md)
