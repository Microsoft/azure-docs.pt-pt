---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201949"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Com exceção dos gatilhos HTTP e temporizador, as encadernações são implementadas como pacotes de extensão. Execute o seguinte comando de pacote de [dotnet](/dotnet/core/tools/dotnet-add-package) na janela terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.  
::: zone-end  