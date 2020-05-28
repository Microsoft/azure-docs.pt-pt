---
title: Informações de Acesso Condicional e livro de reporte - Diretório Ativo Azure
description: Utilização dos insights de Acesso Condicional da AD Azure e livro de reporte para políticas de resolução de problemas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2b4864232892f1940de5c8fe46fa23879c92f3
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112780"
---
# <a name="conditional-access-insights-and-reporting"></a>Insights e relatórios de acesso condicional

O livro de informação de Acesso Condicional e de reporte permite-lhe compreender o impacto das políticas de Acesso Condicional na sua organização ao longo do tempo. Durante o registo, pode aplicar-se uma ou mais políticas de Acesso Condicional, concedendo acesso se determinados controlos de subvenção estiverem satisfeitos ou se negarem o acesso de outra forma. Como várias políticas de Acesso Condicional podem ser avaliadas durante cada inscrição, os insights e o livro de reporte permitem examinar o impacto de uma política individual ou um subconjunto de todas as políticas.  

## <a name="prerequisites"></a>Pré-requisitos

Para ativar os insights e o livro de reporte, o seu inquilino deve ter um espaço de trabalho log Analytics para reter os dados de log-in. Os utilizadores devem ter licenças Azure AD Premium P1 ou P2 para utilizar o Acesso Condicional.

As seguintes funções podem aceder a insights e relatórios:  

- Administrador de Acesso Condicional 
- Leitor de segurança 
- Administrador de segurança 
- Leitor global 
- Administrador global 

Os utilizadores também precisam de uma das seguintes funções do espaço de trabalho Log Analytics:  

- Contribuinte  
- Proprietário 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Registos de entrada de fluxo de registos de AD Azure para registos do Monitor Azure 

Se não tiver integrado os registos da AD Azure com registos do Monitor Azure, terá de tomar os seguintes passos antes de o livro carregar:  

1. [Crie um espaço de trabalho de Log Analytics no Monitor Azure.](../../azure-monitor/learn/quick-create-workspace.md)
1. [Integre os registos de AD Azure com registos do Monitor Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Como funciona 

Para aceder aos insights e relatórios:  

1. Inicie sessão no **portal do Azure**.
1. Navegue até **azure Ative Directy**  >  **Security**  >  **Conditional Access**Insights e  >  **reporte.**

### <a name="get-started-select-parameters"></a>Começar: Selecione parâmetros 

Os insights e relatórios permitem-lhe ver o impacto de uma ou mais políticas de Acesso Condicional durante um período determinado. Comece por colocar cada um dos parâmetros no topo do livro. 

![Insights de acesso condicional e painel de reporte no portal Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Política**de Acesso Condicional : Selecione uma ou mais políticas de Acesso Condicional para ver o seu impacto combinado. As políticas são separadas em dois grupos: políticas habilitadas e apenas de relatório. Por predefinição, todas as políticas Ativadas são selecionadas. Estas políticas ativadas são as políticas atualmente aplicadas no seu inquilino.  

**Intervalo**de tempo: Selecione um intervalo de tempo de 4 horas a 90 dias. Se selecionar um intervalo de tempo mais para trás do que quando integrou os registos da AD Azure com o Monitor Azure, apenas aparecerão os registos após o momento da integração.  

**Utilizador**: Por padrão, o painel de instrumentos mostra o impacto das políticas selecionadas para todos os utilizadores. Para filtrar por um utilizador individual, digite o nome do utilizador no campo de texto. Para filtrar por todos os utilizadores, digite "Todos os utilizadores" no campo de texto ou deixe o parâmetro vazio. 

**App**: Por padrão, o dashboard mostra o impacto das políticas selecionadas para todas as aplicações. Para filtrar por uma aplicação individual, digite o nome da aplicação no campo de texto. Para filtrar todas as aplicações, digite "Todas as aplicações" no campo de texto ou deixe o parâmetro vazio. 

**Visualização de dados**: Selecione se pretende que o dashboard mostre resultados em termos do número de utilizadores ou número de inscrições. Um utilizador individual pode ter centenas de inscrições para muitas aplicações com muitos resultados diferentes durante um determinado intervalo de tempo. Se selecionar a visão de dados para serem utilizadores, um utilizador poderia ser incluído nas contagens de Sucesso e Falha (por exemplo, se houver 10 utilizadores, 8 deles poderiam ter tido um resultado de sucesso nos últimos 30 dias e 9 deles poderiam ter tido resultado de falhas nos últimos 30 dias).

## <a name="impact-summary"></a>Resumo do impacto 

Uma vez definidos os parâmetros, o resumo do impacto carrega. O resumo mostra quantos utilizadores ou inscrições durante o intervalo de tempo resultaram em "Sucesso", "Falha", "Ação do Utilizador necessária" ou "Não aplicada" quando as políticas selecionadas foram avaliadas.  

![Resumo de impacto no livro de acesso condicional](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: O número de utilizadores ou de inscrições durante o período em que pelo menos uma das políticas selecionadas foi avaliada.

**Sucesso**: O número de utilizadores ou inscrições durante o período em que o resultado combinado das políticas selecionadas foi "Sucesso" ou "Report-only: Success".

**Falha**: O número de utilizadores ou de inscrições durante o período em que o resultado de pelo menos uma das políticas selecionadas foi "Falha" ou "apenas relatório:Falha".

**Ação do utilizador necessária**: O número de utilizadores ou de sessão de sessão durante o período em que o resultado combinado das políticas selecionadas foi "apenas relatório: ação do utilizador necessária". A ação do utilizador é necessária quando um controlo de subvenções interativo, como a autenticação de vários fatores, é exigido por uma política de acesso condicional apenas para relatórios. Uma vez que os controlos de subvenções interativos não são aplicados por políticas apenas de relatório, o sucesso ou o fracasso não podem ser determinados.  

**Não aplicado**: O número de utilizadores ou de inscrições durante o período em que nenhuma das políticas selecionadas foi aplicada.

### <a name="understanding-the-impact"></a>Compreender o impacto 

![Repartição do livro por condição e estado](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Consulte a avaria dos utilizadores ou os insames para cada uma das condições. Pode filtrar os sign-ins de um resultado específico (por exemplo, Sucesso ou Falha) selecionando os azulejos resumidos na parte superior do livro. Pode ver a avaria dos logins para cada uma das condições de Acesso Condicional: estado do dispositivo, plataforma do dispositivo, aplicação de cliente, localização, aplicação e risco de login.  

## <a name="sign-in-details"></a>Detalhes de sessão 

![Detalhes de inscrição no livro](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Também pode investigar os sign-ins de um utilizador específico, procurando inscrições na parte inferior do painel de instrumentos. A consulta à esquerda apresenta os utilizadores mais frequentes. A seleção de um utilizador filtrará a consulta para a direita.  

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Porque é que as consultas estão a falhar devido a um erro de permissões?

Para aceder ao livro, necessita das permissões de AD Azure adequadas, bem como de permissões no espaço de trabalho log Analytics. Para testar se tem as permissões adequadas do espaço de trabalho executando uma consulta de análise de registo de amostras:

1. Inicie sessão no **portal do Azure**.
1. Navegue nos registos **de diretórios ativos do Azure.**  >  **Logs**
1. Digite `SigninLogs` na caixa de consulta e selecione **Executar**.
1. Se a consulta não devolver quaisquer resultados, o seu espaço de trabalho pode não ter sido configurado corretamente. 

![Problemas falhando consultas](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Para obter mais informações sobre como transmitir registos de entrada de AD Azure para um espaço de trabalho de Log Analytics, consulte os [registos da AD Azure com registos do Monitor Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Porque é que o parâmetro das políticas de acesso condicional está vazio?

A lista de políticas é gerada olhando para as políticas avaliadas para o mais recente evento de inscrição. Se não houver inscrições recentes no seu inquilino, poderá ter de esperar alguns minutos para que o livro de trabalho carregue a lista de políticas de Acesso Condicional. Isto pode acontecer imediatamente após configurar o Log Analytics ou pode demorar mais tempo se um inquilino não tiver atividade de login recente.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Porque é que o livro está a demorar muito tempo a carregar?  

Dependendo do intervalo de tempo selecionado e do tamanho do seu inquilino, o livro de trabalho pode estar a avaliar um número extraordinariamente grande de eventos de inscrição. Para os grandes inquilinos, o volume de inscrições pode exceder a capacidade de consulta do Log Analytics. Tente encurtar o intervalo de tempo para 4 horas para ver se o livro carrega.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Depois de carregar por alguns minutos, porque é que o livro está a devolver zero resultados? 

Quando o volume de inscrições exceder a capacidade de consulta do Log Analytics, o livro devolverá zero resultados. Tente encurtar o intervalo de tempo para 4 horas para ver se o livro carrega.  

### <a name="can-i-save-my-parameter-selections"></a>Posso salvar as minhas seleções de parâmetros?  

Pode guardar as suas seleções de parâmetros no topo do livro, indo para **o Azure Ative Directory**  >  **Workbooks**  >  **Diretório Sintetizador**Insights de Acesso Condicional insights e reportagens . Aqui encontrará o modelo do livro, onde pode editar o livro de trabalho e guardar uma cópia para o seu espaço de trabalho, incluindo as seleções de parâmetros, em **Meus relatórios** ou **relatórios partilhados.** 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Posso editar e personalizar o livro com consultas adicionais? 

Pode editar e personalizar o livro de trabalho indo para os Livros de Acesso Condicional do **Diretório Ativo Azure**  >  **Workbooks**  >  **e reportando**. Aqui encontrará o modelo do livro, onde pode editar o livro de trabalho e guardar uma cópia para o seu espaço de trabalho, incluindo as seleções de parâmetros, em **Meus relatórios** ou **relatórios partilhados.** Para começar a editar as consultas, clique em **Editar** no topo do livro.  
 
## <a name="next-steps"></a>Próximos passos

[Modo de relatório de acesso condicional](concept-conditional-access-report-only.md)
