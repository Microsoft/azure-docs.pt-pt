---
title: Restauro pontual para bolhas de bloco (pré-visualização)
titleSuffix: Azure Storage
description: A restauração pontual das bolhas de blocos proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar uma conta de armazenamento no seu estado anterior num dado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 66682e953e4e262604d1b0c07720ebaab5995364
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195216"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Restauro pontual para bolhas de bloco (pré-visualização)

A restauração pontual fornece proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar os dados de blocos blob para um estado anterior. A restauração pontual é útil em cenários em que um utilizador ou aplicação acidentalmente elimina dados ou quando um erro de aplicação corrompe dados. A restauração pontual também permite testar cenários que exigem reverter um conjunto de dados para um estado conhecido antes de executar mais testes.

Para aprender a permitir a restauração ponto-a-tempo para uma conta de armazenamento, consulte [Enable e gerencie a restauração ponto-a-tempo para bolhas de bloco (pré-visualização)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Como o ponto-no-tempo restaura obras

Para permitir a restauração pontual, cria uma política de gestão para a conta de armazenamento e especifica um período de retenção. Durante o período de retenção, pode restaurar as bolhas de bloco do estado presente para um estado num ponto anterior.

Para iniciar uma restauração pontual, ligue para a operação [Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) e especifique um ponto de restauro no tempo UTC. Pode especificar uma gama lexicográfica de nomes de contentores e blob para restaurar, ou omitir a gama para restaurar todos os recipientes na conta de armazenamento. A operação **Restore Blob Ranges** devolve um ID de restauro que identifica exclusivamente a operação.

O Azure Storage analisa todas as alterações que foram feitas às bolhas especificadas entre o ponto de restauro solicitado, especificado no tempo UTC, e o momento presente. A operação de restauro é atómica, por isso ou consegue restaurar completamente todas as alterações, ou falha. Se houver bolhas que não possam ser restauradas, então a operação falha e as operações de leitura e escrita para os contentores afetados retomam.

Quando solicita uma operação de restauro, o Armazenamento Azure bloqueia as operações de dados nas bolhas da gama que estão a ser restauradas durante a operação. Leia, escreva e elimine as operações estão bloqueadas no local principal. As operações de leitura a partir da localização secundária podem prosseguir durante a operação de restauro se a conta de armazenamento for geo-replicada.

Apenas uma operação de restauro pode ser executada numa conta de armazenamento de cada vez. Uma operação de restauro não pode ser cancelada uma vez que esteja em curso, mas uma segunda operação de restauro pode ser realizada para desfazer a primeira operação.

Para verificar o estado de uma restauração pontual, ligue para a operação **Get Restore Status** com o ID de restauro devolvido da operação Restore **Blob Ranges.**

Tenha em mente as seguintes limitações nas operações de restauro:

- Um bloco que tenha sido carregado via [Bloco de Colocação](/rest/api/storageservices/put-block) ou [Bloco de Colocação de URL](/rest/api/storageservices/put-block-from-url), mas não cometido através da Lista de [Blocos De Put](/rest/api/storageservices/put-block-list), não faz parte de uma bolha e por isso não é restaurado como parte de uma operação de restauro.
- Uma bolha com um arrendamento ativo não pode ser restaurada. Se uma bolha com um arrendamento ativo estiver incluída na gama de bolhas para restaurar, a operação de restauro falhará atomicamente.
- Os instantâneos não são criados ou eliminados como parte de uma operação de restauro. Apenas a bolha base é restaurada ao seu estado anterior.
- Se uma bolha se moveu entre os níveis quentes e frescos no período entre o momento presente e o ponto de restauro, a bolha é restaurada ao seu nível anterior. No entanto, uma bolha que se tenha deslocado para o nível de arquivo não será restaurada.

> [!CAUTION]
> O restauro pontual suporta a restauração das operações apenas em blocos. As operações nos contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento, ligando para o funcionamento do [recipiente eliminar](/rest/api/storageservices/delete-container) durante a pré-visualização de restauro pontual, esse recipiente não pode ser restaurado com uma operação de restauro. Durante a pré-visualização, em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

### <a name="prerequisites-for-point-in-time-restore"></a>Pré-requisitos para a restauração pontual

A restauração pontual requer que estejam ativadas as seguintes funcionalidades de Armazenamento Azure:

- [Eliminação recuperável](soft-delete-overview.md)
- [Alterar o feed (pré-visualização)](storage-blob-change-feed.md)
- [Versão blob (pré-visualização)](versioning-overview.md)

Ative estas funcionalidades para a conta de armazenamento antes de ativar a restauração ponto-a-tempo. Certifique-se de que se regista para as pré-visualizações de versão de feed de alteração e blob antes de as ativar.

### <a name="retention-period-for-point-in-time-restore"></a>Período de retenção para restauro pontual

Quando ativa restabelecimento pontual para uma conta de armazenamento, especifica um período de retenção. As bolhas de bloco na sua conta de armazenamento podem ser restauradas durante o período de retenção.

O período de retenção começa quando permite a restauração ponto-a-tempo. Tenha em mente que não pode restaurar as bolhas num estado antes do início do período de retenção. Por exemplo, se permitiu a restauração pontual no dia 1 de maio com uma retenção de 30 dias, então no dia 15 de maio poderá restaurar a um máximo de 15 dias. No dia 1 de junho, pode restaurar os dados entre 1 e 30 dias.

O período de retenção para a restauração pontual deve ser pelo menos um dia do que o período de retenção especificado para a eliminação suave. Por exemplo, se o período de retenção de eliminação suave for definido para 7 dias, então o período de retenção de restauração ponto-a-tempo pode ser entre 1 e 6 dias.

### <a name="permissions-for-point-in-time-restore"></a>Permissões para restauro pontual

Para iniciar uma operação de restauro, um cliente deve ter permissões de escrita para todos os recipientes na conta de armazenamento. Para conceder permissões para autorizar uma operação de restauro com o Azure Ative Directory (Azure AD), atribuir a função de Contribuinte de Conta de **Armazenamento** ao principal de segurança ao nível da conta de armazenamento, grupo de recursos ou subscrição.

## <a name="about-the-preview"></a>Sobre a pré-visualização

A restauração ponto-a-tempo é suportada apenas para contas de armazenamento v2 de uso geral. Apenas os dados nos níveis de acesso quentes e frescos podem ser restaurados com a restauração pontual.

As seguintes regiões apoiam a restauração ponto-a-tempo na pré-visualização:

- Canadá Central
- Leste do Canadá
- França Central

A pré-visualização inclui as seguintes limitações:

- A restauração de bolhas de blocopremium não é suportada.
- A restauração de bolhas no nível de arquivo não é suportada. Por exemplo, se uma bolha no nível quente foi transferida para o nível de arquivo há dois dias, e uma operação de restauro restaura um ponto há três dias, a bolha não é restaurada para o nível quente.
- Restaurar os espaços de armazenamento de lagos de dados Azure Gen2 não é suportado.
- O restabelecimento das contas de armazenamento utilizando as chaves fornecidas pelo cliente não é suportado.

> [!IMPORTANT]
> A pré-visualização de restauro pontual destina-se apenas à utilização não produção. Os acordos de nível de serviço de produção (SLAs) não estão atualmente disponíveis.

### <a name="register-for-the-preview"></a>Registe-se na pré-visualização

Para se registar para a pré-visualização, execute os seguintes comandos da Azure PowerShell:

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="check-registration-status"></a>Verifique o estado do registo

Para verificar o estado do seu registo, execute os seguintes comandos:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed
```

## <a name="pricing-and-billing"></a>Preços e faturação

A faturação para restauro pontual depende da quantidade de dados processados para realizar a operação de restauro. A quantidade de dados tratados baseia-se no número de alterações que ocorreram entre o ponto de restauro e o momento presente. Por exemplo, assumindo uma taxa de variação relativamente constante para bloquear os dados de blob numa conta de armazenamento, uma operação de restauro que remonta ao tempo 1 dia custaria 1/10de um restauro que remonta no tempo 10 dias.

Para estimar o custo de uma operação de restauro, reveja o registo de alimentação de alteração para estimar a quantidade de dados que foram modificados durante o período de restauro. Por exemplo, se o período de retenção para a alimentação por alterações for de 30 dias, e a dimensão do alimento para alterações for de 10 MB, então restaurar para um ponto 10 dias antes custaria aproximadamente um terço do preço listado para uma conta LRS naquela região. Restaurar a um ponto que é 27 dias antes custaria aproximadamente nove décimos do preço listado.

Para obter mais informações sobre os preços para a restauração pontual, consulte o preço do [bloco blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Faça perguntas ou forneça feedback

Para fazer perguntas sobre a pré-visualização de restauro pontual, ou para fornecer feedback, contacte a Microsoft em pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Passos seguintes

- [Ativar e gerir a restauração ponto-a-tempo para bolhas de bloco (pré-visualização)](point-in-time-restore-manage.md)
- [Alterar suporte para alimentação em Armazenamento de Blob Azure (Pré-visualização)](storage-blob-change-feed.md)
- [Ativar a eliminação suave para bolhas](soft-delete-enable.md)
- [Ativar e gerir a versão blob](versioning-enable.md)
