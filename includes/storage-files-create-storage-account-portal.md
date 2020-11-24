---
title: storage-files-create-storage-account-portal
description: Crie uma conta de armazenamento para Ficheiros do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: ad9f3a115ed226b492a08dae29db61029d9098eb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555853"
---
Uma conta de armazenamento é um conjunto partilhado de armazenamento no qual pode implementar uma partilha de ficheiros do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

Para criar uma conta de armazenamento:

1. No menu esquerdo, selecione **+** para criar um recurso.
1. Na caixa de pesquisa, introduza **conta de armazenamento**, selecione **Conta de armazenamento - blob, ficheiro, tabela, fila** e, em seguida, selecione **Criar**.
    ![Uma captura de ecrã do aspeto que a entrada da conta de armazenamento deve ter na caixa de diálogo de pesquisa de recursos](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. Em **Nome**, introduza *mystorageacct* seguido de alguns números aleatórios até ver uma marca de verificação verde a indicar que é um nome exclusivo. Um nome de conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Tome nota do nome da conta de armazenamento. Irá utilizá-lo mais tarde. 
1. No **modelo de Implementação,** deixe o valor predefinido do Gestor de **Recursos**. Para saber mais sobre as diferenças entre o Azure Resource Manager e o modelo de implementação clássica, veja [Compreender os modelos de implementação e o estado dos seus recursos](../articles/azure-resource-manager/management/deployment-models.md).
1. No **Desempenho**, mantenha o valor padrão da **Norma**.
    
    > [!NOTE]
    > Este quickstart cria uma partilha de ficheiros padrão mas, se quiser utilizar ações de ficheiros premium, selecione **Premium** em vez disso.

1. Em **Tipo de conta**, selecione **StorageV2**. Para saber mais sobre os diferentes tipos de contas de armazenamento, veja [Compreender as contas de armazenamento](../articles/storage/common/storage-account-overview.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).

    > [!NOTE]
    > Este quickstart cria uma conta v2 de uso geral. Se quiser utilizar ações de ficheiros premium, selecione **FileStorage** em vez disso.

1. Em **Replicação**, selecione **Armazenamento localmente redundante (LRS)**. 
1. Em **Transferência segura obrigatória**, recomendamos que selecione sempre **Ativada**. Para saber mais sobre esta opção, veja [Compreender a encriptação em trânsito](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
1. Em **Subscrição**, selecione a subscrição utilizada para criar a conta de armazenamento. Se tiver apenas uma subscrição, deve ser a predefinição.
1. Em **Grupo de recursos**, selecione **Criar novo**. Para o nome, introduza *myResourceGroup*.
1. Em **Localização**, selecione **E.U.A. Leste**.
1. Em **Redes virtuais**, deixe a opção predefinida como **Desativado**. 
1. Para que seja mais fácil encontrar a conta de armazenamento, selecione **Afixar ao dashboard**.
1. Quanto terminar, selecione **Criar** para iniciar a implementação.