---
title: Monitorizar a saúde de inscrição para resiliência no Diretório Ativo do Azure
description: Crie consultas e notificações para monitorizar a saúde de inscrição das suas aplicações.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad99c8d319a22f8b5388838b9d537de2f610478a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650996"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Monitorização da saúde de inscrição do pedido de resiliência

Para aumentar a resiliência da infraestrutura, crie a monitorização da saúde de inscrição da aplicação para as suas aplicações críticas para que receba um alerta caso ocorra um incidente de impacto. Para ajudá-lo neste esforço, pode configurar alertas com base no livro de saúde de inscrição. 

Este livro permite aos administradores monitorizar pedidos de autenticação de pedidos no seu inquilino. Fornece estas capacidades-chave:

* Configure o livro para monitorizar todas ou aplicações individuais com dados quase em tempo real.

* Configure alertas para notificá-lo quando os padrões de autenticação mudarem para que possa investigar e tomar medidas.

* Compare as tendências durante um período, por exemplo, semana após semana, que é a definição padrão do livro.

> [!NOTE]
> Para ver todos os livros disponíveis e os pré-requisitos para a sua utilização, consulte [como utilizar os livros do Azure Monitor para obter relatórios.](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

Durante um evento de impacto, duas coisas podem acontecer:

* O número de inscrições para uma aplicação pode cair de forma precipitada porque os utilizadores não podem fazer o súplic.

* O número de falhas de inscrição pode aumentar. 

Este artigo percorre a criação do livro de saúde de inscrição para monitorizar as interrupções nas inscrições dos seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos 

* Um inquilino do Azure AD.

* Um utilizador com papel de administrador global ou administrador de segurança para o inquilino Azure AD.

* Um espaço de trabalho log Analytics na sua subscrição Azure para enviar registos para registos do Azure Monitor. 

   * Saiba como [criar um espaço de trabalho Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

* Registos AD AD azure integrados com registos do Azure Monitor

   * Saiba como integrar o [sinal AD Ad Azure em Logs com Azure Monitor Stream.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

 

## <a name="configure-the-app-sign-in-health-workbook"></a>Configurar o sinal da App no livro de saúde 

Para aceder aos livros, abra o **portal Azure,** selecione **Azure Ative Directory** e, em seguida, selecione **Livros de Trabalho**.

Você verá livros de trabalho em Uso, Acesso Condicional e Resolução de Problemas. O sinal de aplicação no livro de saúde aparece na secção de utilização.

Uma vez utilizado um livro, pode aparecer na secção de livros recentemente modificados.

![Screenshot mostrando a galeria de livros no portal Azure.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


O sinal da App no livro de saúde permite-lhe visualizar o que está a acontecer com as suas entradas. 

Por predefinição, o livro apresenta dois gráficos. Estes gráficos comparam o que está a acontecer com as suas aplicações agora, em comparação com o mesmo período de há uma semana. As linhas azuis estão em corrente, e as linhas laranja são na semana anterior.

![Screenshot mostrando sinal em gráficos de saúde.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**O primeiro gráfico é a utilização horária (número de utilizadores bem sucedidos)**. Comparar o seu número atual de utilizadores bem sucedidos com um período de utilização típico ajuda-o a detetar uma queda no uso que pode exigir uma investigação. Uma queda na taxa de utilização bem sucedida pode ajudar a detetar problemas de desempenho e utilização que a taxa de falha não consegue. Por exemplo, se os utilizadores não conseguirem chegar à sua aplicação para tentar iniciar sação, não haverá falhas, apenas uma queda no uso. Uma consulta de amostra para estes dados pode ser encontrada na secção seguinte.

O segundo gráfico é a taxa de falha horária. Um aumento na taxa de avaria pode indicar um problema com os seus mecanismos de autenticação. A taxa de avaria só pode ser medida se os utilizadores puderem tentar autenticar. Se os utilizadores não conseguirem aceder à tentativa, as falhas não aparecerão.

Pode configurar um alerta que notifica um grupo específico quando a taxa de utilização ou falha excede um limiar especificado. Uma consulta de amostra para estes dados pode ser encontrada na secção seguinte.

 ## <a name="configure-the-query-and-alerts"></a>Configurar a consulta e os alertas

Cria regras de alerta no Azure Monitor e pode executar automaticamente consultas guardadas ou pesquisas de registo personalizados em intervalos regulares.

Utilize as seguintes instruções para criar alertas de e-mail com base nas consultas refletidas nos gráficos. Os scripts da amostra abaixo enviarão uma notificação por e-mail quando

* o uso bem sucedido diminui 90% em comparação com a mesma hora de há dois dias, como no gráfico de utilização horária na secção anterior. 

* a taxa de insucesso aumenta 90% em relação à mesma hora de há dois dias, como no gráfico de taxa de insucesso horário na secção anterior. 

 Para configurar a consulta subjacente e definir alertas, complete os seguintes passos. Utilizará a Consulta de Amostra como base para a sua configuração. Uma explicação da estrutura de consulta aparece no final desta secção.

Para obter mais informações sobre como criar, visualizar e gerir alertas de registo usando o Azure Monitor consulte [Gerir os alertas de registo .](../../azure-monitor/alerts/alerts-log.md)

 
1. No livro de trabalhos, **selecione Editar,** em seguida, selecione o ícone de **consulta** logo acima do lado direito do gráfico.   

   [![Screenshot mostrando o livro de edição.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   O registo de consulta abre.

  [![Screenshot mostrando o registo de consulta.](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Copie um dos seguintes scripts de amostra para uma nova consulta kusto.

**Consulta de Kusto para queda no uso**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**Consulta de Kusto para aumento da taxa de insucesso**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. Cole a consulta na janela e selecione **Executar**. Certifique-se de que vê a mensagem concluída mostrada na imagem abaixo e os resultados abaixo dessa mensagem.

   [![Screenshot mostrando os resultados da consulta de execução.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Realce a consulta e selecione + **Nova regra de alerta**. 
 
   [![Screenshot mostrando o novo ecrã de regra de alerta.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Configure as condições de alerta. ‎In the Condition section, select the link **Whenever the average custom log search is greater than logic defined count**. No painel de lógica de sinal de configuração, percorra para a lógica de alerta

   [![Screenshot mostrando ecrã de alertas de configuração.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Valor limiar:** 0. Este valor alertará para quaisquer resultados.

   * **Período de avaliação (em minutos):** 60. Este valor olha para uma hora de tempo

   * **Frequência (em minutos)**: 60. Este valor define o período de avaliação para uma vez por hora na hora anterior.

   * Selecione **Concluído**.

6. Na secção **Ações,** configurar estas definições:  

    [![Screenshot mostrando a página de regra de alerta Criar.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * Em **Ações**, escolha **Selecionar grupo de ação** e adicionar o grupo que pretende ser notificado de alertas.

   * Em **ações personalizadas selecione** **alertas de e-mail**.

   * Adicione uma **linha de assunto.**

7. Em **detalhes da regra de alerta,** configurar estas definições:

   * Adicione um nome descritivo e uma descrição.

   * Selecione o **grupo de recursos** para adicionar o alerta.

   * Selecione a **gravidade padrão** do alerta.

   * **Selecione Ative a regra de alerta após a criação** se quiser viver imediatamente, caso contrário selecione **alertas de Supressão**.

8. Selecione **Criar regra de alerta**.

9. **Selecione Guardar**, insira um nome para a consulta, **Guarde como uma consulta com uma categoria de Alerta**. Em seguida, **selecione Guardar** novamente.  

   [![Screenshot mostrando o botão de consulta de poupança.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>Refine as suas consultas e alertas
Modifique as suas consultas e alertas para obter a máxima eficácia.

* Certifique-se de testar os seus alertas.

* Modifique a sensibilidade e a frequência do alerta para que obtenha notificações importantes. Os administradores podem ficar dessensibilizados com os alertas se ficarem a mais e falharem algo importante. 

* Certifique-se de que o e-mail a partir do qual os alertas vêm no e-mail do seu administrador os clientes são adicionados à lista de remetentes permitidos. Caso contrário, poderá perder notificações devido a um filtro de spam no seu cliente de e-mail. 

* A consulta de alertas no Azure Monitor só pode incluir resultados de últimas 48 horas. [Esta é uma limitação atual por design.](https://github.com/MicrosoftDocs/azure-docs/issues/22637)

## <a name="create-processes-to-manage-alerts"></a>Criar processos para gerir alertas

Depois de configurar a consulta e alertas, crie processos de negócio para gerir os alertas.

* Quem vai monitorizar o livro e quando?
* Quando um alerta é gerado, quem vai investigar?

* Quais são as necessidades de comunicação? Quem vai criar as comunicações e quem as receberá?

* Se ocorrer uma paralisação, que processos de negócio precisam de ser desencadeados?

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre livros de trabalho](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

 

 

