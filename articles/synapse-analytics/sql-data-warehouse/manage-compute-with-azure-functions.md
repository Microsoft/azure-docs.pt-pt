---
title: 'Tutorial: Gerir a computação com funções azure'
description: Como utilizar as funções Azure para gerir a computação da sua piscina SQL no Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c69d28d2be6b04286bb04a2ede6eebc69400c777
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014898"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Use funções Azure para gerir recursos de computação em piscina Azure Synapse Analytics SQL

Este tutorial utiliza funções Azure para gerir recursos de computação para uma piscina SQL em Azure Synapse Analytics.

Para utilizar a App de Função Azure com piscina SQL, deve criar uma [Conta Principal](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de Serviço com acesso ao contribuinte sob a mesma subscrição que a sua instância de piscina SQL.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implante a escala baseada no temporizador com um modelo de Gestor de Recursos Azure

Para implementar o modelo, precisa das seguintes informações:

- Nome do grupo de recursos em que a sua instância de piscina SQL está em
- Nome do servidor em que a sua instância de piscina SQL está em
- Nome da sua instância de piscina SQL
- ID do inquilino (ID do Diretório) do seu Azure Active Directory
- ID da subscrição
- ID da Aplicação Principal de Serviço
- Chave do Segredo do Principal de Serviço

Assim que tiver as informações anteriores, implemente este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Uma vez implementado o modelo, deverá encontrar três novos recursos: um Plano de Serviço de Aplicações Azure gratuito, um plano de aplicação de função baseado no consumo e uma conta de armazenamento que trate da exploração madeireira e da fila de operações. Continue a ler as outras secções para ver como modificar as funções implementadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de computação

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App.

   ![Funções que são implementadas com o modelo](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar a hora de aumento ou redução vertical. No menu suspenso, selecione Integrar.

   ![Selecionar Integrate para a função](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Atualmente, o valor apresentado deverá indicar *%ScaleDownTime%* ou *%ScaleUpTime%*. Estes valores indicam que a agenda se baseia nos valores definidos nas [Definições da Aplicação](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Por enquanto, pode ignorar este valor e alterar o horário para o seu tempo preferido com base nos próximos passos.

4. Na área “schedule”, adicione a hora na expressão CRON que pretende que reflita a frequência com que quer aumentar verticalmente o SQL Data Warehouse.

   ![Alterar agenda da função](./media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo, *"0 30 9 * 1-5"* refletiria um gatilho todos os dias da semana às 9:30 da manhã. Para obter mais informações, veja os [exemplos de agendas](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) do Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Alterar o tempo da operação de escala

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App.

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger*, dependendo se quer alterar o valor de computação de aumento ou redução vertical. Após selecionar as funções, o painel deve mostrar o ficheiro *index.js*.

   ![Alterar o nível de computação do acionador de função](././media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível que pretende e prima “Save”. Este valor é o nível de cálculo que a sua instância de armazém de dados irá escalar com base no calendário definido na secção Integração.

## <a name="use-pause-or-resume-instead-of-scale"></a>Utilizar a pausa ou a retoma em vez do dimensionamento

Atualmente, as funções ligadas por predefinição são *DWScaleDownTrigger* e *DWScaleUpTrigger*. Em alternativa, se pretender utilizar a funcionalidade de pausa e retoma, pode ativar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue para o painel Functions.

   ![Painel Functions](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Clique no botão deslizante dos acionadores que quer ativar.

3. Navegue para os separadores *Integrate* de cada acionador, para alterar as agendas dos mesmos.

   > [!NOTE]
   > A diferença funcional entre os gatilhos de escala e os gatilhos de pausa/retoma é a mensagem que é enviada para a fila. Para mais informações, consulte [Adicionar uma nova função](manage-compute-with-azure-functions.md#add-a-new-trigger-function)de gatilho .

## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de acionador

Atualmente, o modelo inclui apenas duas funções de dimensionamento. Com estas funções, durante um dia, só se pode escalar uma vez por dia. Para um controlo mais granular, como escalonar várias vezes por dia ou ter um comportamento de escala diferente aos fins de semana, precisa adicionar outro gatilho.

1. Crie uma função em branco nova. Selecione o botão perto da localização das *+* suas Funções para mostrar o painel do modelo de função.

   ![Criar função nova](./media/manage-compute-with-azure-functions/create-new-function.png)

2. A partir do Idioma, selecione *JavaScript*, e depois selecione *TimerTrigger*.

   ![Criar função nova](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Dê um nome à função e defina a agenda. A imagem mostra como um utilizador pode acionar a função todos os sábados à meia-noite (sexta ao fim do dia ).

   ![Reduzir verticalmente ao sábado](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de acionador.

   ![Copiar index js](././media/manage-compute-with-azure-functions/index-js.png)

5. Desloque a sua variável de operação ao comportamento desejado da seguinte forma:

   ```JavaScript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Agendamento complexo

Esta secção demonstra brevemente o que é necessário para obter um agendamento mais complexo de capacidades de pausa, currículo e escala.

### <a name="example-1"></a>Exemplo 1

Todos os dias, aumente verticalmente às 8:00 para DW600 e reduza verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exemplo 2

Escala diária até às 8h 00 00 000, escala uma vez para DW600 às 16:00 e escala para baixo às 22h para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Exemplo 3

Aumentar verticalmente às 8:00 para DW1000 e reduzir verticalmente uma vez para DW600 às 16:00 aos dias de semana. É colocado em pausa à sexta-feira às 23:00 e retomado às 7:00 de segunda-feira.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as funções do Azure de [acionador de temporização](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Check-out o [repositório](https://github.com/Microsoft/sql-data-warehouse-samples)de amostras de piscina SQL .
