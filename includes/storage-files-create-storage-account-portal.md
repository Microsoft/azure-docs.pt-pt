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
ms.openlocfilehash: d4054760c77a7a70b7ed84a9f95b88a3bcf2bda3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76021248"
---
Uma conta de armazenamento é um conjunto partilhado de armazenamento no qual pode implementar uma partilha de ficheiros do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

Para criar uma conta de armazenamento:

1. No menu esquerdo, **+** selecione para criar um recurso.
2. Na caixa de pesquisa, introduza **conta de armazenamento**, selecione **Conta de armazenamento - blob, ficheiro, tabela, fila** e, em seguida, selecione **Criar**.
    ![Uma captura de ecrã do aspeto que a entrada da conta de armazenamento deve ter na caixa de diálogo de pesquisa de recursos](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Em **Nome**, introduza *mystorageacct* seguido de alguns números aleatórios até ver uma marca de verificação verde a indicar que é um nome exclusivo. Um nome de conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Tome nota do nome da conta de armazenamento. Irá utilizá-lo mais tarde. 
4. No **modelo de implantação,** deixe o valor padrão do Gestor de **Recursos**. Para saber mais sobre as diferenças entre o Azure Resource Manager e o modelo de implementação clássica, veja [Compreender os modelos de implementação e o estado dos seus recursos](../articles/azure-resource-manager/management/deployment-models.md).
5. Em **Tipo de conta**, selecione **StorageV2**. Para saber mais sobre os diferentes tipos de contas de armazenamento, veja [Compreender as contas de armazenamento](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. Em **Desempenho**, mantenha o valor predefinido de **Armazenamento Standard**. Atualmente, os Ficheiros do Azure só suportam o armazenamento standard; mesmo se selecionar o Armazenamento Premium do Azure, a partilha de ficheiros é armazenada no armazenamento standard.
7. Em **Replicação**, selecione **Armazenamento localmente redundante (LRS)**. 
8. Em **Transferência segura obrigatória**, recomendamos que selecione sempre **Ativada**. Para saber mais sobre esta opção, veja [Compreender a encriptação em trânsito](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. Em **Subscrição**, selecione a subscrição utilizada para criar a conta de armazenamento. Se tiver apenas uma subscrição, deve ser a predefinição.
10. Em **Grupo de recursos**, selecione **Criar novo**. Para o nome, introduza *myResourceGroup*.
11. Em **Localização**, selecione **E.U.A. Leste**.
12. Em **Redes virtuais**, deixe a opção predefinida como **Desativado**. 
13. Para que seja mais fácil encontrar a conta de armazenamento, selecione **Afixar ao dashboard**.
14. Quanto terminar, selecione **Criar** para iniciar a implementação.