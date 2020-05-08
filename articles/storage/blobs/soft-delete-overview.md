---
title: Eliminação suave para armazenamento Blob
titleSuffix: Azure Storage
description: Ative a eliminação suave de objetos blob para que possa recuperar mais facilmente os seus dados quando estes forem erroneamente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: dd5d9c721c3e0204a66367b76654f9a917e26ba6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884634"
---
# <a name="soft-delete-for-blob-storage"></a>Eliminação suave para armazenamento Blob

A eliminação suave protege os dados blob de serem acidentalmente ou erroneamente modificados ou eliminados. Quando o soft delete está ativado para uma conta de armazenamento, bolhas, versões blob (pré-visualização) e instantâneos nessa conta de armazenamento podem ser recuperados após a sua eliminação, num período de retenção que especifica.

Se houver a possibilidade de os seus dados poderem ser acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de uma conta de armazenamento, a Microsoft recomenda que se vire a eliminar suavemente.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete"></a>Sobre o soft delete

Quando o soft delete é ativado numa conta de armazenamento, pode recuperar objetos depois de terem sido eliminados, dentro do período de retenção de dados especificado. Esta proteção estende-se a todos os dados blob (blocos, bolhas de apêndice e bolhas de página) que são apagados como resultado de uma sobreescrita.

Se os dados de uma bolha ou instantâneo existentes forem eliminados enquanto o soft delete está ativado, mas a versão blob (pré-visualização) não está ativada, então um instantâneo suave apagado é gerado para salvar o estado dos dados sobreescritos. Após o período de retenção especificado ter expirado, o objeto é permanentemente eliminado.

Se a versão blob e o soft delete estiverem ativados na conta de armazenamento, então a eliminação de uma bolha cria uma nova versão em vez de uma imagem suave. A nova versão não é eliminada suavemente e não é removida quando expira o período de retenção soft-delete. As versões soft-delete de uma bolha podem ser restauradas dentro do período de retenção, ligando para a operação [Undelete Blob.](/rest/api/storageservices/undelete-blob) A bolha pode ser posteriormente restaurada a partir de uma das suas versões, chamando a operação [Copy Blob.](/rest/api/storageservices/copy-blob) Para obter mais informações sobre a utilização da versão blob e apagar suavemente em conjunto, consulte a [versão Blob e o soft delete](versioning-overview.md#blob-versioning-and-soft-delete).

Os objetos apagados macios são invisíveis a menos que explicitamente listados.

O soft delete é compatível com o retrocesso, pelo que não é preciso fazer alterações nas suas aplicações para tirar partido das proteções que esta funcionalidade oferece. No entanto, [a recuperação de dados](#recovery) introduz uma nova API Não **DeleteB.**

A eliminação suave está disponível para contas de armazenamento v2 de uso geral, v1 de uso geral e de armazenamento blob. Os tipos de conta standard e premium são suportados. O soft delete está disponível para todos os níveis de armazenamento, incluindo quente, fresco e arquivo. O soft delete está disponível para discos não geridos, que são bolhas de página sob as capas, mas não estão disponíveis para discos geridos.

### <a name="configuration-settings"></a>Definições de configuração

Quando cria uma nova conta, o soft delete é desativado por padrão. A eliminação suave também é desativada por padrão para as contas de armazenamento existentes. Pode ativar ou desativar a eliminação suave para uma conta de armazenamento a qualquer momento.

Quando ativa a eliminação suave, tem de configurar o período de retenção. O período de retenção indica a quantidade de tempo que os dados eliminados suaves são armazenados e disponíveis para recuperação. Para objetos explicitamente eliminados, o relógio do período de retenção começa quando os dados são eliminados. Para versões ou instantâneos eliminados suaves gerados pela funcionalidade soft delete quando os dados são substituídos, o relógio começa quando a versão ou o instantâneo são gerados. O período de retenção pode ser entre 1 e 365 dias.

Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado aplica-se apenas aos dados recentemente eliminados. Os dados previamente eliminados expiram com base no período de retenção que foi configurado quando esses dados foram eliminados. Tentar eliminar um objeto apagado suave não afeta o seu tempo de validade.

Se desativar a eliminação suave, pode continuar a aceder e recuperar dados apagados suaves na sua conta de armazenamento que foram guardados enquanto a funcionalidade estava ativada.

### <a name="saving-deleted-data"></a>Guardar dados eliminados

A eliminação suave preserva os seus dados em muitos casos em que os objetos são eliminados ou substituídos.

Quando uma bolha é substituída usando **put blob**, **Put Block List**, ou Copy **Blob**, uma versão ou instantâneo do estado da bolha antes da operação de escrita é automaticamente gerada. Este objeto é invisível a menos que objetos eliminados suavemente estejam explicitamente listados. Consulte a secção [Recovery](#recovery) para aprender a listar objetos apagados macios.

![](media/soft-delete-overview/storage-blob-soft-delete-overwrite.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Quando B0 é substituído com B1, é gerada uma imagem suave apagada de B0. Quando b1 é substituído com B2, é gerada uma imagem suave apagada de B1.*

> [!NOTE]  
> A eliminação suave só oferece proteção de sobreescrita para operações de cópia quando é ligada para a conta da bolha de destino.

> [!NOTE]  
> A eliminação suave não oferece proteção de sobreescrita para bolhas no nível de arquivo. Se uma bolha no arquivo for substituída por uma nova bolha em qualquer nível, a bolha por escrito é permanentemente expirada.

Quando **o Delete Blob** é chamado num instantâneo, este instantâneo é marcado como suave mente apagado. Não é gerado um novo instantâneo.

![](media/soft-delete-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Quando **o Snapshot Blob** é chamado, b0 torna-se um instantâneo e B1 é o estado ativo da bolha. Quando o instantâneo B0 é apagado, é marcado como suave mente apagado.*

Quando **delete Blob** é chamado em uma bolha base (qualquer bolha que não seja em si um instantâneo), essa bolha é marcada como suave eliminada. Consistente com o comportamento anterior, chamar **Delete Blob** numa bolha que tem instantâneos ativos devolve um erro. Chamar **apagar blob** numa bolha com imagens apagadas suaves não devolve um erro. Ainda pode eliminar uma bolha e todas as suas imagens numa única operação quando a eliminação suave é ligada. Ao fazê-lo marca a bolha base e os instantâneos como apagados suaves.

![](media/soft-delete-overview/storage-blob-soft-delete-explicit-include.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Aqui, é feita uma chamada **Delete Blob** para eliminar B2 e todas as imagens associadas. A bolha ativa, B2 e todos os instantâneos associados são marcados como suavemente apagados.*

> [!NOTE]  
> Quando uma bolha suave apagada é substituída, uma imagem suave apagada do estado da bolha antes da operação de escrita é automaticamente gerada. A nova bolha herda o nível da bolha escrita.

A eliminação suave não guarda os seus dados em casos de eliminação de contentores ou conta, nem quando os metadados blob e as propriedades blob são substituídos. Para proteger uma conta de armazenamento de uma eliminação errónea, pode configurar um bloqueio utilizando o Gestor de Recursos Azure. Consulte o artigo Do Gestor de Recursos Azure Para [Evitar Alterações Inesperadas](../../azure-resource-manager/management/lock-resources.md) para saber mais.

Os seguintes detalhes da tabela esperam comportamento quando o soft delete é ligado:

| Operação REST API | Tipo de recurso | Descrição | Mudança de comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Elimina a conta de armazenamento, incluindo todos os recipientes e bolhas que contém.                           | Sem mudanças. Os recipientes e bolhas na conta eliminada não são recuperáveis. |
| [Eliminar Contentor](/rest/api/storageservices/delete-container) | Contentor | Elimina o recipiente, incluindo todas as bolhas que contém. | Sem mudanças. As bolhas no recipiente apagado não são recuperáveis. |
| [Coloque Blob](/rest/api/storageservices/put-blob) | Bloco, apêndice e bolhas de página | Cria uma nova bolha ou substitui uma bolha existente dentro de um recipiente | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha eliminada anteriormente macia se e apenas se for substituída por uma bolha do mesmo tipo (Bloco, apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Eliminar Blob](/rest/api/storageservices/delete-blob) | Bloco, apêndice e bolhas de página | Marca uma bolha ou uma imagem de bolha para eliminação. A bolha ou instantâneo é mais tarde apagado durante a recolha de lixo | Se for utilizado para apagar uma imagem de bolha, esta imagem é marcada como suave. Se for utilizado para apagar uma bolha, esta bolha é marcada como suave. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Bloco, apêndice e bolhas de página | Copia uma bolha de origem para uma bolha de destino na mesma conta de armazenamento ou noutra conta de armazenamento. | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha eliminada anteriormente macia se e apenas se for substituída por uma bolha do mesmo tipo (Bloco, apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Bloquear](/rest/api/storageservices/put-block) | Blobs de blocos | Cria um novo bloco para ser cometido como parte de uma bolha de bloco. | Se usado para comprometer um bloco a uma bolha ativa, não há mudança. Se usado para comprometer um bloco a uma bolha que é suave mente apagada, uma nova bolha é criada e um instantâneo é automaticamente gerado para capturar o estado da bolha suave apagada. |
| [Lista de blocos](/rest/api/storageservices/put-block-list) | Blobs de blocos | Comete uma bolha especificando o conjunto de IDs de bloco que compõem a bolha do bloco. | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha apagada anteriormente macia se e apenas se for uma bolha de bloco. Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Página de colocação](/rest/api/storageservices/put-page) | Blobs de páginas | Escreve uma série de páginas para uma página blob. | Sem mudanças. Os dados blob da página que são substituídos ou limpos usando esta operação não são guardados e não são recuperáveis. |
| [Bloco de apêndice](/rest/api/storageservices/append-block) | Blobs de acréscimo | Escreve um bloco de dados até ao fim de um Append Blob | Sem mudanças. |
| [Definir propriedades blob](/rest/api/storageservices/set-blob-properties) | Bloco, apêndice e bolhas de página | Define valores para propriedades do sistema definidas para uma bolha. | Sem mudanças. Propriedades de bolhas por escrito não são recuperáveis. |
| [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata) | Bloco, apêndice e bolhas de página | Define os metadados definidos pelo utilizador para a bolha especificada como um ou mais pares de valor de nome. | Sem mudanças. Os metadados de blob por escrito não são recuperáveis. |

É importante notar que ligar para **colocar página** para substituir ou limpar gamas de uma bolha de página não gerará automaticamente instantâneos. Os discos de máquinas virtuais são apoiados por bolhas de página e usam a **Página de Colocação** para escrever dados.

### <a name="recovery"></a>Recuperação

Chamar a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) numa bolha de base suave e apagada restaura-a e todas as imagens suaves e macias associadas como ativas. Chamar a operação **Undelete Blob** numa bolha de base ativa restaura todas as imagens suaves associadas como ativas. Quando as imagens são restauradas como ativas, parecem instantâneos gerados pelo utilizador; não sobreporem a bolha base.

Para restaurar uma bolha a um instantâneo suave específico, pode ligar para **a Blob Undelete** na bolha base. Depois, pode copiar o instantâneo sobre a bolha agora ativa. Também pode copiar o instantâneo para uma nova bolha.

![](media/soft-delete-overview/storage-blob-soft-delete-recover.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Aqui, **Undelete Blob** é chamado na blob B, restaurando assim a bolha base, B1, e todos os instantâneos associados, aqui apenas B0, como ativo. No segundo passo, B0 é copiado sobre a bolha base. Esta operação de cópia gera uma imagem suave apagada de B1.*

Para ver bolhas suaves apagadas e imagens de bolhas, pode optar por incluir dados eliminados nas **Blobs lista**. Pode optar por ver apenas bolhas de base apagadas suaves ou incluir também imagens de bolhas apagadas suaves. Para todos os dados eliminados suaves, pode visualizar o momento em que os dados foram eliminados, bem como o número de dias antes de os dados serem permanentemente expirados.

### <a name="example"></a>Exemplo

Segue-se a saída da consola de um script .NET que carrega, substitui, snapshots, elimina e restaura uma bolha chamada *HelloWorld* quando a eliminação suave é ligada:

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

Consulte a secção [de passos Seguintes](#next-steps) para obter um ponteiro para a aplicação que produziu esta saída.

## <a name="pricing-and-billing"></a>Preços e faturação

Todos os dados eliminados suaves são faturados ao mesmo ritmo que os dados ativos. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção configurado. Para um mergulho mais profundo em instantâneos e como acumulam encargos, por favor, veja como as [imagens acumulam encargos](storage-blob-snapshots.md).

Não será cobrado para as transações relacionadas com a geração automática de instantâneos. Será cobrado para transações **Blob Não Apagadas** à taxa de operações de escrita.

Para mais detalhes sobre os preços do Armazenamento De Blob Azure em geral, consulte a página de preços de [armazenamento de Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando inicialmente liga o soft delete, a Microsoft recomenda utilizar um curto período de retenção para entender melhor como a funcionalidade irá afetar a sua conta.

Permitir a eliminação suave de dados frequentemente sobreescritos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência na listagem de bolhas. Pode atenuar este custo adicional e latência armazenando os dados frequentemente sobreescritos numa conta de armazenamento separada onde a eliminação suave é desativada.

## <a name="faq"></a>FAQ

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Posso usar a API de nível de set blob para nidificar bolhas com imagens apagadas macias?

Sim. As imagens suaves apagadas permanecerão no nível original, mas a bolha base passará para o novo nível.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>As contas de armazenamento premium têm um limite de instantâneo por bolha de 100. As imagens apagadas suaves contam para este limite?

Não, as imagens apagadas suaves não contam para este limite.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Se eu apagar uma conta inteira ou recipiente com eliminação suave ligada, todas as bolhas associadas serão guardadas?

Não, se apagar uma conta ou recipiente inteiro, todas as bolhas associadas serão permanentemente eliminadas. Para obter mais informações sobre a proteção de uma conta de armazenamento de eliminações acidentais, consulte [os Recursos de Bloqueio para Evitar Alterações Inesperadas](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Posso ver métricas de capacidade para dados eliminados?

Os dados eliminados suaves são incluídos como parte da sua capacidade total de conta de armazenamento. Para obter mais informações sobre a capacidade de rastreio e monitorização da capacidade de armazenamento, consulte [Storage Analytics](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Posso ler e copiar fotos apagadas suaves da minha bolha?  

Sim, mas primeiro deves ligar para o Undelete on the Blob.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>A eliminação suave está disponível para discos de máquinas virtuais?  

O soft delete está disponível para discos não geridos premium e standard, que são bolhas de página sob as capas. A eliminação suave só o ajudará a recuperar os dados eliminados pelas operações **delete Blob**, **Put Blob,** **Put Block List**e **Copy Blob.** Os dados sobressados por uma chamada para **Put Page** não são recuperáveis.

Uma máquina virtual Azure escreve para um disco não gerido usando chamadas para **Put Page**, por isso usar soft delete para desfazer escreve para um disco não gerido de um VM Azure não é um cenário suportado.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Preciso de alterar as minhas aplicações existentes para usar a eliminação suave?

É possível tirar partido da eliminação suave independentemente da versão API que está a utilizar. No entanto, para enumerar e recuperar bolhas e instantâneos suaves apagados, terá de utilizar a versão 2017-07-29 da API REST [De armazenamento Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou maior. A Microsoft recomenda sempre a utilização da versão mais recente da API de Armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

- [Ativar a eliminação suave para bolhas](soft-delete-enable.md)
- [Versão blob (pré-visualização)](versioning-overview.md)
