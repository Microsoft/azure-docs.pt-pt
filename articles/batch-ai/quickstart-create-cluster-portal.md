---
title: Início Rápido do Azure - Criar um cluster do Batch AI - Portal | Microsoft Docs
description: Início Rápido - Criar um cluster do Batch AI preparar modelos de machine learning e IA - Portal do Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 49e78f1774a2998de7070453f7c098aef1761242
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093233"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Início rápido: Criar um cluster para tarefas de formação de IA do Batch no portal do Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Este início rápido mostra como utilizar o portal do Azure para criar um cluster do Batch AI que pode utilizar para preparar modelos de machine learning e IA. O Batch AI é um serviço gerido que permite aos cientistas de dados e investigadores de IA preparar modelos de machine learning e IA em escala em clusters de máquinas virtuais do Azure.

Inicialmente, o cluster tem um único nó de GPU e um servidor de ficheiros anexado. Depois de concluir este início rápido, terá um cluster que pode aumentar verticalmente e utilizar para preparar modelos de aprendizagem aprofundada. Submeta tarefas de preparação para o cluster com o Batch AI, ferramentas do [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou o [Visual Studio Tools para IA](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este início rápido. Se tiver um par de chaves SSH existente, este passo pode ser ignorado.

Para criar um par de chaves SSH, execute o seguinte comando a partir da shell do Bash e siga as direções no ecrã. Por exemplo, pode utilizar o [Azure Cloud Shell](../cloud-shell/overview.md) ou, no Windows, o [Windows Subsystem for Linux](/windows/wsl/install-win10). A saída do comando inclui o nome de ficheiro do ficheiro de chave pública. Copie o conteúdo do ficheiro de chave pública (`cat ~/.ssh/id_rsa.pub`) para a área de transferência ou outro local a que possa aceder num passo posterior.

```bash
ssh-keygen -t rsa -b 2048
```

Para obter informações mais detalhadas sobre como criar pares de chaves SSH, veja [Como criar e utilizar um par de chaves públicas e privadas SSH para VMs do Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Criar uma área de trabalho do Batch AI

Comece por criar uma área de trabalho do Batch AI para organizar os recursos do Batch AI. Uma área de trabalho pode conter um ou mais clusters ou outros recursos do Batch AI.

1. Selecione **Todos os serviços** e filtre por **Batch AI**.

2. Selecione **Adicionar Área de Trabalho**.

3. Introduza valores para **Nome da área de trabalho** e **Grupo de recursos**. Se quiser, selecione diferentes opções para **Subscrição** e **Localização** para a área de trabalho. Selecione **Criar Área de Trabalho**.

   ![Criar área de trabalho do Batch AI](./media/quickstart-create-cluster-portal/create-workspace.png)

Quando a mensagem **Implementação concluída com êxito** for apresentada, aceda ao recurso que criou e selecione a área de trabalho.

## <a name="create-a-file-server"></a>Criar um servidor de ficheiros

Um servidor de ficheiros do Batch AI é um NFS de nó único que pode ser montado automaticamente em nós de cluster. É uma das várias formas de fornecer armazenamento para dados de entrada e saída das suas tarefas de preparação.

1. Na área de trabalho, selecione **Servidor de ficheiros** > **Adicionar servidor de ficheiros do Batch AI**.

2. Introduza os valores para **Nome do servidor de ficheiros** e **Tamanho da VM**. Neste início rápido, é sugerido um tamanho de VM *D1_v2 Standard* para o servidor de ficheiros. Escolha um tamanho diferente se tiver de armazenar grandes quantidades de dados de entrada ou de saída para tarefas de preparação.

3. Introduza um **Nome de utilizador de administrador** e copie o conteúdo do ficheiro de chave pública SSH para **Chave SSH**. Aceite as predefinições para os restantes valores e selecione **Criar servidor de ficheiros**.

   ![Criar um servidor de ficheiros do Batch AI](./media/quickstart-create-cluster-portal/create-file-server.png)

A implementação do servidor de ficheiros demora alguns minutos.

Após a criação do servidor, clique em **Propriedades** e anote as **Definições de montagem**. Pode encaminhar o SSH para o endereço IP público do servidor para carregar e transferir dados de preparação e ficheiros de saída no diretório indicado (*/data*).

![Propriedades do servidor de ficheiros](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Criar um cluster

Os passos seguintes criam um cluster com um único nó de GPU. O nó de cluster executa uma imagem do Ubuntu Server predefinida criada para alojar aplicações baseadas em contentores, que pode utilizar para a maioria das cargas de trabalho de preparação. O nó de cluster monta o servidor de ficheiros no respetivo ponto de montagem. 

1. Na área de trabalho do Batch AI, selecione **Cluster** > **Adicionar cluster do Batch AI**.

2. Introduza os valores para **Nome do cluster** e as seguintes definições. O tamanho de VM sugerido tem um GPU NVIDIA Tesla K80.
  
   |Definição  |Value  |
   |---------|---------|
   |**Tamanho da VM**     |Standard NC6|
   |**Número de nós de destino**     |1|

3. Introduza um **Nome de utilizador de administrador** e copie o conteúdo do ficheiro de chave pública SSH para **Chave SSH**. Aceite as predefinições para os restantes valores nesta página e selecione **seguinte: Configuração de nó**.

   ![Introduzir as informações de cluster básicas](./media/quickstart-create-cluster-portal/create-cluster.png)

4. Em **Montar volumes**, selecione **Referências do servidor de ficheiros** > **Adicionar**. Selecione o servidor de ficheiros que criou anteriormente. Introduza um **Caminho de montagem relativo** onde o servidor de ficheiros está montado em cada nó de cluster. Selecione **Guardar e continuar**.

   ![Adicionar referência do servidor de ficheiros](./media/quickstart-create-cluster-portal/file-server-reference.png)

Guarde a configuração do nó e selecione **Criar Cluster**.

O Batch AI demora alguns minutos a alocar o nó. Durante deste período, o **Estado de alocação** do cluster é **A Redimensionar**. Após alguns minutos, o estado do cluster passa para **Estável** e o nó é iniciado.

![Início do cluster](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Selecione o nome do cluster para verificar o estado do nó. Quando o estado de um nó for **Inativo**, o mesmo estará pronto para executar tarefas de preparação.

### <a name="list-cluster-nodes"></a>Listar nós de cluster

Se precisar de ligar aos nós de cluster (neste caso, um único nó) para instalar aplicações ou efetuar manutenção, obtenha as informações de ligação no portal. Após a criação do cluster, clique em **Nós** e anote as definições de **Ligação** SSH (endereço IP e número da porta).

![Nós de cluster](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Utilize estas informações para fazer uma ligação SSH ao nó. Por exemplo, substitua o endereço IP e o número da porta corretos do nó no comando seguinte:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Redimensionar o cluster

Quando utilizar o seu cluster para preparar um modelo, poderá precisar de mais recursos de computação. Por exemplo, para aumentar o tamanho para dois nós para uma tarefa de preparação distribuída, selecione **Dimensionar** e defina o **Número de nós de destino** para dois. Guarde a configuração.

![Dimensionar o cluster](./media/quickstart-create-cluster-portal/scale-cluster.png)

Demora alguns minutos a redimensionar o cluster.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar com os tutoriais e exemplos do Batch AI, utilize a área de trabalho, o servidor de ficheiros e o cluster do Batch AI criados neste início rápido.

São cobrados o cluster e o servidor de ficheiros do Batch AI enquanto as máquinas virtuais subjacentes estiverem em execução, mesmo se não existirem tarefas agendadas. Se quiser manter a configuração do cluster quando não existirem tarefas em execução, redimensione o cluster para 0 nós. Mais tarde, redimensione-o para 1 ou mais nós para executar as suas tarefas. 

Quando já não for necessária, elimine a área de trabalho do Batch AI que contém o cluster e o servidor de ficheiros. Para tal, selecione a área de trabalho do Batch AI e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar um cluster e um servidor de ficheiros anexado do Batch AI com o portal do Azure. Para saber mais sobre como utilizar um cluster do Batch AI para preparar um modelo, avance para o início rápido para preparar um modelo de aprendizagem aprofundada.

> [!div class="nextstepaction"]
> [Preparar um modelo de aprendizagem aprofundada](./quickstart-tensorflow-training-cli.md)
