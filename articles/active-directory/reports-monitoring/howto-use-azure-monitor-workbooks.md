---
title: Azure Monitor livros para relatórios | Microsoft Docs
description: Saiba como utilizar os livros de trabalho do Azure Monitor para relatórios do Azure Ative Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588048"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como utilizar os livros do Azure Monitor para relatórios do Azure Ative Directory

> [!IMPORTANT]
> Para otimizar as consultas subjacentes neste livro, clique em "Editar", clique no ícone Definições e selecione o espaço de trabalho onde pretende executar estas consultas. Os livros de trabalho por predefinição selecionarão todos os espaços de trabalho onde está a encaminhar os seus registos AD Azure. 

Quer:

- Compreender o efeito das suas políticas de [Acesso Condicional](../conditional-access/overview.md) na experiência de inscrição dos seus utilizadores?

- Falhas de resolução de problemas para ter uma melhor visão da saúde da sua organização e resolver problemas rapidamente?

- Sabe quem está a usar autenticações antigas para se inscrever no seu ambiente? [(Bloqueando a autenticação do legado,](../conditional-access/block-legacy-authentication.md)pode melhorar a proteção do seu inquilino.)

- Precisa entender o impacto das políticas de acesso condicional no seu inquilino?

- Gostaria de ter a capacidade de rever: consultas de registo de login, o livro relata quantos utilizadores foram autorizados ou impedidos de aceder, bem como quantos utilizadores contornaram as políticas de Acesso Condicional ao aceder aos recursos?

- Interessado em desenvolver uma compreensão mais profunda de: os detalhes do livro por condição para que o impacto de uma política possa ser contextualizado por condição, incluindo plataforma de dispositivo, estado do dispositivo, app do cliente, risco de login, localização e aplicação?

- Obtenha informações mais profundas sobre as consultas de login, o livro relata quantos utilizadores foram concedidos ou negados de acesso, bem como quantos utilizadores contornaram as políticas de Acesso Condicional ao aceder aos recursos.

- Para ajudá-lo a resolver estas questões, o Azure Ative Directory fornece livros de trabalho para monitorização. [Os livros do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md) combinam texto, consultas de análise, métricas e parâmetros em ricos relatórios interativos.



Este artigo:

- Assume que está familiarizado com a forma de [criar relatórios interativos utilizando livros de monitorização.](../../azure-monitor/visualize/workbooks-overview.md)

- Explica como utilizar os livros do Monitor para compreender o efeito das suas políticas de Acesso Condicional, para resolver falhas de acesso a problemas e identificar autenticações antigas.
 


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os livros do Monitor, é necessário:

- Um inquilino do Azure Ative Directory com uma licença premium (P1 ou P2). Saiba como [obter uma licença premium.](../fundamentals/active-directory-get-started-premium.md)

- Uma [área de trabalho do Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

- [Acesso](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) ao espaço de trabalho de análise de log analytics
- Seguintes funções no Azure Ative Directory (se estiver a aceder ao Log Analytics através do portal Azure Ative Directory)
    - Administrador de segurança
    - Leitor de segurança
    - Leitor de relatórios
    - Administrador global

## <a name="roles"></a>Funções
Deve estar numa das seguintes funções, bem como ter acesso ao espaço de trabalho [subjacente ao Log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) para gerir os livros:
-   Administrador global
-   Administrador de segurança
-   Leitor de segurança
-   Leitor de relatórios
-   Administrador de aplicação

## <a name="workbook-access"></a>Acesso ao livro 

Para aceder a livros de trabalho:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue para **Azure Ative**  >  **Directory Monitoring**  >  **Workbooks**. 

1. Selecione um relatório ou modelo, ou na barra de ferramentas **selecione Abrir**. 

![Encontre os livros do Azure Monitor em Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Análise de inscrição

Para aceder ao livro de análise de inscrição, na secção **Utilização,** selecione **Iniciar s-ins**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os insus máximos

- Com êxito

- Pendente de ação do utilizador

- Falha

Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Análise de inscrição](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, obtém-se uma desagregação pelas seguintes categorias:

- Localização

    ![Insuposições por localização](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Inícios de sessão por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Insuposições utilizando a autenticação do legado 


Para aceder ao livro de trabalho para iniciar sentutagens que utilizem [autenticação antiga,](../conditional-access/block-legacy-authentication.md)na secção **Utilização,** selecione **Iniciar s-ins utilizando a Autenticação Legacy**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os insus máximos

- Com êxito


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

- Protocolos

![Inscrições por autenticação de legados](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, obtém-se uma avaria por app e protocolo.

![Inscrições de autenticação de legados por app e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Ins-ins por Acesso Condicional 


Para aceder ao livro de inscrições por políticas de [acesso condicional,](../conditional-access/overview.md)na secção **Acesso Condicional,** selecione **Iniciar sê-ins por Acesso Condicional**. 

Este livro mostra as tendências para inserções com deficiência. Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de Sessão com Acesso Condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para insinuações desativadas, obtém-se uma avaria pelo estado de Acesso Condicional.

![A screenshot mostra o estado de acesso condicional e as entradas recentes.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Informações de Acesso Condicional

### <a name="overview"></a>Descrição Geral

Os livros contêm consultas de registo de login que podem ajudar os administradores de TI a monitorizar o impacto das políticas de Acesso Condicional no seu inquilino. Tem a capacidade de informar sobre quantos utilizadores teriam sido concedidos ou negados acesso. O livro contém informações sobre quantos utilizadores teriam contornado as políticas de Acesso Condicional com base nos atributos desses utilizadores no momento da s início. Contém detalhes por condição para que o impacto de uma política possa ser contextualizado por condição, incluindo plataforma de dispositivo, estado do dispositivo, app do cliente, risco de login, localização e aplicação.

### <a name="instructions"></a>Instruções 
Para aceder ao livro de dados para Insights de Acesso Condicional, selecione o livro **de Insights de Acesso Condicional** na secção Acesso Condicional. Este livro mostra o impacto esperado de cada política de acesso condicional no seu inquilino. Selecione uma ou mais políticas de Acesso Condicional a partir da lista de abandono e reduza o âmbito do livro aplicando os seguintes filtros: 

- **Intervalo de Tempo**

- **Utilizador**

- **Aplicações**

- **Ver dados**

![A screenshot mostra o painel de acesso condicional onde pode selecionar uma Política de Acesso Condicional.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


O Resumo do Impacto mostra o número de utilizadores ou de inscrições para as quais as políticas selecionadas tiveram um resultado particular. Total é o número de utilizadores ou inscrições para os quais as políticas selecionadas foram avaliadas no intervalo de tempo selecionado. Clique num azulejo para filtrar os dados no livro por esse tipo de resultado. 

![A screenshot mostra azulejos para usar para filtrar resultados como Total, Sucesso e Falha.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Este livro também mostra o impacto das políticas selecionadas discriminadas por cada uma das seis condições: 
- **Estado do dispositivo**
- **Plataforma de dispositivos**
- **Aplicações do cliente**
- **Risco de início de sessão**
- **Localização**
- **Aplicações**

![A screenshot mostra os detalhes do filtro de insusição total.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Também pode investigar as entradas individuais, filtradas pelos parâmetros selecionados no livro. Procure por utilizadores individuais, ordenados pela frequência de entrada e veja os seus eventos de inscrição correspondentes. 

![A screenshot mostra insusouções individuais que pode rever.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Inscrições por controlos de subvenções

Para aceder ao livro de inscrições através de controlos de [concessão,](../conditional-access/controls.md)na secção **Acesso Condicional,** selecione **Iniciar sing-ins por Grant Controls**. 

Este livro mostra as seguintes tendências de inserção de inserção de deficientes:

- Requerer MFA
 
- Pedir os termos de utilização

- Requerer declaração de privacidade

- Outro


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inscrições por controlos de subvenções](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, obtém-se uma avaria por app e protocolo.

![Desagregação de inscrições recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de insucesso de inscrição

Utilize o livro **de análise de falhas de inscrição** para obter erros de resolução de problemas com:

- Inícios de sessão
- Políticas de Acesso Condicional
- Autenticação do legado 


Para aceder aos dados de acesso condicional, na secção **Resolução** de Problemas, selecione **Iniciar sessão utilizando a Autenticação Legacy**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os insus máximos

- Com êxito

- Ação pendente

- Falha


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inscrições de resolução de problemas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudá-lo a resolver os sign-ins, o Azure Monitor dá-lhe uma repartição pelas seguintes categorias:

- Erros de topo

    ![Resumo dos erros de topo](./media/howto-use-azure-monitor-workbooks/53.png)

- Insuposições à espera da ação do utilizador

    ![Resumo dos insusitórios à espera da ação do utilizador](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passos seguintes

[Crie relatórios interativos utilizando livros de monitorização.](../../azure-monitor/visualize/workbooks-overview.md)