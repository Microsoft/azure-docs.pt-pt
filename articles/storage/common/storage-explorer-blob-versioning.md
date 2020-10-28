---
title: Guia de versão blob do Azure Storage Explorer Microsoft Docs
description: Orientação de versão blob para Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783832"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Guia de versão blob do Azure Storage Explorer

O Microsoft Azure Storage Explorer proporciona fácil acesso e gestão de versões blob. Este guia irá ajudá-lo a entender como a versão blob funciona no Storage Explorer. Antes de continuar, recomenda-se que leia mais sobre [a versão blob](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologia

Esta secção fornece algumas definições para ajudar a entender o seu uso neste artigo.

- Excluir suave: Uma funcionalidade alternativa de proteção automática de dados. Pode saber mais sobre a eliminação suave [aqui.](../blobs/soft-delete-blob-overview.md)
- Blob ativo: Uma versão blob ou blob é criada em estado ativo. Só pode operar em bolhas ou versões blob em estado ativo.
- Bolha apagada suave: Uma versão blob ou blob marcada como soft-deleted. As bolhas apagadas e macias são mantidas apenas durante o seu período de retenção.
- Versão blob: Uma bolha criada com a versão blob ativada. Cada versão blob está associada a um ID de versão.
- Versão atual: Uma versão blob marcada como a versão atual.
- Versão anterior: Uma versão blob que não é a versão atual.
- Blob não versão: Uma bolha criada com a versão blob desativada. Uma bolha não versão não tem um ID de versão.

## <a name="view-blob-versions"></a>Ver versões blob

O Storage Explorer suporta quatro vistas diferentes para visualizar bolhas.

| Vista | Bolhas não-versão ativas | Bolhas de versão não eliminadas com soft-apagado | Versões blob |
| ---- | :----------: | :-----------: | :------------------: |
| Bolhas ativas | Sim | Não | Versão atual apenas |
| Bolhas ativas e bolhas comvidade | Sim | Sim | Versão atual apenas |
| Bolhas e bolhas ativas sem versão atual | Sim | Não | Versão atual ou versão ativa mais recente |
| Todas as bolhas e bolhas sem versão atual | Sim | Sim | Versão atual ou versão mais recente |

### <a name="active-blobs"></a>Bolhas ativas

Nesta vista, o Storage Explorer exibe:

- Bolhas não-versão ativas
- Versões atuais

### <a name="active-blobs-and-soft-deleted-blobs"></a>Bolhas ativas e bolhas comvidade

Nesta vista, o Storage Explorer exibe:

- Bolhas não-versão ativas
- Bolhas de versão não eliminadas com soft-apagado
- Versões atuais.

### <a name="active-blobs-and-blobs-without-current-version"></a>Bolhas e bolhas ativas sem versão atual

Nesta vista, o Storage Explorer exibe:

- Bolhas não-versão ativas
- Versões atuais
- Versões anteriores mais recentes. 

Para blobs que não têm uma versão atual mas têm uma versão anterior ativa, o Storage Explorer apresenta a sua versão anterior mais recente como uma representação dessa bolha.

### <a name="all-blobs-and-blobs-without-current-version"></a>Todas as bolhas e bolhas sem versão atual

Nesta vista, o Storage Explorer exibe:

- Bolhas não-versão ativas
- Bolhas de versão não eliminadas com soft-apagado
- Versões atuais
- Versões anteriores mais recentes. 

Para blobs que não têm uma versão atual, o Storage Explorer apresenta a sua versão anterior mais recente como uma representação dessa bolha.

> [!Note]
> Devido à limitação do serviço, o Storage Explorer precisa de algum processamento adicional para obter uma visão hierárquica dos seus diretórios virtuais ao listar versões blob. Levará mais tempo a listar bolhas nas seguintes vistas:
> 
> - Bolhas e bolhas ativas sem versão atual
> - Todas as bolhas e bolhas sem versão atual

## <a name="manage-blob-versions"></a>Gerir versões blob

### <a name="view-versions-of-a-blob"></a>Versões de uma bolha

O Storage Explorer fornece um comando **Manage Versions** para visualizar todas as versões de uma bolha. Para visualizar as versões de uma bolha, selecione a bolha para a qual pretende visualizar versões e selecione **&rarr; Versões De Gestão** de História a partir da barra de ferramentas ou do menu de contexto.

### <a name="download-blob-versions"></a>Baixar versões blob

Para descarregar uma ou mais versões blob, selecione as versões blob que pretende descarregar e selecione **Descarregue** na barra de ferramentas ou no menu de contexto.

Se estiver a descarregar várias versões de uma bolha, os ficheiros descarregados terão os seus IDs de versão no início dos seus nomes de ficheiros.

### <a name="delete-blob-versions"></a>Eliminar versões blob

Para eliminar uma ou mais versões blob, selecione as versões blob que pretende eliminar e selecione **Eliminar** da barra de ferramentas ou do menu de contexto.

As versões blob estão sujeitas à sua política de eliminação suave. Se a eliminação suave estiver ativada, as versões blob serão apagadas suavemente. Um caso especial é apagar uma versão atual. A eliminação de uma versão atual fará com que se torne automaticamente uma versão anterior ativa.

### <a name="promote-blob-version"></a>Promover a versão blob

Pode restaurar o conteúdo de uma bolha promovendo uma versão anterior para se tornar a versão atual. Selecione a versão blob que pretende promover e selecione **Promote Version** a partir da barra de ferramentas ou do menu de contexto.

As bolhas não-versão serão substituídas pela versão blob promovida. Certifique-se de que já não necessita desses dados ou faça o próprio back up dos dados antes de confirmar a operação. As versões atuais tornam-se automaticamente versões anteriores, pelo que o Storage Explorer não solicitará a confirmação.

### <a name="undelete-blob-version"></a>Versão blob undelete

Versões blob não podem ser despromufactos individualmente. Devem ser indeletados de uma vez. Para desembaraçar todas as versões blob de uma bolha, selecione qualquer uma das versões da bolha e selecione **Undelete Selected** a partir da barra de ferramentas ou do menu de contexto.

### <a name="change-access-tier-of-blob-versions"></a>Alterar o nível de acesso das versões blob

Cada versão blob tem o seu próprio nível de acesso. Para alterar o nível de acesso das versões blob, selecione as versões blob que pretende alterar o nível de acesso e selecione **Change Access Tier...** a partir do menu de contexto.

## <a name="see-also"></a>Consulte também

* [Versão blob](../blobs/versioning-overview.md)
* [Eliminação recuperável para blobs](../blobs/soft-delete-blob-overview.md)