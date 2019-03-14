---
title: Compor uma cena do Blender com o Azure Batch e o Batch Explorer
description: Tutorial - Como compor vários frames de uma cena do Blender com o Azure Batch e a aplicação cliente Batch Explorer
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 8a512676ab0e56f51c0fb9c59f2e530cfcf73333
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791431"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Tutorial: Compor uma cena Blender com o Explorador do Batch

Este tutorial mostra como compor vários frames de uma cena de demonstração do Blender. O Blender é utilizado no tutorial porque é gratuito para o cliente e para as VMs de composição, mas o processo é muito semelhante se forem utilizadas outras aplicações, como Maya ou 3ds Max.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Carregar uma cena do Blender para o armazenamento do Azure
> * Criar um conjunto do Batch com múltiplos nós para realizar a composição
> * Compor vários frames
> * Ver e transferir os ficheiros de frames compostos

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma subscrição pay as you go ou de outra opção de compra do Azure para utilizar aplicações de composição no Batch no modelo de faturação de pagamento por utilização. O licenciamento de pagamento por utilização não será suportado se utilizar uma oferta gratuita do Azure que fornece um crédito monetário.

Precisa de uma conta do Azure Batch com uma conta de armazenamento associada.  Veja um dos artigos de Início Rápido do Batch, como o [artigo da CLI](https://docs.microsoft.com/azure/batch/quick-create-cli), para criar uma conta do Batch.

Para o tamanho de VM e o número de VMs especificados neste tutorial, é necessária uma quota de núcleos de baixa prioridade de, pelo menos, 50 núcleos; a quota predefinida pode ser utilizada, mas terá de ser utilizada uma VM com um tamanho inferior, o que significa que as imagens demoram mais tempo a ser compostas. O processo para pedir uma quota de núcleos maior está detalhado [neste artigo](https://docs.microsoft.com/azure/batch/batch-quota-limit).

Por fim, o [Batch Explorer](https://azure.github.io/BatchExplorer/) tem de estar instalado; está disponível para Windows, OSX e Linux. É opcional, mas se o [Blender](https://www.blender.org/download/) for instalado, o ficheiro de modelo de exemplo pode ser visto.

## <a name="download-the-demo-scene"></a>Transferir a cena de demonstração

Transfira o ficheiro ZIP de demonstração “Class room” do Blender na [página Web Blender Demo Files](https://www.blender.org/download/demo-files/) (Ficheiros de Demonstração do Blender) e deszipe-o numa unidade local.

## <a name="upload-a-scene-to-azure-storage"></a>Carregar uma cena para o armazenamento do Azure

Criar um contentor de conta de armazenamento para os ficheiros de cena de demonstração:

* Inicie o Batch Explorer
* Selecione o item de menu “Data” (“Dados”) no menu principal, no lado esquerdo
* Confirme que “File groups” (“Grupos de ficheiros”) está selecionado no menu pendente.
* Selecione o botão “+” e crie um “Empty file group” (“Grupo de ficheiros vazio”) novo, denominado “blender-classroom”
  * Os grupos de ficheiros são simplesmente contentores de blobs do Armazenamento do Azure com o prefixo “fgrp-”; é uma convenção utilizada para filtrar outros contentores na conta de armazenamento.

![Grupo de ficheiros para ficheiros de cenas](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Carregar os ficheiros de cenas:

* Selecione o contentor novo e arraste e largue os conteúdos da pasta “classroom” para o contentor no Batch Explorer.

![Ficheiros de cenas carregados](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Criar o contentor do armazenamento do Azure para as imagens de saída

Criar um contentor de conta de armazenamento para os ficheiros de saída de cena de demonstração:

* Selecione o item de menu “Data” (“Dados”) no menu principal, no lado esquerdo
* Selecione o botão “+” e crie um “Empty file group” (“Grupo de ficheiros vazio”) novo, denominado “render-output”

![Grupo de ficheiros para ficheiros de saída](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Criar um conjunto de VMs para composição

Crie um conjunto do Batch com a imagem da VM do Azure Marketplace de composição que contém a aplicação Blender:

* Selecione o item de menu “Galery” (“Galeria”) no menu principal, no lado esquerdo
* Selecione o item “Blender” para a lista de itens de aplicação.
* Selecionar os itens para a composição de frames no Windows Server
* Opcionalmente, o ícone de ligação no lado direito do item pode ser selecionado para ver os ficheiros de modelo que vão ser utilizados para criar um conjunto e um trabalho.

![Itens da galeria do Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Selecione o botão “Create pool for later use” (“Criar conjunto para utilização posterior”)
  * Deixe o nome do conjunto como “blender-windows”
  * Defina a “contagem de vms dedicadas” como “0”
  * Defina a “contagem de vms de baixa prioridade” como “3”
  * Defina “Node size” (“Tamanho do nó”) como “Standard_F16” – pode ser selecionado outro tamanho de VM, mas o tempo de composição de um frame dependerá sobretudo do número de núcleos.
* Selecione o botão verde para criar o conjunto
  * O conjunto é criado quase de imediato, mas as VMs demorarão alguns minutos a serem alocadas e iniciadas.

![Modelo de conjunto para o Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Tenha em conta que, se houve VMs num conjunto, o custo das mesmas é cobrado na sua subscrição do Azure. Para parar esses custos, o conjunto tem de ser eliminado ou as VMs eliminadas. Elimine o conjunto no final do tutorial, para evitar custos contínuos.

O estado do agrupamento e VMs pode ser monitorizado na vista de 'Conjuntos'; o exemplo seguinte mostra as três VMs teriam sido alocadas, dois foram iniciadas e estão inativos, ainda está a iniciar: ![Mapa térmico do conjunto](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Criar um trabalho de composição

Criar um trabalho de composição para compor alguns frames com o conjunto que foi criado:
* Selecione o item de menu “Galery” (“Galeria”) no menu principal, no lado esquerdo
* Selecione o item “Blender” para a lista de itens de aplicação.
* Selecione os itens para a composição de frames no Windows Server.
* Selecione o botão “Run job with existing pool” (“Executar trabalho com o conjunto existente”).
* Selecione o conjunto “blender-windows”
* Defina “Job name” (“Nome do trabalho”) como “blender-render-tutorial1”
* Selecione “fgrp-blender-classroom” em “Input data” (“Dados de entrada”)
* Selecione o ícone de ficheiro para “Blend file” (“Ficheiro blend”) e selecione “classroom.blend”
* Deixe “Frame start” (“Início do frame”) como “1” e defina “Frame end” (“Fim do frame”) como “5”
* Defina “Outputs” (“Saídas”) como “fgrp-render-output”
* Selecione o botão verde para criar o trabalho; é criado um trabalho com cinco tarefas, uma para cada frame

![Modelo de trabalho para o Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Depois da tarefa e todas as tarefas foram criadas, a tarefa será apresentada juntamente com as tarefas da tarefa: ![Lista de tarefas da tarefa](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Quando uma tarefa é executada pela primeira vez numa VM de conjunto, é executada uma tarefa de preparação do trabalho do Batch, a qual copia os ficheiros de cenas do grupo de ficheiros de armazenamento para a VM, de modo a que o Blender possa aceder aos mesmos.
Pode determinar o estado da composição no ficheiro de registo stdout.txt que o Blender produz.  Selecione uma tarefa. Por predefinição, são apresentadas as “Task Outputs” (“Saídas da Tarefa”) e o ficheiro “stdout.txt” pode ser selecionado e visto.
![ficheiro stdout](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Se o conjunto de "windows blender" estiver selecionado, o pool de VMs será visto no estado de execução: ![Mapa térmico do conjunto connosco de execução](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

As imagens compostas demorarão vários minutos a ser produzidas, consoante o tamanho de VM escolhido.  Se for utilizada a VM F16 especificada acima, a composição dos frames demorará aproximadamente 16 minutos a ser concluída.

## <a name="view-the-rendering-output"></a>Ver a saída da composição

Quando terminar composição de quadros, essas tarefas serão apresentadas como concluído: ![A conclusão de tarefas](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

A imagem composta é escrita para a VM pela primeira vez e pode ser visualizada ao selecionar a pasta "wd": ![Composto de imagem no nó no conjunto](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

O modelo do trabalho também especifica que o frame de saída e os ficheiros de registo são escritos primeiro no grupo de ficheiros da conta do Armazenamento do Azure que foi indicado durante a criação do trabalho.  Os "dados" da interface do Usuário podem ser utilizados para ver os registos; e ficheiros de saída pode ser também utilizado para transferir os ficheiros: ![Composto de imagem no grupo de ficheiros de armazenamento](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Quando todas as tarefas estiverem concluídas, a tarefa será marcada como concluída: ![Tarefa e todas as tarefas concluídas](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

> [!WARNING]
> Para parar os custos que as VMs estão a acumular para a subscrição do Azure, o conjunto tem de ser eliminado (também pode ser redimensionado para zero nós).

* Selecione “Pools” (“Conjuntos”)
* Selecione o conjunto “blender-windows”
* Clique com o botão direito do rato e em “Delete” (“Eliminar”) ou selecione o ícone de caixote do lixo, acima do conjunto.

## <a name="next-steps"></a>Passos Seguintes
* Na secção “Gallery” (“Galeria”), explore as aplicações de composição disponíveis através do Batch Explorer.
* Há vários modelos disponíveis para cada aplicação, que vão aumentar ao longo do tempo.  Por exemplo, para os modelos do Blender, existe um que divide uma imagem em mosaicos, pelo que é possível compor partes de uma imagem em paralelo.
* Para obter uma descrição completa das capacidades de composição, veja o conjunto de artigos [aqui](https://docs.microsoft.com/azure/batch/batch-rendering-service).
