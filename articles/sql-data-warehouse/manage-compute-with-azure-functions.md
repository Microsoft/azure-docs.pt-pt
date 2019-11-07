---
title: 'Tutorial: gerenciar a computação com o Azure Functions'
description: Como utilizar as funções do Azure para gerir a computação do seu armazém de dados.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc350ed092c063dcc7eca479f064114be9eb28f5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693014"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Usar Azure Functions para gerenciar recursos de computação no Azure SQL Data Warehouse

Este tutorial usa Azure Functions para gerenciar recursos de computação para um data warehouse no Azure SQL Data Warehouse.

Para utilizar o Azure Function App com o SQL Data Warehouse, tem de criar uma [Conta de Principal de Serviço](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) com acesso de contribuidor na mesma subscrição da sua instância do armazém de dados. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implantar o dimensionamento baseado em temporizador com um modelo de Azure Resource Manager

Para implantar o modelo, você precisará das seguintes informações:

- Nome do grupo de recursos em que a sua instância do armazém do SQL DW se encontra
- Nome do servidor lógico em que a sua instância do armazém do SQL DW se encontra
- Nome da sua instância do SQL DW
- ID do inquilino (ID do Diretório) do seu Azure Active Directory
- ID da subscrição 
- ID da Aplicação Principal de Serviço
- Chave do Segredo do Principal de Serviço

Depois de ter as informações anteriores, implante este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Depois de implantar o modelo, você deve encontrar três novos recursos: um plano de serviço Azure App gratuito, um plano de Aplicativo de funções baseado em consumo e uma conta de armazenamento que manipula o log e a fila de operações. Continue a ler as outras secções para ver como modificar as funções implementadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de computação

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

   ![Funções que são implementadas com o modelo](media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar a hora de aumento ou redução vertical. No menu suspenso, selecione integrar.

   ![Selecionar Integrate para a função](media/manage-compute-with-azure-functions/select-integrate.png)

3. Atualmente, o valor apresentado deverá indicar *%ScaleDownTime%* ou *%ScaleUpTime%* . Esses valores indicam que a agenda é baseada em valores definidos nas [configurações do aplicativo][Application Settings]. Por enquanto, você pode ignorar esse valor e alterar a agenda para sua hora preferida com base nas próximas etapas.

4. Na área “schedule”, adicione a hora na expressão CRON que pretende que reflita a frequência com que quer aumentar verticalmente o SQL Data Warehouse. 

   ![Alterar agenda da função](media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo, *"0 30 9 * * 1-5"* refletiria um gatilho todo dia da semana em 9: às 9h30. Para obter mais informações, visite Azure Functions [exemplos de agendamento][schedule examples].


## <a name="change-the-time-of-the-scale-operation"></a>Alterar a hora da operação de dimensionamento

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar o valor de computação de aumento ou redução vertical. Após selecionar as funções, o painel deve mostrar o ficheiro *index.js*.

   ![Alterar o nível de computação do acionador de função](media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível que pretende e prima “Save”. Esse valor é o nível de computação para o qual sua instância do data warehouse será dimensionada com base na agenda definida na seção integrar.

## <a name="use-pause-or-resume-instead-of-scale"></a>Utilizar a pausa ou a retoma em vez do dimensionamento 

Atualmente, as funções ligadas por predefinição são *DWScaleDownTrigger* e *DWScaleUpTrigger*. Em alternativa, se pretender utilizar a funcionalidade de pausa e retoma, pode ativar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue para o painel Functions.

   ![Painel Functions](media/manage-compute-with-azure-functions/functions-pane.png)



2. Clique no botão deslizante dos acionadores que quer ativar.

3. Navegue para os separadores *Integrate* de cada acionador, para alterar as agendas dos mesmos.

   > [!NOTE]
   > A diferença funcional entre os gatilhos de dimensionamento e a pausa/retomada é a mensagem que é enviada para a fila. Para obter mais informações, consulte [Adicionar uma nova função de gatilho][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de acionador

Atualmente, o modelo inclui apenas duas funções de dimensionamento. Com essas funções, durante o curso de um dia, você só pode reduzir verticalmente uma vez e até mesmo. Para um controle mais granular, como reduzir verticalmente várias vezes por dia ou ter um comportamento de dimensionamento diferente nos fins de semana, você precisa adicionar outro gatilho.

1. Crie uma função em branco nova. Selecione o botão de *+* próximo ao local de funções para mostrar o painel de modelo de função.

   ![Criar função nova](media/manage-compute-with-azure-functions/create-new-function.png)

2. Em Linguagem, selecione *Javascript* e, em seguida, selecione *TimerTrigger*.

   ![Criar função nova](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Dê um nome à função e defina a agenda. A imagem mostra como um utilizador pode acionar a função todos os sábados à meia-noite (sexta ao fim do dia ).

   ![Reduzir verticalmente ao sábado](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de acionador.

   ![Copiar index js](media/manage-compute-with-azure-functions/index-js.png)

5. Defina a variável de operação para o comportamento desejado da seguinte maneira:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Agendamento complexo

Esta seção demonstra brevemente o que é necessário para obter um agendamento mais complexo de recursos de pausa, retomada e dimensionamento.

### <a name="example-1"></a>Exemplo 1:

Todos os dias, aumente verticalmente às 8:00 para DW600 e reduza verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exemplo 2: 

Escalar verticalmente verticalmente às 8:00 para DW1000, reduzir horizontalmente uma vez para DW600 em 16:00 e reduzir verticalmente em 19:10 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Exemplo 3: 

Aumentar verticalmente às 8:00 para DW1000 e reduzir verticalmente uma vez para DW600 às 16:00 aos dias de semana. É colocado em pausa à sexta-feira às 23:00 e retomado às 7:00 de segunda-feira.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as funções do Azure de [acionador de temporização](../azure-functions/functions-create-scheduled-function.md).

Veja o [repositório de exemplos](https://github.com/Microsoft/sql-data-warehouse-samples) do SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function
