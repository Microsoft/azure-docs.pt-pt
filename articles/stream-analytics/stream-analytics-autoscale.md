---
title: Empregos de Autoscale Stream Analytics
description: Este artigo descreve como autoescalar o trabalho stream Analytics com base num horário predefinido ou valores de métricas de emprego
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016291"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Trabalhos de Streaming de Autoscale Analytics utilizando a Azure Automation

Pode otimizar o custo dos seus trabalhos stream Analytics configurando a autoescala. A autoscalagem aumenta ou diminui as Unidades de Streaming (SUs) do seu trabalho para corresponder à alteração da sua carga de entrada. Em vez de exagerar no seu trabalho, pode aumentar ou descer se necessário. Há duas formas de configurar os seus empregos para a autoescalação:
1. **Pré-definir um horário** quando tiver uma carga de entrada previsível. Por exemplo, espera uma taxa mais elevada de eventos de entrada durante o dia e quer que o seu trabalho seja executado com mais SUs.
2. **O gatilho escala e escala as operações com base em métricas de trabalho** quando não se tem uma carga de entrada previsível. Pode alterar dinamicamente o número de SUs com base nas suas métricas de trabalho, tais como o número de eventos de entrada ou eventos de entrada atrasados.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar autoscaling para o seu trabalho, complete os seguintes passos.
1. O seu trabalho está otimizado para ter uma [topologia paralela.](./stream-analytics-parallelization.md) Se conseguir alterar a escala do seu trabalho enquanto está em funcionamento, então o seu trabalho tem uma topologia paralela e pode ser configurado para autoescala.
2. [Criar uma conta Azure Automation](../automation/automation-create-standalone-account.md) com a opção "RunAsAccount" ativada. Esta conta deve ter permissões para gerir os seus trabalhos stream Analytics.

## <a name="set-up-azure-automation"></a>Configurar a Azure Automation
### <a name="configure-variables"></a>Configure variáveis
Adicione as seguintes variáveis dentro da conta Azure Automation. Estas variáveis serão usadas nos livros que são descritos nos próximos passos.

| Nome | Tipo | Valor |
| --- | --- | --- |
| **trabalhoName** | String | Nome do seu trabalho stream Analytics que pretende fazer uma escala automática. |
| **nome do Grupo de Recursos** | String | Nome do grupo de recursos em que o seu trabalho está presente. |
| **subId** | String | ID de assinatura no qual o seu trabalho está presente. |
| **aumento da SU** | Número inteiro | O valor de SU mais elevado que quer que o seu trabalho seja dimensionado num horário. Este valor deve ser uma das opções de SU válidas que vê nas definições de **Escala** do seu trabalho enquanto está em execução. |
| **diminuição da SU** | Número inteiro | O valor su menor que você quer que o seu trabalho escalone em um horário. Este valor deve ser uma das opções de SU válidas que vê nas definições de **Escala** do seu trabalho enquanto está em execução. |
| **maxSU** | Número inteiro | O valor máximo de SU que deseja que o seu trabalho escale em etapas ao autoscalar por carga. Este valor deve ser uma das opções de SU válidas que vê nas definições de **Escala** do seu trabalho enquanto está em execução. |
| **minSU** | Número inteiro | O valor mínimo de SU que deseja que o seu trabalho escale em etapas ao autoscalar por carga. Este valor deve ser uma das opções de SU válidas que vê nas definições de **Escala** do seu trabalho enquanto está em execução. |

![Adicione variáveis na Azure Automation](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Criar runbooks
O próximo passo é criar dois livros powerShell. Um para escalar e o outro para operações de escala para baixo.
1. Na sua conta Azure Automation, vá a **Runbooks** em **Automação de Processos**  e selecione **Create Runbook**.
2. Nomeie o primeiro livro de execução *ScaleUpRunbook* com o tipo definido para PowerShell. Utilize o [script PowerShell scaleUpRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) disponível no GitHub. Salve e publique.
3. Crie outro livro de recortes chamado *ScaleDownRunbook* com o tipo PowerShell. Utilize o [script PowerShell do ScaleDownRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) disponível no GitHub. Salve e publique.

![Livros de escala automática na Azure Automation](./media/autoscale/runbooks.png)

Tem agora livros de execução que podem desencadear automaticamente a escala e reduzir automaticamente as operações no seu trabalho stream Analytics. Estes livros podem ser desencadeados usando um horário pré-definido ou podem ser definidos dinamicamente com base em métricas de trabalho.

## <a name="autoscale-based-on-a-schedule"></a>Autoescala com base num horário
A Azure Automation permite-lhe configurar um horário para ativar os seus runbooks.
1. Na sua conta Azure Automation, selecione Agendas com **recursos Partilhados.**  Em seguida, **selecione Adicionar um horário**.
2. Por exemplo, pode criar dois horários. Um que representa quando se quer que o seu trabalho se escalone e outro que representa quando se quer que o seu trabalho se reduza. Pode definir uma recorrência para estes horários.

   ![Horários em Azure Automation](./media/autoscale/schedules.png)

3. Abra o seu **ScaleUpRunbook** e, em seguida, **selecione Agendas** em **Recursos**. Em seguida, pode ligar o seu livro de execução a um horário que criou nos passos anteriores. Pode ter vários horários ligados ao mesmo livro de recortes que podem ser úteis quando pretender executar a mesma operação de escala em diferentes horas do dia.

![Agendamento de livros na Azure Automation](./media/autoscale/schedulerunbook.png)

1. Repita o passo anterior para **scaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Autoescala com base na carga
Pode haver casos em que não se pode prever a carga de entrada. Nestes casos, é mais ideal escalar para cima/para baixo em degraus dentro de um limite mínimo e máximo. Pode configurar regras de alerta nos seus trabalhos stream Analytics para desencadear livros de execução quando as métricas de trabalho vão acima ou abaixo de um limiar.
1. Na sua conta Azure Automation, crie mais duas variáveis Inteiros chamadas **minSU** e **maxSU.** Isto define os limites dentro dos quais o seu trabalho vai escalar em passos.
2. Crie dois novos livros. Pode utilizar o [script StepScaleUp PowerShell](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) que aumenta as SUs do seu trabalho em incrementos até ao valor **máximo.** Também pode utilizar o [script StepScaleDown PowerShell](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) que diminui as SUs do seu trabalho em etapas até que o valor **minSU** seja atingido. Em alternativa, pode utilizar os livros de execução da secção anterior se tiver valores de SU específicos a que pretende escalar.
3. No seu trabalho stream Analytics, selecione **as regras de alerta** em **Monitorização**. 
4. Criar dois grupos de ação. Um para ser usado para aumentar a escala e outro para a operação de escala para baixo. Selecione **Gerir Ações** e, em seguida, clique no **Grupo de Ação Adicionar**. 
5. Preencha os campos necessários. Escolha **o Livro de Execução de Automação** quando selecionar o Tipo de **Ação**. Selecione o livro de bordo que pretende acionar quando o alerta disparar. Então, crie o grupo de ação.

   ![Criar grupo de ações](./media/autoscale/create-actiongroup.png)
6. Crie uma [**nova regra de alerta**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) no seu trabalho. Especifique uma condição com base numa métrica à sua escolha. [ *Eventos de entrada,* *SU% Utilização* ou *Eventos de Entrada Recuado*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) são métricas recomendadas para a definição da lógica de autoscaling. Recomenda-se também a utilização de *granularidade de agregação de* 1 minuto e *frequência de avaliação* ao desencadear operações de escala. Ao fazê-lo, garante que o seu trabalho tem amplos recursos para lidar com grandes picos no volume de entrada.
7. Selecione o Grupo de Ação criado no último passo e crie o alerta.
8. Repita os passos 2 a 4 para quaisquer operações de escala adicionais que pretenda desencadear com base no estado das métricas de trabalho.

É uma boa prática fazer testes à escala antes de executar o seu trabalho na produção. Ao testar o seu trabalho contra cargas de entrada variadas, você tem uma noção de quantas SUs o seu trabalho precisa para diferentes entradas. Isto pode informar as condições que define nas suas regras de alerta que desencadeiam a escala e reduzem as operações. 

## <a name="next-steps"></a>Passos seguintes
* [Criar consultas paralizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Escala Azure Stream Analytics empregos para aumentar a produção](stream-analytics-scale-jobs.md)