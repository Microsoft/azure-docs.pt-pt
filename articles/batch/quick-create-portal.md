---
title: Azure Quickstart - Executar o seu primeiro trabalho em Batch no portal Azure
description: Este quickstart mostra como usar o portal Azure para criar uma conta Batch, um conjunto de nós computacional, e um trabalho que executa tarefas básicas na piscina.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: 1234a932a732cdb6fda1c412a423ae0b1ea089e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184020"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Início Rápido: executar o seu primeiro trabalho do Batch com o portal do Azure

Começa com o Azure Batch utilizando o portal Azure para criar uma conta Batch, um conjunto de nós computacional (máquinas virtuais) e um trabalho que executa tarefas na piscina. Depois de completar este quickstart, você entenderá os conceitos-chave do serviço Batch e estará pronto para experimentar Batch com cargas de trabalho mais realistas em maior escala.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Siga estes passos para criar uma conta do Batch de exemplo para fins de teste. Tem de ter uma conta do Batch para criar conjuntos e trabalhos. Conforme mostrado aqui, pode associar uma conta de armazenamento do Azure à conta do Batch. Apesar de não ser obrigatório para este início rápido, a conta de armazenamento é útil para implementar aplicações e armazenar dados de entrada e saída para a maioria das cargas de trabalho do mundo real.

1. No [portal Azure](https://portal.azure.com), selecione **Criar um** serviço de lote  >  **compute**  >  **de** recurso . 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Screenshot do Serviço de Lote no Mercado Azure.":::

1. No campo **grupo De recursos,** selecione **Criar novo** e insira um nome para o seu grupo de recursos.

1. Introduza um valor para **o nome da conta.** Este nome deve ser único dentro da **Localização** Azure selecionada. Pode conter apenas letras e números minúsculos, e deve estar entre 3-24 caracteres.

1. Na **conta de Armazenamento**, selecione uma conta de armazenamento existente ou crie uma nova.

1. Não altere quaisquer outras definições. Selecione **Rever + criar** e, em seguida, selecione **Criar** para criar a conta Lote.

Quando a mensagem **de implementação tiver sido bem sucedida,** aceda à conta Batch que criou.

## <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Agora que tem uma conta do Batch, crie um conjunto de exemplo de nós de computação do Windows para fins de teste. A piscina para este exemplo rápido consiste em dois nós que executam uma imagem do Windows Server 2019 a partir do Azure Marketplace.

1. Na conta Lote, selecione **Pools**  >  **Add**.

1. Introduza um **ID do Conjunto** com o nome *mypool*.

1. Em **Sistema Operativo**, selecione as definições seguintes (pode explorar outras opções).
  
   |Definição  |Valor  |
   |---------|---------|
   |**Tipo de imagem**|Marketplace|
   |**Publisher**     |microsoftwindowsserver|
   |**Oferta**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. Desloque-se para baixo para introduzir as definições de **Tamanho do Nó** e **Dimensionamento**. O tamanho do nó sugerido oferece um bom equilíbrio de desempenho em comparação com o custo deste exemplo rápido.
  
   |Definição  |Valor  |
   |---------|---------|
   |**Escalão de preço do nó**     |Padrão A1|
   |**Nós dedicados de destino**     |2|

1. Nas definições restantes, mantenha as predefinições e selecione **OK** para criar o conjunto.

O Batch cria o conjunto de imediato, mas demora alguns minutos a alocar e a iniciar os nós de computação. Durante deste período, o **Estado de alocação** do conjunto é **A Redimensionar**. Pode prosseguir e criar um trabalho e tarefas enquanto o conjunto é redimensionado.

Após alguns minutos, o estado de atribuição muda para **Steady**, e os nós começam. Para verificar o estado dos nós, selecione a piscina e, em seguida, selecione **Nós**. Quando o estado de um nó for **Inativo**, o mesmo estará pronto para executar tarefas.

## <a name="create-a-job"></a>Criar uma tarefa

Agora que tem um conjunto, crie um trabalho para ser executado no mesmo. Um trabalho em Lote é um grupo lógico de uma ou mais tarefas. Os trabalhos incluem definições comuns às tarefas, como a prioridade e o conjunto no qual as tarefas vão ser executadas. Inicialmente, o trabalho não tem tarefas.

1. Na vista da conta Lote, selecione **Jobs**  >  **Add**.

1. Introduza um **ID de Trabalho** com o nome *myjob*. Em **Conjunto**, selecione *mypool*. Mantenha as predefinições nas restantes definições e selecione **OK**.

## <a name="create-tasks"></a>Criar tarefas

Agora, selecione o trabalho para abrir a página **Tarefas.** É aqui que vai criar tarefas de amostra para executar no trabalho. Tipicamente, cria-se múltiplas tarefas que o Batch faz e distribui para executar nos nós de computação. Neste exemplo, vai criar duas tarefas idênticas. Cada tarefa executa uma linha de comandos para apresentar as variáveis de ambiente do Batch num nó de computação e, em seguida, aguarda 90 segundos.

Quando utiliza o Batch, a linha de comandos é onde especifica a aplicação ou o script. O Batch apresenta várias formas de implementar aplicações e scripts para os nós de computação.

Para criar a primeira tarefa:

1. Selecione **Adicionar**.

1. Introduza um **ID de Tarefa** com o nome *mytask*.

1. Em **Linha de comandos**, introduza `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Mantenha as predefinições para as definições restantes e **selecione Enviar por isso.**

Depois de criar uma tarefa, o Batch coloca em fila para a execução no conjunto. Assim que um nó estiver disponível para executá-la, a tarefa é executada.

Para criar uma segunda tarefa, repita os passos acima. Introduza outro **ID de Tarefa**, mas especifique uma linha de comandos idêntica. Se a primeira tarefa ainda estiver em execução, o Batch começa a segunda tarefa no outro nó do conjunto.

## <a name="view-task-output"></a>Ver resultado das tarefas

As tarefas de exemplo que criou serão concluídas em alguns minutos. Para visualizar a saída de uma tarefa concluída, selecione a tarefa e, em seguida, selecione **Ficheiros no nó**. Selecione o ficheiro `stdout.txt` para visualizar a saída padrão da tarefa. Os conteúdos são semelhantes ao seguinte:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Screenshot da saída a partir de uma tarefa completa.":::

O conteúdo mostra as variáveis de ambiente do Azure Batch que estão definidas no nó. Quando criar os seus próprios trabalhos e as suas próprias tarefas do Batch, pode referenciar estas variáveis de ambiente nas linhas de comandos de tarefas e nas aplicações e scripts executados pelas linhas de comandos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar com os tutoriais e exemplos do Batch, utilize a conta do Batch e a conta de armazenamento associada que foi criada neste início rápido. A conta do Batch em si não é cobrada.

É cobrado o conjunto enquanto os nós estiverem em execução, mesmo se não existirem tarefas agendadas. Quando já não precisar do conjunto, elimine-o. Na vista da conta, selecione **Conjuntos** e o nome do conjunto. Em seguida, selecione **Eliminar**.  Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados.

Quando já não for necessário, elimine o grupo de recursos, a conta do Batch e todos os recursos relacionados. Para tal, selecione o grupo de recursos da conta do Batch e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma conta, um conjunto e um trabalho do Batch. O trabalho executou tarefas de exemplo e viu os resultados num dos nós. Agora que compreende os conceitos principais do serviço do Batch, está pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch.

> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)
