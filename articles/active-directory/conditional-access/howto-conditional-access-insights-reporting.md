---
title: Insights de acesso condicional e livro de relatórios - Azure Ative Directory
description: Utilizando os insights de acesso condicional AD Azure e reportando o livro de trabalho para resolver problemas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae802038626a1fbf8d533800a0b8eb43c4565e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574182"
---
# <a name="conditional-access-insights-and-reporting"></a>Informações e relatórios de acesso condicional

Os insights de acesso condicional e o livro de relatórios permitem-lhe compreender o impacto das políticas de Acesso Condicional na sua organização ao longo do tempo. Durante a entrada, podem ser aplicadas uma ou mais políticas de acesso condicional, concedendo acesso se determinados controlos de subvenção estiverem satisfeitos ou negar o acesso de outra forma. Como várias políticas de Acesso Condicional podem ser avaliadas durante cada sindução, os insights e o livro de relatórios permitem examinar o impacto de uma política individual ou um subconjunto de todas as políticas.  

## <a name="prerequisites"></a>Pré-requisitos

Para ativar os insights e relatórios, o seu inquilino deve ter um espaço de trabalho Log Analytics para reter dados de registos de inscrição. Os utilizadores devem ter licenças Azure AD Premium P1 ou P2 para utilizar acesso condicional.

As seguintes funções podem aceder a insights e relatórios:  

- Administrador de acesso condicional 
- Leitor de segurança 
- Administrador de segurança 
- Leitor global 
- Administrador global 

Os utilizadores também precisam de uma das seguintes funções de espaço de trabalho Log Analytics:  

- Contribuinte  
- Proprietário 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Faça streaming de registos de login de Azure AD para registos do Monitor Azure 

Se não tiver integrado registos AD do Azure com registos do Azure Monitor, terá de dar os seguintes passos antes de o livro carregar:  

1. [Criar um espaço de trabalho log analytics no Azure Monitor](../../azure-monitor/logs/quick-create-workspace.md).
1. [Integre os registos AD do Azure com os registos do Monitor Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Como funciona 

Para aceder aos insights e relatórios:  

1. Inicie sessão no **portal do Azure**.
1. Navegue para **Azure Ative**  >  **Directy Security**  >  **Conditional Access** Insights e  >  **reportagens**.

### <a name="get-started-select-parameters"></a>Começar: Selecione parâmetros 

O painel de informação permite-lhe ver o impacto de uma ou mais políticas de Acesso Condicional durante um período determinado. Comece por definir cada um dos parâmetros na parte superior do livro. 

![Insights de acesso condicional e painel de relatórios no portal Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Política de Acesso Condicional**: Selecione uma ou mais políticas de Acesso Condicional para visualizar o seu impacto combinado. As políticas são separadas em dois grupos: Políticas ativadas e apenas para relatórios. Por predefinição, todas as políticas ativadas são selecionadas. Estas políticas habilitadas são as políticas atualmente aplicadas no seu inquilino.  

**Intervalo de tempo**: Selecione um intervalo de tempo de 4 horas até 90 dias. Se selecionar um intervalo de tempo mais para trás do que quando integrou os registos AD do Azure com o Azure Monitor, apenas aparecerão os logins após o tempo de integração.  

**Utilizador:** Por predefinição, o painel de instrumentos mostra o impacto das políticas selecionadas para todos os utilizadores. Para filtrar por um utilizador individual, digite o nome do utilizador no campo de texto. Para filtrar por todos os utilizadores, escreva "Todos os utilizadores" no campo de texto ou deixe o parâmetro vazio. 

**App**: Por padrão, o dashboard mostra o impacto das políticas selecionadas para todas as aplicações. Para filtrar por uma aplicação individual, digite o nome da aplicação no campo de texto. Para filtrar por todas as aplicações, digite "Todas as aplicações" no campo de texto ou deixe o parâmetro vazio. 

**Visualização de dados**: Selecione se deseja que o painel de instrumentos apresente resultados em termos do número de utilizadores ou do número de inserções. Um utilizador individual pode ter centenas de insusagens para muitas aplicações com muitos resultados diferentes durante um determinado intervalo de tempo. Se selecionar a visão de dados para serem utilizadores, um utilizador pode ser incluído tanto nas contagens de Sucesso como de Falha (por exemplo, se existem 10 utilizadores, 8 deles poderiam ter tido resultado de sucesso nos últimos 30 dias e 9 deles poderiam ter tido resultado de falha nos últimos 30 dias).

## <a name="impact-summary"></a>Resumo do impacto 

Uma vez definidos os parâmetros, o resumo do impacto carrega. O resumo mostra quantos utilizadores ou insinusações durante o intervalo de tempo resultaram em "Sucesso", "Falha", "Ação do utilizador necessária" ou "Não aplicada" quando as políticas selecionadas foram avaliadas.  

![Resumo do impacto no livro de acesso condicional](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: O número de utilizadores ou de inscrições durante o período em que pelo menos uma das políticas selecionadas foi avaliada.

**Sucesso**: O número de utilizadores ou de inscrições durante o período em que o resultado combinado das políticas selecionadas foi "Sucesso" ou "Relatório apenas: Sucesso".

**Falha**: O número de utilizadores ou de inscrições durante o período em que o resultado de pelo menos uma das políticas selecionadas foi "Falha" ou "Relatório apenas: Falha".

**Ação do utilizador necessária**: O número de utilizadores ou de inscrições durante o período em que o resultado combinado das políticas selecionadas foi "Report-only: User action required". A ação do utilizador é necessária quando um controlo interativo de subvenções, como a autenticação de vários fatores, é exigido por uma política de acesso condicional apenas para relatórios. Uma vez que os controlos interativos das subvenções não são aplicados por políticas apenas de relatório, o sucesso ou o fracasso não podem ser determinados.  

**Não aplicados**: O número de utilizadores ou inscrições durante o período em que nenhuma das políticas selecionadas foi aplicada.

### <a name="understanding-the-impact"></a>Compreender o impacto 

![Avaria do livro por condição e estado](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Ver a avaria dos utilizadores ou inscrições para cada uma das condições. Pode filtrar as entradas de um resultado específico (por exemplo, Sucesso ou Falha) selecionando os azulejos sumários na parte superior do livro. Pode ver a desagregação de logins para cada uma das condições de Acesso Condicional: estado do dispositivo, plataforma do dispositivo, aplicação do cliente, localização, aplicação e risco de inscrição.  

## <a name="sign-in-details"></a>Detalhes de início de sessão 

![Detalhes de inscrição no livro](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Também pode investigar as entradas de um utilizador específico, procurando insus máximos na parte inferior do painel de instrumentos. A consulta à esquerda mostra os utilizadores mais frequentes. Selecionar um utilizador filtrará a consulta à direita.  

> [!NOTE]
> Ao descarregar os registos de login, escolha o formato JSON para incluir dados de resultados do relatório do Acesso Condicional.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Configure uma política de acesso condicional no modo apenas de relatório

Para configurar uma política de acesso condicional no modo apenas de relatório:

1. Inscreva-se no **portal Azure** como administrador de acesso condicional, administrador de segurança ou administrador global.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione uma política existente ou crie uma nova política.
1. No **modo "Ativar"** o alternância para o modo **apenas de relatório.**
1. Selecione **Guardar**

> [!TIP]
> A edição do estado **político de Enable** de uma política existente de **On** to **Report-only** desativa a aplicação de políticas existente. 

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Porque é que as consultas estão a falhar devido a um erro de permissões?

Para aceder ao livro, precisa das permissões adequadas do AD Azure, bem como das permissões do espaço de trabalho log Analytics. Para testar se tem as permissões adequadas do espaço de trabalho executando uma consulta de análise de registo de amostras:

1. Inicie sessão no **portal do Azure**.
1. Navegue por Registos **de Diretório Ativo Azure**  >  .
1. Digite `SigninLogs` na caixa de consulta e selecione **Executar**.
1. Se a consulta não devolver quaisquer resultados, o seu espaço de trabalho pode não ter sido configurado corretamente. 

![Consultas falhadas de resolução de problemas](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Para obter mais informações sobre como transmitir registos de login AD AZure para um espaço de trabalho Log Analytics, consulte o artigo [Integre os registos AD do Azure com registos do Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Porque é que as consultas no livro estão a falhar?

Os clientes notaram que as consultas às vezes falham se os espaços de trabalho errados ou múltiplos estiverem associados ao livro. Para corrigir este problema, clique em **Editar** na parte superior do livro e, em seguida, na engrenagem Definições. Selecione e, em seguida, remova espaços de trabalho que não estejam associados ao livro. Deve haver apenas um espaço de trabalho associado a cada livro.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Porque é que o parâmetro das políticas de acesso condicional está vazio?

A lista de políticas é gerada olhando para as políticas avaliadas para o evento de inscrição mais recente. Se não houver inscrições recentes no seu inquilino, poderá ter de esperar alguns minutos para que o livro carregue a lista de políticas de Acesso Condicional. Isto pode acontecer imediatamente após configurar o Log Analytics ou pode demorar mais tempo se um inquilino não tiver atividade de login recente.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Porque é que o livro está a demorar muito tempo a carregar?  

Dependendo do intervalo de tempo selecionado e do tamanho do seu inquilino, o livro pode estar a avaliar um número extraordinariamente grande de eventos de inscrição. Para os grandes inquilinos, o volume de logins pode exceder a capacidade de consulta da Log Analytics. Tente encurtar o intervalo de tempo para 4 horas para ver se o livro está carregado.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Depois de carregar por alguns minutos, porque é que o livro está a devolver zero resultados? 

Quando o volume de logins exceder a capacidade de consulta do Log Analytics, o livro devolverá zero resultados. Tente encurtar o intervalo de tempo para 4 horas para ver se o livro está carregado.  

### <a name="can-i-save-my-parameter-selections"></a>Posso guardar as minhas seleções de parâmetros?  

Pode guardar as suas seleções de parâmetros no topo do livro, indo ao **Azure Ative Directory**  >  **Workbooks**  >  **Conditional Access Insights e reportando**. Aqui encontrará o modelo do livro, onde pode editar o livro e guardar uma cópia para o seu espaço de trabalho, incluindo as seleções de parâmetros, nos **meus relatórios** ou **relatórios partilhados.** 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Posso editar e personalizar o livro com consultas adicionais? 

Pode editar e personalizar o livro indo ao **Azure Ative Directory**  >  **Workbooks**  >  **Conditional Access Insights e reportando**. Aqui encontrará o modelo do livro, onde pode editar o livro e guardar uma cópia para o seu espaço de trabalho, incluindo as seleções de parâmetros, nos **meus relatórios** ou **relatórios partilhados.** Para começar a editar as consultas, clique em **Editar** no topo do livro.  
 
## <a name="next-steps"></a>Passos seguintes

- [Modo de relatório de acesso condicional](concept-conditional-access-report-only.md)

- Para obter mais informações sobre os livros AZURE AD, consulte o artigo, [Como utilizar os livros do Azure Monitor para relatórios do Azure Ative Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
