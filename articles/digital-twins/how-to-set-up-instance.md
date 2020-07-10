---
title: Criar uma instância do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como configurar um caso do serviço Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206515"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Configurar um exemplo de Gémeos Digitais Azure

Este artigo irá acompanhá-lo através dos passos básicos para configurar um novo exemplo Azure Digital Twins. Isto inclui a criação do caso, e a atribuição de permissões do [Azure Ative Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) para o exemplo para si mesmo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Configurar um exemplo de Gémeos Digitais Azure

Em seguida, irá criar um novo grupo de recursos Azure para uso neste como-fazer. Em seguida, pode **criar uma nova instância de Azure Digital Twins** dentro desse grupo de recursos. 

Também terá de fornecer um nome para o seu exemplo e escolher uma região para a implantação. Para ver que regiões suportam a Azure Digital Twins, visite [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)

>[!NOTE]
> O nome do novo caso deve ser único dentro da região (o que significa que se outra instância Azure Digital Twins naquela região já estiver a usar o nome que escolher, terá de escolher um nome diferente).

Criar o grupo de recursos e a instância com os seguintes comandos:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

O resultado destes comandos é algo parecido com isto, fornecendo informações sobre os recursos que criou:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Note o *nome de anfitrião*da Azure Digital Twins, *nome*e grupo *de recursos* a partir da saída. Estes são todos os valores-chave que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados.

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Atribuir permissões de Diretório Ativo Azure

A Azure Digital Twins usa [o Azure Ative Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) para controlo de acesso baseado em funções (RBAC). Isto significa que antes de poder fazer chamadas de data plane para a sua instância Azure Digital Twins, você deve primeiro atribuir-se um papel com estas permissões.

Para utilizar a Azure Digital Twins com uma aplicação para clientes, também terá de se certificar de que a aplicação do seu cliente pode autenticar contra a Azure Digital Twins. Isto é feito através da criação de um registo de aplicações Azure Ative (AAD), que pode ler em [Como-a: Autenticar uma aplicação de cliente.](how-to-authenticate-client.md)

#### <a name="assign-yourself-a-role"></a>Atribua-se um papel

Crie uma atribuição de papel para si no caso Azure Digital Twins, utilizando o seu e-mail associado ao inquilino da AAD na sua assinatura Azure. 

Para poder fazê-lo, tem de ser classificado como proprietário na sua assinatura Azure. Pode verificar isto executando o `az role assignment list --assignee <your-Azure-email>` comando e verificando na saída que o valor do *nome dedefinição* é *Proprietário*. Se descobrir que o valor é *Contribuinte* ou algo diferente do *Proprietário,* contacte o seu administrador de subscrição com o poder de conceder permissões na sua subscrição. Podem elevar o seu papel em toda a subscrição para que possa executar o seguinte comando, ou um proprietário pode executar o seguinte comando em seu nome para configurar as suas permissões Azure Digital Twins para si.

Para atribuir as permissões de "proprietário" do seu utilizador na sua instância Azure Digital Twins, utilize o seguinte comando (deve ser gerido por um proprietário da subscrição Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado deste comando é informação sobre a atribuição de funções que foi criada.

> [!TIP]
> Se obter um erro *400: BadRequest,* executar o seguinte comando para obter o *ObjectID* para o seu utilizador:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Em seguida, repita o comando de atribuição de funções utilizando o *ID* do objeto do seu utilizador no lugar do seu e-mail.

Tem agora uma instância Azure Digital Twins pronta a ser executada, e permissões para geri-lo.

## <a name="next-steps"></a>Passos seguintes

Veja como configurar e autenticar uma aplicação do cliente para trabalhar com o seu exemplo:
* [Como fazer: Autenticar uma aplicação de cliente](how-to-authenticate-client.md)
