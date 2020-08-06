---
author: baanders
description: incluir ficheiro para requisito de função na configuração de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832360"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: Requisitos de permissão

Para poder completar todos os passos deste artigo, tem de ser classificado como Proprietário na sua assinatura Azure. 

Pode verificar o seu nível de permissão executando este comando em Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Se este comando retornar um erro dizendo que o CLI **não pode encontrar o utilizador ou o principal de serviço na base de dados de gráficos:**
>
> Utilize o *ID* do objeto do seu utilizador em vez do seu e-mail para o resto deste artigo. Isto pode acontecer para utilizadores em [contas pessoais da Microsoft (MSAs)](https://account.microsoft.com/account). 
>
> Utilize a [página do portal Azure dos utilizadores do Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a sua conta de utilizador e abrir os seus dados. Copie o *ObjectID*do seu utilizador:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Vista da página do utilizador no portal Azure destacando o GUID no campo 'Objeto ID'" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuições de funções utilizando o *ID* do objeto do seu utilizador no lugar do seu e-mail.

Depois de executar o comando da lista de atribuições de funções, se for proprietário, o `roleDefinitionName` valor na saída é *Proprietário:*

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Janela cloud Shell mostrando saída do comando da lista de atribuições de funções az":::

Se descobrir que o valor é *Contribuinte* ou algo diferente do *Proprietário,* pode proceder de uma das seguintes formas:
* Contacte o proprietário da subscrição e solicite ao Proprietário que complete os passos deste artigo em seu nome
* Contacte o Proprietário da sua subscrição ou alguém com função de Administração de Acesso ao Utilizador na subscrição e solicite que o eleve ao Proprietário na subscrição para que tenha as permissões para prosseguir. Se isso é apropriado depende da sua organização e do seu papel dentro dela.