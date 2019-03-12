---
title: Suportado métricas com o Azure Monitor no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as métricas suportadas para o Azure Monitor no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: cccc646026457648fc477ac4a858cae7504dcc2a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759360"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Métricas suportadas com o Azure Monitor no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Pode recuperar as suas métricas do Azure monitor, no Azure Stack, no mesmo global Azure. Pode criar as suas medidas no portal, obtê-los a partir da API REST ou consultá-los com o PowerShell ou CLI.

As tabelas seguintes listam as métricas disponíveis com o pipeline de métrico do Monitor do Azure no Azure Stack. Para consultar e aceder a estas métricas, terá de utilizar o **2018-01-01** versão de api-version do perfil de API. Para obter mais informações sobre os perfis de API e o Azure Stack, veja [perfis de versão de API de gerir no Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Percentagem da CPU | Percentagem da CPU | Percentagem | Média | A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais | Nenhuma dimensão |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Capacidade utilizada | Bytes | Média | Capacidade da conta utilizada | Nenhuma dimensão |
| Transações | Transações | Contagem | Total | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média | A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média | A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Percentagem | Média | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Capacidade do Blob | Bytes | Total | A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes. | BlobType |
| BlobCount | Contagem de Blobs | Contagem | Total | O número de blobs no serviço Blob da conta de armazenamento. | BlobType |
| ContainerCount | Contagem do Contentor de Blobs | Contagem | Média | O número de contentores no serviço Blob da conta de armazenamento. | Nenhuma dimensão |
| Transações | Transações | Contagem | Total | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média | A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média | A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Percentagem | Média | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Capacidade de Tabelas | Bytes | Média | A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes. | Nenhuma dimensão |
| TableCount | Contagem de Tabelas | Contagem | Média | O número de tabelas no serviço Tabela da conta de armazenamento. | Nenhuma dimensão |
| TableEntityCount | Contagem de Entidade de Tabelas | Contagem | Média | O número de entidades de tabelas no serviço Tabela da conta de armazenamento. | Nenhuma dimensão |
| Transações | Transações | Contagem | Total | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média | A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média | A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Percentagem | Média | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Capacidade de Fila | Bytes | Média | A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes. | Nenhuma dimensão |
| QueueCount | Contagem de Filas | Contagem | Média | O número de filas no serviço Fila da conta de armazenamento. | Nenhuma dimensão |
| QueueMessageCount | Contagem de Mensagens em Fila | Contagem | Média | O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento. | Nenhuma dimensão |
| Transações | Transações | Contagem | Total | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média | A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média | A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Percentagem | Média | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [monitorizar o Azure no Azure Stack](azure-stack-metrics-azure-data.md).
