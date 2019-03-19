---
title: Início Rápido do Azure - Executar Trabalho do Batch - Portal
description: Aprenda rapidamente a executar um trabalho do Batch com o portal do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: d478307ff4393d84a854fcd3b2ea0efd77de7135
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087844"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Início rápido: Executar o seu primeiro trabalho do Batch no portal do Azure

Este início rápido mostra como utilizar o portal do Azure para criar uma conta do Batch, um *conjunto* de nós de computação (máquinas virtuais) e um *trabalho* que executa *tarefas* no conjunto. Depois de concluir este início rápido, irá compreender os conceitos principais do serviço do Batch e estará pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Siga estes passos para criar uma conta do Batch de exemplo para fins de teste. Tem de ter uma conta do Batch para criar conjuntos e trabalhos. Conforme mostrado aqui, pode associar uma conta de armazenamento do Azure à conta do Batch. Apesar de não ser obrigatório neste início rápido, a conta de armazenamento é útil para implementar aplicações e armazenar dados de entrada e saída para a maioria das cargas de trabalho do mundo real.


1. Selecione **Criar um recurso** > **Computação** > **Serviço do Batch**. 

   ![Batch no Marketplace][marketplace_portal]

2. Introduza valores em **Nome da conta** e **Grupo de recursos**. O nome da conta tem de ser exclusivo dentro da **localização** do Azure selecionada, só pode ter carateres em minúsculas ou números e conter entre 3 e 24 carateres. 

3. Em **Conta de armazenamento**, selecione uma conta de armazenamento existente ou crie uma nova.

4. Nas definições restantes, mantenha as predefinições e selecione **Criar** para criar a conta.

   ![Criar uma conta do Batch][account_portal]  

Quando for apresentada a mensagem **Implementação concluída com êxito**, vá para a conta do Batch no portal.

## <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Agora que tem uma conta do Batch, crie um conjunto de exemplo de nós de computação do Windows para fins de teste. O conjunto deste exemplo rápido é constituído por dois nós a executar uma imagem do Windows Server 2012 R2 do Azure Marketplace.


1. Na conta do Batch, selecione **Conjuntos** > **Adicionar**.

2. Introduza um **ID do Conjunto** com o nome *mypool*. 

3. Em **Sistema Operativo**, selecione as definições seguintes (pode explorar outras opções).
  
   |Definição  |Value  |
   |---------|---------|
   |**Tipo de Imagem**|Marketplace (Linux/Windows)|
   |**Publicador**     |MicrosoftWindowsServer|
   |**Oferta**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![Selecionar um sistema operativo para o conjunto][pool_os] 

4. Desloque-se para baixo para introduzir as definições de **Tamanho do Nó** e **Dimensionamento**. Neste exemplo rápido, o tamanho do nó sugerido oferece um bom equilíbrio de desempenho em comparação com o custo.
  
   |Definição  |Value  |
   |---------|---------|
   |**Escalão de preço do nó**     |Standard_A1|
   |**Nós dedicados de destino**     |2|

   ![Selecionar um tamanho para o conjunto][pool_size] 

5. Nas definições restantes, mantenha as predefinições e selecione **OK** para criar o conjunto.

O Batch cria o conjunto de imediato, mas demora alguns minutos a alocar e a iniciar os nós de computação. Durante deste período, o **Estado de alocação** do conjunto é **A Redimensionar**. Pode prosseguir e criar um trabalho e tarefas enquanto o conjunto é redimensionado. 

![Conjunto no estado A Redimensionar][pool_resizing]

Após alguns minutos, o estado do conjunto passa para **Estável** e o nó é iniciado. Selecione **Nós** para verificar o estado dos nós. Quando o estado de um nó for **Inativo**, o mesmo estará pronto para executar tarefas. 

## <a name="create-a-job"></a>Criar uma tarefa

Agora que tem um conjunto, crie um trabalho para ser executado no mesmo. Os trabalhos do Batch são grupos lógicos de uma ou mais tarefas. Os trabalhos incluem definições comuns às tarefas, como a prioridade e o conjunto no qual as tarefas vão ser executadas. Inicialmente, os trabalhos não têm tarefas. 

1. Na vista de conta do Batch, selecione **Tarefas** > **Adicionar**. 

2. Introduza um **ID de Trabalho** com o nome *myjob*. Em **Conjunto**, selecione *mypool*. Mantenha as predefinições nas restantes definições e selecione **OK**.

   ![Criar uma tarefa][job_create]

Depois de o trabalho ser criado, é aberta a página **Tarefas**.

## <a name="create-tasks"></a>Criar tarefas

Agora, crie tarefas de exemplo para serem executadas no trabalho. Normalmente, são criadas várias tarefas que o Batch coloca em fila e distribui para serem executadas nos nós de computação. Neste exemplo, vai criar duas tarefas idênticas. Cada tarefa executa uma linha de comandos para apresentar as variáveis de ambiente do Batch num nó de computação e, em seguida, aguarda 90 segundos. 

Quando utiliza o Batch, a linha de comandos é onde especifica a aplicação ou o script. O Batch apresenta várias formas de implementar aplicações e scripts para os nós de computação. 

Para criar a primeira tarefa:

1. Selecione **Adicionar**.

2. Introduza um **ID de Tarefa** com o nome *mytask*. 

3. Em **Linha de comandos**, introduza `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Mantenha as predefinições nas restantes definições e selecione **OK**.

   ![Criar uma tarefa][task_create]

Depois de criar uma tarefa, o Batch coloca-a em fila para executá-la no conjunto. Assim que um nó estiver disponível para executá-la, a tarefa é executada.

Para criar uma segunda tarefa, volte ao passo 1. Introduza outro **ID de Tarefa**, mas especifique uma linha de comandos idêntica. Se a primeira tarefa ainda estiver em execução, o Batch começa a segunda tarefa no outro nó do conjunto.

## <a name="view-task-output"></a>Ver o resultado das tarefas

As tarefas de exemplo anteriores são concluídas em poucos minutos. Para ver o resultado de uma tarefa concluída, selecione **Ficheiros no nó** e selecione o ficheiro `stdout.txt`. Este ficheiro mostra o resultado padrão da tarefa. Os conteúdos são semelhantes ao seguinte:

![Ver o resultado das tarefas][task_output]

O conteúdo mostra as variáveis de ambiente do Azure Batch que estão definidas no nó. Quando criar os seus próprios trabalhos e as suas próprias tarefas do Batch, pode referenciar estas variáveis de ambiente nas linhas de comandos de tarefas e nas aplicações e scripts executados pelas linhas de comandos.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar com os tutoriais e exemplos do Batch, utilize a conta do Batch e a conta de armazenamento associada que foi criada neste início rápido. A conta do Batch em si não é cobrada.

É cobrado o conjunto enquanto os nós estiverem em execução, mesmo se não existirem tarefas agendadas. Quando já não precisar do conjunto, elimine-o. Na vista da conta, selecione **Conjuntos** e o nome do conjunto. Em seguida, selecione **Eliminar**.  Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados. 

Quando já não for necessário, elimine o grupo de recursos, a conta do Batch e todos os recursos relacionados. Para tal, selecione o grupo de recursos da conta do Batch e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma conta, um conjunto e um trabalho do Batch. O trabalho executou tarefas de exemplo e viu os resultados num dos nós. Agora que compreende os conceitos principais do serviço do Batch, está pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch. 

> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png