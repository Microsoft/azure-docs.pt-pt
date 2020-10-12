---
title: Configurar uma instância e a autenticação (CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar um caso do serviço Azure Digital Twins usando o CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bb5ff0bf7347b87fdc3a103a03f9ff58279a367d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320779"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurar uma instância e autenticação Azure Digital Twins (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um a um, utilizando o CLI.
* Para passar por estes passos manualmente utilizando o portal Azure, consulte a versão porta deste artigo: [*Como fazer: Configurar uma instância e autenticação (portal)*](how-to-set-up-instance-portal.md).
* Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância Azure Digital Twins

Nesta secção, irá **criar uma nova instância de Azure Digital Twins** utilizando o comando Cloud Shell. Terá de providenciar:
* Um grupo de recursos para implantá-lo. Se ainda não tiver um grupo de recursos existente em mente, pode criar um agora com este comando:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Uma região para o destacamento. Para ver que regiões suportam a Azure Digital Twins, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)
* Um nome para o seu exemplo. O nome do novo caso deve ser único na região para a sua subscrição (o que significa que se a sua subscrição tiver outra instância Azure Digital Twins na região que já está a usar o nome que escolher, será solicitado que escolha um nome diferente).

Utilize estes valores no seguinte comando para criar o caso:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verifique o sucesso e colete valores importantes

Se o caso foi criado com sucesso, o resultado na Cloud Shell parece algo parecido com isto, despavendo informações sobre o recurso que criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Note o *nome de anfitrião*da Azure Digital Twins, *nome*e grupo *de recursos* a partir da saída. Estes são valores importantes que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados. Se outros utilizadores estiverem a programar contra a ocorrência, deverá partilhar estes valores com eles.

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso ao utilizador

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Utilize o seguinte comando para atribuir a função (deve ser gerido por um utilizador com [permissões suficientes](#prerequisites-permission-requirements) na subscrição do Azure). O comando requer que passe o *nome principal* do utilizador na conta AZure AD para o utilizador que deve ser atribuído à função. Na maioria dos casos, isto corresponderá ao e-mail do utilizador na conta AZure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado deste comando é informação sobre a atribuição de funções que foi criada.

> [!NOTE]
> Se este comando retornar um erro dizendo que o CLI **não pode encontrar o utilizador ou o principal de serviço na base de dados de gráficos:**
>
> Em vez disso, atribua a função utilizando o *ID* do objeto do utilizador. Isto pode acontecer para utilizadores em [contas pessoais da Microsoft (MSAs)](https://account.microsoft.com/account). 
>
> Utilize a [página do portal Azure dos utilizadores do Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a conta de utilizador e abrir os seus dados. Copie o *ObjectID*do utilizador:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure" lightbox="media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuições de funções utilizando o *ID* do objeto do utilizador para o `assignee` parâmetro acima.

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo. Em seguida, irá configurar permissões para uma aplicação de clientes para aceder a ela.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicações de clientes

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Para criar um registo de aplicações, você precisa fornecer os IDs de recursos para as APIs de Gémeos Digitais Azure, e as permissões de base para a API.

No seu diretório de trabalho, crie um novo ficheiro e introduza o seguinte corte JSON para configurar estes detalhes: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Guarde este ficheiro à medida _** quemanifest.jsligado**_.

> [!NOTE] 
> Existem alguns locais onde uma corda "amigável", legível pelo homem `https://digitaltwins.azure.net` pode ser usada para o ID de recurso Azure Digital Twins em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos ao longo desta documentação definem a autenticação com a biblioteca MSAL, e a corda amigável pode ser usada para isso. No entanto, durante este passo de criação do registo da aplicação, é necessário o formulário GUID do ID tal como é mostrado acima. 

Em seguida, vais enviar este ficheiro para a Cloud Shell. Na sua janela Cloud Shell, clique no ícone "Carregar/Descarregar ficheiros" e escolha "Upload".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::
Navegue até ao *manifest.jsem* que acabou de criar e bater "Open".

Em seguida, executar o seguinte comando para criar um registo de aplicações, com um *cliente público/nativo (mobile & desktop)* resposta URL de `http://localhost` . Substitua os espaços reservados conforme necessário:

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Aqui está um excerto da saída deste comando, mostrando informações sobre o registo que criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Em seguida, verifique se as definições do seu *manifest.js* carregado estavam corretamente definidas no registo. Para isso, selecione *Manifesto* da barra de menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure os campos a partir do * seumanifest.jsem* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Recolher valores importantes

Em seguida, *selecione Overview* da barra de menus para ver os detalhes do registo da aplicação:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Tome nota do ID de *Aplicação (cliente)* e *Diretório (inquilino)* mostrado na **sua** página. Estes valores serão necessários mais tarde para [autenticar uma aplicação do cliente contra as APIs das Gémeas Digitais Azure.](how-to-authenticate-client.md) Se você não é a pessoa que vai escrever código para tais aplicações, você precisará compartilhar estes valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outros passos possíveis para a sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

Teste as chamadas individuais da API REST no seu caso utilizando os comandos CLI dos Gémeos Digitais Azure: 
* [az dt referência](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Como fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md)

Ou, veja como ligar a aplicação do seu cliente ao seu caso escrevendo o código de autenticação da aplicação do cliente:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)