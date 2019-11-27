---
title: A ferramenta de What If de acesso condicional-Azure Active Directory
description: Saiba como você pode entender o impacto de suas políticas de acesso condicional em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5846934a8ad8455ca375b4bc54fc46d45aba1cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379985"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Solucionar problemas usando a ferramenta de What If no acesso condicional

O [acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure Active Directory (AD do Azure) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Como você sabe o que esperar das políticas de acesso condicional em seu ambiente? Para responder a essa pergunta, você pode usar a **ferramenta de What If de acesso condicional**.

Este artigo explica como você pode usar essa ferramenta para testar suas políticas de acesso condicional.

## <a name="what-it-is"></a>O que é

A **ferramenta de política de What If de acesso condicional** permite que você entenda o impacto de suas políticas de acesso condicional em seu ambiente. Em vez de testar as suas políticas ao executar vários inícios de sessão manualmente, esta ferramenta permite avaliar um início de sessão simulado de um utilizador. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação. O relatório não lista apenas as políticas de acesso condicional aplicadas, mas também [as políticas clássicas](policy-migration.md#classic-policies) , se existirem.    

A ferramenta de **What If** fornece uma maneira de determinar rapidamente as políticas que se aplicam a um usuário específico. Você pode usar as informações, por exemplo, se precisar solucionar um problema.    

## <a name="how-it-works"></a>Como funciona

Na **ferramenta de What If de acesso condicional**, primeiro você precisa definir as configurações do cenário de entrada que deseja simular. Essas configurações incluem:

- O usuário que você deseja testar 
- Os aplicativos de nuvem que o usuário tentaria acessar
- As condições sob as quais o acesso ao configura os aplicativos de nuvem são executadas
     
Como uma próxima etapa, você pode iniciar uma execução de simulação que avalia suas configurações. Somente as políticas habilitadas fazem parte de uma execução de avaliação.

Quando a avaliação for concluída, a ferramenta gerará um relatório das políticas afetadas.

## <a name="running-the-tool"></a>Executando a ferramenta

Você pode encontrar a ferramenta **What If** na página **[acesso condicional-políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** no portal do Azure.

Para iniciar a ferramenta, na barra de ferramentas na parte superior da lista de políticas, clique em **What If**.

![What If](./media/what-if-tool/01.png)

Para poder executar uma avaliação, você deve definir as configurações.

## <a name="settings"></a>Definições

Esta seção fornece informações sobre as configurações de execução de simulação.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Utilizador

Você só pode selecionar um usuário. Esse é o único campo obrigatório.

### <a name="cloud-apps"></a>Aplicativos de nuvem

O padrão para essa configuração é **todos os aplicativos de nuvem**. A configuração padrão executa uma avaliação de todas as políticas disponíveis em seu ambiente. Você pode restringir o escopo às políticas que afetam aplicativos de nuvem específicos.

### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a [condição de local](location-condition.md). O endereço representa o endereço voltado para a Internet do dispositivo usado pelo usuário para entrar. Você pode verificar o endereço IP de um dispositivo, por exemplo, navegando até [o que é meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivo

Essa configuração imita a [condição de plataformas de dispositivo](conditions.md#device-platforms) e representa o equivalente a **todas as plataformas (incluindo sem suporte)** . 

### <a name="client-apps"></a>Aplicativos cliente

Essa configuração imita a [condição de aplicativos cliente](conditions.md#client-apps).
Por padrão, essa configuração faz com que uma avaliação de todas as políticas tenha **aplicativos móveis e de navegador e clientes de área de trabalho** individualmente ou ambos selecionados. Ele também detecta políticas que impõem o **Exchange ActiveSync (EAS)** . Você pode restringir essa configuração selecionando:

- **Navegador** para avaliar todas as políticas que têm pelo menos o **navegador** selecionado. 
- **Aplicativos móveis e clientes de desktop** para avaliar todas as políticas que têm pelo menos **aplicativos móveis e clientes de desktop** selecionados. 

### <a name="sign-in-risk"></a>Risco de entrada

Essa configuração imita a [condição de risco de entrada](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Período 

Você inicia uma avaliação clicando em **What If**. O resultado da avaliação fornece um relatório que consiste em: 

![What If](./media/what-if-tool/03.png)

- Um indicador de se existem políticas clássicas em seu ambiente
- Políticas que se aplicam ao usuário
- Políticas que não se aplicam ao usuário

Se houver [políticas clássicas](policy-migration.md#classic-policies) para os aplicativos de nuvem selecionados, um indicador será apresentado a você. Ao clicar no indicador, você será redirecionado para a página políticas clássicas. Na página políticas clássicas, você pode migrar uma política clássica ou apenas desabilitá-la. Você pode retornar ao resultado da avaliação fechando esta página.

Na lista de políticas que se aplicam ao usuário selecionado, você também pode encontrar uma lista de [controles de concessão](controls.md#grant-controls) e de [sessão](controls.md#session-controls) que seu usuário deve atender.

Na lista de políticas que não se aplicam ao usuário, você também pode encontrar os motivos pelos quais essas políticas não se aplicam. Para cada política listada, o motivo representa a primeira condição que não foi satisfeita. Um possível motivo para uma política que não é aplicada é uma política desabilitada porque elas não são mais avaliadas.   

## <a name="next-steps"></a>Passos seguintes

- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 
- Se você quiser migrar políticas clássicas, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md)  
