---
title: Versão blob (pré-visualização)
titleSuffix: Azure Storage
description: A versão de armazenamento blob (pré-visualização) mantém automaticamente versões anteriores de um objeto e identifica-as com selos temporais. Pode restaurar versões anteriores de uma bolha para recuperar os seus dados se for erroneamente modificado ou eliminado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fa28e07c28c36c03ab9e85d8436e3f1a2b36ad1c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993966"
---
# <a name="blob-versioning-preview"></a>Versão blob (pré-visualização)

Pode ativar a versão de armazenamento Blob (pré-visualização) para manter automaticamente as versões anteriores de um objeto.  Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificado ou eliminado.

A versão blob está ativada na conta de armazenamento e aplica-se a todas as bolhas na conta de armazenamento. Depois de ativar a versão blob para uma conta de armazenamento, o Azure Storage mantém automaticamente versões para cada bolha na conta de armazenamento.

A Microsoft recomenda a utilização de versão blob para manter versões anteriores de uma bolha para uma proteção de dados superior. Quando possível, utilize a versão blob em vez de imagens blob para manter versões anteriores. As imagens blob fornecem uma funcionalidade semelhante na medida em que mantêm versões anteriores de uma bolha, mas as imagens devem ser mantidas manualmente pela sua aplicação.

> [!IMPORTANT]
> A versão blob não pode ajudá-lo a recuperar da eliminação acidental de uma conta de armazenamento ou recipiente. Para evitar a supressão acidental da conta de armazenamento, configure um bloqueio **Não Excluir** no recurso da conta de armazenamento. Para obter mais informações sobre o bloqueio dos recursos do Azure, consulte [os recursos de bloqueio para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-blob-versioning-works"></a>Como funciona a versão blob

Uma versão captura o estado de uma bolha num dado momento. Quando a versão blob está ativada para uma conta de armazenamento, o Azure Storage cria automaticamente uma nova versão de uma bolha cada vez que a bolha é modificada ou eliminada.

Quando se cria uma bolha com versão ativada, a nova bolha é a versão atual da bolha (ou a bolha base). Se modificar posteriormente essa bolha, o Azure Storage cria uma versão que captura o estado da bolha antes de ser modificada. A bolha modificada torna-se a nova versão atual. Uma nova versão é criada cada vez que modifica a bolha.

Ao eliminar uma bolha com versão ativada, o Azure Storage cria uma versão que captura o estado da bolha antes de ser eliminada. A versão atual da bolha é então eliminada, mas as versões da bolha persistem, para que possa ser recriada se necessário. 

Versões blob são imutáveis. Não é possível modificar o conteúdo ou metadados de uma versão blob existente.

### <a name="version-id"></a>ID da versão

Cada versão blob é identificada por um ID da versão. O valor da versão ID é o carimbo de tempo em que a bolha foi escrita ou atualizada. O ID da versão é atribuído no momento em que a versão é criada.

Pode efetuar operações de leitura ou exclusão numa versão específica de uma bolha, fornecendo o seu ID de versão. Se omite o ID da versão, a operação atua contra a versão atual (a bolha base).

Quando chama uma operação de escrita para criar ou modificar uma bolha, o Azure Storage devolve o cabeçalho *de versão x-ms* na resposta. Este cabeçalho contém a versão ID para a versão atual da bolha que foi criada pela operação de escrita.

O ID da versão permanece o mesmo durante toda a vida da versão.

### <a name="versioning-on-write-operations"></a>Versão em operações de escrita

Quando a versão blob é ligada, cada operação de escrita para uma bolha cria uma nova versão. As operações de escrita incluem [Colocar Blob,](/rest/api/storageservices/put-blob) [Colocar Lista de Blocos,](/rest/api/storageservices/put-block-list) [Copy Blob](/rest/api/storageservices/copy-blob)e [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata).

Se a operação de escrita criar uma nova bolha, então a bolha resultante é a versão atual da bolha. Se a operação de escrita modificar uma bolha existente, então os novos dados são capturados na bolha atualizada, que é a versão atual, e o Azure Storage cria uma versão que salva o estado anterior da bolha.

Para a simplicidade, os diagramas mostrados neste artigo mostram a versão ID como um simples valor inteiro. Na realidade, a versão ID é uma marca de tempo. A versão atual é mostrada em azul, e as versões anteriores são mostradas em cinza.

O diagrama que se segue mostra como as operações de escrita afetam as versões blob. Quando uma bolha é criada, esta bolha é a versão atual. Quando a mesma bolha é modificada, uma nova versão é criada para salvar o estado anterior da bolha, e a bolha atualizada torna-se a versão atual.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagrama que mostra como as operações de escrita afetam bolhas versonizadas":::

> [!NOTE]
> Uma bolha que foi criada antes de ser ativada a versão para a conta de armazenamento não tem um ID de versão. Quando essa bolha é modificada, a bolha modificada torna-se a versão atual, e uma versão é criada para salvar o estado da bolha antes da atualização. A versão é atribuída uma versão ID que é o seu tempo de criação.

### <a name="versioning-on-delete-operations"></a>Versão em operações de exclusão

Ao eliminar uma bolha, a versão atual da bolha torna-se uma versão anterior e a bolha base é eliminada. Todas as versões anteriores existentes da bolha são preservadas quando a bolha é eliminada.

Chamar a operação [Delete Blob](/rest/api/storageservices/delete-blob) sem uma versão ID elimina a bolha base. Para eliminar uma versão específica, forneça o ID para essa versão na operação de eliminação.

O diagrama seguinte mostra o efeito de uma operação de eliminação numa bolha versísta:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagrama mostrando a eliminação da bolha versonizada":::

Escrever novos dados para a bolha cria uma nova versão da bolha. Quaisquer versões existentes não são afetadas, como mostra o diagrama seguinte.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagrama mostrando recriação de bolha versonizada após eliminação":::

### <a name="blob-types"></a>Tipos de blobs

Quando a versão blob está ativada para uma conta de armazenamento, todas as operações de escrita e exclusão de blocos desencadeiam a criação de uma nova versão, com exceção da operação [Do Bloco de Colocação.](/rest/api/storageservices/put-block)

Para bolhas de página e bolhas anexas, apenas um subconjunto de operações de escrita e exclusão desencadeia a criação de uma versão. Estas operações incluem:

- [Coloque Blob](/rest/api/storageservices/put-blob)
- [Lista de blocos](/rest/api/storageservices/put-block-list)
- [Eliminar Blob](/rest/api/storageservices/delete-blob)
- [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

As seguintes operações não desencadeiam a criação de uma nova versão. Para capturar alterações dessas operações, tire uma foto manual:

- [Página de Colocação](/rest/api/storageservices/put-page) (página blob)
- [Bloco apêndice](/rest/api/storageservices/append-block) (bloco de apêndice)

Todas as versões de uma bolha devem ser do mesmo tipo de bolha. Se uma bolha tiver versões anteriores, não pode substituir uma bolha de um tipo com outro tipo, a menos que primeiro apague a bolha e todas as suas versões.

### <a name="access-tiers"></a>Camadas de acesso

Pode mover qualquer versão de uma bolha de bloco, incluindo a versão atual, para um nível de acesso blob diferente, ligando para a operação [set Blob Tier.](/rest/api/storageservices/set-blob-tier) Pode aproveitar os preços de menor capacidade movendo versões mais antigas de uma bolha para o nível de arrefecimento ou arquivo. Para mais informações, consulte o [armazenamento do Azure Blob: hot, cool e archive access tiers](storage-blob-storage-tiers.md).

Para automatizar o processo de deslocação de bolhas de blocos para o nível adequado, utilize a gestão do ciclo de vida blob. Para obter mais informações sobre a gestão do ciclo de vida, consulte Gerir o ciclo de [vida de armazenamento de Blob Azure](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Ativar ou desativar a versão blob

Para aprender a ativar ou desativar a versão blob, consulte [enable ou desative a versão blob](versioning-enable.md).

A versão blob incapacitante não elimina bolhas, versões ou instantâneos existentes. Quando desliga a versão blob, quaisquer versões existentes permanecem acessíveis na sua conta de armazenamento. Posteriormente, não são criadas novas versões.

Se uma bolha foi criada ou modificada após a versão ter sido desativada na conta de armazenamento, então a sobreposição da bolha cria uma nova versão. A bolha atualizada já não é a versão atual e não tem um ID de versão. Todas as atualizações subsequentes à bolha irão sobrepor os seus dados sem salvar o estado anterior.

Pode ler ou eliminar versões utilizando o ID da versão depois de a versão ser desativada. Também pode listar as versões de uma bolha depois de a versão ser desativada.

O diagrama que se segue mostra como modificar uma bolha após a versão é desativada cria uma bolha que não é versíscada. Quaisquer versões existentes associadas à bolha persistem.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagrama mostrando bolha base modificada após versonio desativado":::

## <a name="blob-versioning-and-soft-delete"></a>Versão blob e soft delete

A versão blob e a eliminação suave blob trabalham em conjunto para lhe proporcionar uma melhor proteção de dados. Quando ativa a eliminação suave, especifica quanto tempo o Armazenamento Azure deve manter uma bolha suavemente eliminada. Qualquer versão blob eliminada suave permanece no sistema e pode não ser eliminada dentro do período de retenção de eliminação suave. Para obter mais informações sobre a eliminação suave de blob, consulte [Soft delete para as bolhas de armazenamento Azure](storage-blob-soft-delete.md).

### <a name="deleting-a-blob-or-version"></a>Apagar uma bolha ou versão

O soft delete oferece proteção adicional para eliminar versões blob. Se tanto a versão como o soft delete estiverem ativados na conta de armazenamento, então quando apaga uma bolha, o Azure Storage cria uma nova versão para salvar o estado da bolha imediatamente antes da eliminação e elimina a versão atual. A nova versão não é eliminada suavemente e não é removida quando expira o período de retenção soft-delete.

Quando elimina uma versão anterior da bolha, a versão é eliminada suavemente. A versão soft-delete é mantida durante todo o período de retenção especificado nas definições de eliminação suave para a conta de armazenamento e é permanentemente eliminada quando o período de retenção de eliminação suave expirar.

Para remover uma versão anterior de uma bolha, elimine-a explicitamente especificando o ID da versão.

O diagrama que se segue mostra o que acontece quando se elimina uma versão blob ou blob.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagrama mostrando a eliminação de uma versão com eliminação suave ativada":::

Se tanto a versão como o soft delete estiverem ativados numa conta de armazenamento, então não é criado nenhum instantâneo soft-delete quando uma versão blob ou blob é modificada ou eliminada.

### <a name="restoring-a-soft-deleted-version"></a>Restaurar uma versão soft-deleted

Pode restaurar uma versão blob eliminada suave, ligando para a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) na versão enquanto o período de retenção soft delete está em vigor. A operação **Undelete Blob** restaura todas as versões soft-delete da bolha.

Restaurar versões soft-deleted com a operação **Undelete Blob** não promove nenhuma versão para ser a versão atual. Para restaurar a versão atual, primeiro restaure todas as versões soft-deleted e, em seguida, use a operação [Copy Blob](/rest/api/storageservices/copy-blob) para copiar uma versão anterior para restaurar a bolha.

O diagrama seguinte mostra como restaurar as versões blob eliminadas suavemente com a operação **Undelete Blob,** e como restaurar a versão atual da bolha com a operação **Copy Blob.**

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagrama mostrando como restaurar versões soft-deleted":::

Após o período de retenção de eliminação suave, quaisquer versões blob eliminadas suavemente são eliminadas permanentemente.

## <a name="blob-versioning-and-blob-snapshots"></a>Versão blob e fotos blob

Uma imagem blob é uma cópia apenas de uma bolha que é tirada em um ponto específico no tempo. As imagens blob e as versões blob são semelhantes, mas um instantâneo é criado manualmente por si ou pela sua aplicação, enquanto uma versão blob é criada automaticamente numa operação de escrita ou exclusão quando a versão blob está ativada para a sua conta de armazenamento.

> [!IMPORTANT]
> A Microsoft recomenda que depois de ativar a versão blob, também atualize a sua aplicação para deixar de tirar fotografias de bolhas de blocos. Se a versão estiver ativada para a sua conta de armazenamento, todas as atualizações e eliminações de blocos blob são capturadas e preservadas por versões. Tirar fotografias não oferece proteções adicionais aos dados do bloco blob se a versão blob estiver ativada, podendo aumentar os custos e a complexidade da aplicação.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Snapshot uma bolha quando a versão está ativada

Apesar de não ser recomendado, pode tirar uma foto de uma bolha que também é versificada. Se não conseguir atualizar a sua aplicação para deixar de tirar fotografias de bolhas quando ativa a versão, a sua aplicação pode suportar imagens e versões.

Quando se tira uma fotografia de uma bolha versífica, cria-se uma nova versão ao mesmo tempo que o instantâneo é criado. Uma nova versão atual também é criada quando um instantâneo é tirado.

O diagrama que se segue mostra o que acontece quando se tira uma foto de uma bolha versífica. No diagrama, versões blob e instantâneos com a versão ID 2 e 3 contêm dados idênticos.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagrama mostrando fotos de uma bolha versonizada":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizar operações em versões blob

Pode autorizar o acesso a versões blob utilizando uma das seguintes abordagens:

- Utilizando o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança do Azure Ative Directory (Azure AD). A Microsoft recomenda a utilização de AD Azure para uma segurança superior e facilidade de utilização. Para obter mais informações sobre a utilização de Azure AD com operações blob, consulte [Autorizar o acesso a blobs e filas utilizando o Diretório Ativo Azure](../common/storage-auth-aad.md).
- Utilizando uma assinatura de acesso partilhado (SAS) para delegar o acesso às versões blob. Especifique a versão `bv`ID para o tipo de recurso assinado, representando uma versão blob, para criar um símbolo SAS para operações numa versão específica. Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).
- Utilizando as chaves de acesso à conta para autorizar operações contra versões blob com Chave Partilhada. Para mais informações, consulte [Autorizar com chave partilhada](/rest/api/storageservices/authorize-with-shared-key).

A versão blob foi concebida para proteger os seus dados de eliminação acidental ou maliciosa. Para melhorar a proteção, a apagar uma versão blob requer permissões especiais. As seguintes secções descrevem as permissões necessárias para eliminar uma versão blob.

### <a name="rbac-action-to-delete-a-blob-version"></a>Ação RBAC para apagar uma versão blob

A tabela que se segue mostra quais as ações do RBAC que suportam a apagar uma versão blob ou blob.

| Descrição | Operação de serviço blob | Ação de dados RBAC necessária | Suporte de papel integrado RBAC |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Apagar a versão atual da bolha | Eliminar Blob | **Microsoft.Armazenamento/armazenamentoContas/blobServices/contentores/blobs/delete/actionDeleting** | Contribuinte de dados blob de armazenamento |
| Apagar uma versão | Eliminar Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/** | Proprietário de dados blob de armazenamento |

### <a name="shared-access-signature-sas-parameters"></a>Parâmetros de assinatura de acesso partilhado (SAS)

O recurso assinado para uma `bv`versão blob é . Para mais informações, consulte [Criar um serviço SAS](/rest/api/storageservices/create-service-sas) ou [Criar uma delegação de utilizadores SAS](/rest/api/storageservices/create-user-delegation-sas).

A tabela seguinte mostra a permissão necessária num SAS para eliminar uma versão blob.

| **Permissão** | **Símbolo URI** | **Operações permitidas** |
|----------------|----------------|------------------------|
| Eliminar         | x              | Apague uma versão blob. |

## <a name="about-the-preview"></a>Sobre a pré-visualização

A versão blob está disponível em pré-visualização nas seguintes regiões:

- França Central
- Leste do Canadá
- Canadá Central

A pré-visualização destina-se apenas à utilização não produção.

Versão 2019-10-10 e superior da API de Armazenamento Azure suporta versão blob.

### <a name="storage-account-support"></a>Suporte de conta de armazenamento

A versão blob está disponível para os seguintes tipos de contas de armazenamento:

- Contas de armazenamento v2 de uso geral
- Bloqueie contas de armazenamento blob
- Contas do Blob Storage

Se a sua conta de armazenamento for uma conta v1 de uso geral, utilize o portal Azure para fazer upgrade para uma conta v2 de propósito geral. Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](../common/storage-account-overview.md)

As contas de armazenamento com um espaço de nome hierárquico habilitado para uso com o Azure Data Lake Storage Gen2 não são atualmente suportadas.

### <a name="register-for-the-preview"></a>Registe-se na pré-visualização

Para se inscrever na pré-visualização de versão blob, utilize o PowerShell ou o Azure CLI para submeter um pedido de registo da funcionalidade com a sua subscrição. Após a aprovação do seu pedido, pode ativar a versão blob com quaisquer contas v2 de uso geral novas ou existentes, armazenamento Blob ou depósito de bloco sinuoso premium.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar na PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
    
# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registar no Azure CLI, ligue para o comando de registo de [características az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name Versioning
```

---

### <a name="check-the-status-of-your-registration"></a>Verifique o estado do seu registo

Para verificar o estado do seu registo, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado do seu registo com a PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o estado do seu registo com o Azure CLI, ligue para o comando de [recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name Versioning
```

---

## <a name="pricing-and-billing"></a>Preços e faturação

Permitir a versão blob pode resultar em custos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

As versões blob, como imagens blob, são faturadas ao mesmo ritmo que os dados ativos. Se uma versão partilha blocos ou páginas com a sua bolha base, então você paga apenas por quaisquer blocos ou páginas adicionais que não sejam partilhadas entre a versão e a bolha base.

> [!NOTE]
> Permitir a versão para dados frequentemente subscritos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência durante as operações de listagem. Para mitigar estas preocupações, guarde dados frequentemente sobreescritos numa conta de armazenamento separada com versão desativada.

### <a name="important-billing-considerations"></a>Considerações importantes de faturação

Certifique-se de que considera os seguintes pontos ao permitir a versão blob:

- A sua conta de armazenamento incorre em encargos para blocos ou páginas únicos, quer estejam na bolha ou numa versão anterior da bolha. A sua conta não incorre em encargos adicionais para versões associadas a uma bolha até atualizar a bolha em que se baseiam. Depois de atualizar a bolha, diverge das versões anteriores. Quando isto acontece, é cobrado pelos blocos ou páginas únicos em cada bolha ou versão.
- Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID de bloco e os mesmos dados que tem na versão. Depois de o bloco ser novamente comprometido, diverge da sua congénere em qualquer versão, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa página blob que é atualizada com dados idênticos.
- O armazenamento de blob não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo bloco DE IDENTIFICAção. Como os encargos acumulam-se para blocos únicos, é importante considerar que atualizar uma bolha quando a versão está ativada resultará em blocos únicos adicionais e encargos adicionais.
- Quando a versão blob está ativada, as operações de atualização do design em blocos blobs para que atualizem o menor número possível de blocos. As operações de escrita que permitem o controlo de grãos finos sobre os blocos são a Lista de [Blocos de Colocação](/rest/api/storageservices/put-block) e Colocação de [Blocos](/rest/api/storageservices/put-block-list). A operação [Put Blob,](/rest/api/storageservices/put-blob) por outro lado, substitui todo o conteúdo de uma bolha, pelo que pode levar a encargos adicionais.

### <a name="versioning-billing-scenarios"></a>Verdição de cenários de faturação

Os seguintes cenários demonstram como as taxas acumulam-se para uma bolha de bloco e suas versões.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha tem uma versão anterior. A bolha não foi atualizada desde que a versão foi criada, pelo que as taxas são incorridas apenas para os blocos 1, 2 e 3 únicos.

![Recursos de Armazenamento Azure](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, um bloco (bloco 3 no diagrama) na bolha foi atualizado. Mesmo que o bloco atualizado contenha os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 na versão anterior. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha foi atualizada, mas a versão não. O bloco 3 foi substituído pelo bloco 4 na bolha base, mas a versão anterior ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada &mdash; pelos oito blocos únicos quatro na bolha base, e quatro na versão anterior. Este cenário pode ocorrer se estiver a escrever para uma bolha com a operação Put Blob, porque substitui todo o conteúdo da bolha base.

![Recursos de Armazenamento Azure](./media/versioning-overview/versions-billing-scenario-4.png)

## <a name="see-also"></a>Consulte também

- [Ativar a criação de versões de blobs](versioning-enable.md)
- [Criando uma imagem de uma bolha](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Eliminação suave para Blobs de Armazenamento Azure](storage-blob-soft-delete.md)
