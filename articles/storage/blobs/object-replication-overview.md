---
title: Visão geral da replicação de objetos (pré-visualização)
titleSuffix: Azure Storage
description: A replicação do objeto (pré-visualização) copia assincronicamente bolhas de blocos entre uma conta de armazenamento de origem e uma conta de destino. Utilize a replicação de objetos para minimizar a latência nos pedidos de leitura, para aumentar a eficiência das cargas de trabalho computacionais, para otimizar a distribuição de dados e minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c23360360f97babebe9efe8afecc6a27052faad5
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758435"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replicação de objetos para bolhas de bloco (pré-visualização)

A replicação do objeto (pré-visualização) copia assincronicamente bolhas de blocos entre uma conta de armazenamento de origem e uma conta de destino. Alguns cenários apoiados pela replicação de objetos incluem:

- **Minimizar a latência.** A replicação de objetos pode reduzir a latência para pedidos de leitura, permitindo aos clientes consumir dados de uma região que está mais próxima da proximidade física.
- **Aumentar a eficiência para as cargas de trabalho computacionais.** Com a replicação de objetos, as cargas de trabalho computacionais podem processar os mesmos conjuntos de blocos em diferentes regiões.
- **Otimizar a distribuição de dados.** Pode processar ou analisar dados num único local e, em seguida, replicar apenas os resultados para regiões adicionais.
- **Otimização de custos.** Depois de replicados os seus dados, pode reduzir os custos transferindo-os para o nível de arquivo utilizando políticas de gestão do ciclo de vida.

O diagrama seguinte mostra como a replicação de objetos replica bolhas de blocos de uma conta de armazenamento de origem numa região para contas de destino em duas regiões diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama mostrando como a replicação de objetos funciona":::

Para aprender a configurar a replicação do objeto, consulte a replicação do [objeto (pré-visualização)](object-replication-configure.md).

## <a name="object-replication-policies-and-rules"></a>Políticas e regras de replicação de objetos

Ao configurar a replicação de objetos, cria-se uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um recipiente de origem e um recipiente de destino e indicam quais as bolhas de bloco no recipiente de origem que serão replicadas.

Depois de configurar a replicação de objetos, o Armazenamento Azure verifica periodicamente o feed de alteração da conta de origem e replica assincronicamente qualquer gravação ou exclusão de operações para a conta de destino. A latência da replicação depende do tamanho da bolha do bloco ser replicada.

> [!IMPORTANT]
> Como os dados do bloco blob são replicados assíncronamente, a conta fonte e a conta de destino não estão imediatamente sincronizadas. Não existe atualmente SLA sobre quanto tempo demora a replicar dados para a conta de destino.

### <a name="replications-policies"></a>Políticas de replicação

Quando configura a replicação de objetos, é criada uma política de replicação tanto na conta fonte como na conta de destino através do fornecedor de recursos de Armazenamento Azure. A política de replicação é identificada por uma identificação política. A política sobre as contas de origem e destino deve ter o mesmo ID político para que a replicação ocorra.

Uma conta de armazenamento pode servir como a conta fonte para até duas contas de destino. As contas de origem e destino podem estar todas em diferentes regiões. Pode configurar políticas de replicação separadas para replicar dados em cada uma das contas de destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Armazenamento Azure irá replicar bolhas de um recipiente de origem para um contentor de destino. Pode especificar até 10 regras de replicação para cada política de replicação. Cada regra define um único recipiente de origem e destino, e cada fonte e recipiente de destino podem ser utilizados em apenas uma regra.

Quando se cria uma regra de replicação, por defeito apenas são copiadas novas bolhas de bloco que são posteriormente adicionadas ao recipiente de origem. Também pode especificar que as bolhas de blocos novas e existentes são copiadas, ou pode definir um âmbito de cópia personalizado que copia bolhas de blococriadas a partir de um tempo determinado.

Também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar bolhas de blocos por prefixo. Quando especificar um prefixo, apenas as bolhas correspondentes a esse prefixo no recipiente de origem serão copiadas para o recipiente de destino.

Os recipientes de origem e destino devem existir antes de poder especificar em regra. Depois de criar a política de replicação, o recipiente de destino torna-se apenas de leitura. Qualquer tentativa de escrever para o recipiente de destino falha com o código de erro 409 (Conflito). No entanto, pode ligar para a operação [set Blob Tier](/rest/api/storageservices/set-blob-tier.md) numa bolha no contentor de destino para movê-la para um nível de acesso diferente. Por exemplo, pode mover bolhas no contentor de destino para o nível de arquivo para economizar custos.

## <a name="about-the-preview"></a>Sobre a pré-visualização

A replicação do objeto é suportada apenas para contas de armazenamento v2 de uso geral. A replicação de objetos está disponível nas seguintes regiões em pré-visualização:

- França Central
- Leste do Canadá
- Canadá Central

Tanto as contas de origem como de destino devem residir numa dessas regiões para utilizar a replicação de objetos. As contas podem ser em duas regiões diferentes.

Durante a pré-visualização, não existem custos adicionais associados à replicação de dados entre contas de armazenamento.

> [!IMPORTANT]
> A pré-visualização da replicação do objeto destina-se apenas à utilização não-produção. Os acordos de nível de serviço de produção (SLAs) não estão atualmente disponíveis.

### <a name="prerequisites-for-object-replication"></a>Pré-requisitos para a replicação de objetos

A replicação do objeto requer que estejam ativadas as seguintes funcionalidades de Armazenamento Azure:

Antes de configurar a replicação do objeto, ative os seus pré-requisitos. O feed de alteração deve ser ativado na conta fonte e a versão blob deve ser ativada tanto na conta de origem como no destino. Para obter mais informações sobre a ativação destas funcionalidades, consulte estes artigos:

- [Ativar e desativar o feed de mudança](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

Certifique-se de que se regista para as pré-visualizações de versão de feed de alteração e blob antes de as ativar.

Permitir a alteração do feed e a versão blob podem incorrer em custos adicionais. Para mais detalhes, consulte a página de preços do [Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Registe-se na pré-visualização

Pode registar-se na pré-visualização da replicação do objeto utilizando o PowerShell ou o Azure CLI. Certifique-se de que também se regista para as pré-visualizações de versões de feed de alteração e blob se ainda não o fez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar na pré-visualização com a PowerShell, execute os seguintes comandos:

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

Para se registar na pré-visualização com o Azure CLI, execute os seguintes comandos:

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

Para fazer perguntas sobre a pré-visualização da replicação do objeto, ou para fornecer feedback, contacte a Microsoft em AzureStorageFeedback@microsoft.com . Ideias e sugestões sobre o Armazenamento Azure são sempre bem-vindas no fórum de feedback do [Armazenamento Azure.](https://feedback.azure.com/forums/217298-storage)

## <a name="next-steps"></a>Passos seguintes

- [Configurar a replicação do objeto (pré-visualização)](object-replication-configure.md)