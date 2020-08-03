---
author: baanders
description: incluir ficheiro para requisito de função na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407477"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: Requisitos de permissão

Para poder completar todos os passos deste artigo, tem de ser classificado como Proprietário na sua assinatura Azure. 

Pode verificar o seu nível de permissão executando este comando em Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se você é proprietário, o `roleDefinitionName` valor na saída é *Proprietário:*

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Janela cloud Shell mostrando saída do comando da lista de atribuições de funções az":::

Se descobrir que o valor é *Contribuinte* ou algo diferente do *Proprietário,* pode proceder de uma das seguintes formas:
* Contacte o proprietário da subscrição e solicite ao Proprietário que complete os passos deste artigo em seu nome
* Contacte o Proprietário da sua subscrição ou alguém com função de Administração de Acesso ao Utilizador na subscrição e solicite que o eleve ao Proprietário na subscrição para que tenha as permissões para prosseguir. Se isso é apropriado depende da sua organização e do seu papel dentro dela.