---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050390"
---
Todos os Blobs no armazenamento do Azure têm de residir num contentor. O contentor faz parte do nome do blob. Por exemplo, `mycontainer` é o nome do contentor nestes URIs de exemplo de blob:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

Um nome de contentor tem de ser um nome DNS válido, cumprindo as seguintes regras de nomenclatura:

1. Os nomes dos contentores têm de começar com uma letra ou um número e só podem conter letras, números e o caráter de travessão (-).
2. Cada caráter de travessão (-) tem de ser imediatamente precedido e seguido por uma letra ou um número; os traços consecutivos não são permitidos em nomes de contentor.
3. Tenha em atenção que o nome do contentor tem de estar em minúsculas.
4. Os nomes de contentor devem ter entre 3 e 63 carateres.

> [!IMPORTANT]
> Tenha em atenção que o nome de um contentor deve estar sempre em minúsculas. Se incluir uma letra em maiúsculas num nome de contentor ou violar as regras dos nomes de contentores, poderá receber um erro 400 (Pedido Incorreto). 
> 
> 

