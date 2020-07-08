---
title: Restauro pontual para bolhas de bloco (pré-visualização)
titleSuffix: Azure Storage
description: A restauração pontual das bolhas de blocos proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar uma conta de armazenamento no seu estado anterior num dado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 60f83fae6e7e685a1065d1c01327a004d9bb2864
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675657"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Restauro pontual para bolhas de bloco (pré-visualização)

A restauração pontual proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar os dados de blob de bloqueio a um estado anterior. A restauração pontual é útil em cenários em que um utilizador ou aplicação elimina acidentalmente dados ou onde um erro de aplicação corrompe dados. A restauração pontual também permite testar cenários que requerem reverter um conjunto de dados para um estado conhecido antes de realizar mais testes.

Para aprender a permitir a restauração pontual para uma conta de armazenamento, consulte [Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Como o ponto-a-tempo restaurar funciona

Para permitir a restauração pontual, cria uma política de gestão para a conta de armazenamento e especifica um período de retenção. Durante o período de retenção, pode restaurar as bolhas de bloqueio do estado atual para um estado num momento anterior.

Para iniciar uma restauração pontual, ligue para a operação [Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) e especifique um ponto de restauro no tempo UTC. Pode especificar gamas lexicográficas de contentores e nomes de bolhas para restaurar, ou omitir o alcance para restaurar todos os recipientes na conta de armazenamento. Até 10 gamas lexicográficas são suportadas por operação de restauro.

O Azure Storage analisa todas as alterações que foram feitas às bolhas especificadas entre o ponto de restauro solicitado, especificado no tempo UTC, e o momento presente. A operação de restauro é atómica, por isso ou consegue restaurar todas as alterações, ou falha. Se houver bolhas que não possam ser restauradas, a operação falha e lê e escreve as operações para os recipientes afetados.

Apenas uma operação de restauro pode ser executada numa conta de armazenamento de cada vez. Uma operação de restauro não pode ser cancelada uma vez que está em curso, mas uma segunda operação de restauro pode ser realizada para desfazer a primeira operação.

A operação **Restore Blob Ranges** devolve um ID de restauro que identifica exclusivamente a operação. Para verificar o estado de uma restauração pontual, ligue para a operação **Get Restore Status** com o ID de restauro devolvido da operação Restore **Blob Ranges.**

Tenha em mente as seguintes limitações em operações de restauro:

- Um bloco que foi carregado através de [Put Block](/rest/api/storageservices/put-block) ou Put Block [de URL](/rest/api/storageservices/put-block-from-url), mas não cometido através da Put [Block List](/rest/api/storageservices/put-block-list), não faz parte de uma bolha e, portanto, não é restaurado como parte de uma operação de restauro.
- Uma bolha com um arrendamento ativo não pode ser restaurada. Se uma bolha com um arrendamento ativo for incluída na gama de bolhas para restaurar, a operação de restauro falhará atomicamente.
- As imagens instantâneas não são criadas ou eliminadas como parte de uma operação de restauro. Apenas a bolha da base é restaurada ao seu estado anterior.
- Se uma bolha se moveu entre os níveis quentes e frescos no período entre o momento presente e o ponto de restauro, a bolha é restaurada para o seu nível anterior. No entanto, uma bolha que se tenha deslocado para o nível de arquivo não será restaurada.

> [!IMPORTANT]
> Quando executa uma operação de restauro, o Azure Storage bloqueia as operações de dados nas bolhas das gamas que estão a ser restauradas durante a operação. As operações de leitura, escrita e eliminação estão bloqueadas na localização primária. Por esta razão, as operações como a listagem de contentores no portal Azure podem não funcionar como esperado enquanto a operação de restauro estiver em curso.
>
> As operações de leitura a partir do local secundário podem prosseguir durante a operação de restauro se a conta de armazenamento for geo-replicada.

> [!CAUTION]
> O ponto-a-tempo restaura os suportes de restauração apenas em blobs de blocos. As operações em contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento chamando a operação [do Recipiente de Eliminação](/rest/api/storageservices/delete-container) durante a pré-visualização do restauro pontual, esse recipiente não pode ser restaurado com uma operação de restauro. Durante a pré-visualização, em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

### <a name="prerequisites-for-point-in-time-restore"></a>Pré-requisitos para restauro pontual

A restauração pontual requer que estejam ativadas as seguintes funcionalidades de Armazenamento Azure:

- [Eliminação recuperável](soft-delete-overview.md)
- [Alterar feed (pré-visualização)](storage-blob-change-feed.md)
- [Veragem blob (pré-visualização)](versioning-overview.md)

Ative estas funcionalidades para a conta de armazenamento antes de permitir a restauração pontual. Certifique-se de que se regista para as pré-visualizações de alterações e versões blob antes de as ativar.

### <a name="retention-period-for-point-in-time-restore"></a>Período de retenção para restauro pontual

Quando ativar a restauração pontual para uma conta de armazenamento, especifique um período de retenção. As bolhas de bloqueio na sua conta de armazenamento podem ser restauradas durante o período de retenção.

O período de retenção começa quando se permite a restauração pontual. Tenha em mente que não pode restaurar as bolhas num estado antes do início do período de retenção. Por exemplo, se for habilitado a restaurar o ponto a tempo no dia 1 de maio com uma retenção de 30 dias, então no dia 15 de maio pode restaurar até um máximo de 15 dias. No dia 1 de junho, pode restaurar os dados entre 1 e 30 dias.

O período de retenção para a restauração pontual deve ser pelo menos um dia a menos do que o período de retenção especificado para a eliminação suave. Por exemplo, se o período de retenção de eliminação suave for definido para 7 dias, então o período de retenção de restauração pontual pode ser entre 1 e 6 dias.

### <a name="permissions-for-point-in-time-restore"></a>Permissões para restauro pontual

Para iniciar uma operação de restauro, um cliente deve ter permissões de escrita para todos os recipientes na conta de armazenamento. Para autorizar uma operação de restauro com o Azure Ative Directory (Azure AD), atribua a função **de Contribuinte de Conta de Armazenamento** ao principal de segurança ao nível da conta de armazenamento, grupo de recursos ou subscrição.

## <a name="about-the-preview"></a>Sobre a pré-visualização

A restauração pontual é suportada apenas para contas de armazenamento v2 para fins gerais. Apenas os dados nos níveis de acesso quente e fresco podem ser restaurados com a restauração pontual.

As seguintes regiões apoiam o ponto de compensação no momento em visualização:

- Canadá Central
- Leste do Canadá
- França Central

A pré-visualização inclui as seguintes limitações:

- Restaurar bolhas de bloco premium não é suportado.
- O restauro de blobs na camada de arquivo não é suportado. Por exemplo, se um blob na camada de acesso frequente tiver sido movido para a camada de arquivo há dois dias e uma operação de restauro restaurar para um ponto há três dias, o blob não é restaurado para a camada de acesso frequente.
- Restaurar espaços de nome plano e hierárquico de armazenamento de dados Azure Data Lake Gen2 não é suportado.
- Restaurar as contas de armazenamento utilizando chaves fornecidas pelo cliente não é suportado.

> [!IMPORTANT]
> A pré-visualização de restauro pontual destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.

### <a name="register-for-the-preview"></a>Registre-se para a pré-visualização

Para se registar para a pré-visualização, execute os seguintes comandos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Verifique o estado do registo

O registo de point in time restore é automático e deve demorar menos de 10 minutos. Para verificar o estado do seu registo, execute os seguintes comandos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>Preços e faturação

A faturação para a restauração pontual depende da quantidade de dados processados para executar a operação de restauro. A quantidade de dados processados baseia-se no número de alterações que ocorreram entre o ponto de restauro e o momento presente. Por exemplo, assumindo uma taxa de variação relativamente constante para bloquear dados blob numa conta de armazenamento, uma operação de restauro que remonta no tempo de 1 dia custaria 1/10 de uma restauração que remonta no tempo 10 dias.

Para estimar o custo de uma operação de restauro, reveja o registo de alteração para estimar a quantidade de dados que foi modificado durante o período de restauração. Por exemplo, se o período de retenção para a alteração do alimento para animais for de 30 dias, e o tamanho do alimento para alterações for de 10 MB, então o reposição para um ponto 10 dias antes custaria aproximadamente um terço do preço indicado para uma conta LRS nessa região. Restaurar a um ponto que é 27 dias antes custaria aproximadamente nove décimos do preço listado.

Para obter mais informações sobre os preços para a restauração pontual, consulte [os preços do blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Faça perguntas ou forneça feedback

Para fazer perguntas sobre a pré-visualização de restauro pontual ou para fornecer feedback, contacte a Microsoft em pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Próximos passos

- [Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)](point-in-time-restore-manage.md)
- [Alterar suporte de feed no armazenamento de blob Azure (pré-visualização)](storage-blob-change-feed.md)
- [Ativar a eliminação suave para bolhas](soft-delete-enable.md)
- [Ativar e gerir a versão blob](versioning-enable.md)
