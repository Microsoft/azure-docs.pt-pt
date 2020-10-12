---
title: Agende os seus trabalhos
description: Use o agendamento de empregos para gerir as suas tarefas.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 7da3c78e00f5d7e41a5396603cf4885a50cb6e5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146356"
---
# <a name="schedule-jobs-for-efficiency"></a>Agendar trabalhos para eficiência

Agendar trabalhos em lote permite-lhe priorizar os empregos que pretende executar primeiro, tendo em conta tarefas que têm dependências de outras tarefas. Ao agendar os seus empregos, pode certificar-se de que utiliza o mínimo de recursos. Os nós podem ser desativados quando não são necessários, as tarefas que dependem de outras tarefas são giradas mesmo a tempo de otimizar os fluxos de trabalho. Só há um trabalho de cada vez. Um novo não começará até que o anterior termine. Pode definir o seu trabalho para auto-preconizar. 

## <a name="benefit-of-job-scheduling"></a>Benefício do agendamento de empregos

O benefício do agendamento de postos de trabalho é que você pode especificar um calendário para a criação de emprego. As tarefas que agenda usando a tarefa de gestor de emprego estão associadas a um trabalho. A tarefa de gerente de emprego criará tarefas para o trabalho. Para tal, a tarefa do gestor de emprego tem de autenticar com a conta Batch. Use o símbolo de acesso AZ_BATCH_AUTHENTICATION_TOKEN. O símbolo permitirá o acesso ao resto do trabalho. 

## <a name="use-the-portal-to-schedule-a-job"></a>Use o portal para agendar um trabalho

   1. Inscreva-se no [portal Azure](https://portal.azure.com/).

   2. Selecione a conta Batch em que pretende agendar postos de trabalho.

   3. **Selecione Adicionar** para criar um novo horário de trabalho e completar o **formulário Basic**.



![Agende um trabalho][1]

**Identificação do horário de trabalho**: O identificador único para este horário de trabalho.

**Nome do visor**: O nome do visor do trabalho não tem de ser único, mas tem um comprimento máximo de 1024 caracteres.

**Não corra até:** Especifica o momento mais cedo para o funcionação do trabalho. Se não definir isto, o horário fica pronto para executar empregos imediatamente.

**Não corra depois:** Não há trabalhos executados após o tempo que definir aqui. Se não especificar uma hora, está a criar um horário de trabalho recorrente que permanece ativo até que o termine explicitamente.

**Intervalo de recorrência**: Pode especificar a quantidade de tempo entre os trabalhos. Só pode ter um emprego de cada vez, por isso, se for altura de criar um novo emprego sob um horário de trabalho, mas o trabalho anterior ainda está em funcionamento, o serviço Batch não criará o novo emprego até que o trabalho anterior termine.  

**Janela de início**: Aqui especifica o intervalo de tempo, a partir do momento em que o horário indica que deve ser criado um trabalho, até que seja concluído. Se o trabalho atual não estiver completo durante a janela, o próximo trabalho não começará.

Na parte inferior do formulário básico, você especificará a piscina em que deseja que o trabalho seja executado. Para encontrar as informações de ID da piscina, selecione **Update**. 

![Especificar piscina][2]


**Pool ID**: Identifique a piscina em que irá executar o trabalho.

**Tarefa de configuração de trabalho**: Selecione **Update** para nomear a tarefa de Job Manager, bem como as tarefas de preparação e libertação de Trabalho, se estiver a usá-las.

**Prioridade**: Dê prioridade ao trabalho.

**Tempo do relógio da parede máxima**: Desa esta medida de tempo máxima para que o trabalho possa funcionar. Se não estiver completo dentro do prazo, o Batch termina o trabalho. Se não definir isto, não há prazo para o trabalho.

**Contagem máxima de re-padrão**de tarefa : Especifique o número de vezes que uma tarefa pode ser novamente julgada até um máximo de quatro vezes. Isto não é o mesmo que o número de retrós assim que uma chamada da API pode ter.

**Quando todas as tarefas estiverem concluídas:** O padrão não é ação.

**Quando uma tarefa falha:** O padrão não é ação. Uma tarefa falha se a contagem de repetição estiver esgotada ou se houver um erro ao iniciar a tarefa. 

Depois de selecionar **Guardar**, se for a **horários de Trabalho** na navegação à esquerda, pode acompanhar a execução do trabalho, selecionando informações de **Execução**.


## <a name="for-more-information"></a>Para obter mais informações:

Para gerir um trabalho utilizando o Azure CLI, consulte [o horário de trabalho do lote az](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Passos seguintes

[Criar dependências de tarefas para executar tarefas que dependam de outras tarefas](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


