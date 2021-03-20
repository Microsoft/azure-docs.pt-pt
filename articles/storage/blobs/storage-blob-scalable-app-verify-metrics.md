---
title: Verificar as métricas de débito e latência de uma conta de armazenamento no portal do Azure | Microsoft Docs
description: Saiba como verificar as métricas de débito e latência de uma conta de armazenamento no portal.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 1bec42dabde64fed8126e47e39f19536b28014f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714838"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Verificar as métricas de débito e latência de uma conta de armazenamento

Este tutorial é a quarta e última parte de uma série. Nos tutoriais anteriores, aprendeu a carregar e a transferir grandes quantidades de dados aleatórios para uma conta de armazenamento do Azure. Este tutorial mostra como pode utilizar as métricas para ver o débito e a latência no portal do Azure.

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Configurar gráficos no portal do Azure
> * Verificar as métricas de débito e latência

As [métricas do armazenamento do Azure](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) utilizam o monitor do Azure para proporcionar uma vista unificada do desempenho e da disponibilidade da conta de armazenamento.

## <a name="configure-metrics"></a>Configurar as métricas

Navegue para **métricas** em **DEFINIÇÕES** na sua conta de armazenamento.

Escolha Blob no menu pendente **SUBSERVIÇO**.

Em **MÉTRICA**, selecione uma das métricas encontradas na tabela seguinte:

As métricas seguintes dão-lhe uma ideia da latência e do débito da aplicação. As métricas que configurar no portal encontram-se em médias de 1 minuto. Se uma transação terminada a meio de um minuto, os dados dos minutos são reduzidos para metade para a média. Na aplicação, as operações de carregamento e transferência foram cronometradas e proporcionaram-lhe o resultado do tempo real que demorou a carregar e a transferir os ficheiros. Estas informações podem ser utilizadas em conjunto com as métricas do portal para compreender totalmente o débito.

|Metric|Definição|
|---|---|
|**Latência de E2E Com Êxito**|A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|
|**Latência do servidor de sucesso**|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. |
|**Transações**|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. No exemplo, o tamanho do bloco foi definido como 100 MB. Neste caso, cada bloco de 100 MB é considerado uma transação.|
|**Entrada**|A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. |
|**Saída**|A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. |

Selecione **Últimas 24 horas (Automático)**, junto a **Tempo**. Escolha **Última hora** e **Minuto** para **Granularidade de tempo** e, em seguida, clique em **Aplicar**.

![Métricas da conta de armazenamento](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Os gráficos podem ter mais do que uma métrica atribuída, mas atribuir mais de uma métrica desativa a capacidade de agrupar por dimensões.

## <a name="dimensions"></a>Dimensões

As [dimensões](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) servem para ver mais aprofundadamente os gráficos e obter informações mais detalhadas. Métricas diferentes têm dimensões diferentes. Uma dimensão disponível é a dimensão **Nome da API**. Esta dimensão divide o gráfico em chamadas à API separadas. A primeira imagem abaixo mostra um gráfico de exemplo do total de transações para uma conta de armazenamento. A segunda imagem mostra o mesmo gráfico, mas com a dimensão do nome da API selecionada. Como pode ver, cada transação está listada para dar mais detalhes sobre o número de chamadas realizadas pelo nome da API.

![Métricas da conta de armazenamento – transações sem uma dimensão](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Métricas da conta de armazenamento – transações](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos da VM e clique em Eliminar.

## <a name="next-steps"></a>Passos seguintes

Na quarta parte da série, ficou a saber mais sobre a visualização de métricas para a solução de exemplo, tal como:

> [!div class="checklist"]
> * Configurar gráficos no portal do Azure
> * Verificar as métricas de débito e latência

Siga esta ligação para ver os exemplos de armazenamento pré-criados.

> [!div class="nextstepaction"]
> [Exemplos de script de armazenamento do Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md