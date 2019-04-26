---
title: Usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory | Documentos da Microsoft
description: Saiba como utilizar as pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287324"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como: Usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory

Deseja:

- Compreender o impacto dos seus [políticas de acesso condicional](../conditional-access/overview.md) na experiência de início de sessão dos seus utilizadores?

- Resolver problemas de falhas de início de sessão para obter uma visão melhor de sua organização iniciar sessão no estado de funcionamento, bem como resolver problemas rapidamente?

- Saber quem está a utilizar as autenticações herdadas para iniciar sessão seu ambiente? Por [antigos de autenticação de bloqueio](../conditional-access/block-legacy-authentication.md), pode melhorar a proteção do seu inquilino.


[Pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinar o texto, consultas de análise, métricas do Azure e parâmetros em relatórios interativos avançados. O Azure Active Directory fornece pastas de trabalho de monitorização que ajudam a encontrar respostas às perguntas acima.

Este artigo:

- Parte do princípio de que está familiarizado com a [criar relatórios interativos com pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como pode utilizar as pastas de trabalho do Azure Monitor sobre a monitorização de responder às perguntas acima.
 


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:

- Um inquilino do Azure Active Directory, com uma licença premium (P1/P2). Saiba como [obter uma licença premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- R [área de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Pastas de trabalho de acesso 

Para aceder a pastas de trabalho:

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com).

2. Na barra de navegação esquerda, clique em **do Azure Active Directory**.

3. Na **monitorização** secção, clique em **Insights**. 

    ![Informações](./media/howto-use-azure-monitor-workbooks/41.png)

4. Clique num relatório ou modelo ou clique em **aberto** na barra de ferramentas. 

    ![Galeria](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Análise de início de sessão

Para acessar a pasta de trabalho de análise de início de sessão, clique em **inícios de sessão** no **utilização** secção. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito

- Ação de utilizador pendente

- Falha

Pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Galeria](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, para obter uma análise detalhada:

- Location

    ![Galeria](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Galeria](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inícios de sessão através da autenticação de legado 


Para utilizar o acesso os inícios de sessão [antigos de autenticação](../conditional-access/block-legacy-authentication.md) pasta de trabalho, clique em **inícios de sessão através da autenticação legada** no **utilização** secção. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito


Pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicações

- Utilizadores

- Protocolos 

![Galeria](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, obtém uma análise detalhada através da aplicação e protocolo.

![Galeria](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inícios de sessão pelo acesso condicional 


Para aceder os inícios de sessão por [políticas de acesso condicional](../conditional-access/overview.md) pasta de trabalho, clique em **inícios de sessão pelo acesso condicional** no **acesso condicional** secção. 

Este livro mostra a tendência para inícios de sessão desativados.

Pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Galeria](./media/howto-use-azure-monitor-workbooks/49.png)


Para os inícios de sessão desativados, receberá uma divisão pelo Estado de acesso condicional.

![Estado do acesso condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Inícios de sessão por conceder controlos

Para aceder os inícios de sessão por [conceder controlos](../conditional-access/controls.md) pasta de trabalho, clique em **inícios de sessão por conceder controlos** no **acesso condicional** secção. 

Este livro mostra as seguintes desativadas início de sessão tendências:

- Requerer MFA
 
- Exigir a termos de utilização

- Exigir a declaração de privacidade

- Outros


Pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Galeria](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, obtém uma análise detalhada através da aplicação e protocolo.

![Galeria](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falhas de inícios de sessão

Utilize o **análise de falhas de inícios de sessão** livro para resolver problemas de erros com:

- Inícios de sessão
- Políticas de acesso condicional
- Autenticação de legado. 


Para aceder os inícios de sessão, dados de acesso condicional, clique em **inícios de sessão através da autenticação de legado** no **resolução de problemas** secção. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito

- Ação pendente

- Falha


Pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Galeria](./media/howto-use-azure-monitor-workbooks/52.png)


Para resolver problemas de inícios de sessão, para obter uma análise detalhada:

- Erros principais

    ![Galeria](./media/howto-use-azure-monitor-workbooks/53.png)

- Inícios de sessão à espera de ação do utilizador

    ![Galeria](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passos Seguintes

* [Criar relatórios interativos com pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)