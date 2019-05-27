---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914280"
---
## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

Certifique-se de que ainda está no diretório de raiz do projeto de exemplo. Crie um arquivo ZIP de tudo no seu projeto. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Mais tarde, carregue este ficheiro ZIP para o Azure e implemente-o no Serviço de Aplicações.