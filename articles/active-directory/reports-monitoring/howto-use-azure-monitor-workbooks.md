---
title: Usar pastas de trabalho Azure Monitor para relatórios Azure Active Directory | Microsoft Docs
description: Saiba como usar pastas de trabalho do Azure Monitor para relatórios Azure Active Directory.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: c1ecdb80263efda4cbbb43caaa4e27a04b261f81
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989808"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como usar pastas de trabalho do Azure Monitor para relatórios Azure Active Directory

Deseja:

- Entender o efeito de suas [políticas de acesso condicional](../conditional-access/overview.md) na experiência de entrada de seus usuários?

- Solucionar problemas de falhas de entrada para obter uma exibição melhor da integridade de entrada da sua organização e resolver problemas rapidamente?

- Sabe quem está usando autenticações herdadas para entrar em seu ambiente? (Ao [bloquear a autenticação herdada](../conditional-access/block-legacy-authentication.md), você pode melhorar a proteção do locatário.)

Para ajudá-lo a resolver essas perguntas, Active Directory fornece pastas de trabalho para monitoramento. [Azure monitor pastas de trabalho](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinam texto, consultas de análise, métricas e parâmetros em relatórios interativos sofisticados. 

Este artigo:

- Pressupõe que você esteja familiarizado com a [criação de relatórios interativos usando as pastas de trabalho do monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como usar pastas de trabalho do monitor para entender o efeito de suas políticas de acesso condicional, para solucionar problemas de falhas de entrada e para identificar autenticações herdadas.
 


## <a name="prerequisites"></a>Pré-requisitos

Para usar pastas de trabalho de monitoramento, você precisa de:

- Um locatário Active Directory com uma licença Premium (P1 ou P2). Saiba como [obter uma licença Premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Um [espaço de trabalho log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Acesso à pasta de trabalho 

Para acessar pastas de trabalho:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, selecione **Azure Active Directory**.

3. Na seção **monitoramento** , selecione **pastas de trabalho**. 

    ![Selecionar informações](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selecione um relatório ou modelo ou, na barra de ferramentas, selecione **abrir**. 

    ![Selecione abrir](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Análise de entrada

Para acessar a pasta de trabalho de análise de entrada, na seção **uso** , selecione **entradas**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todos os inícios de sessão

- Êxito

- Ação de utilizador pendente

- Falha

Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Análise de entrada](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, você obtém uma divisão das seguintes categorias:

- Location

    ![Entradas por local](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Entradas por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Entradas usando autenticação herdada 


Para acessar a pasta de trabalho para entradas que usam [autenticação herdada](../conditional-access/block-legacy-authentication.md), na seção **uso** , selecione **entradas usando autenticação herdada**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todos os inícios de sessão

- Êxito


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

- Protocolos

![Entradas por autenticação herdada](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Herdado-entradas de autenticação por aplicativo e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Entradas por acesso condicional 


Para acessar a pasta de trabalho para entradas por [políticas de acesso condicional](../conditional-access/overview.md), na seção **acesso condicional** , selecione **entradas por acesso condicional**. 

Esta pasta de trabalho mostra as tendências para as entradas desabilitadas. Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de Sessão com Acesso Condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para entradas desabilitadas, você obtém uma divisão pelo status de acesso condicional.

![Status de acesso condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Entradas por conceder controles

Para acessar a pasta de trabalho para entradas por meio de [controles de concessão](../conditional-access/controls.md), na seção **acesso condicional** , selecione **entradas por conceder controles**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada desabilitadas:

- Requerer MFA
 
- Pedir os termos de utilização

- Exigir política de privacidade

- Outros


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Entradas por conceder controles](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Divisão de entradas recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de entradas

Use a pasta de trabalho **análise de falha de entradas** para solucionar erros com o seguinte:

- Inícios de sessão
- Políticas de acesso condicional
- Autenticação legada 


Para acessar as entradas por dados de acesso condicional, na seção **solucionar problemas** , selecione **entradas usando a autenticação herdada**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todos os inícios de sessão

- Êxito

- Ação pendente

- Falha


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Solucionando problemas de entradas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudá-lo a solucionar problemas de entrada, Azure Monitor oferece uma divisão das seguintes categorias:

- Principais erros

    ![Resumo dos principais erros](./media/howto-use-azure-monitor-workbooks/53.png)

- Entradas aguardando a ação do usuário

    ![Resumo de entradas aguardando a ação do usuário](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passos Seguintes

[Crie relatórios interativos usando monitorar pastas de trabalho](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
