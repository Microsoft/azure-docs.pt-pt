---
title: Criar uma instância do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como configurar um caso do serviço Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9489b9e1afe5e42121f61a3b0b50b28b2401bd3
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392289"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Configurar um exemplo de Gémeos Digitais Azure

Este artigo irá acompanhá-lo através dos passos básicos para configurar um novo exemplo Azure Digital Twins. Isto inclui a criação do caso, e a atribuição de permissões do [Azure Ative Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) para o exemplo para si mesmo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Configurar um exemplo de Gémeos Digitais Azure

Em seguida, executar os seguintes comandos para criar um novo grupo de recursos Azure para uso neste como-fazer, e, em seguida, criar uma nova instância de Azure Digital Twins neste grupo de recursos.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Para obter uma lista de nomes da região de Azure que podem ser passados em comandos no CLI Azure, executar este comando:
> ```azurecli
> az account list-locations -o table
> ```
> Para ver que regiões suportam a Azure Digital Twins, visite [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)

O resultado destes comandos é algo parecido com isto, fornecendo informações sobre os recursos que criou:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Note o *nome de anfitrião*da Azure Digital Twins, *nome*e grupo *de recursos* a partir da saída. Estes são todos os valores-chave que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados.

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Atribuir permissões de Diretório Ativo Azure

A Azure Digital Twins usa [o Azure Ative Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) para controlo de acesso baseado em funções (RBAC). Isto significa que antes de poder fazer chamadas de data plane para a sua instância Azure Digital Twins, você deve primeiro atribuir-se um papel com estas permissões.

Para utilizar a Azure Digital Twins com uma aplicação para clientes, também terá de se certificar de que a aplicação do seu cliente pode autenticar contra a Azure Digital Twins. Isto é feito através da criação de um registo de aplicações Azure Ative (AAD), que pode ler em [Como-a: Autenticar uma aplicação de cliente.](how-to-authenticate-client.md)

#### <a name="assign-yourself-a-role"></a>Atribua-se um papel

Crie uma atribuição de papel para si mesmo, utilizando o seu e-mail associado ao inquilino da AAD na sua assinatura Azure. 

Em primeiro lugar, certifique-se de que é classificado como proprietário na sua assinatura Azure. Pode verificar isto utilizando o `az role assignment list --assignee <your-Azure-email>` comando e verificar se o valor do nome *dedefinição* é *proprietário.* Como proprietário na subscrição, pode utilizar o seguinte comando para atribuir o seu utilizador a uma função de proprietário para a sua instância Azure Digital Twins:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado deste comando é informação sobre a função que criou.

> [!TIP]
> Se obter um erro *400: BadRequest,* executar o seguinte comando para obter o *ObjectID* para o seu utilizador:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Em seguida, repita o comando de atribuição de funções utilizando o *ID* do objeto do seu utilizador no lugar do seu e-mail.

Agora tens uma instância do Azure Digital Twins pronta para ir.

## <a name="next-steps"></a>Passos seguintes

Veja como configurar e autenticar uma aplicação do cliente para trabalhar com o seu exemplo:
* [Como fazer: Autenticar uma aplicação de cliente](how-to-authenticate-client.md)
