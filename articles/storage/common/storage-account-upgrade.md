---
title: Atualizar para uma conta de armazenamento v2 de uso geral-armazenamento do Azure | Microsoft Docs
description: Atualize para contas de armazenamento v2 de uso geral.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: e24b7efb9f4af9f730ce79751e2fc5a9d210edbd
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806988"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Atualizar para uma conta de armazenamento v2 de uso geral

As contas de armazenamento de uso geral v2 dão suporte aos recursos mais recentes do armazenamento do Azure e incorporam toda a funcionalidade das contas de armazenamento de blob v1 e de uso geral. Contas de uso geral v2 são recomendadas para a maioria dos cenários de armazenamento. As contas v2 de uso geral fornecem os preços de capacidade por gigabyte mais baixos para o armazenamento do Azure, bem como preços de transação competitivos do setor. As contas v2 de uso geral dão suporte a camadas de acesso de conta padrão de camadas de nível quente ou fria e de camada de blob entre Hot, frio ou arquivo morto.

A atualização para uma conta de armazenamento v2 de uso geral de suas contas de armazenamento de blob v1 ou de uso geral é simples. Você pode atualizar usando o portal do Azure, o PowerShell ou o CLI do Azure.

> [!IMPORTANT]
> A atualização de uma conta de armazenamento de blob v1 ou de uso geral para uso geral V2 é permanente e não pode ser desfeita.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na seção **configurações** , clique em **configuração**.
4. Em **Tipo de conta**, clique em **Atualizar**.
5. Em **Confirmar atualização**, introduza o nome da sua conta.
6. Clique em **Atualizar** na parte inferior da folha.

    ![Tipo de conta de atualização](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para atualizar uma conta v1 de uso geral para uma conta v2 de uso geral usando o PowerShell, primeiro atualize o PowerShell para usar a versão mais recente do módulo **AZ. Storage** . Veja [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para obter informações sobre a instalação do PowerShell.

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e a camada de acesso da conta desejada.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar uma conta v1 de uso geral para uma conta v2 de uso geral usando CLI do Azure, primeiro instale a versão mais recente do CLI do Azure. Veja [Install the Azure CLI 2.0 ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Instalar a CLI 2.0 do Azure) para obter informações sobre a instalação da CLI.

Em seguida, chame o seguinte comando para atualizar a conta, substituindo o nome do grupo de recursos, o nome da conta de armazenamento e a camada de acesso da conta desejada.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Especificar uma camada de acesso para dados de BLOB

As contas de uso geral v2 dão suporte a todos os serviços de armazenamento do Azure e objetos de dados, mas as camadas de acesso estão disponíveis somente se aplicam a blobs de blocos no armazenamento de BLOBs. Ao atualizar para uma conta de armazenamento v2 de uso geral, você pode especificar uma camada de acesso de conta padrão de frequente ou esporádica, que indica a camada padrão em que os dados de blob serão carregados como se o parâmetro de camada de acesso de blob individual não for especificado.

As camadas de acesso ao blob permitem que você escolha o armazenamento mais econômico com base nos padrões de uso previstos. Blobs de blocos podem ser armazenados em camadas quentes, frias ou de arquivo morto. Para obter mais informações sobre camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de armazenamento quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

Por padrão, uma nova conta de armazenamento é criada na camada de acesso quente e uma conta de armazenamento v1 de uso geral pode ser atualizada para a camada de conta quente ou fria. Se uma camada de acesso à conta não for especificada na atualização, ela será atualizada para ativa por padrão. Se você estiver explorando qual camada de acesso usar para a atualização, considere seu cenário de uso de dados atual. Há dois cenários de usuário típicos para migrar para uma conta v2 de uso geral:

* Você tem uma conta de armazenamento de uso geral v1 existente e deseja avaliar uma atualização para uma conta de armazenamento de uso geral v2, com a camada de acesso de armazenamento correta para dados de BLOB.
* Você decidiu usar uma conta de armazenamento de uso geral v2 ou já tem uma e deseja avaliar se deve usar a camada de acesso de armazenamento quente ou fria para dados de BLOB.

Em ambos os casos, a primeira prioridade é estimar o custo de armazenamento, acesso e operação em seus dados armazenados em uma conta de armazenamento de uso geral V2 e compará-los com os custos atuais.

## <a name="pricing-and-billing"></a>Preços e faturação

A atualização de uma conta de armazenamento v1 para uma conta v2 de uso geral é gratuita. Você pode especificar a camada de conta desejada durante o processo de atualização. Se uma camada de conta não for especificada na atualização, a camada de conta padrão da conta atualizada será `Hot`. No entanto, a alteração da camada de acesso de armazenamento após a atualização pode resultar em alterações na sua fatura para que seja recomendável especificar a nova camada de conta durante a atualização.

Todas as contas de armazenamento utilizam um modelo de preços para o armazenamento de blobs com base na camada de cada blob. Ao utilizar uma conta de armazenamento, aplicam-se as seguintes considerações de faturação:

* **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia dependendo da camada de acesso de armazenamento. O custo por gigabyte diminui conforme a camada se torna mais esporádica.

* **Custos de acesso a dados**: os custos de acesso a dados aumenta conforme a camada se torna mais esporádica. Para dados na camada de acesso de armazenamento fria e de arquivo morto, você será cobrado por um encargo de acesso a dados por gigabyte para leituras.

* **Custos de transação**: há um encargo por transação para todas as camadas que aumenta cada vez que a camada é mais esporádica.

* **Custos de transferência de dados de georreplicação**: este custo aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.

* **Custos de transferência de dados de saída**: transferências de dados de saída (dados transferidos para fora de uma região do Azure) incorrem em cobrança de uso de largura de banda por gigabyte, consistente com contas de armazenamento de uso geral.

* **Alterando a camada de acesso de armazenamento**: alterar a camada de acesso de armazenamento de conta de fria para quente incorre em um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de acesso da conta de quente para fria incorre em um encargo igual à gravação de todos os dados na camada fria (somente contas GPv2).

> [!NOTE]
> Para obter mais informações sobre o modelo de preços das contas de armazenamento, veja a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Estimar custos para seus padrões de uso atuais

Para estimar o custo de armazenar e acessar dados de BLOB em uma conta de armazenamento de uso geral V2 em uma determinada camada, avalie seu padrão de uso existente ou aproximando seu padrão de uso esperado. Em geral, precisa de saber o seguinte:

* Seu consumo de armazenamento de BLOBs, em gigabytes, incluindo:
    - Que quantidade de dados está a ser armazenada na conta de armazenamento?
    - De que forma o volume de dados é alterado mensalmente: os novos dados substituem constantemente os dados antigos?
* O padrão de acesso primário para seus dados de armazenamento de BLOBs, incluindo:
    - A quantidade de dados que estão sendo lidos e gravados na conta de armazenamento?
    - Quantas operações de leitura versus operações de gravação ocorrem nos dados na conta de armazenamento?

Para decidir sobre a melhor camada de acesso às suas necessidades, pode ser útil determinar a capacidade dos dados do blob e como esses dados estão sendo usados. Isso pode ser feito melhor examinando as métricas de monitoramento da sua conta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorizar contas de armazenamento existentes

Para monitorizar as suas contas de armazenamento existentes e recolher estes dados, pode utilizar a Análise do Armazenamento do Azure, que efetua o registo e fornece dados de métricas para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas agregadas de transações e dados de capacidade sobre pedidos enviados para o serviço de armazenamento relativamente aos tipos de contas de Armazenamento de blobs, GPv1 e GPv2. Estes dados são armazenados em tabelas bem conhecidas na mesma conta de armazenamento.

Para obter mais informações, veja [About Storage Analytics Metrics (Sobre Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Storage Analytics Metrics Table Schema (Esquema da Tabela de Métricas da Análise de Armazenamento)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas de Armazenamento de blobs expõem o ponto final do serviço Tabela apenas para armazenar e aceder aos dados de métricas relativos a essa conta.

Para monitorizar o consumo de armazenamento do Armazenamento de blobs, tem de ativar as métricas de capacidade.
Com esta opção ativada, os dados de capacidade são registados diariamente para um serviço Blob de uma conta de armazenamento e registados como uma entrada que é escrita na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar os padrões de acesso a dados do Armazenamento de blobs, tem de ativar as métricas de transação horária a partir da API. Com as métricas de transação horária ativadas, as transações por API são agregadas ao fim de cada hora e registadas como uma entrada que é escrita na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* regista as transações para o ponto final secundário quando são utilizadas contas de armazenamento RA-GRS.

> [!NOTE]
> Se você tiver uma conta de armazenamento de uso geral na qual você armazenou blobs de páginas e discos de máquina virtual, ou filas, arquivos ou tabelas, juntamente com os dados de blob de bloco e acréscimo, esse processo de estimativa não será aplicável. Os dados de capacidade não distinguem os blobs de blocos dos outros tipos e não disponibilizam dados de capacidade para outros tipos de dados. Se utilizar estes tipos, uma metodologia alternativa é ver as quantidades na sua fatura mais recente.

Para obter uma boa aproximação do seu consumo de dados e padrão de acesso, recomendamos que escolha um período de retenção para as métricas que seja representativo da sua utilização normal e o utilize para tirar conclusões. Uma opção é manter os dados das métricas durante sete dias e recolher os dados todas as semanas, para análise no fim do mês. Outra opção é manter os dados das métricas para os últimos 30 dias e recolher e analisar os dados no final desse período de 30 dias.

Para obter detalhes sobre como habilitar, coletar e exibir dados de métricas, consulte [métricas de análise de armazenamento](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> O armazenamento, o acesso e a transferência de dados de análise são cobrados também como dados normais do utilizador.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizar métricas de utilização para estimar os custos

#### <a name="capacity-costs"></a>Custos de capacidade

A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida pelos dados do utilizador. A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos registos de análise.

Esta capacidade total consumida pelos dados de utilizador e pelos registos de análise (se ativados) pode depois ser utilizada para estimar o custo do armazenamento dos dados na conta de armazenamento. O mesmo método também pode ser utilizado para estimar os custos de armazenamento nas contas GPv1.

#### <a name="transaction-costs"></a>Custos de transação

A soma dos *'TotalBillableRequests'* , em todas as entradas de uma API na tabela de métricas de transação indica o número total de transações para essa API específica. *Por exemplo,* , o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma do total de pedidos faturáveis para todas as entradas com a chave de linha *'user;GetBlob'* .

Para que possa estimar os custos de transação para as contas do Armazenamento de blobs, tem de dividir as transações em três grupos, uma vez que têm um preço diferente.

* Transações de escrita como *'PutBlob'* , *'PutBlock'* , *'PutBlockList'* , *'AppendBlock'* , *'ListBlobs'* , *'ListContainers'* , *'CreateContainer'* , *'SnapshotBlob'* e *'CopyBlob'* .
* Transações de eliminação como *'DeleteBlob'* e *'DeleteContainer'* .
* Todas as outras transações.

Para estimar os custos de transação nas contas de armazenamento GPv1, tem de agregar todas as transações, independentemente da operação/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de acesso a dados e custos de transferência de dados de georreplicação

Embora a análise de armazenamento não forneça a quantidade de dados lidos e escritos numa conta de armazenamento, esta pode ser aproximadamente estimada ao consultar a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas de uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API específica. Do mesmo modo, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para que possa estimar os custos de acesso a dados para as contas do Armazenamento de blobs, precisa de dividir as transações em dois grupos.

* A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada ao ver a soma de *'TotalEgress'* para, principalmente, as operações *'GetBlob'* e *'CopyBlob'* .

* A quantidade de dados escritos na conta de armazenamento pode ser estimada ao ver a soma de *'TotalIngress'* para, principalmente, as operações *'PutBlob'* , *'PutBlock'* , *'CopyBlob'* e *'AppendBlock'* .

Também pode ser calculado o custo de transferência de dados de georreplicação para contas do Armazenamento de blobs mediante a utilização da estimativa da quantidade de dados escritos se for utilizada uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado sobre como calcular os custos para usar a camada de acesso de armazenamento quente ou fria, dê uma olhada nas perguntas frequentes intituladas *' o que são as camadas de acesso quente e frio e como devo determinar qual delas usar? '* na [Página de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](storage-quickstart-create-account.md)
- [Gerenciar contas de armazenamento do Azure](storage-account-manage.md)
