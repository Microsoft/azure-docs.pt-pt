---
title: Versão blob
titleSuffix: Azure Storage
description: A versão de armazenamento de blob mantém automaticamente versões anteriores de um objeto e identifica-as com timetamps. Pode restaurar versões anteriores de uma bolha para recuperar os seus dados se estes forem erroneamente modificados ou eliminados.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 692a820bea69071485a973a988ae91bd70b74f35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380819"
---
# <a name="blob-versioning"></a>Versão blob

Pode permitir que a versão de armazenamento Blob mantenha automaticamente as versões anteriores de um objeto.  Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificada ou eliminada.

A versão blob está ativada na conta de armazenamento e aplica-se a todas as bolhas na conta de armazenamento. Depois de ativar a versão blob para uma conta de armazenamento, o Azure Storage mantém automaticamente versões para cada bolha na conta de armazenamento.

A Microsoft recomenda a utilização de versões blob para manter versões anteriores de uma bolha para uma proteção de dados superior. Quando possível, utilize a versão blob em vez de imagens blob para manter as versões anteriores. Os instantâneos blob fornecem uma funcionalidade semelhante na medida em que mantêm versões anteriores de uma bolha, mas as imagens devem ser mantidas manualmente pela sua aplicação.

Para aprender a permitir a versão blob, consulte [Ativar e gerir a versão blob](versioning-enable.md).

> [!IMPORTANT]
> A versão blob não pode ajudá-lo a recuperar da eliminação acidental de uma conta de armazenamento ou de um recipiente. Para evitar a eliminação acidental da conta de armazenamento, configuure um bloqueio no recurso da conta de armazenamento. Para obter mais informações sobre o bloqueio dos recursos do Azure, consulte [os recursos do Lock para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md). Para proteger os recipientes contra a eliminação acidental, configurar o recipiente para apagar suavemente para a conta de armazenamento. Para obter mais informações, consulte [a exclusão suave para recipientes (pré-visualização)](soft-delete-container-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Como funciona a versão blob

Uma versão captura o estado de uma bolha num dado momento. Quando a versão blob é ativada para uma conta de armazenamento, o Azure Storage cria automaticamente uma nova versão de uma bolha cada vez que a bolha é modificada ou eliminada.

Quando se cria uma bolha com a versão ativada, a nova bolha é a versão atual do blob (ou a bolha base). Se modificar posteriormente essa bolha, o Azure Storage cria uma versão que captura o estado da bolha antes de ser modificada. A bolha modificada torna-se a nova versão atual. Uma nova versão é criada cada vez que modifica a bolha.

O diagrama a seguir mostra como as versões são criadas em operações de escrita e eliminação, e como uma versão anterior pode ser promovida para ser a versão atual:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagrama mostrando como funciona a versão blob":::

Ter um grande número de versões por blob pode aumentar a latência para operações de listagem de bolhas. A Microsoft recomenda manter menos de 1000 versões por blob. Pode utilizar a gestão do ciclo de vida para eliminar automaticamente versões antigas. Para obter mais informações sobre a gestão do ciclo de vida, consulte [os custos da Otimização automatizando os níveis de acesso ao armazenamento Azure Blob](storage-lifecycle-management-concepts.md).

Quando elimina uma bolha com versão ativada, o Azure Storage cria uma versão que captura o estado da bolha antes de ser eliminada. A versão atual do blob é então eliminada, mas as versões do blob persistem, para que possa ser recriada se necessário. 

As versões blob são imutáveis. Não é possível modificar o conteúdo ou metadados de uma versão blob existente.

A versão blob está disponível para as contas de armazenamento v2, block blob e Blob. As contas de armazenamento com um espaço hierárquico habilitado para uso com Azure Data Lake Storage Gen2 não são atualmente suportadas.

Versão 2019-10-10 e superior do Azure Storage REST API suporta a versão blob.

### <a name="version-id"></a>ID da versão

Cada versão blob é identificada por um ID de versão. O valor da versão ID é o tempotampício em que a bolha foi escrita ou atualizada. A versão ID é atribuída no momento em que a versão é criada.

Pode executar operações de leitura ou eliminação numa versão específica de uma bolha, fornecendo o seu ID de versão. Se omitir o ID da versão, a operação atua contra a versão atual (a bolha base).

Quando chama uma operação de escrita para criar ou modificar uma bolha, o Azure Storage devolve o cabeçalho *x-ms-versão id* na resposta. Este cabeçalho contém o ID da versão atual da bolha que foi criada pela operação de escrita.

O ID da versão permanece o mesmo durante toda a vida da versão.

### <a name="versioning-on-write-operations"></a>Versão em operações de escrita

Quando a versão blob é ligada, cada operação de escrita para uma bolha cria uma nova versão. As operações de escrita incluem [Put Blob,](/rest/api/storageservices/put-blob) [Put Block List,](/rest/api/storageservices/put-block-list)Copy [Blob](/rest/api/storageservices/copy-blob)e [set Blob Metadados](/rest/api/storageservices/set-blob-metadata).

Se a operação de escrita criar uma nova bolha, então a bolha resultante é a versão atual do blob. Se a operação de escrita modificar uma bolha existente, então os novos dados são capturados na bolha atualizada, que é a versão atual, e o Azure Storage cria uma versão que salva o estado anterior da bolha.

Para simplificar, os diagramas apresentados neste artigo exibem o ID da versão como um simples valor inteiro. Na realidade, a versão ID é uma etiqueta de tempo. A versão atual é mostrada em azul, e as versões anteriores são mostradas em cinza.

O diagrama que se segue mostra como as operações de escrita afetam as versões blob. Quando uma bolha é criada, esta bolha é a versão atual. Quando a mesma bolha é modificada, uma nova versão é criada para salvar o estado anterior da bolha, e a bolha atualizada torna-se a versão atual.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagrama mostrando como as operações de escrita afetam as bolhas versadas.":::

> [!NOTE]
> Uma bolha que foi criada antes de a versão ser ativada para a conta de armazenamento não tem um ID de versão. Quando esta bolha é modificada, a bolha modificada torna-se a versão atual, e uma versão é criada para salvar o estado da bolha antes da atualização. A versão é atribuída a uma versão ID que é o seu tempo de criação.

### <a name="versioning-on-delete-operations"></a>Versões em operações de eliminação

Quando elimina uma bolha, a versão atual do blob torna-se uma versão anterior e a bolha de base é eliminada. Todas as versões anteriores existentes do blob são preservadas quando a bolha é eliminada.

Chamar a operação [Delete Blob](/rest/api/storageservices/delete-blob) sem uma versão ID elimina a bolha de base. Para eliminar uma versão específica, forneça o ID para essa versão na operação de eliminação.

O diagrama a seguir mostra o efeito de uma operação de eliminação numa bolha versada:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagrama mostrando eliminação da bolha versada.":::

Escrever novos dados para a bolha cria uma nova versão da bolha. Quaisquer versões existentes não são afetadas, como mostra o diagrama seguinte.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagrama mostrando a recriação da bolha versada após a eliminação.":::

### <a name="blob-types"></a>Tipos de blobs

Quando a versão blob está ativada para uma conta de armazenamento, todas as operações de escrita e eliminação em blobs de blocos desencadeiam a criação de uma nova versão, com exceção da operação [Put Block.](/rest/api/storageservices/put-block)

Para bolhas de página e bolhas de apêndice, apenas um subconjunto de operações de escrita e eliminação desencadeia a criação de uma versão. Estas operações incluem:

- [Colocar o Blob](/rest/api/storageservices/put-blob)
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list)
- [Eliminar Blob](/rest/api/storageservices/delete-blob)
- [Definir metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Copiar Blob](/rest/api/storageservices/copy-blob)

As seguintes operações não despoletam a criação de uma nova versão. Para capturar as alterações dessas operações, tire uma foto manual:

- [Página de colocar](/rest/api/storageservices/put-page) (bolha de página)
- [Bloco do Apêndice](/rest/api/storageservices/append-block) (blob do apêndice)

Todas as versões de uma bolha devem ser do mesmo tipo de bolha. Se uma bolha tiver versões anteriores, não pode substituir uma bolha de um tipo com outro tipo, a menos que apague primeiro a bolha e todas as suas versões.

### <a name="access-tiers"></a>Camadas de acesso

Pode mover qualquer versão de uma bolha de bloco, incluindo a versão atual, para um nível de acesso de blob diferente, chamando a operação [set Blob Tier.](/rest/api/storageservices/set-blob-tier) Pode tirar partido dos preços de menor capacidade movendo versões mais antigas de uma bolha para o nível fresco ou de arquivo. Para mais informações, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e arquivados.](storage-blob-storage-tiers.md)

Para automatizar o processo de mover bolhas de bloco para o nível apropriado, utilize a gestão do ciclo de vida blob. Para obter mais informações sobre a gestão do ciclo de vida, consulte [Gerir o ciclo de vida de armazenamento Azure Blob](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Ativar ou desativar a versão blob

Para aprender a ativar ou desativar a versão blob, consulte [Ativar e gerir a versão blob](versioning-enable.md).

A versão blob incapacitante não apaga bolhas, versões ou instantâneos existentes. Quando desativar a versão blob, quaisquer versões existentes permanecem acessíveis na sua conta de armazenamento. Posteriormente, não são criadas novas versões.

Se uma bolha foi criada ou modificada após a versão ter sido desativada na conta de armazenamento, então a sobreposição da bolha cria uma nova versão. O blob atualizado já não é a versão atual e não tem um ID de versão. Todas as atualizações subsequentes à bolha substituirão os seus dados sem salvar o estado anterior.

Pode ler ou eliminar versões utilizando o ID da versão após a versão ser desativada. Também pode listar as versões de uma bolha depois de a versão ser desativada.

O diagrama seguinte mostra como modificar uma bolha após a versão é desativada cria uma bolha que não é versão. As versões existentes associadas à bolha persistem.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagrama mostrando a bolha de base modificada após a versão desativada.":::

## <a name="blob-versioning-and-soft-delete"></a>Versões blob e eliminação suave

A versão blob e o blob soft delete funcionam em conjunto para lhe proporcionar uma proteção de dados ótima. Quando ativar a eliminação suave, especifica quanto tempo o Azure Storage deve reter uma bolha apagada suave. Qualquer versão blob apagada escasseia no sistema e pode ser não desestada dentro do período de retenção de eliminação suave. Para obter mais informações sobre a eliminação suave da bolha, consulte [Soft delete para blobs de armazenamento Azure](./soft-delete-blob-overview.md).

### <a name="deleting-a-blob-or-version"></a>Apagar uma bolha ou versão

O soft delete oferece proteção adicional para eliminar versões blob. Se tanto a versão como a eliminação suave estiverem ativadas na conta de armazenamento, então quando apaga uma bolha, o Azure Storage cria uma nova versão para salvar o estado da bolha imediatamente antes da eliminação e elimina a versão atual. A nova versão não é eliminada suavemente e não é removida quando o período de retenção de eliminação suave expirar.

Quando elimina uma versão anterior do blob, a versão é apagada suavemente. A versão de eliminação suave é mantida durante todo o período de retenção especificado nas definições de eliminação suave para a conta de armazenamento e é permanentemente eliminada quando o período de retenção de eliminação suave expirar.

Para remover uma versão anterior de uma bolha, elimine-a explicitamente especificando o ID da versão.

O diagrama que se segue mostra o que acontece quando se apaga uma bolha ou uma versão blob.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagrama mostrando a eliminação de uma versão com exclusão suave ativada.":::

Se a versão e a eliminação suave estiverem ativadas numa conta de armazenamento, então não é criada nenhuma imagem de imagem com eliminação suave quando uma versão blob ou blob é modificada ou eliminada.

### <a name="restoring-a-soft-deleted-version"></a>Restaurar uma versão de soft-deleed

Pode restaurar uma versão blob de eliminação suave, chamando a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) na versão enquanto o período de retenção de eliminação suave está em vigor. A **operação Undelete Blob** restaura todas as versões de bolhas apagadas suavemente.

Restaurar versões de soft-dele com a operação **Undelete Blob** não promove nenhuma versão para ser a versão atual. Para restaurar a versão atual, primeiro restaure todas as versões de eliminar suavemente e, em seguida, use a operação [Copy Blob](/rest/api/storageservices/copy-blob) para copiar uma versão anterior para restaurar a bolha.

O diagrama que se segue mostra como restaurar as versões blob desagravada com a operação **Undelete Blob** e como restaurar a versão atual do blob com a operação **Copy Blob.**

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagrama mostrando como restaurar versões desagravamento suave.":::

Após o período de retenção de eliminação suave, quaisquer versões blob apagadas com soft-delete são permanentemente eliminadas.

## <a name="blob-versioning-and-blob-snapshots"></a>Versões blob e snapshots blob

Uma foto de bolhas é uma cópia só de leitura de uma bolha que é tirada num ponto específico no tempo. As imagens blob e as versões blob são similares, mas uma imagem instantânea é criada manualmente por si ou pela sua aplicação, enquanto uma versão blob é criada automaticamente numa operação de escrita ou eliminação quando a versão blob está ativada para a sua conta de armazenamento.

> [!IMPORTANT]
> A Microsoft recomenda que, depois de ativar a versão blob, também atualize a sua aplicação para deixar de tirar fotografias de blobs de blocos. Se a versão estiver ativada para a sua conta de armazenamento, todas as atualizações e eliminações de blob de blocos são capturadas e preservadas por versões. Tirar instantâneos não oferece quaisquer proteções adicionais aos dados do blob do bloco se a versão blob estiver ativada, podendo aumentar os custos e a complexidade da aplicação.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Snapshot uma bolha quando a versão estiver ativada

Embora não seja recomendado, pode tirar uma foto de uma bolha que também é ver versão. Se não conseguir atualizar a sua aplicação para deixar de tirar fotografias de bolhas quando ativar a versão, a sua aplicação pode suportar imagens e versões.

Quando tira uma foto de uma bolha em versão, uma nova versão é criada ao mesmo tempo que o instantâneo é criado. Uma nova versão atual também é criada quando um instantâneo é tirado.

O diagrama que se segue mostra o que acontece quando se tira uma fotografia de uma bolha em versão. No diagrama, as versões blob e as imagens com a versão ID 2 e 3 contêm dados idênticos.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagrama mostrando instantâneos de uma bolha em versão.":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizar operações em versões blob

Pode autorizar o acesso às versões blob utilizando uma das seguintes abordagens:

- Utilizando o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um diretor de segurança Azure Ative (Azure AD). A Microsoft recomenda a utilização do Azure AD para uma segurança superior e facilidade de utilização. Para obter mais informações sobre a utilização do Azure AD com operações de [bolhas, consulte o Acesso autorizado a bolhas e filas utilizando o Azure Ative Directory](../common/storage-auth-aad.md).
- Utilizando uma assinatura de acesso partilhado (SAS) para delegar o acesso às versões blob. Especifique o ID da versão para o tipo de recurso `bv` assinado, representando uma versão blob, para criar um token SAS para operações numa versão específica. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).
- Utilizando as chaves de acesso à conta para autorizar operações contra versões blob com Chave Partilhada. Para mais informações, consulte [Autorizar com chave partilhada.](/rest/api/storageservices/authorize-with-shared-key)

A versão blob foi concebida para proteger os seus dados de eliminação acidental ou maliciosa. Para melhorar a proteção, eliminar uma versão blob requer permissões especiais. As seguintes secções descrevem as permissões necessárias para eliminar uma versão blob.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>A ação do Azure RBAC para eliminar uma versão blob

A tabela que se segue mostra quais as ações do Azure RBAC que suportam a eliminação de uma bolha ou uma versão blob.

| Description | Operação de serviço blob | A ação de dados do RBAC Azure necessária | Suporte de função incorporado Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Apagar a versão atual da bolha | Eliminar Blob | **Microsoft.Storage/storageAcounts/blobServices/containers/blobs/delete** | Contribuinte de Dados do Armazenamento de Blobs |
| Apagar uma versão | Eliminar Blob | **Microsoft.Storage/storageAcounts/blobServices/containers/blobs/deleteBlobVersion/action** | Proprietário dos Dados do Armazenamento de Blobs |

### <a name="shared-access-signature-sas-parameters"></a>Parâmetros de assinatura de acesso partilhado (SAS)

O recurso assinado para uma versão blob é `bv` . Para mais informações, consulte [Criar um serviço SAS](/rest/api/storageservices/create-service-sas) ou [criar uma delegação de utilizador SAS](/rest/api/storageservices/create-user-delegation-sas).

A tabela seguinte mostra a permissão necessária num SAS para eliminar uma versão blob.

| **Permissão** | **Símbolo URI** | **Operações permitidas** |
|----------------|----------------|------------------------|
| Eliminar         | x              | Apague uma versão blob. |

## <a name="pricing-and-billing"></a>Preços e faturação

Permitir a versão blob pode resultar em custos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

As versões blob, como as imagens blob, são faturadas ao mesmo ritmo que os dados ativos. A forma como as versões são faturadas depende se definiu explicitamente o nível para a bolha de base ou para qualquer uma das suas versões (ou instantâneos). Para obter mais informações sobre os níveis de blob, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo](storage-blob-storage-tiers.md).

Se não alterou o nível de uma bolha ou versão, então é cobrado por blocos únicos de dados através dessa bolha, das suas versões e de quaisquer instantâneos que possa ter. Para obter mais informações, consulte [Billing quando o nível de bolha não tiver sido explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se mudou o nível de uma bolha ou versão, então é cobrado por todo o objeto, independentemente de a bolha e a versão estarem eventualmente novamente no mesmo nível. Para obter mais informações, consulte [Billing quando o nível de bolhas tiver sido explicitamente definido](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> Permitir a versão para dados que são frequentemente substituídos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência durante as operações de cotação. Para mitigar estas preocupações, guarde frequentemente dados sobreescritos numa conta de armazenamento separada com a versão desativada.

Para obter mais informações sobre detalhes de faturação para fotos blob, consulte [as fotos blob](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Faturação quando o nível blob não foi explicitamente definido

Se não tiver definido explicitamente o nível blob para uma bolha de base ou qualquer uma das suas versões, então é cobrado por blocos ou páginas únicos através da bolha, suas versões e quaisquer instantâneos que possa ter. Os dados que são partilhados através de uma bolha e as suas versões são cobrados apenas uma vez. Quando uma bolha é atualizada, os dados numa bolha de base divergem dos dados armazenados nas suas versões, e os dados únicos são carregados por bloco ou página.

Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID do bloco e os mesmos dados que tem na versão anterior. Depois de o bloco ser novamente comprometido, diverge da sua contrapartida na versão anterior, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa bolha de página que é atualizada com dados idênticos.

O armazenamento de bolhas não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo ID do bloco. Como os encargos acumulam-se para blocos únicos, é importante ter em mente que atualizar uma bolha quando a versão está ativada resultará em blocos exclusivos adicionais e custos adicionais.

Quando a versão blob estiver ativada, ligue para as operações de atualização em blobs de blocos para que atualizem o menor número possível de blocos. As operações de escrita que permitem um controlo fino sobre blocos são [Put Block](/rest/api/storageservices/put-block) and Put [Block List](/rest/api/storageservices/put-block-list). A operação [Put Blob,](/rest/api/storageservices/put-blob) por outro lado, substitui todo o conteúdo de uma bolha, pelo que pode levar a custos adicionais.

Os seguintes cenários demonstram como os encargos acumulam-se para uma bolha de bloco e as suas versões quando o nível blob não foi explicitamente definido.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha tem uma versão anterior. A bolha não foi atualizada desde que a versão foi criada, pelo que os encargos são incorridos apenas para blocos únicos 1, 2 e 3.

![Diagrama 1 mostrando faturação para blocos únicos na bolha base e versão anterior.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, um bloco (bloco 3 no diagrama) na bolha foi atualizado. Apesar de o bloco atualizado conter os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 na versão anterior. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 2 mostrando faturação para blocos únicos na bolha base e versão anterior.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha foi atualizada, mas a versão não. O bloco 3 foi substituído pelo bloco 4 na bolha base, mas a versão anterior ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 3 mostrando faturação para blocos únicos na bolha base e versão anterior.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha de base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada para todos os oito blocos únicos &mdash; quatro na bolha base, e quatro na versão anterior. Este cenário pode ocorrer se estiver a escrever para uma bolha com a operação [Put Blob,](/rest/api/storageservices/put-blob) porque substitui todo o conteúdo da bolha de base.

![Diagrama 4 mostrando faturação para blocos únicos na bolha base e versão anterior.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Faturação quando o nível blob foi explicitamente definido

Se tiver definido explicitamente o nível blob para uma bolha ou versão (ou instantâneo), então é cobrado o comprimento total do conteúdo do objeto no novo nível, independentemente de partilhar blocos com um objeto no nível original. Também é cobrado o comprimento total do conteúdo da versão mais antiga do nível original. Quaisquer outras versões ou instantâneos anteriores que permaneçam no nível original são cobrados por blocos únicos que possam partilhar, como descrito em [Billing quando o nível blob não foi explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Mover uma bolha para um novo nível

A tabela seguinte descreve o comportamento de faturação para uma bolha ou versão quando é movido para um novo nível.

| Quando o nível do blob é definido explicitamente em... | Então é cobrado por... |
|-|-|
| Uma bolha de base com uma versão anterior | A bolha base no novo nível e a versão mais antiga do nível original, além de quaisquer blocos únicos em outras versões. <sup>1</sup> |
| Uma bolha de base com uma versão anterior e um instantâneo | A bolha base no novo nível, a versão mais antiga do nível original, e o instantâneo mais antigo no nível original, além de quaisquer blocos únicos em outras versões ou instantâneos<sup>1</sup>. |
| Uma versão anterior | A versão no novo nível e a bolha base no nível original, além de quaisquer blocos únicos em outras versões. <sup>1</sup> |

<sup>1</sup> Se existirem outras versões ou instantâneos anteriores que não tenham sido retirados do seu nível original, essas versões ou instantâneos são carregados com base no número de blocos únicos que contêm, como descrito em [Billing quando o nível de bolhas não foi explicitamente definido](#billing-when-the-blob-tier-has-not-been-explicitly-set).

O diagrama seguinte ilustra como os objetos são faturados quando uma bolha ver versão é movida para um nível diferente.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagrama mostrando como os objetos são faturados quando uma bolha em versão é explicitamente nivelada.":::

Definir explicitamente o nível para uma bolha, versão ou instantâneo não pode ser desfeito. Se mover uma bolha para um novo nível e depois movê-la de volta para o seu nível original, é cobrado o comprimento total do conteúdo do objeto, mesmo que partilhe blocos com outros objetos no nível original.

As operações que definem explicitamente o nível de uma bolha, versão ou instantâneo incluem:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Definir Camada de Blob)
- [Coloque blob](/rest/api/storageservices/put-blob) com nível especificado
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list) com nível especificado
- [Bolha de cópia](/rest/api/storageservices/copy-blob) com nível especificado

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Apagar uma bolha quando a eliminação suave está ativada

Quando a eliminação suave do blob estiver ativada, se eliminar ou substituir uma bolha de base que tenha definido explicitamente o seu nível, então quaisquer versões anteriores da bolha de eliminação suave são faturadas em pleno comprimento de conteúdo. Para obter mais informações sobre como a versão blob e o soft delete funcionam em conjunto, consulte [a versão Blob e a eliminação suave](#blob-versioning-and-soft-delete).

A tabela seguinte descreve o comportamento de faturação de uma bolha que é apagada suavemente, dependendo se a versão está ativada ou desativada. Quando a versão está ativada, uma versão é criada quando uma bolha é apagada suavemente. Quando a versão é desativada, a eliminação suave de uma bolha cria um instantâneo de eliminação suave.

| Quando se substitui uma bolha de base com o seu nível explicitamente definido... | Então é cobrado por... |
|-|-|
| Se a eliminação suave e a versão macias estão ativadas | Todas as versões existentes com o comprimento total do conteúdo, independentemente do nível. |
| Se a eliminação suave blob estiver ativada, mas a versão é desativada | Todos os instantâneos de exclusão suave existentes com todo o comprimento do conteúdo, independentemente do nível. |

## <a name="see-also"></a>Ver também

- [Ativar e gerir a versão blob](versioning-enable.md)
- [Criando um instantâneo de uma bolha](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Excluir suave para bolhas de armazenamento Azure](./soft-delete-blob-overview.md)