---
author: baanders
description: incluir ficheiro para informações iniciais na configuração Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371464"
---
>[!NOTE]
>Estas operações destinam-se a ser concluídas por um utilizador com uma função *de Proprietário* na subscrição do Azure. Embora algumas peças possam ser concluídas sem esta autorização elevada, a cooperação do proprietário será necessária para configurar completamente um caso utilizável. Veja mais informações sobre esta informação na secção [*Pré-Requisitos: Permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para um novo exemplo de Azure Digital Twins consiste em três partes:
1. **Criar o caso**
2. **Configurar as permissões de acesso do seu utilizador**: O utilizador do Azure precisa de ter o papel de *Azure Digital Twins Owner (Preview)* no caso para realizar atividades de gestão. Neste passo, irá atribuir-se este papel (se for proprietário na subscrição Azure), ou obter um Proprietário na sua subscrição para atribuí-lo a si.
3. **Configurar permissões de acesso para aplicações de clientes**: É comum escrever uma aplicação de cliente que utilize para interagir com o seu exemplo. Para que essa aplicação de clientes aceda aos seus Azure Digital Twins, é necessário configurar um *registo de aplicações* no [Azure Ative Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que a aplicação do cliente utilizará para autenticar o caso.

Para prosseguir, você precisará de uma assinatura Azure. Pode configurar um de graça [aqui.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: Requisitos de permissão

Para poder completar todos os passos deste artigo, tem de ser classificado como Proprietário na sua assinatura Azure. 

Pode verificar o seu nível de permissão executando este comando em Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se você é proprietário, o `roleDefinitionName` valor na saída é *Proprietário:*

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Janela cloud Shell mostrando saída do comando da lista de atribuições de funções az":::

Se descobrir que o valor é *Contribuinte* ou algo diferente do *Proprietário,* pode proceder de uma das seguintes formas:
* Contacte o proprietário da subscrição e solicite ao Proprietário que complete os passos deste artigo em seu nome
* Contacte o Proprietário da sua subscrição ou alguém com função de Administração de Acesso ao Utilizador na subscrição e solicite que o eleve ao Proprietário na subscrição para que tenha as permissões para prosseguir. Se isso é apropriado depende da sua organização e do seu papel dentro dela.
