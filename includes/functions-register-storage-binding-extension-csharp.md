---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201949"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Com exceção dos gatilhos HTTP e timer, as ligações são implementadas como pacotes de extensão. Executar o seguinte [comando de pacote de dotnet](/dotnet/core/tools/dotnet-add-package) na janela Terminal para adicionar o pacote de extensão de Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.  
::: zone-end  