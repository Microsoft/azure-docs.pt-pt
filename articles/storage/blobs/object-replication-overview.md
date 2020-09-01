---
title: Visão geral da replicação do objeto (pré-visualização)
titleSuffix: Azure Storage
description: A replicação de objetos (pré-visualização) assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Use a replicação de objetos para minimizar a latência em pedidos de leitura, para aumentar a eficiência das cargas de trabalho computacional, para otimizar a distribuição de dados e para minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: edbce017b1090a029d49c9f2f8812e7e65133fcb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073136"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replicação de objetos para bolhas de bloco (pré-visualização)

A replicação de objetos (pré-visualização) assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Alguns cenários suportados pela replicação de objetos incluem:

- **Minimizar a latência.** A replicação de objetos pode reduzir a latência para pedidos de leitura, permitindo que os clientes consumam dados de uma região que está em proximidade física mais próxima.
- **Aumentar a eficiência para cargas de trabalho de computação.** Com a replicação de objetos, as cargas de trabalho computativas podem processar os mesmos conjuntos de blobs de blocos em diferentes regiões.
- **Otimizar a distribuição de dados.** Pode processar ou analisar dados num único local e depois replicar apenas os resultados em regiões adicionais.
- **Otimizar custos.** Depois de os seus dados terem sido replicados, pode reduzir custos movendo-os para o nível de arquivo usando políticas de gestão do ciclo de vida.

O diagrama seguinte mostra como a replicação de objetos replica blobs de uma conta de armazenamento de origem numa região para contas de destino em duas regiões diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama mostrando como funciona a replicação do objeto":::

Para aprender a configurar a replicação de objetos, consulte a replicação do [objeto configurado (pré-visualização)](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="object-replication-policies-and-rules"></a>Políticas e regras de replicação de objetos

Ao configurar a replicação de objetos, cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contentor de origem e um contentor de destino e indicam quais as bolhas de bloqueio no recipiente de origem que serão replicadas.

Depois de configurar a replicação de objetos, o Azure Storage verifica periodicamente e assincroticamente o feed da conta de origem e reproduz assíncronamente qualquer gravação ou exclusão de operações para a conta de destino. A latência da replicação depende do tamanho da bolha do bloco ser replicada.

> [!IMPORTANT]
> Como os dados do blob de bloco são replicados assíncroniamente, a conta de origem e a conta de destino não estão imediatamente sincronizadas. Atualmente não há SLA sobre o tempo que leva para replicar dados para a conta de destino.

### <a name="replications-policies"></a>Políticas de replicação

Ao configurar a replicação de objetos, é criada uma política de replicação tanto na conta de origem como na conta de destino através do fornecedor de recursos de armazenamento Azure. A política de replicação é identificada por uma identificação de política. A política das contas de origem e de destino deve ter a mesma identificação política para que a replicação se realize.

Uma conta de armazenamento pode servir como a conta de origem de até duas contas de destino. E uma conta de destino pode não ter mais do que duas contas de origem. As contas de origem e de destino podem estar todas em regiões diferentes. Pode configurar políticas de replicação separadas para replicar dados em cada uma das contas de destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Azure Storage irá replicar bolhas de um recipiente de origem para um recipiente de destino. Pode especificar até 10 regras de replicação para cada política de replicação. Cada regra define um único recipiente de origem e destino, e cada recipiente de origem e destino pode ser utilizado numa única regra.

Quando se cria uma regra de replicação, por padrão apenas são copiadas novas bolhas de blocos que são posteriormente adicionadas ao recipiente de origem. Também pode especificar que tanto as bolhas de blocos novas como existentes são copiadas, ou pode definir um âmbito de cópia personalizado que copia blobs de blocos criados a partir de um tempo especificado.

Também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar bolhas de bloqueio por prefixo. Quando especificar um prefixo, apenas as bolhas correspondentes ao prefixo no recipiente de origem serão copiadas para o recipiente de destino.

Os recipientes de origem e destino devem existir antes de os especificar numa regra. Após criar a política de replicação, o contentor de destino torna-se só de leitura. Qualquer tentativa de escrever no contentor de destino falhará com o código de erro 409 (Conflito). No entanto, pode ligar para a operação [set Blob Tier](/rest/api/storageservices/set-blob-tier) numa bolha no recipiente de destino para movê-la para o nível de arquivo. Para obter mais informações sobre o nível de arquivo, consulte o [armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md#archive-access-tier)

## <a name="about-the-preview"></a>Sobre a pré-visualização

A replicação de objetos é suportada apenas para contas de armazenamento v2 para fins gerais. A replicação de objetos está disponível nas seguintes regiões em pré-visualização:

- França Central
- Leste do Canadá
- Canadá Central
- E.U.A. Leste 2
- E.U.A. Central

Tanto as contas de origem como de destino devem residir numa dessas regiões, a fim de utilizar a replicação de objetos. As contas podem estar em duas regiões diferentes.

Durante a pré-visualização, não existem custos adicionais associados à replicação de dados entre contas de armazenamento.

> [!IMPORTANT]
> A pré-visualização da replicação do objeto destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.

### <a name="prerequisites-for-object-replication"></a>Pré-requisitos para a replicação de objetos

A replicação do objeto requer que as seguintes funcionalidades de Armazenamento Azure estejam ativadas: 
- [Feed de alterações](storage-blob-change-feed.md)
- [Controlo de versões](versioning-overview.md)

Antes de configurar a replicação de objetos, ative os seus pré-requisitos. O feed de alteração deve ser ativado na conta de origem e a versão blob deve ser ativada tanto na conta de origem como de destino. Para obter mais informações sobre como ativar estas funcionalidades, consulte estes artigos:

- [Ativar e desativar o feed de alteração](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

Certifique-se de que se regista para as pré-visualizações de alterações e versões blob antes de as ativar.

Permitir a alteração do feed e da versão blob pode incorrer em custos adicionais. Para mais detalhes, consulte a [página de preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Registre-se para a pré-visualização

Pode registar-se para a pré-visualização da replicação do objeto utilizando o PowerShell ou o Azure CLI. Certifique-se de que também se regista para as pré-visualizações de alterações e versões blob se ainda não o fez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar para a pré-visualização com a PowerShell, execute os seguintes comandos:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registar para a pré-visualização com o Azure CLI, execute os seguintes comandos:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Verifique o estado do registo

Pode verificar o estado dos seus pedidos de registo utilizando o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado dos seus pedidos de registo utilizando o PowerShell, execute os seguintes comandos:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o estado dos seus pedidos de registo utilizando o Azure CLI, execute os seguintes comandos:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Faça perguntas ou forneça feedback

Para fazer perguntas sobre a pré-visualização da replicação do objeto, ou para fornecer feedback, contacte a Microsoft em AzureStorageFeedback@microsoft.com . Ideias e sugestões sobre o Azure Storage são sempre bem-vindos no [fórum de feedback do Azure Storage](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Passos seguintes

- [Configure a replicação do objeto (pré-visualização)](object-replication-configure.md)
- [Alterar suporte de feed no armazenamento de blob Azure (pré-visualização)](storage-blob-change-feed.md)
- [Ativar e gerir a versão blob](versioning-enable.md)
