---
title: Atualizar para uma conta de armazenamento para fins gerais v2 - armazenamento do Azure | Documentos da Microsoft
description: Atualizar para contas de armazenamento para fins gerais v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/28/2019
ms.author: tamram
ms.openlocfilehash: d57023063fe23db9f57d52ab9cdf99e0687c1fdf
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217296"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Atualizar para uma conta de armazenamento para fins gerais v2

Contas de armazenamento para fins gerais v2 de suportar as funcionalidades de armazenamento do Azure mais recente e incorporam toda a funcionalidade de fins gerais v1 e contas de armazenamento de Blobs. Contas para fins gerais v2 são recomendadas para a maioria dos cenários de armazenamento. Contas para fins gerais v2 fornecem o mais baixo por gigabyte os preços de capacidade do armazenamento do Azure, bem como competitiva em termos da indústria para transações.

Atualizar para uma conta de armazenamento para fins gerais v2 de sua para fins gerais v1 ou contas de armazenamento de BLOBs é simples. Pode atualizar com o portal do Azure, PowerShell ou da CLI do Azure.

> [!IMPORTANT]
> Atualizar uma conta de armazenamento v1 para fins gerais v2 é permanente e não pode ser anulada.

## <a name="upgrade-using-the-azure-portal"></a>Atualizar com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na **configurações** secção, clique em **configuração**.
4. Em **Tipo de conta**, clique em **Atualizar**.
5. Em **Confirmar atualização**, introduza o nome da sua conta. 
6. Clique em **atualizar** na parte inferior do painel.

## <a name="upgrade-with-powershell"></a>Atualizar com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para atualizar uma conta para fins gerais v1 para uma conta de fins gerais v2 com o PowerShell, primeiro Atualize o PowerShell para utilizar a versão mais recente do **Az.Storage** módulo. Veja [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para obter informações sobre a instalação do PowerShell. 

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos e a conta de armazenamento:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Atualizar com a CLI do Azure

Para atualizar uma conta para fins gerais v1 para uma conta de fins gerais v2 com a CLI do Azure, instale primeiro a versão mais recente da CLI do Azure. Veja [Install the Azure CLI 2.0 ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Instalar a CLI 2.0 do Azure) para obter informações sobre a instalação da CLI. 

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos e a conta de armazenamento:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Especificar um escalão de acesso para os dados de blob

As contas de fins gerais v2 suportam todos os serviços de armazenamento do Azure e objetos de dados, mas o escalão de acesso está disponível apenas para blobs de blocos no armazenamento de Blobs. Quando atualizar para uma conta de armazenamento para fins gerais v2, pode especificar um escalão de acesso para os seus dados de Blobs. 

Escalões de acesso permitem-lhe escolher o armazenamento mais económico com base nos seus padrões de utilização antecipada. Os blobs de blocos podem ser armazenados numa camada de acesso frequente, esporádico ou arquivo. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de Blobs do Azure: Acesso frequente, esporádico e de camadas de armazenamento de arquivo](../blobs/storage-blob-storage-tiers.md).

Por predefinição, é criada uma nova conta de armazenamento no escalão acesso frequente e uma conta de armazenamento para fins gerais v1 é atualizada para o escalão de acesso frequente. Se está a explorar a camada de acesso a utilizar para a sua pós-atualização de dados, considere o seu cenário. Existem dois cenários de usuário comum para a migração para uma conta para fins gerais v2:

* Tem uma conta de armazenamento para fins gerais v1 existente e quer avaliar uma atualização para uma conta de armazenamento para fins gerais v2, com a camada de acesso de armazenamento correta para dados de Blobs.
* Decidiu utilizar uma conta de armazenamento para fins gerais v2 ou já tiver uma e pretende avaliar se deve utilizar a camada de acesso de armazenamento de acesso frequente ou esporádico para dados de Blobs.

Em ambos os casos, a primeira prioridade é estimar os custos de armazenamento, acesso e a funcionar nos seus dados armazenados numa conta de armazenamento para fins gerais v2 e compará-los com seus custos atuais.

## <a name="pricing-and-billing"></a>Preços e faturação

A atualização de uma conta de armazenamento v1 para uma conta para fins gerais v2 é gratuita. No entanto, a alteração da camada de acesso de armazenamento pode resultar em alterações à sua fatura. 

Todas as contas de armazenamento utilizam um modelo de preços para o armazenamento de blobs com base na camada de cada blob. Ao utilizar uma conta de armazenamento, aplicam-se as seguintes considerações de faturação:

* **Os custos de armazenamento**: Para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso de armazenamento. O custo por gigabyte diminui conforme a camada se torna mais esporádica.

* **Os custos de acesso a dados**: Aumentar os custos de acesso de dados conforme a camada se torna mais esporádica. Para dados na camada de acesso de armazenamento esporádico e de arquivo, é cobrada uma taxa de acesso de dados por gigabyte para leituras.

* **Os custos de transação**: Existe um custo por transação para todas as camadas que aumenta conforme a camada se torna mais esporádica.

* **Os custos de transferência de dados de Georreplicação**: Este custo aplica-se apenas a contas com a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.

* **Custos de transferência de dados de saída**: Transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) incorrem a faturação de utilização de largura de banda numa base por gigabyte, consistente com as contas de armazenamento para fins gerais.

* **A alteração da camada de acesso de armazenamento**: Alteração da camada de acesso de armazenamento de conta de acesso esporádico para frequente incorre um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de acesso da conta de frequente para esporádica incorre um encargo igual à escrita de todos os dados na camada esporádica (contas GPv2 apenas).

> [!NOTE]
> Para obter mais informações sobre o modelo de preços das contas de armazenamento, veja a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar os custos para os padrões de utilização atual

Para estimar os custos de armazenar e aceder aos dados de BLOBs numa conta de armazenamento para fins gerais v2 numa camada específica, avaliar o seu padrão de utilização existente ou fazer uma aproximação do padrão de utilização esperado. Em geral, precisa de saber o seguinte:

* O consumo de armazenamento Blob, em gigabytes, incluindo:
    - Que quantidade de dados está a ser armazenada na conta de armazenamento?
    - De que forma o volume de dados é alterado mensalmente: os novos dados substituem constantemente os dados antigos?
* O padrão de acesso primária para os seus dados de armazenamento de BLOBs, incluindo:
    - A quantidade de dados está a ser lida da e escrita para a conta de armazenamento? 
    - Quantas operações de leitura versus escrever operações ocorrem nos dados na conta de armazenamento?

Para decidir qual a camada de acesso melhor às suas necessidades, pode ser útil para determinar a capacidade de dados de BLOBs e como esses dados está a ser utilizados. Isso pode ser melhor feito ao observar as métricas de monitorização para a sua conta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorizar contas de armazenamento existentes

Para monitorizar as suas contas de armazenamento existentes e recolher estes dados, pode utilizar a Análise do Armazenamento do Azure, que efetua o registo e fornece dados de métricas para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas agregadas de transações e dados de capacidade sobre pedidos enviados para o serviço de armazenamento relativamente aos tipos de contas de Armazenamento de blobs, GPv1 e GPv2. Estes dados são armazenados em tabelas bem conhecidas na mesma conta de armazenamento.

Para obter mais informações, veja [About Storage Analytics Metrics (Sobre Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Storage Analytics Metrics Table Schema (Esquema da Tabela de Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas de Armazenamento de blobs expõem o ponto final do serviço Tabela apenas para armazenar e aceder aos dados de métricas relativos a essa conta. 

Para monitorizar o consumo de armazenamento do Armazenamento de blobs, tem de ativar as métricas de capacidade.
Com esta opção ativada, os dados de capacidade são registados diariamente para um serviço Blob de uma conta de armazenamento e registados como uma entrada que é escrita na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar os padrões de acesso a dados do Armazenamento de blobs, tem de ativar as métricas de transação horária a partir da API. Com as métricas de transação horária ativadas, as transações por API são agregadas ao fim de cada hora e registadas como uma entrada que é escrita na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* regista as transações para o ponto final secundário quando são utilizadas contas de armazenamento RA-GRS.

> [!NOTE]
> Se tiver uma conta de armazenamento de fins gerais na qual armazenou blobs de páginas e discos de máquinas virtuais ou filas, ficheiros ou tabelas, juntamente com dados de blobs de blocos e de acréscimo, este processo de estimativa não é aplicável. Os dados de capacidade não distinguem os blobs de blocos dos outros tipos e não disponibilizam dados de capacidade para outros tipos de dados. Se utilizar estes tipos, uma metodologia alternativa é ver as quantidades na sua fatura mais recente.

Para obter uma boa aproximação do seu consumo de dados e padrão de acesso, recomendamos que escolha um período de retenção para as métricas que seja representativo da sua utilização normal e o utilize para tirar conclusões. Uma opção é manter os dados das métricas durante sete dias e recolher os dados todas as semanas, para análise no fim do mês. Outra opção é manter os dados das métricas para os últimos 30 dias e recolher e analisar os dados no final desse período de 30 dias.

Para obter detalhes sobre como ativar, recolher e ver dados de métricas, veja [Enabling Azure Storage metrics and viewing metrics data)](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Ativar as métricas do Armazenamento do Azure e visualizar os dados das mesmas).

> [!NOTE]
> O armazenamento, o acesso e a transferência de dados de análise são cobrados também como dados normais do utilizador.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizar métricas de utilização para estimar os custos

#### <a name="capacity-costs"></a>Custos de capacidade

A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida pelos dados do utilizador. A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos registos de análise.

Esta capacidade total consumida pelos dados de utilizador e pelos registos de análise (se ativados) pode depois ser utilizada para estimar o custo do armazenamento dos dados na conta de armazenamento. O mesmo método também pode ser utilizado para estimar os custos de armazenamento nas contas GPv1.

#### <a name="transaction-costs"></a>Custos de transação

A soma dos *'TotalBillableRequests'*, em todas as entradas de uma API na tabela de métricas de transação indica o número total de transações para essa API específica. *Por exemplo,*, o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma do total de pedidos faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para que possa estimar os custos de transação para as contas do Armazenamento de blobs, tem de dividir as transações em três grupos, uma vez que têm um preço diferente.

* Transações de escrita como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de eliminação como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação nas contas de armazenamento GPv1, tem de agregar todas as transações, independentemente da operação/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de acesso a dados e custos de transferência de dados de georreplicação

Embora a análise de armazenamento não forneça a quantidade de dados lidos e escritos numa conta de armazenamento, esta pode ser aproximadamente estimada ao consultar a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas de uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API específica. Do mesmo modo, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para que possa estimar os custos de acesso a dados para as contas do Armazenamento de blobs, precisa de dividir as transações em dois grupos.

* A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada ao ver a soma de *'TotalEgress'* para, principalmente, as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados escritos na conta de armazenamento pode ser estimada ao ver a soma de *'TotalIngress'* para, principalmente, as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

Também pode ser calculado o custo de transferência de dados de georreplicação para contas do Armazenamento de blobs mediante a utilização da estimativa da quantidade de dados escritos se for utilizada uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado sobre o cálculo dos custos de utilização da camada de acesso de armazenamento frequente ou esporádico, dê uma olhada nas perguntas mais frequentes intituladas *"quais são os escalões de acesso frequente e esporádico e como devo decidir sobre qual devo utilizar?"* na [Página de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)
- [Gerir contas de armazenamento do Azure](storage-account-manage.md)
