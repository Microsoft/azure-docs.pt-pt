---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
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