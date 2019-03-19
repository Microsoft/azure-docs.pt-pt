---
title: 'Tutorial: Gerir a computação com as funções do Azure no Azure SQL Data Warehouse | Documentos da Microsoft'
description: Como utilizar as funções do Azure para gerir a computação do seu armazém de dados.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b94e4c6f178119d6205c302cf35a9effaf2aa885
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870935"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Utilize as funções de Azure para gerir recursos de computação no Azure SQL Data Warehouse

Este tutorial utiliza as funções do Azure para gerir os recursos de computação de um armazém de dados no Azure SQL Data Warehouse.

Para utilizar o Azure Function App com o SQL Data Warehouse, tem de criar uma [Conta de Principal de Serviço](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) com acesso de contribuidor na mesma subscrição da sua instância do armazém de dados. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implementar com base no temporizador de dimensionamento com um modelo Azure Resource Manager

Para implementar o modelo, terá das seguintes informações:

- Nome do grupo de recursos em que a sua instância do armazém do SQL DW se encontra
- Nome do servidor lógico em que a sua instância do armazém do SQL DW se encontra
- Nome da sua instância do SQL DW
- ID do inquilino (ID do Diretório) do seu Azure Active Directory
- ID da subscrição 
- ID da Aplicação Principal de Serviço
- Chave do Segredo do Principal de Serviço

Depois de ter as informações anteriores, implemente este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Depois de implementar o modelo, deverá ver três recursos novos: um plano gratuito do App Service do Azure, um plano do Function App baseado no consumo e uma conta de armazenamento que manipula o Registro em log e a fila de operações. Continue a ler as outras secções para ver como modificar as funções implementadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de computação

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

   ![Funções que são implementadas com o modelo](media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar a hora de aumento ou redução vertical. No menu pendente, selecione Integrate.

   ![Selecionar Integrate para a função](media/manage-compute-with-azure-functions/select-integrate.png)

3. Atualmente, o valor apresentado deverá indicar *%ScaleDownTime%* ou *%ScaleUpTime%*. Estes valores indicam que a agenda se baseia nos valores definidos nas [Definições da Aplicação][Application Settings]. Por agora, pode ignorar este valor e alterar a agenda para a hora que preferir com base nos próximos passos.

4. Na área “schedule”, adicione a hora na expressão CRON que pretende que reflita a frequência com que quer aumentar verticalmente o SQL Data Warehouse. 

   ![Alterar agenda da função](media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo, *"0 30 9 * * 1-5"* reflete um acionador todos os dias da semana às 9h30. Para obter mais informações, veja os [exemplos de agendas][schedule examples] do Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Alterar a hora da operação de dimensionamento

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App. 

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar o valor de computação de aumento ou redução vertical. Após selecionar as funções, o painel deve mostrar o ficheiro *index.js*.

   ![Alterar o nível de computação do acionador de função](media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível que pretende e prima “Save”. Este valor é o nível de computação que sua instância de armazém de dados será dimensionada para com base na agenda definida na secção Integrate.

## <a name="use-pause-or-resume-instead-of-scale"></a>Utilizar a pausa ou a retoma em vez do dimensionamento 

Atualmente, as funções ligadas por predefinição são *DWScaleDownTrigger* e *DWScaleUpTrigger*. Em alternativa, se pretender utilizar a funcionalidade de pausa e retoma, pode ativar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue para o painel Functions.

   ![Painel Functions](media/manage-compute-with-azure-functions/functions-pane.png)



2. Clique no botão deslizante dos acionadores que quer ativar.

3. Navegue para os separadores *Integrate* de cada acionador, para alterar as agendas dos mesmos.

   > [!NOTE]
   > A diferença funcional entre os acionadores de dimensionamento e os acionadores de colocação em pausa/retomar é a mensagem que é enviada para a fila. Para obter mais informações, consulte [adicionar uma nova função de Acionador][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de acionador

Atualmente, o modelo inclui apenas duas funções de dimensionamento. Com estas funções, no decorrer de um dia, pode só pode reduzir verticalmente uma vez e aumentar uma vez. Para um controle mais granular, tais como reduzir verticalmente várias vezes por dia ou ter o comportamento de dimensionamento diferente aos fins de semana, terá de adicionar outro acionador.

1. Crie uma função em branco nova. Selecione o *+* junto ao seu local de funções para mostrar o painel do modelo de função.

   ![Criar função nova](media/manage-compute-with-azure-functions/create-new-function.png)

2. Em Linguagem, selecione *Javascript* e, em seguida, selecione *TimerTrigger*.

   ![Criar função nova](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Dê um nome à função e defina a agenda. A imagem mostra como um utilizador pode acionar a função todos os sábados à meia-noite (sexta ao fim do dia ).

   ![Reduzir verticalmente ao sábado](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de acionador.

   ![Copiar index js](media/manage-compute-with-azure-functions/index-js.png)

5. Defina a variável da operação para o comportamento desejado da seguinte forma:

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

Esta secção demonstra, brevemente, o que é necessário para obter mais complexos, agendamento de pausa, retomar e capacidades de dimensionamento.

### <a name="example-1"></a>Exemplo 1:

Todos os dias, aumente verticalmente às 8:00 para DW600 e reduza verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exemplo 2: 

Aumente verticalmente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 e reduzir verticalmente às 22:00 para DW200.

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



## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as funções do Azure de [acionador de temporização](../azure-functions/functions-create-scheduled-function.md).

Veja o [repositório de exemplos](https://github.com/Microsoft/sql-data-warehouse-samples) do SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function
