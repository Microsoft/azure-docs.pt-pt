---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5105df5793d37b166b017585a62c962933a0b019
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081043"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Criar um WebJob programado


1. Cheguei ao [portal Azure.](https://portal.azure.com)
1. Vá ao seu Serviço de **Aplicações** da sua <abbr title="O seu recurso de aplicação pode ser uma aplicação web, app API ou aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

   ![Selecione WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Utilize as definições **Add WebJob** conforme especificado na tabela.

    ![Adicionar página WebJob](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Definição      | Valor da amostra   |
    | ------------ | ----------------- | 
    | <abbr title="Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais para além `-` de `_` .">Name</a> | mySchededWebJob | 
    | <abbr title="Um *ficheiro .zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script.">Upload de ficheiros</abbr> | ConsoleApp.zip |
    | <abbr title="Os tipos incluem contínuo, desencadeado.">Tipo</abbr> | Desencadeado |
    | <abbr title="Para que o agendamento funcione de forma fiável, ative a funcionalidade Always On. Always On está disponível apenas nos níveis de preços Básico, Standard e Premium.">Acionadores</a> | Agendado |
    | Expressão CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Saiba mais sobre expressões cron</summary>
     <a name="#ncrontab-expressions"></a>
    
     Pode introduzir uma [expressão NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um `settings.job` ficheiro na raiz do seu ficheiro WebJob *.zip,* como no seguinte exemplo:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Para saber mais, consulte [Agendar um WebJob desencadeado.](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Clique em **OK**.

    O novo WebJob aparece na página **WebJobs.**
    
    ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)
