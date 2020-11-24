---
title: Monitorizar e resolver problemas de uma aplicação de armazenamento na cloud no Azure | Microsoft Docs
description: Utilize as ferramentas de diagnóstico, as métricas e os alertas para resolver problemas e monitorizar uma aplicação na cloud.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544790"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorizar e resolver problemas de uma aplicação de armazenamento na cloud

Este tutorial é a quarta e última parte de uma série. Ficará a saber como monitorizar e resolver problemas de uma aplicação de armazenamento na cloud.

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Ativar o registo e as métricas
> * Ativar os alertas de erros de autorização
> * Executar o tráfego de teste com tokens SAS incorretos
> * Transferir e analisar registos

A [Análise de armazenamento do Azure](../common/storage-analytics.md) proporciona dados de métricas e de registos para uma conta de armazenamento. Estes dados disponibilizam informações sobre o estado de funcionamento da conta de armazenamento. Para recolher dados da análise de armazenamento do Azure, pode configurar registos, métricas e alertas. Este processo envolve ativar o registo, configurar as métricas e ativar os alertas.

O registo e as métricas das contas de armazenamento são ativados no separador **Diagnóstico** do portal do Azure. O registo de armazenamento permite-lhe registar detalhes de pedidos com êxito e de pedidos com falhas na conta de armazenamento. Estes registos permitem-lhe ver os detalhes de operações de leitura, de escrita e de eliminação relativamente a tabelas, filas e blobs do Azure. Também lhe permitem ver os motivos dos pedidos com falhas como erros de tempos limite, de limitação e de autorização.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Faça login no [portal Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Ativar o registo e as métricas

No menu à esquerda, selecione **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione a conta de armazenamento na lista de recursos.

Em **Definições de diagnóstico (clássico)**, defina **Estado** como **Ativado**. Certifique-se de que todas as opções em **Propriedades do Blob** estão ativadas.

Quando concluído, clique em **Guardar**

![Screenshot que realça a secção que contém as definições de configuração para ligar o registo e as métricas.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Ativar alertas

Os alertas proporcionam uma forma de enviar um e-mail aos administradores ou de acionar um webhook com base numa métrica que viola um limiar. Neste exemplo, ativa um alerta para a métrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navegue até à conta de armazenamento no portal do Azure

Na secção **Monitorização**, selecione **Alertas (clássico)**.

Selecione **Adicionar alerta (clássico)** e preencha o formulário **Adicionar regra** com as informações necessárias. No menu pendente **Métrica**, selecione `SASClientOtherError`. Para permitir que o alerta seja acionado após o primeiro erro, no menu pendente **Condição**, selecione **Maior que ou igual a**.

![Painel Diagnóstico](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simular um erro

Para simular um alerta válido, pode tentar pedir um blob inexistente na conta de armazenamento. O comando seguinte requer um nome de contentor de armazenamento. Pode utilizar o nome de um contentor existente ou criar um novo para os fins deste exemplo.

Substitua os marcadores de posição pelos valores real (certifique-se de que `<INCORRECT_BLOB_NAME>` está definido como um valor que não existe) e execute o comando.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

A imagem seguinte é um alerta de exemplo que se baseia na falha simulada executada com o exemplo anterior.

 ![Alerta de exemplo](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Transferir e ver registos

Os registos de armazenamento armazenam dados num conjunto de blobs num contentor do blobs denominado **$logs** na conta de armazenamento. Este contentor não aparecerá se listar todos os contentores de blobs na sua conta, mas poderá ver o conteúdo se aceder diretamente ao ele.

Neste cenário, utilize o [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) para interagir com a conta de armazenamento do Azure.

### <a name="download-microsoft-message-analyzer"></a>Transferir o Microsoft Message Analyzer

Transfira o [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) e instale a aplicação.

Lance a aplicação e escolha **O Ficheiro**  >  **Aberto**  >  **de Outras Fontes de Ficheiros**.

Na caixa de diálogo **Seletor de Ficheiros**, selecione **+ Adicionar Ligação do Azure**. Introduza o **nome da conta de armazenamento** e a **chave da conta** e clique em **OK**.

![Microsoft Message Analyzer – Caixa de Diálogo Adicionar Ligação ao Armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Assim que estiver ligado, expanda os contentores na vista de árvore de armazenamento para visualizar os blobs de registo. Selecione o registo mais recente e clique em **OK**.

![Screenshot que mostra o Analisador de Mensagens do Microsoft e realça o ficheiro de registo selecionado.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Na caixa de diálogo **Nova Sessão**, clique em **Iniciar** para ver o registo.

Depois de abrir o registo, pode ver os eventos de armazenamento. Como pode ver na imagem seguinte, foi acionado um `SASClientOtherError` na conta de armazenamento. Para obter informações adicionais sobre o registo de armazenamento, visite [Análise de Armazenamento](../common/storage-analytics.md).

![Microsoft Message Analyzer – Ver eventos](media/storage-monitor-troubleshoot-storage-application/figure5.png)

O [Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) é outra ferramenta que serve para interagir com as contas de armazenamento, incluindo o contentor **$logs** e os registos nele contidos.

## <a name="next-steps"></a>Passos seguintes

Na quarta e última parte da série, aprendeu a monitorizar e a resolver problemas da conta de armazenamento, como:

> [!div class="checklist"]
> * Ativar o registo e as métricas
> * Ativar os alertas de erros de autorização
> * Executar o tráfego de teste com tokens SAS incorretos
> * Transferir e analisar registos

Siga esta ligação para ver os exemplos de armazenamento pré-criados.

> [!div class="nextstepaction"]
> [Exemplos de script de armazenamento do Azure](storage-samples-blobs-cli.md)