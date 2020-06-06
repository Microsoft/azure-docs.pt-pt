---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: f89383c1b7d0ccce82f35d91acbe1ff8c902db29
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84448920"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Criar e ativar um ambiente virtual

Numa pasta adequada, executar os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv` . Certifique-se de que utiliza Python 3.8, 3.7 ou 3.6, que são suportados por Funções Azure.

# <a name="bash"></a>[festa](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se python não instalou o pacote venv na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Você dirige todos os comandos subsequentes neste ambiente virtual ativado.   
::: zone-end
