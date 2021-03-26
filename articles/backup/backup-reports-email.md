---
title: E-mail relatórios de backup da Azure
description: Criar tarefas automatizadas para receber relatórios periódicos via e-mail
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: d3ec76fa1a23657578979e65c0e54bb428722244
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561343"
---
# <a name="email-azure-backup-reports"></a>E-mail relatórios de backup da Azure

Utilizando a funcionalidade **Relatório de E-mail** disponível em Relatórios de Cópia de Segurança, pode criar tarefas automatizadas para receber relatórios periódicos via e-mail. Esta funcionalidade funciona através da implementação de uma aplicação lógica no seu ambiente Azure que consulta dados dos seus espaços de trabalho selecionados log Analytics (LA), com base nas entradas que fornece. [Saiba mais sobre as aplicações lógicas e os seus preços.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="getting-started"></a>Introdução

Para configurar tarefas de e-mail através de Relatórios de Cópia de Segurança, execute os seguintes passos:

1.  Navegue para **relatórios** de backup do Backup Center  >   e clique no separador Relatório **de E-mail.**
2.  Criar uma tarefa especificando as seguintes informações:
    * **Detalhes da Tarefa** - O nome da aplicação lógica a criar e a subscrição, grupo de recursos e localização em que deve ser criada. Note que a aplicação lógica pode consultar dados em várias subscrições, grupos de recursos e locais (conforme selecionado na secção Filtros de Relatório), mas é criado no contexto de uma única subscrição, grupo de recursos e localização.
    * **Dados Para Exportar** - O separador que pretende exportar. Pode criar uma única aplicação de tarefa por aba, ou enviar por e-mail todos os separadores utilizando uma única tarefa, selecionando a opção **Todos os Separadores.**
    * **Opções de e-mail**: A frequência de e-mail, iD(s) de e-mail do destinatário e o sujeito de e-mail.

    ![Separador de e-mail](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Depois de clicar em **Enviar** e **Confirmar,** a aplicação lógica será criada. A aplicação lógica e as conexões API associadas são criadas com a tag **UsedByBackupReports: true** for easy discoverability. Terá de executar um passo de autorização único para que a aplicação lógica funcione com sucesso, como descrito na secção abaixo.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorizar ligações aos Registos do Monitor Azure e ao Office 365

A aplicação lógica utiliza o conector [azuremonitorlogs](/connectors/azuremonitorlogs/) para consulta do espaço de trabalho de LA e utiliza o conector [Office365 Outlook](/connectors/office365connector/) para o envio de e-mails. Terá de efetuar uma autorização única para estes dois conectores. 
 
Para efetuar a autorização, siga os passos abaixo:

1.  Navegue para **Aplicações Lógicas** no portal Azure.
2.  Procure o nome da aplicação lógica que criou e navegue para o recurso.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Clique no item do menu **de ligações API.**

    ![Conexões API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Você verá duas ligações com o formato `<location>-azuremonitorlogs` e `<location>-office365` - isto é, _eastus-azuremonitorlogs_ e _eastus-office365_.
5.  Navegue para cada uma destas ligações e selecione o item do menu **de conexão Editar API.** No ecrã que aparece, **selecione Authorize**, e guarde a ligação uma vez que a autorização esteja completa.

    ![Autorizar a ligação](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Para testar se a aplicação lógica funciona após a autorização, pode navegar de volta para a aplicação lógica, abrir a **Visão Geral** e selecionar **Run Trigger** no painel superior, para testar se um e-mail está a ser gerado com sucesso.

## <a name="contents-of-the-email"></a>Conteúdo do e-mail

* Todos os gráficos e gráficos apresentados no portal estão disponíveis como conteúdo inline no e-mail.
* As grelhas mostradas no portal estão disponíveis como anexos *.csv no e-mail.
* Os dados apresentados no e-mail utilizam todos os filtros de nível de relatório selecionados pelo utilizador no relatório, no momento da criação da tarefa de e-mail.
* Não são aplicados filtros de nível de separador como nome de instância de cópia de **segurança,** **nome de política** e assim por diante. A única exceção a isso é a grelha **de Otimizações de Retenção** no **separador Otimize,** onde são aplicados os filtros para a retenção **diária,** **semanal,** **mensal** e **anual.**
* O intervalo de tempo e o tipo de agregação (para gráficos) baseiam-se na seleção do intervalo de tempo do utilizador nos relatórios. Por exemplo, se a seleção do intervalo de tempo for de duração de 60 dias (traduzindo para o tipo de agregação semanal), e a frequência de e-mail for diária, o destinatário receberá um e-mail todos os dias com gráficos que abrangem dados recolhidos ao longo dos últimos 60 dias, com dados agregados a nível semanal.

## <a name="troubleshooting-issues"></a>Resolução de problemas

Se não estiver a receber e-mails como esperado mesmo após a implementação bem sucedida da aplicação lógica, pode seguir os passos abaixo para resolver problemas na configuração:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Cenário 1: Receber nem um e-mail bem sucedido nem um e-mail de erro

* Este problema pode ocorrer porque o conector API do Outlook não está autorizado. Para autorizar a ligação, siga as etapas de autorização acima fornecidas.

* Este problema também pode ocorrer se tiver especificado um destinatário de e-mail incorreto enquanto cria a aplicação lógica. Para verificar se o destinatário do e-mail foi especificado corretamente, pode navegar para a aplicação lógica no portal Azure, abrir o designer da Aplicação Lógica e selecionar o passo de e-mail para ver se os IDs de e-mail corretos estão a ser usados.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Cenário 2: Receber um email de erro que diz que a aplicação lógica não executou até ao final

Para resolver este problema:
1.  Navegue para a aplicação lógica no portal Azure.
2.  Na parte inferior do ecrã de **visão** geral, verá uma secção **de História das Corridas.** Pode abrir na última execução e ver quais os passos no fluxo de trabalho que falharam. Algumas causas possíveis podem ser:
    * **O Conector de Registos do Monitor Azure não foi autorizado**: Para corrigir este problema, siga as etapas de autorização conforme indicado acima.
    * **Erro na consulta de LA**: No caso de ter personalizado a aplicação lógica com as suas próprias consultas, um erro em qualquer uma das consultas de LA pode estar a causar a falha da aplicação lógica. Pode selecionar o passo relevante e ver o erro que está a causar o funcionamento incorreto da consulta.

Se os problemas persistirem, contacte o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre Relatórios de Backup](./configure-reports.md)