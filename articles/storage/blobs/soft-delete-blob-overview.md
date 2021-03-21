---
title: Eliminação recuperável para blobs
titleSuffix: Azure Storage
description: A eliminação suave para blobs protege os seus dados para que possa recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados por uma aplicação ou por outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a370a7f04e0e43b96e4a574313c4f24c4990ab6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390362"
---
# <a name="soft-delete-for-blobs"></a>Eliminação recuperável para blobs

A eliminação suave das bolhas protege os seus dados de serem modificados ou eliminados acidentalmente ou erroneamente. Quando a eliminação suave para bolhas estiver ativada para uma conta de armazenamento, as versões blob, blob e instantâneos nessa conta de armazenamento podem ser recuperadas após a sua eliminação, num período de retenção que especifique.

Se houver a possibilidade de os seus dados poderem ser acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de uma conta de armazenamento, a Microsoft recomenda que se apague suavemente. Para obter mais informações sobre a ativação da eliminação suave, consulte [Ativar e gerir a eliminação suave para bolhas](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Sobre excluir suave para bolhas

Quando a eliminação suave para bolhas estiver ativada numa conta de armazenamento, pode recuperar objetos depois de eliminados, dentro do período de retenção de dados especificado. Esta proteção estende-se a quaisquer bolhas (bolhas de bloco, bolhas de apêndio ou bolhas de página) que são apagadas como resultado de um excesso de substituição.

O seguinte diagrama mostra como uma bolha apagada pode ser restaurada quando a mancha de exclusão suave está ativada:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagrama mostrando como uma bolha desagravada pode ser restaurada":::

Se os dados de uma bolha ou instantâneo existentes forem eliminados enquanto a eliminação macia de blob estiver ativada, mas a versão blob não estiver ativada, então é gerada uma imagem suave eliminada para salvar o estado dos dados substituídos. Após o período de retenção especificado ter expirado, o objeto é permanentemente eliminado.

Se a versão blob e a eliminação suave do blob estiverem ativadas na conta de armazenamento, então a eliminação de uma bolha cria uma nova versão em vez de uma imagem depagou-se suavemente. A nova versão não é eliminada suavemente e não é removida quando o período de retenção de eliminação suave expirar. As versões de uma bolha com soft-dele podem ser restauradas dentro do período de retenção, chamando a operação [Undelete Blob.](/rest/api/storageservices/undelete-blob) A bolha pode ser posteriormente restaurada a partir de uma das suas versões, chamando a operação [Copy Blob.](/rest/api/storageservices/copy-blob) Para obter mais informações sobre a utilização da versão blob e eliminar suavemente, consulte [a versão Blob e a eliminação suave](versioning-overview.md#blob-versioning-and-soft-delete).

Os objetos suaves apagados são invisíveis a menos que explicitamente listados.

O blob soft delete é compatível com o contrário, pelo que não tem de fazer alterações nas suas aplicações para tirar partido das proteções que esta funcionalidade oferece. No entanto, [a recuperação de dados](#recovery) introduz uma nova API **da Undelete Blob.**

Blob soft delete está disponível para as contas de armazenamento v2, v1 e Blob de uso geral novos e existentes. Os tipos de conta standard e premium são suportados. Blob soft delete está disponível para todos os níveis de armazenamento, incluindo quente, fresco e arquivo. A soft delete está disponível para discos não geridos, que são bolhas de página sob as capas, mas não estão disponíveis para discos geridos.

### <a name="configuration-settings"></a>Definições de configuração

Quando cria uma nova conta, a eliminação suave é desativada por padrão. A eliminação suave também é desativada por padrão para as contas de armazenamento existentes. Pode ativar ou desativar a exclusão suave para uma conta de armazenamento a qualquer momento.

Quando ativar a eliminação suave, deve configurar o período de retenção. O período de retenção indica a quantidade de tempo em que os dados eliminados suaves são armazenados e disponíveis para recuperação. Para objetos que são explicitamente eliminados, o relógio do período de retenção começa quando os dados são eliminados. Para versões ou instantâneos apagados suaves gerados pela função de exclusão suave quando os dados são substituídos, o relógio começa quando a versão ou instantâneo é gerado. O período de retenção pode ser de 1 a 365 dias.

Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado aplica-se apenas aos dados recentemente eliminados. Os dados previamente eliminados expiram com base no período de retenção configurado quando esses dados foram eliminados. Tentar eliminar um objeto apagado suave não afeta o seu tempo de validade.

Se desativar a eliminação suave, pode continuar a aceder e a recuperar dados suaves eliminados na sua conta de armazenamento que foram guardados enquanto a funcionalidade estava ativada.

### <a name="saving-deleted-data"></a>Guardar dados eliminados

A eliminação suave preserva os seus dados em muitos casos em que os objetos são eliminados ou substituídos.

Quando uma bolha é substituída através do **Put Blob**, **Put Block List**, ou Copy **Blob**, uma versão ou instantâneo do estado da bolha antes da operação de escrita é gerada automaticamente. Este objeto é invisível a menos que os objetos apagados sejam explicitamente listados. Consulte a secção [Recovery](#recovery) para saber como listar objetos apagados suaves.

![Um diagrama que mostra como as imagens de bolhas são armazenadas à medida que são substituídas através da Put Blob, Put Block List ou Copy Blob.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*Os dados apagados suaves são cinzentos, enquanto os dados ativos são azuis. Os dados mais recentemente escritos aparecem abaixo dos dados mais antigos. Quando B0 é substituído com B1, é gerada uma imagem suave apagada de B0. Quando B1 é substituído com B2, é gerada uma imagem suave apagada de B1.*

> [!NOTE]  
> A eliminação suave só oferece proteção excessiva para operações de cópia quando é ligada para a conta da blob de destino.

> [!NOTE]  
> A eliminação suave não permite uma proteção excessiva para bolhas no nível de arquivo. Se uma bolha no arquivo for substituída com uma nova bolha em qualquer nível, a bolha substituída expirará permanentemente.

Quando **delete Blob** é chamado em um instantâneo, esse instantâneo é marcado como suave apagado. Um novo instantâneo não é gerado.

![Um diagrama que mostra como as imagens de bolhas são apagadas suavemente quando se utilizam Delete Blob.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados apagados suaves são cinzentos, enquanto os dados ativos são azuis. Os dados mais recentemente escritos aparecem abaixo dos dados mais antigos. Quando **o Snapshot Blob** é chamado, B0 torna-se um instantâneo e B1 é o estado ativo da bolha. Quando a imagem B0 é apagada, é marcada como suavemente apagada.*

Quando **delete Blob** é chamado numa bolha de base (qualquer bolha que não seja em si uma instantânea), essa bolha é marcada como suave apagada. Consistente com o comportamento anterior, chamar **Delete Blob** numa bolha que tenha instantâneos ativos devolve um erro. Chamar **Delete Blob** numa bolha com instantâneos apagados suaves não devolve um erro. Pode ainda apagar uma bolha e todas as suas imagens numa única operação quando a eliminação suave é ligada. Ao fazê-lo, marca a bolha de base e as instantâneas como suaves apagadas.

![Um diagrama que mostra o que acontece quando o Delete Blog é chamado numa bolha de base.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*Os dados apagados suaves são cinzentos, enquanto os dados ativos são azuis. Os dados mais recentemente escritos aparecem abaixo dos dados mais antigos. Aqui, é feita uma chamada **Delete Blob** para eliminar B2 e todas as imagens associadas. A bolha ativa, B2, e todos os instantâneos associados são marcados como suaves apagados.*

> [!NOTE]  
> Quando uma bolha apagada suave é substituída, uma imagem suave apagada do estado da bolha antes da operação de escrita é gerada automaticamente. A nova bolha herda o nível da bolha substituída.

A eliminação suave não guarda os seus dados em casos de eliminação de contentores ou contas, nem quando os metadados blob e as propriedades blob são substituídos. Para proteger uma conta de armazenamento da eliminação, pode configurar uma fechadura utilizando o Gestor de Recursos Azure. Para obter mais informações, consulte o Azure Resource Manager artigo [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).  Para proteger os recipientes contra a eliminação acidental, configurar o recipiente para apagar suavemente para a conta de armazenamento. Para obter mais informações, consulte [a exclusão suave para recipientes (pré-visualização)](soft-delete-container-overview.md).

Os seguintes detalhes da tabela são comportamentos esperados quando a eliminação suave é ligada:

| Operação REST API | Tipo de recurso | Description | Mudança de comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Elimina a conta de armazenamento, incluindo todos os recipientes e bolhas que contém.                           | Nenhuma alteração. Os recipientes e bolhas na conta eliminada não são recuperáveis. |
| [Eliminar Contentor](/rest/api/storageservices/delete-container) | Contentor | Elimina o recipiente, incluindo todas as bolhas que contém. | Nenhuma alteração. As bolhas no recipiente apagado não são recuperáveis. |
| [Colocar o Blob](/rest/api/storageservices/put-blob) | Bloco, apêndice e bolhas de página | Cria uma nova bolha ou substitui uma bolha existente dentro de um recipiente | Se for utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto também se aplica a uma bolha previamente macia apagada se e somente se for substituída por uma bolha do mesmo tipo (Bloco, apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados suaves existentes serão expirados permanentemente. |
| [Eliminar Blob](/rest/api/storageservices/delete-blob) | Bloco, apêndice e bolhas de página | Marca uma bolha ou uma foto de bolha para a eliminação. A bolha ou instantâneo é mais tarde apagada durante a recolha do lixo | Se for utilizado para apagar uma imagem de bolha, esta imagem é marcada como suave apagada. Se for utilizado para apagar uma bolha, esta bolha é marcada como suave apagada. |
| [Copiar Blob](/rest/api/storageservices/copy-blob) | Bloco, apêndice e bolhas de página | Copia uma bolha de origem para uma bolha de destino na mesma conta de armazenamento ou em outra conta de armazenamento. | Se for utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto também se aplica a uma bolha previamente macia apagada se e somente se for substituída por uma bolha do mesmo tipo (Bloco, apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados suaves existentes serão expirados permanentemente. |
| [Colocar Bloco](/rest/api/storageservices/put-block) | Blobs de blocos | Cria um novo bloco para ser cometido como parte de uma bolha de bloco. | Se usado para comprometer um bloqueio a uma bolha que está ativa, não há nenhuma mudança. Se usado para comprometer um bloco a uma bolha que é suavemente eliminada, uma nova bolha é criada e um instantâneo é gerado automaticamente para capturar o estado da bolha suave apagada. |
| [Colocar lista de blocos](/rest/api/storageservices/put-block-list) | Blobs de blocos | Compromete uma bolha especificando o conjunto de IDs de bloco que compõem a bolha de bloco. | Se for utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto também se aplica a uma bolha previamente macia apagada se e somente se for uma bolha de bloco. Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados suaves existentes serão expirados permanentemente. |
| [Colocar página](/rest/api/storageservices/put-page) | Blobs de páginas | Escreve uma gama de páginas para uma bolha de página. | Nenhuma alteração. Os dados do blob da página que são substituídos ou limpos utilizando esta operação não são guardados e não são recuperáveis. |
| [Bloco de Apêndice](/rest/api/storageservices/append-block) | Blobs de acréscimo | Escreve um bloco de dados para o fim de uma bolha de apêndice | Nenhuma alteração. |
| [Definir propriedades blob](/rest/api/storageservices/set-blob-properties) | Bloco, apêndice e bolhas de página | Define valores para as propriedades do sistema definidas para uma bolha. | Nenhuma alteração. As propriedades de bolhas sobreescritas não são recuperáveis. |
| [Definir metadados blob](/rest/api/storageservices/set-blob-metadata) | Bloco, apêndice e bolhas de página | Define os metadados definidos pelo utilizador para a bolha especificada como um ou mais pares de valor-nome. | Nenhuma alteração. Os metadados blob sobrescritos não são recuperáveis. |

É importante notar que chamar **a Página put** para substituir ou limpar intervalos de uma bolha de página não gerará automaticamente instantâneos. Os discos de máquinas virtuais são apoiados por bolhas de página e usam **a Página Put** para escrever dados.

### <a name="recovery"></a>Recuperação

Chamar a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) numa bolha de base apagada suave restaura-a e todos os instantâneos suaves apagados associados como ativos. Chamar a **operação Undelete Blob** numa bolha de base ativa restaura todos os instantâneos suaves e suaves associados como ativos. Quando as imagens são restauradas como ativas, parecem imagens geradas pelo utilizador; não substituem a bolha da base.

Para restaurar uma bolha para um instantâneo específico e suave, pode chamar **Undelete Blob** na bolha de base. Em seguida, pode copiar a imagem sobre a bolha agora ativa. Também pode copiar a imagem para uma nova bolha.

![Um diagrama que mostra o que acontece quando a bolha undelete é usada.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*Os dados apagados suaves são cinzentos, enquanto os dados ativos são azuis. Os dados mais recentemente escritos aparecem abaixo dos dados mais antigos. Aqui, **Undelete Blob** é chamado na bolha B, restaurando assim a bolha de base, B1, e todos os instantâneos associados, aqui apenas B0, como ativo. No segundo passo, B0 é copiado sobre a bolha da base. Esta operação de cópia gera uma imagem suave apagada de B1.*

Para visualizar bolhas e instantâneos de bolhas apagadas suaves, pode optar por incluir dados eliminados em **List Blobs**. Pode optar por ver apenas bolhas de base apagadas suaves ou incluir instantâneos de bolhas apagados suaves também. Para todos os dados eliminados suavemente, pode ver a hora em que os dados foram eliminados, bem como o número de dias antes de os dados serem expirados permanentemente.

### <a name="example"></a>Exemplo

Segue-se a saída da consola de um script .NET que carrega, substitui, faz instantâneos, elimina e restaura uma bolha chamada *HelloWorld* quando a eliminação suave é ligada:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Consulte a secção [etapas seguintes](#next-steps) para obter um ponteiro para a aplicação que produziu esta saída.

## <a name="pricing-and-billing"></a>Preços e faturação

Todos os dados eliminados suaves são faturados à mesma taxa que os dados ativos. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção configurado. Para um mergulho mais profundo em instantâneos e como acumulam encargos, consulte [compreender como os instantâneos acumulam encargos](./snapshots-overview.md).

Não será cobrado para as transações relacionadas com a geração automática de instantâneos. Será cobrado para transações **da Undelete Blob** à taxa para operações de escrita.

Para obter mais detalhes sobre os preços do Azure Blob Storage em geral, consulte a [página de preços de armazenamento Azure Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando inicialmente liga o soft delete, a Microsoft recomenda usar um curto período de retenção para entender melhor como a funcionalidade irá afetar a sua conta.

Permitir a eliminação suave de dados frequentemente substituídos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência ao enumerar bolhas. Pode atenuar este custo e latência adicionais armazenando os dados frequentemente sobreescritos numa conta de armazenamento separada onde a eliminação suave é desativada.

## <a name="faq"></a>FAQ

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Posso utilizar o set Blob Tier API para colocar bolhas de nível com instantâneos suaves apagados?

Sim. As imagens suaves apagadas permanecerão no nível original, mas a bolha de base irá mover-se para o novo nível.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>As contas de armazenamento premium têm um limite de instantâneo por bolha de 100. Os instantâneos apagados suaves contam para este limite?

Não, as imagens apagadas suaves não contam para este limite.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Se eu apagar uma conta inteira ou um recipiente com exclusão suave ligado, todas as bolhas associadas serão guardadas?

Não, se apagar uma conta ou um recipiente inteiro, todas as bolhas associadas serão permanentemente eliminadas. Para obter mais informações sobre a proteção de uma conta de armazenamento ser acidentalmente eliminada, consulte [Os Recursos de Bloqueio para Evitar Alterações Inesperadas](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Posso ver as métricas de capacidade para dados eliminados?

Os dados eliminados suaves estão incluídos como parte da sua capacidade total de conta de armazenamento. Para obter mais informações sobre a capacidade de armazenamento de rastreio e monitorização, consulte [Storage Analytics](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Posso ler e copiar fotos suaves apagadas da minha bolha?  

Sim, mas primeiro tens de ligar ao Undelete.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>A exclusão suave está disponível para discos de máquinas virtuais?  

A eliminação suave está disponível tanto para discos premium como não geridos padrão, que são bolhas de página sob as capas. A eliminação suave só o ajudará a recuperar os dados eliminados por **Delete Blob,** **Put Blob,** **Put Block List** e **Copy Blob** operações. Os dados substituídos por uma chamada para **Put Page** não são recuperáveis.

Uma máquina virtual Azure escreve para um disco não gerido usando chamadas para **Put Page**, por isso usar exclusão suave para desfazer escreve para um disco não gerido a partir de um Azure VM não é um cenário suportado.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Preciso de alterar as minhas aplicações existentes para utilizar a eliminação suave?

É possível aproveitar a eliminação suave independentemente da versão API que está a utilizar. No entanto, para listar e recuperar blobs e snapshots de bolhas apagadas suaves, terá de utilizar a versão 2017-07-29 da [API de Armazenamento Azure REST](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou superior. A Microsoft recomenda sempre a utilização da versão mais recente da Azure Storage API.

## <a name="next-steps"></a>Passos seguintes

- [Ativar a eliminação recuperável para blobs](./soft-delete-blob-enable.md)
- [Versão blob](versioning-overview.md)