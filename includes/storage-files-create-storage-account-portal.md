---
title: storage-files-create-storage-account-portal
description: Crie uma conta de armazenamento para Ficheiros do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717874"
---
Uma conta de armazenamento é um conjunto partilhado de armazenamento no qual pode implementar uma partilha de ficheiros do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

Para criar uma conta de armazenamento:

1. No menu esquerdo, selecione **+** para criar um recurso.
1. Selecione **a conta de Armazenamento** para criar uma conta de armazenamento.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Uma imagem da opção da conta de armazenamento na criação de uma lâmina de recurso." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. Em **Nome**, introduza *mystorageacct* seguido de alguns números aleatórios até ver uma marca de verificação verde a indicar que é um nome exclusivo. Um nome de conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Tome nota do nome da conta de armazenamento. Irá utilizá-lo mais tarde. 
1. No **Desempenho**, mantenha o valor padrão da **Norma**.
1. Em **Replicação**, selecione **Armazenamento localmente redundante (LRS)**.
1. Em **Subscrição**, selecione a subscrição utilizada para criar a conta de armazenamento. Se tiver apenas uma subscrição, deve ser a predefinição.
1. Em **Grupo de recursos**, selecione **Criar novo**. Para o nome, introduza *myResourceGroup*.
1. Em **Localização**, selecione **E.U.A. Leste**.
1. Quanto terminar, selecione **Criar** para iniciar a implementação.