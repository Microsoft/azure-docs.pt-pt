---
title: Configure o assistente de análise de registo em Azure AD | Microsoft Docs
description: Aprenda a configurar a análise de registos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574376"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Tutorial: Configurar o assistente de análise de registo


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure um espaço de trabalho de análise de registo para a sua auditoria e registos de login
> * Executar consultas utilizando a língua de consulta kusto (KQL)
> * Crie uma regra de alerta que envia alertas quando uma conta específica é usada
> * Crie um livro personalizado usando o modelo de arranque rápido
> * Adicione uma consulta a um modelo de livro existente

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura Azure com pelo menos um administrador licenciado P1. Se não tiver uma subscrição do Azure, pode [inscrever-se para um teste gratuito](https://azure.microsoft.com/free/).

- Um inquilino do Azure AD.

- Um utilizador que seja administrador global ou administrador de segurança do inquilino do Azure AD.


Familiarize-se com estes artigos:

- [Tutorial: Recolher e analisar registos de recursos a partir de um recurso Azure](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Como integrar registos de atividades com Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Gerir conta de acesso de emergência em Azure AD](../roles/security-emergency-access.md)

- [Referência rápida de KQL](/azure/data-explorer/kql-quick-reference)

- [Livros de trabalho do Monitor Azure](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Configure um espaço de trabalho 

Este procedimento descreve como configurar um espaço de trabalho de análise de registo para a sua auditoria e registos de login.
Configurar um espaço de trabalho de análise de log é composto por dois passos principais:
 
1. Criação de um espaço de trabalho de analítica de log
2. Definição de definições de diagnóstico

**Para configurar um espaço de trabalho:** 


1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Procure **espaços de trabalho de análise de registo.**

    ![Serviços de recursos de pesquisa e docs](./media/tutorial-log-analytics-wizard/search-services.png)

3. Na página de espaços de trabalho de analítica de registo, clique em **Adicionar**.

    ![A screenshot mostra o botão Adicionar na página de espaços de trabalho de analítica de registo.](./media/tutorial-log-analytics-wizard/add.png)

4.  Na página do **espaço de trabalho Create Log Analytics,** execute os seguintes passos:

    ![Criar uma área de trabalho do Log Analytics](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Selecione a sua subscrição.

    2. Selecione um grupo de recursos.
 
    3. Na caixa de texto **Name,** escreva um nome (por exemplo: MytestWorkspace1).

    4. Selecione a sua região.

5. Clique em **Rever + Criar**.

    ![Rever e criar](./media/tutorial-log-analytics-wizard/review-create.png)

6. Clique **em Criar** e aguarde que a implementação seja bem sucedida. Poderá ser necessário refrescar a página para ver o novo espaço de trabalho.

    ![Criar](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Procure **Azure Active Directory**.

    ![A screenshot mostra Azure Ative Directory em pesquisa Azure.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. Na secção **de Monitorização,** clique na **definição de Diagnóstico**.

    ![A screenshot mostra as definições de diagnóstico selecionadas a partir da Monitorização.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Na página de **definições de Diagnóstico,** clique **na definição de diagnóstico de adicionar**.

    ![Adicionar definição de diagnóstico](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Na página de **definição de Diagnóstico,** execute os seguintes passos:

    ![Selecione definições de diagnóstico](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. Em **detalhes de categoria**, selecione **AuditLogs** e **SigninLogs**.

    2. Em **Detalhes do Destino**, selecione Enviar para Registar **Analytics** e, em seguida, selecione o seu novo espaço de trabalho de analítica de registo. 
   
    3. Clique em **Guardar**. 

## <a name="run-queries"></a>Executar consultas  

Este procedimento mostra como executar consultas utilizando a **língua de consulta de Kusto (KQL)**.


**Para fazer uma consulta:**


1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Procure **Azure Active Directory**.

    ![A screenshot mostra Azure Ative Directory em pesquisa Azure.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na secção **'Monitorização',** clique em **'Registos'.**

4. Na página **'Logs',** clique em **'Começar'.**

5. Na caixa de texto **Pesquisar,* digite a sua consulta.

6. Clique em **Run** (Executar).  


### <a name="kql-query-examples"></a>Exemplos de consulta KQL

Faça 10 entradas aleatórias a partir dos dados de entrada:

`SigninLogs | take 10`

Veja os sign-ins onde o Acesso Condicional foi um sucesso

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Conte quantos sucessos houve

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Contagem agregada de insusição bem sucedida por utilizador por dia:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Ver quantas vezes um utilizador faz uma determinada operação num determinado período de tempo específico:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Pivô dos resultados no nome da operação

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Misture auditoria e sinal em registos usando uma junção interior:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Ver o número de sinais por tipo de aplicação do cliente:

`SigninLogs | summarize count() by ClientAppUsed`

Conte as entradas de sinal de dia:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Faça 5 entradas aleatórias e projete as colunas que deseja ver nos resultados:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Pegue o top 5 em ordem descendente e projete as colunas que deseja ver

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Criar uma nova coluna combinando os valores a duas outras colunas:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Criar uma regra de alerta  

Este procedimento mostra como enviar alertas quando a conta breakglass é utilizada.

**Para criar uma regra de alerta:**


1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Procure **Azure Active Directory**.

    ![A screenshot mostra Azure Ative Directory em pesquisa Azure.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na secção **'Monitorização',** clique em **'Registos'.**

4. Na página **'Logs',** clique em **'Começar'.**

5. Na caixa de texto **procurar,** escreva: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Clique em **Run** (Executar).  

7. Na barra de ferramentas, clique em **Nova regra de alerta**.

    ![Nova regra de alerta](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Na página **'Criar' regra de alerta,** verifique se o âmbito está correto.

9. Em **Condição,** clique: **Sempre que a pesquisa média de registo personalizado for maior do que a <logic undefined> contagem**

    ![Condição padrão](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Na página lógica de **sinal configurado,** na secção **lógica alerta,** execute os seguintes passos:

    ![Lógica de alerta](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Como **baseado em**, selecione Número de **resultados**.

    2. Como **Operador,** selecione **Maior que**.

    3. Como **valor limiar,** selecione **0**. 

11. Na página lógica de **sinal de configuração,** na secção **Avaliada com base na** secção, execute os seguintes passos:

    ![Avaliado com base em](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Como **Período (em minutos)**, selecione **5**.

    2. Como **Frequência (em minutos)**, selecione **5**.

    3. Clique em **Concluído**. 

12. No **grupo Action**, clique em Select action **group**. 

    ![Grupo de ações](./media/tutorial-log-analytics-wizard/action-group.png)

13. No **Select um grupo de ação para anexar a esta regra de alerta,** clique em **Criar grupo de ação**. 

    ![Criar grupo de ações](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Na página do **grupo de ação Create,** execute os seguintes passos:

    ![Detalhes da instância](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Na caixa de texto **do nome do grupo Action,** **digite O meu grupo de ação**.

    2. Na caixa de texto **do nome do Visor,** **digite a minha ação**.

    3. Clique em **Rever + criar**. 

    4. Clique em **Criar**.


15. Em **ação personalizada,** execute os seguintes passos:

    ![Personalizar ações](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Selecione **e-mail assunto**.

    2. Na caixa de texto da **linha de assunto,** escreva: `Breakglass account has been used`

16. Em **detalhes da regra de alerta,** execute os seguintes passos:

    ![Detalhes da regra de alerta](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Na caixa de texto **do nome da regra de alerta,** escreva: `Breakglass account`

    2. Na caixa de texto **Descrição,** escreva: `Your emergency access account has been used`

17. Clique em **Criar regra de alerta**.   


## <a name="create-a-custom-workbook"></a>Criar um livro personalizado

Este procedimento mostra como criar um novo livro utilizando o modelo de arranque rápido.




1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Procure **Azure Active Directory**.

    ![A screenshot mostra Azure Ative Directory em pesquisa Azure.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na secção **de Monitorização,** clique em **Livros de Trabalho.**

    ![Screenshot mostra monitorização no menu do portal Azure com livros de trabalho selecionados.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Na secção **Quickstart,** clique em **Empty**.

    ![Início rápido](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Clique em **Adicionar**.

    ![Adicionar livro](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Clique **em Adicionar texto**.

    ![Adicionar texto](./media/tutorial-log-analytics-wizard/add-text.png)


7. Na caixa de texto, escreva: `# Client apps used in the past week` e, em seguida, clique em **Edição De Feito**.

    ![Texto do livro](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. No novo livro, clique em **Adicionar** e, em seguida, clique em **Adicionar consulta**.

    ![Adicionar consulta](./media/tutorial-log-analytics-wizard/add-query.png)

9. Na caixa de texto de consulta, escreva: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Clique **em 'Executar Consulta' ( 'Executar'.**

    ![A screenshot mostra o botão de consulta de execução.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Na barra de ferramentas, em **Visualização,** clique na **tabela Pie**.

    ![Gráfico circular](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Clique **em Editar.**

    ![Feito a edição](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Adicione uma consulta a um modelo de livro  

Este procedimento mostra como adicionar uma consulta a um modelo de livro existente. O exemplo baseia-se numa consulta que mostra a distribuição do sucesso de acesso condicional a falhas.


1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Procure **Azure Active Directory**.

    ![A screenshot mostra Azure Ative Directory em pesquisa Azure.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na secção **de Monitorização,** clique em **Livros de Trabalho.**

    ![A screenshot mostra monitorização no menu com livros de trabalho selecionados.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Na secção de **acesso condicional,** clique em **Insights de Acesso Condicional e Relatórios**.

    ![A screenshot mostra a opção De Acesso Condicional e a opção De Relatório.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Na barra de ferramentas, clique em **Editar.**

    ![A imagem mostra o botão Editar.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Na barra de ferramentas, clique nos três pontos, em seguida, **Adicione**, e, em seguida, **Adicione consulta**.

    ![Adicionar consulta de livro](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Na caixa de texto de consulta, escreva: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Clique **em 'Executar Consulta' ( 'Executar'.**

    ![A screenshot mostra o botão ''Fazer's para executar esta consulta.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Clique **no intervalo de tempo** e, em seguida, selecione Definir em **consulta**.

10. Clique **em Visualização** e, em seguida, selecione **gráfico de Barras**. 

11. Clique em **Definições Avançadas**, como título de gráfico, escreva `Conditional Access status over the last 20 days` e, em seguida, clique em **Edição Feita**. 

    ![Conjunto de título de gráfico](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a gerir as identidades dos dispositivos utilizando o portal Azure.
> [!div class="nextstepaction"]
> [Monitorização](overview-monitoring.md)