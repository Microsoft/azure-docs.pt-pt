---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746678"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Criar um WebJob contínuo

1. Cheguei ao [portal Azure.](https://portal.azure.com)
1. Vá ao seu Serviço de **Aplicações** da sua <abbr title="O seu recurso de aplicação pode ser uma aplicação web, app API ou aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

   ![Selecione WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Utilize as definições **Add WebJob** conforme especificado na tabela.

   ![Screenshot que mostra as definições do Add WebJob que precisa de configurar.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | 
   | ------------ | ----------------- | 
   | <abbr title="Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais para além `-` de `_` .">Name</abbr> | myContinuousWebJob | 
   | <abbr title=" Um *ficheiro .zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script.">Upload de ficheiros</abbr> | ConsoleApp.zip |
   | <abbr title="Os tipos incluem contínuo, desencadeado.">Tipo</abbr> | Contínuo | 
   | <abbr title="Disponível apenas para WebJobs contínuos. Determina se o programa ou o script são executados em todas as instâncias ou apenas num caso. A opção de correr em várias instâncias não se aplica aos níveis de preços gratuitos ou partilhados.">Escala</abbr> | Várias instâncias | 

1. Clique em **OK**.

    O novo WebJob aparece na página **WebJobs.**

    ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Para parar ou reiniciar** um WebJob contínuo, clique com o direito no WebJob na lista e clique em **Stop** ou **Start**.

   ![Pare um WebJob contínuo](../media/web-sites-create-web-jobs/continuousstop.png)
