---
title: Eliminação recuperável para blobs
titleSuffix: Azure Storage
description: A eliminação suave para blobs protege os seus dados para que possa recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados por uma aplicação ou por outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 29d9dd7757319e59fc12b42d89c2ce16dec71b8b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551072"
---
# <a name="soft-delete-for-blobs"></a>Eliminação recuperável para blobs

A eliminação macia de blob protege uma bolha, instantâneo ou versão individuais de eliminações ou substituições acidentais mantendo os dados eliminados no sistema por um período de tempo especificado. Durante o período de retenção, pode restaurar um objeto apagado suave ao seu estado no momento em que foi eliminado. Após o período de retenção ter expirado, o objeto é permanentemente eliminado.

## <a name="recommended-data-protection-configuration"></a>Configuração recomendada de proteção de dados

A eliminação suave blob faz parte de uma estratégia abrangente de proteção de dados para dados blob. Para uma proteção ótima para os seus dados blob, a Microsoft recomenda que permita todas as seguintes funcionalidades de proteção de dados:

- Recipiente apagar suavemente, para restaurar um recipiente que foi eliminado. Para aprender a permitir a eliminação suave do recipiente, consulte [Ativar e gerir a eliminação suave para recipientes](soft-delete-container-enable.md).
- Versão blob, para manter automaticamente as versões anteriores de uma bolha. Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificada ou eliminada. Para aprender a permitir a versão blob, consulte [Ativar e gerir a versão blob](versioning-enable.md).
- Blob soft delete, para restaurar uma bolha, instantâneo ou versão que foi eliminada. Para aprender a permitir a eliminação suave do blob, consulte [Ativar e gerir a eliminação suave para bolhas](soft-delete-blob-enable.md).

Para saber mais sobre as recomendações da Microsoft para a proteção de dados, consulte a [visão geral da proteção de dados](data-protection-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-soft-delete-works"></a>Como blob soft delete funciona

Quando ativar a eliminação suave da bolha para uma conta de armazenamento, especifique um período de retenção para objetos apagados entre 1 e 365 dias. O período de retenção indica quanto tempo os dados permanecem disponíveis após a sua exposição ou substituído. O relógio começa no período de retenção assim que um objeto é apagado ou substituído.

Enquanto o período de retenção estiver ativo, pode restaurar uma bolha apagada, juntamente com as suas imagens, ou uma versão eliminada, chamando a operação [Undelete Blob.](/rest/api/storageservices/undelete-blob) O seguinte diagrama mostra como um objeto eliminado pode ser restaurado quando a eliminação suave da bolha está ativada:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagrama mostrando como uma bolha desagravada pode ser restaurada":::

Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado aplica-se apenas aos dados que foram eliminados após a alteração do período de retenção. Quaisquer dados que foram eliminados antes da alteração do período de retenção estão sujeitos ao período de retenção que estava em vigor quando foi eliminado.

Tentar eliminar um objeto apagado não afeta o seu tempo de validade.

Se desativar a mancha de exclusão suave, pode continuar a aceder e a recuperar objetos apagados suavemente na sua conta de armazenamento até que o período de retenção de eliminação suave tenha decorrido.

A versão blob está disponível para as contas de armazenamento v2, block blob e Blob. As contas de armazenamento com um espaço hierárquico habilitado para uso com Azure Data Lake Storage Gen2 não são atualmente suportadas.

Versão 2017-07-29 e superior do suporte Azure Storage REST API soft delete.

> [!IMPORTANT]
> Pode utilizar o blob soft delete apenas para restaurar uma bolha individual, instantâneo ou versão. Para restaurar um recipiente e o seu conteúdo, a eliminação suave do contentor também deve ser ativada para a conta de armazenamento. A Microsoft recomenda que se permita eliminar suavemente o recipiente e a versão blob juntamente com a eliminação macia de bolhas para garantir uma proteção completa para os dados do blob. Para obter mais informações, consulte [a visão geral da proteção de dados.](data-protection-overview.md)
>
> A eliminação macia da bolha não protege contra a eliminação de uma conta de armazenamento. Para proteger uma conta de armazenamento da supressão, configuure um bloqueio no recurso da conta de armazenamento. Para obter mais informações sobre o bloqueio de uma conta de armazenamento, consulte [aplicar um bloqueio do Gestor de Recursos Azure numa conta de armazenamento.](../common/lock-account-resource.md)

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Como as supressões são manuseadas quando a eliminação suave é ativada

Quando a bolha de exclusão suave estiver ativada, eliminando uma mancha que blob como apagada suavemente. Nenhum instantâneo é criado. Quando o período de retenção expirar, a bolha apagada é permanentemente eliminada.

Se uma bolha tiver instantâneos, a bolha não pode ser apagada a menos que as imagens sejam também eliminadas. Quando apaga uma bolha e as suas fotografias, tanto a bolha como as imagens são marcadas como apagadas suavemente. Não são criados novos instantâneos.

Também pode eliminar um ou mais instantâneos ativos sem eliminar a bolha de base. Neste caso, o instantâneo é apagado suavemente.

Os objetos apagados são invisíveis a menos que sejam explicitamente exibidos ou listados. Para obter mais informações sobre como listar objetos com emissões suaves, consulte [Gerir e restaurar as bolhas apagadas suaves](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Como os overwrites são manuseados quando a eliminação suave é ativada

Chamar uma operação como [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list)ou Copy [Blob](/rest/api/storageservices/copy-blob) substitui os dados numa bolha. Quando a eliminação suave da bolha está ativada, a sobreposição de uma bolha cria automaticamente uma imagem do estado da bolha apagada antes da operação de escrita. Quando o período de retenção expira, o instantâneo apagado é permanentemente eliminado.

As imagens apagadas são invisíveis a menos que os objetos apagados sejam explicitamente exibidos ou listados. Para obter mais informações sobre como listar objetos com emissões suaves, consulte [Gerir e restaurar as bolhas apagadas suaves](soft-delete-blob-manage.md).

Para proteger uma operação de cópia, a eliminação macia blob deve ser ativada para a conta de armazenamento de destino.

A blob soft delete não protege contra operações para escrever metadados ou propriedades blob. Nenhum instantâneo apagado é criado quando os metadados ou propriedades de uma bolha são atualizados.

A eliminação macia da bolha não permite uma proteção excessiva para bolhas no nível de arquivo. Se uma bolha no nível de arquivo for substituída com uma nova bolha em qualquer nível, então a bolha substituída é permanentemente eliminada.

Para contas de armazenamento premium, as imagens de acabamentos suaves não contam para o limite por blob de 100 instantâneos.

### <a name="restoring-soft-deleted-objects"></a>Restaurar objetos apagados

Pode restaurar as bolhas apagadas suaves, chamando a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) dentro do período de retenção. A **operação Undelete Blob** restaura uma bolha e quaisquer instantâneos apagados e suaves associados a ela. Quaisquer instantâneos que foram eliminados durante o período de retenção são restaurados.

Chamar **Undelete Blob** numa bolha que não seja apagada suave irá restaurar quaisquer instantâneos apagados suaves que estejam associados à bolha. Se a bolha não tiver instantâneos e não for apagada suavemente, então chamar **Undelete Blob** não tem efeito.

Para promover uma imagem suave apagada para a bolha de base, ligue primeiro para **Undelete Blob** na bolha de base para restaurar a bolha e as suas fotos. Em seguida, copie a imagem desejada sobre a bolha de base. Também pode copiar a imagem para uma nova bolha.

Os dados numa bolha ou instantâneo apagados não podem ser lidos até que o objeto tenha sido restaurado.

Para obter mais informações sobre como restaurar objetos apagados suavemente, consulte [Gerir e restaurar as bolhas apagadas suaves](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Blob soft delete e versão

Se a versão blob e a eliminação suave blob estiverem ambas ativadas para uma conta de armazenamento, então a sobreposição de uma bolha cria automaticamente uma nova versão. A nova versão não é eliminada suavemente e não é removida quando o período de retenção de eliminação suave expirar. Não são criados instantâneos apagados. Quando elimina uma bolha, a versão atual do blob torna-se uma versão anterior e a versão atual é eliminada. Nenhuma nova versão é criada e não são criados instantâneos de apagação suave.

Permitir a eliminação suave e a versão em conjunto protege as versões blob da eliminação. Quando a eliminação suave é ativada, a eliminação de uma versão cria uma versão de eliminação suave. Pode utilizar a operação **Undelete Blob** para restaurar uma versão desafortuçada, desde que exista uma versão atual da bolha. Se não houver uma versão atual, então deve copiar uma versão anterior para a versão atual antes de ligar para a operação **Undelete Blob.**

> [!NOTE]
> Chamar a operação **Undelete Blob** numa bolha apagada quando a versão é ativada restaura quaisquer versões ou instantâneos apagados suaves, mas não restaura a bolha de base. Para restaurar a bolha de base, promova uma versão anterior copiando-a para a bolha de base.

A Microsoft recomenda permitir a eliminação suave de versão e bolha para as suas contas de armazenamento para uma ótima proteção de dados. Para obter mais informações sobre a utilização da versão blob e eliminar suavemente, consulte [a versão Blob e a eliminação suave](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Blob soft delete proteção por operação

A tabela a seguir descreve o comportamento esperado para eliminar e escrever operações quando a blob soft delete está ativada, com ou sem versões blob:

| Operações REST API | Excluir suavemente ativado | Eliminação suave e versão ativada |
|--|--|--|
| [Eliminar Conta de Armazenamento](/rest/api/storagerp/storageaccounts/delete) | Nenhuma alteração. Os recipientes e bolhas na conta eliminada não são recuperáveis. | Nenhuma alteração. Os recipientes e bolhas na conta eliminada não são recuperáveis. |
| [Eliminar Contentor](/rest/api/storageservices/delete-container) | Nenhuma alteração. As bolhas no recipiente apagado não são recuperáveis. | Nenhuma alteração. As bolhas no recipiente apagado não são recuperáveis. |
| [Eliminar Blob](/rest/api/storageservices/delete-blob) | Se for utilizado para apagar uma bolha, esta bolha é marcada como suave apagada. <br /><br /> Se for utilizado para apagar uma imagem de bolha, o instantâneo é marcado como suave apagado. | Se for utilizada para eliminar uma bolha, a versão atual torna-se uma versão anterior e a versão atual é eliminada. Nenhuma nova versão é criada e não são criados instantâneos de apagação suave.<br /><br /> Se for utilizada para eliminar uma versão blob, a versão é marcada como suavemente eliminada. |
| [Undelete Blob](/rest/api/storageservices/delete-blob) | Restaura uma bolha e quaisquer instantâneos que foram eliminados dentro do período de retenção. | Restaura uma bolha e quaisquer versões que foram eliminadas dentro do período de retenção. |
| [Colocar o Blob](/rest/api/storageservices/put-blob)<br />[Colocar lista de blocos](/rest/api/storageservices/put-block-list)<br />[Copiar Blob](/rest/api/storageservices/copy-blob)<br />[Bolha de cópia da URL](/rest/api/storageservices/copy-blob) | Se for chamada numa bolha ativa, então uma imagem do estado da bolha antes da operação é gerada automaticamente. <br /><br /> Se for chamada numa bolha depagou-se suavemente, então uma imagem do estado anterior da bolha só é gerada se for substituída por uma bolha do mesmo tipo. Se a bolha for de um tipo diferente, todos os dados eliminados suaves existentes são permanentemente eliminados. | Uma nova versão que captura o estado da bolha antes da operação é gerada automaticamente. |
| [Colocar Bloco](/rest/api/storageservices/put-block) | Se usado para comprometer um bloco a uma bolha ativa, não há nenhuma mudança.<br /><br />Se usado para comprometer um bloco a uma bolha que é apagada suavemente, uma nova bolha é criada e um instantâneo é gerado automaticamente para capturar o estado da bolha de apagamento suave. | Nenhuma alteração. |
| [Colocar página](/rest/api/storageservices/put-page)<br />[Colocar página de URL](/rest/api/storageservices/put-page-from-url) | Nenhuma alteração. Os dados do blob da página que são substituídos ou limpos utilizando esta operação não são guardados e não são recuperáveis. | Nenhuma alteração. Os dados do blob da página que são substituídos ou limpos utilizando esta operação não são guardados e não são recuperáveis. |
| [Bloco de Apêndice](/rest/api/storageservices/append-block)<br />[Bloco de Apêndice de URL](/rest/api/storageservices/append-block-from-url) | Nenhuma alteração. | Nenhuma alteração. |
| [Definir propriedades blob](/rest/api/storageservices/set-blob-properties) | Nenhuma alteração. As propriedades de bolhas sobreescritas não são recuperáveis. | Nenhuma alteração. As propriedades de bolhas sobreescritas não são recuperáveis. |
| [Definir metadados blob](/rest/api/storageservices/set-blob-metadata) | Nenhuma alteração. Os metadados blob sobrescritos não são recuperáveis. | Uma nova versão que captura o estado da bolha antes da operação é gerada automaticamente. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Definir Camada de Blob) | A bolha da base é transferida para o novo nível. Quaisquer instantâneos ativos ou apagados permanecem no nível original. Não é criado nenhum instantâneo apagado. | A bolha da base é transferida para o novo nível. Quaisquer versões ativas ou desausidas permanecem no nível original. Nenhuma nova versão é criada. |

## <a name="pricing-and-billing"></a>Preços e faturação

Todos os dados eliminados suaves são faturados à mesma taxa que os dados ativos. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção.

Quando ativa a eliminação suave, a Microsoft recomenda a utilização de um curto período de retenção para entender melhor como a funcionalidade irá afetar a sua conta. O período mínimo de retenção recomendado é de sete dias.

Permitir a eliminação suave de dados frequentemente substituídos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência ao enumerar bolhas. Pode atenuar este custo e latência adicionais armazenando os dados frequentemente sobreescritos numa conta de armazenamento separada onde a eliminação suave é desativada.

Não é faturado para transações relacionadas com a geração automática de instantâneos ou versões quando uma bolha é substituída ou eliminada. Está a ser cobrado para chamadas para a operação **Undelete Blob** à taxa de transação para operações de escrita.

Para obter mais informações sobre os preços para o Blob Storage, consulte a página [de preços do Blob Storage.](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Blob soft delete e discos de máquinas virtuais  

Blob soft delete está disponível tanto para discos premium como padrão não geridos, que são bolhas de página sob as capas. A eliminação suave pode ajudá-lo a recuperar os dados eliminados ou substituídos pela **Blob Delete,** **Put Blob,** **Put Block List** e Copy **Blob** apenas.

Os dados que são substituídos por uma chamada para **Put Page** não são recuperáveis. Uma máquina virtual Azure escreve para um disco não gerido usando chamadas para **Put Page**, por isso usar exclusão suave para desfazer escreve para um disco não gerido a partir de um Azure VM não é um cenário suportado.

## <a name="next-steps"></a>Passos seguintes

- [Ativar a eliminação recuperável para blobs](./soft-delete-blob-enable.md)
- [Gerir e restaurar bolhas apagadas suaves](soft-delete-blob-manage.md)
- [Versão blob](versioning-overview.md)