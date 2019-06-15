---
title: Usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory | Documentos da Microsoft
description: Saiba como utilizar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory.
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
ms.openlocfilehash: 6d461725d0d4c86ee7aa5b450d42c01ca77ffb16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107667"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como utilizar as pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory

Deseja:

- Compreender o efeito da sua [políticas de acesso condicional](../conditional-access/overview.md) na experiência de início de sessão dos seus utilizadores?

- Resolver problemas de falhas de início de sessão para obter uma visão melhor de iniciar sessão no estado de funcionamento sua organização e resolver problemas rapidamente?

- Saber quem está a utilizar as autenticações herdadas para iniciar sessão no seu ambiente? (Por [antigos de autenticação de bloqueio](../conditional-access/block-legacy-authentication.md), pode melhorar a proteção do seu inquilino.)

Para ajudá-lo a atender essas perguntas, o Active Directory fornece as pastas de trabalho para monitorização. [Pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinar o texto, consultas de análise, métricas e parâmetros em relatórios interativos avançados. 

Este artigo:

- Pressupõe que esteja familiarizado com a [criar relatórios interativos com livros do Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como utilizar pastas de trabalho do Monitor para compreender o efeito das suas políticas de acesso condicional, para resolver problemas de falhas de início de sessão e para identificar as autenticações de legado.
 


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar pastas de trabalho do Monitor, tem de:

- Um inquilino do Active Directory com uma licença do premium (P1 ou P2). Saiba como [obter uma licença premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- R [área de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Acesso da pasta de trabalho 

Para aceder a pastas de trabalho:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, selecione **do Azure Active Directory**.

3. Na **monitorização** secção, selecione **Insights**. 

    ![Selecione o Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selecione um relatório ou modelo ou na barra de ferramentas, selecione **aberto**. 

    ![Selecione abrir](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Análise de início de sessão

Para acessar a pasta de trabalho de análise de início de sessão, o **utilização** secção, selecione **inícios de sessão**. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito

- Ação do utilizador pendente

- Falha

Pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Análise de início de sessão](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, para obter uma análise detalhada das categorias seguintes:

- Location

    ![Inícios de sessão por localização](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Inícios de sessão por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inícios de sessão através da autenticação de legado 


Para acessar a pasta de trabalho para inícios de sessão que utilizam [antigos de autenticação](../conditional-access/block-legacy-authentication.md), na **utilização** secção, selecione **inícios de sessão através da autenticação de legado**. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito


Pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicações

- Utilizadores

- Protocolos

![Inícios de sessão por autenticação](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, obtém uma análise detalhada através da aplicação e protocolo.

![Autenticação de legado-inícios de sessão por aplicação e o protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inícios de sessão pelo acesso condicional 


Para acessar a pasta de trabalho para inícios de sessão por [políticas de acesso condicional](../conditional-access/overview.md), na **acesso condicional** secção, selecione **inícios de sessão pelo acesso condicional**. 

Este livro mostra as tendências de inícios de sessão desativados. Pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de Sessão com Acesso Condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para inícios de sessão desativados, receberá uma divisão pelo Estado de acesso condicional.

![Estado de acesso condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Inícios de sessão por conceder controlos

Para acessar a pasta de trabalho para inícios de sessão por [conceder controlos](../conditional-access/controls.md), na **acesso condicional** secção, selecione **inícios de sessão por conceder controlos de**. 

Este livro mostra as seguintes desativadas início de sessão tendências:

- Requerer MFA
 
- Pedir os termos de utilização

- Exigir a declaração de privacidade

- Outros


Pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de sessão por conceder controlos](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, obtém uma análise detalhada através da aplicação e protocolo.

![Divisão de inícios de sessão recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falhas de inícios de sessão

Utilize o **análise de falhas de inícios de sessão** livro para resolver problemas de erros com o seguinte:

- Inícios de sessão
- Políticas de acesso condicional
- Autenticação de legado 


Para aceder os inícios de sessão por dados de acesso condicional, o **resolução de problemas** secção, selecione **inícios de sessão através da autenticação de legado**. 

Este livro mostra as tendências de início de sessão seguintes:

- Todos os inícios de sessão

- Êxito

- Ação pendente

- Falha


Pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de sessão de resolução de problemas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudar a resolver problemas de inícios de sessão, Monitor do Azure dá-lhe uma divisão por categorias a seguir:

- Erros principais

    ![Resumo dos erros principais](./media/howto-use-azure-monitor-workbooks/53.png)

- Inícios de sessão à espera de ação do utilizador

    ![Resumo dos inícios de sessão à espera de ação do utilizador](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passos Seguintes

[Criar relatórios interativos com livros do Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).