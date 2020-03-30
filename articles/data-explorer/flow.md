---
title: Conector de fluxo de fluxo de fluxo do Microsoft Azure Data Explorer (Pré-visualização)
description: Aprenda a utilizar o conector Microsoft Flow para criar fluxos de tarefas programadas ou ativadas automaticamente.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501780"
---
# <a name="microsoft-flow-connector-preview"></a>Conector Microsoft Flow (Pré-visualização)

O conector de fluxo do Azure Data Explorer permite ao Azure Data Explorer utilizar as capacidades de [Fluxo do Microsoft Power Automate](https://flow.microsoft.com/) para executar consultas e comandos Kusto automaticamente como parte de uma tarefa programada ou desencadeada.

Os cenários comuns de utilização incluem:

* Envio de relatórios diários contendo tabelas e gráficos
* Definição de notificações com base nos resultados da consulta
* Comandos de controlo de agendamento em clusters
* Dados de exportação e importação entre o Azure Data Explorer e outras bases de dados 

Para obter mais informações, consulte os exemplos de utilização do [conector Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Iniciar sessão 

1. Inicie sessão no [Microsoft Power Automate](https://flow.microsoft.com/).

1. Ao ligar pela primeira vez, será solicitado a iniciar sessão.

1. Selecione **Iniciar sessão** e introduza as suas credenciais.

![Assine o diálogo](./media/flow/flow-signin.png)

## <a name="authentication"></a>Autenticação

Pode autenticar com credenciais de utilizador ou uma aplicação AAD.

> [!Note]
> Certifique-se de que a sua aplicação é uma [aplicação AAD](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) e está autorizada a executar consultas no seu cluster.

1. Selecione os três pontos na parte superior ![direita do conector Microsoft Flow: Adicione uma ligação](./media/flow/flow-addconnection.png)

1. **Selecione Adicionar nova ligação** e, em seguida, selecione **Ligar com o Diretor de Serviço**.
![Assine o diálogo](./media/flow/flow-signin.png)

1. Insira as informações necessárias:
    * Nome de ligação: Um nome descritivo e significativo para a nova ligação
    * ID do cliente: O ID da sua aplicação
    * Segredo do Cliente: A sua chave de aplicação
    * Inquilino: A identificação do diretório AAD em que criou a aplicação. Por exemplo, o ID do inquilino da Microsoft é: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Autenticação de aplicações](./media/flow/flow-appauth.png)

Quando a autenticação estiver completa, verá que o seu fluxo utiliza a ligação recém-adicionada.

![Autenticação de aplicação concluída](./media/flow/flow-appauthcomplete.png)

A partir de agora, este fluxo será executado usando estas credenciais de aplicação.

## <a name="find-the-azure-kusto-connector"></a>Encontre o conector Azure Kusto

Para utilizar o conector Microsoft Flow, é necessário primeiro adicionar um gatilho. Um gatilho pode ser definido com base num período de tempo recorrente, ou como resposta a uma ação de fluxo anterior.

1. [Crie um novo fluxo](https://flow.microsoft.com/manage/flows/new) ou, a partir da página Inicial, selecione a ação **My Flows** e, em seguida, selecione **+ Novo**.

    ![Criar um novo fluxo](./media/flow/flow-newflow.png)

1. Adicione agendado em branco.

    ![Novo fluxo programado](./media/flow/flow-scheduled-from-blank.png)

1. Introduza as informações necessárias na página de fluxo programada.
    ![Construir um fluxo programado](./media/flow/flow-build-scheduled-flow.png)
1. Selecione **Criar**.
1. Selecione **+Novo passo**.
1. Na caixa de pesquisa, insira "Kusto".

    ![Selecione ações de fluxo](./media/flow/flow-actions.png)

1. Selecione **Azure Data Explorer**.

## <a name="flow-actions"></a>Ações de fluxo

Ao abrir o conector Azure Data Explorer, existem três ações possíveis que pode adicionar ao seu fluxo.

Esta secção descreve as capacidades e parâmetros para cada ação do Microsoft Flow.

![Ações do Flow Azure Data Explorer](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Executar comando de controlo e visualizar resultados

Utilize o comando de controlo executar e visualizar a ação dos resultados para executar um comando de comando de [controlo](https://docs.microsoft.com/azure/kusto/management/index).

1. Especifique o URL do cluster. Por exemplo, https://clusterName.eastus.kusto.windows.net
1. Introduza o nome da base de dados.
1. Especifique o comando de controlo:
    * Selecione conteúdo dinâmico das aplicações e conectores utilizados no fluxo
    * Adicione uma expressão para aceder, converter e comparar valores
1. Para enviar os resultados desta ação por e-mail como uma tabela ou um gráfico, especifique o tipo de gráfico, que pode ser:
    * Uma tabela HTML
    * Um gráfico de tortas
    * Um gráfico de tempo
    * Um gráfico de barras

![Comando de controlo de fluxo de execução](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> No campo Nome do *Cluster,* introduza o URL do cluster.

### <a name="run-query-and-list-results"></a>Executar consulta e resultados da lista

> [!Note]
> Se a sua consulta começar com um ponto (o que significa que é um comando de [controlo),](https://docs.microsoft.com/azure/kusto/management/index)use comando de [controlo de execução e visualize os resultados](#run-control-command-and-visualize-results)

Esta ação envia uma consulta ao aglomerado de Kusto. As ações que são adicionadas depois iteram sobre cada linha dos resultados da consulta.

O exemplo seguinte desencadeia uma consulta a cada minuto e envia um e-mail com base nos resultados da consulta. A consulta verifica o número de linhas na base de dados e, em seguida, envia um e-mail apenas se o número de linhas for superior a 0. 

![Executar resultados da lista de consultas](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Se a coluna tiver várias linhas, o conector funcionará para cada linha da coluna.

### <a name="run-query-and-visualize-results"></a>Executar consulta e visualizar resultados
        
> [!Note]
> Se a sua consulta começar com um ponto (o que significa que é um comando de [controlo),](https://docs.microsoft.com/azure/kusto/management/index)use comando de [controlo de execução e visualize os resultados](#run-control-command-and-visualize-results)
        
Utilize a consulta de Executar e visualize a ação dos resultados para visualizar o resultado da consulta de Kusto como uma tabela ou gráfico. Por exemplo, utilize este fluxo para receber relatórios diários de ICM por e-mail. 
    
Neste exemplo, os resultados da consulta são devolvidos como uma tabela HTML.
            
![Executar consulta e visualizar resultados](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> No campo Nome do *Cluster,* introduza o URL do cluster.

## <a name="email-kusto-query-results"></a>E-mail Kusto resultados da consulta

Pode incluir um passo em qualquer fluxo para enviar relatórios por e-mail para qualquer endereço de e-mail. 

1. Selecione **+ Novo Passo** para adicionar um novo passo ao seu fluxo.
1. No campo de pesquisa, insira o Office 365 e selecione **Office 365 Outlook**.
1. Selecione **Enviar uma mensagem de e-mail**.
1. Insira o endereço de e-mail para onde pretende que o relatório de e-mail seja enviado.
1. Insira o tema do e-mail.
1. No campo *Body,* a partir do campo de conteúdo Dinâmico, selecione **Corpo**.
1. Selecione **Mostrar opções avançadas**.
1. No campo *anexos Nome -1,* selecione **Nome de Anexo**.
1. No campo conteúdo de *anexos,* selecione **Conteúdo do Anexo**.
1. Se necessário, estabeleça o nível de importância.
1. Selecione **Guardar**.

![Enviar e-mail](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>Verifique se o seu fluxo foi bem sucedido

Para verificar se o seu fluxo foi bem sucedido, veja o histórico de execução do fluxo:
1. Vá à página inicial do [Microsoft Flow](https://flow.microsoft.com/).
1. A partir do menu principal, selecione [Os meus fluxos.](https://flow.microsoft.com/manage/flows)
    ![Minha página de Fluxos](./media/flow/flow-myflows.png)
1. Na linha do fluxo que pretende investigar, selecione o ícone de mais comandos e, em seguida, **executar a história**.

    ![Executar menu de história](./media/flow//flow-runhistory.png)

    Todos os fluxos são listados com hora de início, duração e estado.
    ![Executar página de resultados do histórico](./media/flow/flow-runhistoryresults.png)

    Para mais detalhes sobre o fluxo, na página [My flows,](https://flow.microsoft.com/manage/flows) selecione o fluxo que pretende investigar.

    ![Executar página de resultados completos do histórico](./media/flow/flow-fulldetails.png) 

Para ver por que uma corrida falhou, selecione a hora de início da corrida. O fluxo aparece e o passo do fluxo que falhou é indicado por um ponto de exclamação vermelho. Expanda o passo falhado para ver os seus detalhes. O painel da direita contém informações sobre a falha para que possa saqueá-la.

![Erro de fluxo](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Exceções de tempo

O seu fluxo pode falhar e devolver uma exceção "RequestTimeout" se tiver uma duração superior a sete minutos.

Saiba mais sobre [as limitações do Microsoft Flow](#limitations).
    
A mesma consulta pode ser executada com sucesso no Azure Data Explorer, onde o tempo não é limitado e pode ser alterado.
            
A exceção "RequestTimeout" é mostrada na imagem abaixo:
    
![Erro de exceção de prazo de pedido de fluxo](./media/flow/flow-requesttimeout.png)
    
Para corrigir um problema de tempo, tente tornar a sua consulta mais eficiente para que corra mais rápido ou separe-a em pedaços. Cada pedaço pode correr em uma parte diferente da consulta.

Para mais informações, leia sobre [as melhores práticas](https://docs.microsoft.com/azure/kusto/query/best-practices)da Consulta.

## <a name="limitations"></a>Limitações

* Os resultados devolvidos ao cliente estão limitados a 500.000 registos. A memória geral desses registos não pode exceder 64 MB e sete minutos de execução.
* O conector não suporta os operadores de [garfos](https://docs.microsoft.com/azure/kusto/query/forkoperator) e [facetas.](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* O Flow funciona melhor no Microsoft Edge e no Chrome.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [conector microsoft Azure Explorer Logic App,](https://docs.microsoft.com/azure/kusto/tools/logicapps) que é outra forma de executar consultas e comandos Kusto automaticamente como parte de uma tarefa programada ou desencadeada.
