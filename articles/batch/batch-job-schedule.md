---
title: Agende os seus trabalhos
description: Utilize o agendamento de trabalho para gerir as suas tarefas.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: c75f635a937dcddd2c1c02cc717f96f1441efe81
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726643"
---
# <a name="schedule-jobs-for-efficiency"></a>Agendar trabalhos para eficiência

Agendar trabalhos em lote permite-lhe priorizar os trabalhos que pretende executar primeiro, tendo em conta tarefas que têm dependências de outras tarefas. Ao agendar os seus empregos, pode certificar-se de que utiliza a menor quantidade de recursos. Os nós podem ser desativados quando não são necessários, tarefas que dependem de outras tarefas são fiadas mesmo a tempo de otimizar os fluxos de trabalho. Só um trabalho de cada vez corre. Um novo só começará quando o anterior estiver completo. Pode definir o seu trabalho para completar automaticamente. 

## <a name="benefit-of-job-scheduling"></a>Benefício do agendamento de emprego

O benefício de agendar empregos é que você pode especificar um horário para a criação de emprego. As tarefas que agenda usando a tarefa de gestor de emprego estão associadas a um trabalho. A tarefa de gestor de emprego criará tarefas para o trabalho. Para tal, a tarefa do gestor de emprego tem de autenticar com a conta Batch. Use o símbolo de acesso AZ_BATCH_AUTHENTICATION_TOKEN. O símbolo permitirá o acesso ao resto do trabalho. 

## <a name="use-the-portal-to-schedule-a-job"></a>Use o portal para agendar um trabalho

   1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

   2. Selecione a conta Batch em que pretende agendar trabalhos.

   3. Selecione **Adicionar** para criar um novo horário de trabalho e preencher o **formulário Básico**.



![Agende um trabalho][1]

**ID do horário de trabalho**: O identificador único para este horário de trabalho.

**Nome**do ecrã : O nome de exibição para o trabalho não tem de ser único, mas tem um comprimento máximo de 1024 caracteres.

**Não corra até**: Especifica o mais cedo que o trabalho irá funcionar. Se não definir isto, o horário fica pronto para executar trabalhos imediatamente.

**Não corra atrás**: Nenhum trabalho corre depois do tempo que se instalou aqui. Se não especificar um tempo, então está a criar um horário de trabalho recorrente que permanece ativo até que o exaque explicitamente.

Intervalo de **recorrência:** Pode especificar a quantidade de tempo entre empregos. Você pode ter apenas um emprego de cada vez programado, então se é hora de criar um novo emprego sob um horário de trabalho, mas o trabalho anterior ainda está em funcionamento, o serviço Batch não vai criar o novo emprego até que o trabalho anterior termine.  

**Janela de início**: Aqui especifica o intervalo de tempo, a partir do momento em que o horário indica que deve ser criado um trabalho, até que seja concluído. Se o trabalho atual não estiver completo durante a janela, o próximo trabalho não começará.

Na parte inferior do formulário básico, você irá especificar a piscina em que você quer que o trabalho seja executado. Para encontrar as informações de ID do seu pool, selecione **Update**. 

![Especificar piscina][2]


**ID da piscina**: Identifique a piscina em que executará o trabalho.

**Tarefa de configuração de trabalho**: Selecione **Atualizar** para nomear a tarefa do Gestor de Emprego, bem como as tarefas de preparação e lançamento do Trabalho, se estiver a utilizá-las.

**Prioridade**: Dar prioridade ao trabalho.

**Tempo**máximo de tempo de parede : Desloque o máximo de tempo que o trabalho pode executar. Se não estiver concluído dentro do prazo, batch termina o trabalho. Se não estabeleceres isto, então não há limite de tempo para o trabalho.

Contagem máxima de **tarefas**: Especifique o número de vezes que uma tarefa pode ser novamente experimentada até um máximo de quatro vezes. Isto não é o mesmo que o número de tentativas que uma chamada da API pode ter.

**Quando todas as tarefas estiverem concluídas**: O padrão não é uma ação.

**Quando uma tarefa falha:** O padrão não é uma ação. Uma tarefa falha se a contagem de retry estiver esgotada ou se houve um erro ao iniciar a tarefa. 

Depois de selecionar **Save**, se for aos **horários** de Trabalho na navegação à esquerda, pode acompanhar a execução do trabalho, selecionando informações de **Execução**.


## <a name="for-more-information"></a>Para obter mais informações:

Para gerir um trabalho utilizando o Azure CLI, consulte o horário de trabalho do [lote az](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Próximos passos

[Criar dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


