---
title: Livros azure Monitor para relatórios Microsoft Docs
description: Saiba como utilizar os livros de trabalho do Azure Monitor para relatórios de Diretório Ativo Azure.
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
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014367"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como utilizar os livros de trabalho do Azure Monitor para relatórios de Diretório Ativo Azure

> [!IMPORTANT]
> Para otimizar as consultas subjacentes neste livro, clique em "Editar", clique no ícone Definições e selecione o espaço de trabalho onde pretende executar estas consultas. Os livros de trabalho por padrão selecionarão todos os espaços de trabalho onde estiver a encaminhar os seus registos de AD Azure. 

Quer:

- Compreender o efeito das suas políticas de [Acesso Condicional](../conditional-access/overview.md) na experiência de inscrição dos seus utilizadores?

- Falhas de sessão de problemas para ter uma melhor visão da saúde da sua organização e resolver os problemas rapidamente?

- Sabe quem está a usar autenticações antigas para se inscrever no seu ambiente? [(Bloqueando a autenticação do legado,](../conditional-access/block-legacy-authentication.md)pode melhorar a proteção do seu inquilino.)

- Precisa entender o impacto das políticas de Acesso Condicional no seu inquilino?

- Gostaria de rever: consultas de login, o livro informa quantos utilizadores receberam ou não tiveram acesso, bem como quantos utilizadores contornaram as políticas de Acesso Condicional ao aceder aos recursos?

- Interessado em desenvolver uma compreensão mais profunda de: os detalhes do livro por condição para que o impacto de uma apólice possa ser contextualizado por condição, incluindo plataforma de dispositivos, estado do dispositivo, app do cliente, risco de entrada, localização e aplicação?

- Obtenha informações mais profundas sobre consultas de login, o livro informa quantos utilizadores receberam ou negaram o acesso, bem como quantos utilizadores contornaram as políticas de Acesso Condicional ao aceder aos recursos.

- Para ajudá-lo a resolver estas questões, o Ative Directy fornece livros de monitorização. [Os livros Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinam texto, consultas de análise, métricas e parâmetros em relatórios interativos ricos.



Este artigo:

- Assume que está familiarizado com a forma [de criar relatórios interativos utilizando livros](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)monitor .

- Explica como usar os livros monitor para compreender o efeito das suas políticas de Acesso Condicional, para resolver falhas de registo e identificar autenticações antigas.
 


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os livros monitor, precisa de:

- Um inquilino de Diretório Ativo com licença premium (P1 ou P2). Saiba como [obter uma licença premium.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)

- Um [espaço de trabalho log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Acesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) ao espaço de trabalho de análise de registo
- Seguindo funções no Diretório Ativo Azure (se estiver a aceder ao Log Analytics através do portal de diretório ativo Azure)
    - Administrador de segurança
    - Leitor de segurança
    - Leitor de relatórios
    - Administrador global

## <a name="roles"></a>Funções
Deve estar numa das seguintes funções, bem como ter acesso ao espaço de trabalho [subjacente ao Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) para gerir os livros de trabalho:
-   Administrador global
-   Administrador de segurança
-   Leitor de segurança
-   Leitor de relatórios
-   Administrador de candidatura

## <a name="workbook-access"></a>Acesso a livro 

Para aceder a livros:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue para **o Azure Ative Directory** > **Monitoring** > **Books**. 

1. Selecione um relatório ou modelo, ou na barra de ferramentas **selecione Open**. 

![Encontre os livros azure monitor em Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Análise de insessão

Para aceder ao livro de trabalho de análise de sessão, na secção **Utilização,** selecione **Sign-ins**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os inscrições

- Êxito

- Ação pendente do utilizador

- Falha

Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Análise de insessão](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, obtém uma desagregação pelas seguintes categorias:

- Localização

    ![Inscrições por localização](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Inscrições por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inscrições usando autenticação legado 


Para aceder ao livro de trabalho para inscrições que utilizem [a autenticação do legado,](../conditional-access/block-legacy-authentication.md)na secção **Utilização,** selecione **Sign-ins utilizando a Autenticação Legacy**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os inscrições

- Êxito


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

- Protocolos

![Inscrições por autenticação legado](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, obtém-se uma avaria por app e protocolo.

![Inscrições de autenticação legado por app e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inscrições por Acesso Condicional 


Para aceder ao livro de trabalho para inscrições por políticas de [Acesso Condicional,](../conditional-access/overview.md)na secção **acesso condicional,** selecione **Sign-ins por Acesso Condicional**. 

Este livro mostra as tendências para os inscrições para deficientes. Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inícios de Sessão com Acesso Condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para inscrições desativadas, obtém uma avaria pelo estado de Acesso Condicional.

![Estado de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Informações de Acesso Condicional

### <a name="overview"></a>Descrição geral

Os livros de trabalho contêm consultas de log in sessão que podem ajudar os administradores de TI a monitorizar o impacto das políticas de Acesso Condicional no seu inquilino. Tem a capacidade de informar quantos utilizadores teriam sido concedidos ou negados acessos. O livro contém informações sobre quantos utilizadores teriam ignorado as políticas de Acesso Condicional com base nos atributos desses utilizadores no momento do início de sessão. Contém detalhes por condição para que o impacto de uma apólice possa ser contextualizado por condição, incluindo plataforma de dispositivos, estado do dispositivo, aplicação do cliente, risco de login, localização e aplicação.

### <a name="instructions"></a>Instruções 
Para aceder ao livro de trabalho para Insights de Acesso Condicional, selecione o livro de informação de **acesso condicional** insights na secção acesso condicional. Este livro mostra o impacto esperado de cada política de Acesso Condicional no seu inquilino. Selecione uma ou mais políticas de Acesso Condicional da lista de abandono escolar e reduza o âmbito do livro aplicando os seguintes filtros: 

- **Intervalo de Tempo**

- **Utilizador**

- **Aplicações**

- **Vista de dados**

![Estado de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/access-insights.png)


O Resumo de Impacto mostra o número de utilizadores ou inscrições para os quais as políticas selecionadas tiveram um resultado particular. Total é o número de utilizadores ou inscrições para os quais as políticas selecionadas foram avaliadas na Faixa de Tempo selecionada. Clique num azulejo para filtrar os dados do livro por esse tipo de resultado. 

![Estado de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Este livro também mostra o impacto das políticas selecionadas discriminadas por cada uma das seis condições: 
- **Estado do dispositivo**
- **Plataforma de dispositivos**
- **Aplicações do cliente**
- **Risco de inscrição**
- **Localização**
- **Aplicações**

![Estado de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Também pode investigar inscrições individuais, filtradas pelos parâmetros selecionados no livro. Procure por utilizadores individuais, classificados por frequência de sessão e veja os seus eventos de inscrição correspondentes. 

![Estado de Acesso Condicional](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Inscrições por controlo de subvenções

Para aceder ao livro de trabalho para inscrições por controlo de [subvenção,](../conditional-access/controls.md)na secção **acesso condicional,** selecione **Sign-ins by Grant Controls**. 

Este livro mostra as seguintes tendências de inscrição para deficientes:

- Requerer MFA
 
- Pedir os termos de utilização

- Exigir declaração de privacidade

- Outros


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inscrições por controlo de subvenções](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, obtém-se uma avaria por app e protocolo.

![Desagregação de inscrições recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de insalo

Utilize o livro de análise de falhas de **insucesso do Sign-ins** para resolver erros com:

- Inícios de sessão
- Políticas de Acesso Condicional
- Autenticação legado 


Para aceder aos dados de acesso condicional, na secção **Troubleshoot,** selecione **Sign-ins utilizando a Autenticação Legacy**. 

Este livro mostra as seguintes tendências de inscrição:

- Todos os inscrições

- Êxito

- Ação pendente

- Falha


Pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de tempo

- Aplicações

- Utilizadores

![Inscrições de resolução de problemas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudá-lo a resolver os seus sign-ins, o Azure Monitor dá-lhe uma avaria pelas seguintes categorias:

- Erros de topo

    ![Resumo dos erros de topo](./media/howto-use-azure-monitor-workbooks/53.png)

- Inscrições à espera da ação do utilizador

    ![Resumo dos inscrições à espera da ação do utilizador](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passos seguintes

[Crie relatórios interativos utilizando livros](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)monitor .
