---
title: Configurar uma instância e a autenticação (CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar um caso do serviço Azure Digital Twins usando o CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8e82f8974b53224b3e471d1628a1ca5819ce2955
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044497"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurar uma instância e autenticação Azure Digital Twins (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um a um, utilizando o CLI.
* Para passar por estes passos manualmente utilizando o portal Azure, consulte a versão porta deste artigo: [*Como fazer: Configurar uma instância e autenticação (portal)*](how-to-set-up-instance-portal.md).
* Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância Azure Digital Twins

Nesta secção, irá **criar uma nova instância de Azure Digital Twins** utilizando o comando Cloud Shell. Terá de providenciar:
* Um grupo de recursos onde o caso será implantado. Se ainda não tiver um grupo de recursos existente em mente, pode criar um agora com este comando:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Uma região para o destacamento. Para ver que regiões suportam a Azure Digital Twins, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)
* Um nome para o seu exemplo. Se a sua subscrição tiver outro exemplo de Azure Digital Twins na região que já está a usar o nome especificado, será solicitado que escolha um nome diferente.

Utilize estes valores no seguinte comando para criar o caso:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verifique o sucesso e colete valores importantes

Se o caso foi criado com sucesso, o resultado na Cloud Shell parece algo parecido com isto, despavendo informações sobre o recurso que criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Note o **nome de anfitrião** da Azure Digital Twins, **nome** e grupo **de recursos** a partir da saída. Estes são valores importantes que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados. Se outros utilizadores estiverem a programar contra a ocorrência, deverá partilhar estes valores com eles.

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso ao utilizador

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Utilize o seguinte comando para atribuir a função (deve ser gerido por um utilizador com [permissões suficientes](#prerequisites-permission-requirements) na subscrição do Azure). O comando requer que passe o *nome principal* do utilizador na conta AZure AD para o utilizador que deve ser atribuído à função. Na maioria dos casos, isto corresponderá ao e-mail do utilizador na conta AZure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

O resultado deste comando é informação sobre a atribuição de funções que foi criada.

> [!NOTE]
> Se este comando retornar um erro dizendo que o CLI **não pode encontrar o utilizador ou o principal de serviço na base de dados de gráficos:**
>
> Em vez disso, atribua a função utilizando o *ID* do objeto do utilizador. Isto pode acontecer para utilizadores em [contas pessoais da Microsoft (MSAs)](https://account.microsoft.com/account). 
>
> Utilize a [página do portal Azure dos utilizadores do Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a conta de utilizador e abrir os seus dados. Copie o *ObjectID* do utilizador:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Vista da página do utilizador no portal Azure destacando o GUID no campo 'Objeto ID'" lightbox="media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuições de funções utilizando o *ID* do objeto do utilizador para o `assignee` parâmetro acima.

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo.

## <a name="next-steps"></a>Passos seguintes

Teste as chamadas individuais da API REST no seu caso utilizando os comandos CLI dos Gémeos Digitais Azure: 
* [az dt referência](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Como fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md)

Ou, veja como ligar uma aplicação do cliente ao seu caso com código de autenticação:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)