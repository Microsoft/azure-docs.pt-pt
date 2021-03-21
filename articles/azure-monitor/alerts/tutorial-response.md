---
title: Responder a eventos com Alertas do Log Analytics do Azure | Microsoft Docs
description: Este tutorial ajuda-o a compreender os alertas com o Log Analytics para identificar informações importantes na sua área de trabalho e notificá-lo proativamente de problemas ou invocar ações para tentar corrigi-las.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: 2564711fce6e8578829fb15fdbc04ae2b9d64959
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039432"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Responder a eventos com Alertas do Azure Monitor
Os alertas no Azure Monitor podem identificar informações importantes no repositório do Log Analytics. Estes são criados pelas regras de alerta que executam automaticamente pesquisas de registos em intervalos regulares, e se os resultados da pesquisa de registos corresponderem a critérios de específicos, então será criado um registo de alerta que poderá ser configurado para efetuar uma resposta automática.  Este tutorial é uma continuação do tutorial [Criar e partilhar dashboards de dados do Log Analytics](../visualize/tutorial-logs-dashboards.md).   

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma regra de alerta
> * Configurar um Grupo de Ações para enviar uma notificação de e-mail

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](../vm/quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Criar alertas
Os alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas guardadas ou pesquisa de registos personalizadas em intervalos regulares.  Pode criar alertas com base em métricas de desempenho específicas ou quando determinados eventos são criados, ausência de um evento ou um número de eventos é criado numa janela de tempo específica.  Por exemplo, os alertas podem ser utilizados para notificar quando a utilização da CPU excede determinado limiar, quando é detetada uma atualização em falta ou quando é gerado um evento depois da deteção que determinado serviço Windows ou Linux daemon não está a ser executado.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, será criado um alerta. A regra pode, em seguida, executar automaticamente uma ou mais ações, como notificá-lo do alerta proativamente ou invocar outro processo. 

No exemplo seguinte, o utilizador cria uma regra de alerta de medição métrica com base na consulta *Azure VMs – Utilização do Processador* guardada em [Visualizar tutorial de dados](../visualize/tutorial-logs-dashboards.md).  É criado um alerta para cada máquina virtual que excede um limiar de 90%.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione o **Log Analytics**.
2. No painel da esquerda, selecione **Alertas** e, em seguida, clique em **Nova Regra de Alerta** na parte superior da página para criar um novo alerta.<br><br> ![Criar nova regra de alerta](./media/tutorial-response/alert-rule-02.png)<br>
3. Para o primeiro passo, na secção **Criar Alerta**, vai selecionar a sua área de trabalho do Log Analytics como o recurso, pois é um sinal de alerta baseado em registo.  Filtre os resultados ao escolher a **Subscrição** específica na lista pendente, se tiver mais do que uma, que contém a área de trabalho do Log Analytics e a VM criadas anteriormente.  Filtre o **Tipo de Recurso** ao selecionar **Log Analytics** na lista pendente.  Por fim, selecione o **Recurso** **DefaultLAWorkspace** e, em seguida, clique em **Concluído**.<br><br> ![Criar alerta, passo 1](./media/tutorial-response/alert-rule-03.png)<br>
4. Na secção **Critérios de Alerta**, clique em **Adicionar Critérios** para selecionar a nossa consulta guardada e, em seguida, especifique a lógica que a regra de alerta segue.  No painel **Configurar lógica de sinal**, selecione *Azure VMs – Utilização do Processador* na lista.  O painel atualiza para apresentar as definições de configuração do alerta.  Na parte superior, mostra os resultados dos últimos 30 minutos do sinal selecionado e a consulta de pesquisa.  
5. Configure o alerta com as seguintes informações:  
   a. Na lista pendente **Com base em**, selecione **Unidades métricas**.  Uma medição métrica cria um alerta para cada objeto na consulta com um valor que excede o nosso limiar especificado.  
   b. Para **Condição**, selecione **Maior que** e introduza **90** para **Limiar**.  
   c. Na secção Acionar Alerta Com Base Em, selecione **Falhas de segurança consecutivas** e na lista pendente selecione **Maior que** e introduza 3.  
   d. Na secção Avaliação baseada em, modifique o valor **Período** para **30** minutos. A regra é executada a cada cinco minutos e devolve registos que foram criados nos últimos trinta minutos anteriores à hora atual.  Definir o período de tempo para uma janela maior tem em conta o potencial da latência de dados e garante que a consulta devolve dados para evitar um falso negativo em que o alerta nunca é acionado.  
6. Clique em **Concluído** para concluir a regra de alerta.<br><br> ![Configurar sinal de alerta](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Agora passando ao segundo passo, indique o nome do alerta no campo **Nome da regra de alerta**, como **Percentagem da CPU maior que 90%**.  Especifique uma **Descrição** detalhando especificidades do alerta e selecione **Crítico (Grav 0)** como o valor de **Gravidade** nas opções fornecidas.<br><br> ![Configurar detalhes do alerta](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Para ativar imediatamente a regra de alerta na criação, aceite o valor predefinido de **Ativar regra após criação**.
9. Para o terceiro e último passo, especifica um **Grupo de Ações**, que garante que as mesmas ações são feitas sempre que um alerta é acionado e pode ser utilizado para cada regra que definir.  Configure um novo grupo de ações com as seguintes informações:  
   a. Selecione **Novo grupo de ações** e o painel **Adicionar grupo de ações** é apresentado.  
   b. Em **Nome do grupo de ações**, especifique um nome como **Operações de TI – Notificar** e um **Nome abreviado** como **itops-n**.  
   c. Verifique se os valores predefinidos de **Subscrição** e **Grupo de recursos** estão corretos. Caso contrário, selecione os valores corretos na lista pendente.   
   d. Na secção Ações, especifique um nome para a ação, como **Enviar E-mail** e em **Tipo de Ação** selecione **E-mail/SMS/Push/Voz** na lista pendente. O painel de propriedades **E-mail/SMS/Push/Voz** abre à direita para facultar informações adicionais.  
   e. No painel **E-mail/SMS/Push/Voz**, ative **E-mail** e indique um endereço de e-mail SMTP válido para entregar a mensagem.  
   f. Clique em **OK** para guardar as alterações.<br><br> 

    ![Criar um novo grupo de ações](./media/tutorial-response/action-group-properties-01.png)

10. Clique em **OK** para concluir o grupo de ações. 
11. Clique em **Criar regra de alerta** para concluir a regra de alerta. Começa imediatamente a executar.<br><br> ![Concluir criação de nova regra de alerta](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Veja os seus alertas no portal do Azure
Agora que criou um alerta, pode ver os alertas do Azure num painel único e gerir todas as regras de alerta nas subscrições do Azure. Lista todas as regras de alerta (ativadas ou desativadas) e podem ser ordenadas com base nos recursos de destino, grupos de recursos, nome da regra ou estado. Há um resumo agregado de todos os alertas acionados e o total de regras de alerta configuradas/ativadas.<br><br> ![Página de estado dos Alertas do Azure](./media/tutorial-response/azure-alerts-02.png)  

Quando o alerta é acionado, a tabela reflete a condição e quantas vezes ocorreu no intervalo de tempo selecionado (a predefinição é as últimas seis horas).  Deve haver um e-mail correspondente na sua caixa de correio semelhante ao seguinte exemplo mostrando a máquina virtual inválida e os principais resultados que correspondem à consulta de pesquisa neste caso.<br><br> ![Exemplo de ação de e-mail de alerta](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu como as regras de alertas podem identificar e responder proativamente a um problema quando executam pesquisas de registo em intervalos agendados e corresponder a critérios específicos.

Siga esta ligação para ver os exemplos de scripts do Log Analytics pré-criados.  

> [!div class="nextstepaction"]
> [Exemplos do script do Log Analytics](../powershell-samples.md)