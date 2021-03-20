---
title: 'Tutorial: Gerir o cálculo com funções Azure'
description: Como utilizar as Funções Azure para gerir o cálculo da sua piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96448382"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Use funções Azure para gerir recursos computativos para a sua piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Este tutorial utiliza funções Azure para gerir recursos computativos para uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.

Para utilizar uma App de Função Azure com uma piscina SQL dedicada (anteriormente SQL DW), tem de criar uma [Conta Principal de Serviço.](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) A Conta Principal de Serviço necessita de acesso ao contribuinte na mesma subscrição que a sua caixa de piscina SQL dedicada (anteriormente SQL DW).

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implementar escalamento baseado em temporizador com um modelo de gestor de recursos Azure

Para implementar o modelo, precisa das seguintes informações:

- Nome do grupo de recursos a sua piscina de SQL dedicada (anteriormente SQL DW) está em
- Nome do servidor a sua piscina de SQL dedicada (anteriormente SQL DW) está em
- Nome da sua piscina de SQL dedicada (anteriormente SQL DW) instância
- ID do inquilino (ID do Diretório) do seu Azure Active Directory
- ID da subscrição
- ID da Aplicação Principal de Serviço
- Chave do Segredo do Principal de Serviço

Assim que tiver as informações anteriores, implemente este modelo:

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Uma vez implementado o modelo, deverá encontrar três novos recursos: um Plano de Serviço de Aplicações Azure gratuito, um plano de aplicação de função baseado no consumo e uma conta de armazenamento que trata da fila de registos e operações. Continue a ler as outras secções para ver como modificar as funções implementadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de cálculo

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App.

   ![Funções que são implementadas com o modelo](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* para escalar ou escalar para baixo. No menu suspenso, selecione Integrate.

   ![Selecionar Integrate para a função](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Atualmente, o valor apresentado deverá indicar *%ScaleDownTime%* ou *%ScaleUpTime%*. Estes valores indicam que a agenda se baseia nos valores definidos nas [Definições da Aplicação](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Por enquanto, pode ignorar este valor e alterar o horário para o seu tempo preferido com base nos próximos passos.

4. Na área de programação, adicione a expressão CRON que gostaria de refletir com que frequência deseja que o Azure Synapse Analytics seja aumentado.

   ![Alterar agenda da função](./media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo, *"0 30 9 * * 1-5"* refletiria um gatilho todos os dias da semana às 9:30 da manhã. Para obter mais informações, veja os [exemplos de agendas](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) do Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Alterar o tempo da operação de escala

1. Navegue para o serviço Function App. Se tiver implementado o modelo com os valores predefinidos, este serviço deverá chamar-se *DWOperations*. Assim que Function App estiver aberto, deverá ver cinco funções implementadas no seu serviço do Function App.

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* para escalar ou escalar o valor do cálculo. Após selecionar as funções, o painel deve mostrar o ficheiro *index.js*.

   ![Alterar o nível de computação do acionador de função](././media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor do *ServiceLevelObjective* para o nível que pretende e selecione guardar. O *ServiceLevelObjective* é o nível de cálculo que a sua instância de armazém de dados irá escalar com base no calendário definido na secção Integração.

## <a name="use-pause-or-resume-instead-of-scale"></a>Utilizar a pausa ou a retoma em vez do dimensionamento

Atualmente, as funções ligadas por predefinição são *DWScaleDownTrigger* e *DWScaleUpTrigger*. Em alternativa, se pretender utilizar a funcionalidade de pausa e retoma, pode ativar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue para o painel Functions.

   ![Painel Functions](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Selecione no toggle deslizante para os gatilhos correspondentes que pretende ativar.

3. Navegue para os separadores *Integrate* de cada acionador, para alterar as agendas dos mesmos.

   > [!NOTE]
   > A diferença funcional entre os gatilhos de escala e os gatilhos de pausa/currículo é a mensagem que é enviada para a fila. Para obter mais informações, consulte [Adicione uma nova função de gatilho](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de acionador

Atualmente, o modelo inclui apenas duas funções de dimensionamento. Com estas funções, durante um dia, só se pode reduzir uma vez e uma vez para cima. Para um maior controlo granular, como reduzir várias vezes por dia ou ter diferentes comportamentos de escala aos fins de semana, é necessário adicionar outro gatilho.

1. Crie uma função em branco nova. Selecione o *+* botão perto da localização das funções para mostrar o painel do modelo de função.

   ![Screenshot que mostra o menu "Apps de função" com o ícone "Plus" ao lado de "Funções" selecionadas.](./media/manage-compute-with-azure-functions/create-new-function.png)

2. A partir do idioma, selecione *JavaScript* e, em seguida, selecione *TimerTrigger*.

   ![Criar função nova](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Dê um nome à função e defina a agenda. A imagem mostra como um utilizador pode acionar a função todos os sábados à meia-noite (sexta ao fim do dia ).

   ![Reduzir verticalmente ao sábado](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de acionador.

   ![Copiar index js](././media/manage-compute-with-azure-functions/index-js.png)

5. Desa parte da variável de operação para o comportamento pretendido da seguinte forma:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Agendamento complexo

Esta secção demonstra brevemente o que é necessário para obter um agendamento mais complexo de capacidades de pausa, currículo e escala.

### <a name="example-1"></a>Exemplo 1

Escala diária às 8:00 para DW600c e escala para baixo às 20:00 para DW200c.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Exemplo 2

Escala diária às 8:00 para DW1000c, escala uma vez para DW600 às 16:00, e escala para baixo às 22:00 para DW200c.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Exemplo 3

Escala às 8:00 para DW1000c, escala uma vez para DW600c às 16:00 nos dias úteis. É colocado em pausa à sexta-feira às 23:00 e retomado às 7:00 de segunda-feira.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o temporizador acionar](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) funções Azure.

Consulte [o repositório](https://github.com/Microsoft/sql-data-warehouse-samples)de amostras de piscina SQL (anteriormente SQL DW).
