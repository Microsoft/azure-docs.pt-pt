---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5ef61502d0b2fccc92ca606992e965b45764baa0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746669"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Criar um WebJob ativado manualmente

1. Cheguei ao [portal Azure.](https://portal.azure.com)
1. Vá ao seu Serviço de **Aplicações** da sua <abbr title="O seu recurso de aplicação pode ser uma aplicação web, app API ou aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

    ![Selecione WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

   ![Página WebJob](../media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **Add WebJob** conforme especificado na tabela.

    ![Screenshot que mostra as definições que precisam de ser definidas para criar um WebJob ativado manualmente.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Definição      | Valor da amostra   | Descrição  |
    | ------------ | ----------------- | ------------ |
   | <abbr title="Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais para além `-` de `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="Um *ficheiro .zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script.">Upload de ficheiros</abbr> | ConsoleApp.zip |
    | <abbr title="Os tipos incluem contínuo, desencadeado.">Tipo</abbr> | Desencadeado | 
    | <abbr title="Os tipos incluem agendado ou manual">Acionadores</a> | Manual | |

4. Clique em **OK**. 

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Para executar um WebJob manual,** clique com o nome à direita na lista e clique em **Executar**.
   
    ![Executar WebJob](../media/web-sites-create-web-jobs/runondemand.png)

