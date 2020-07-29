---
title: Configurar uma instância e autenticação (manual)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço Azure Digital Twins, incluindo a autenticação adequada. Versão manual.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371465"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Configurar uma instância e autenticação Azure Digital Twins (manual)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um por um. Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

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

### <a name="verify-success"></a>Verificar o sucesso

Se o caso foi criado com sucesso, o resultado na Cloud Shell parece algo parecido com isto, despavendo informações sobre o recurso que criou:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Janela de comando com criação bem sucedida de grupo de recursos e exemplo de Gémeos Digitais Azure":::

Note o *nome de anfitrião*da Azure Digital Twins, *nome*e grupo *de recursos* a partir da saída. Estes são valores importantes que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados.

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-your-users-access-permissions"></a>Configurar as permissões de acesso do seu utilizador

A Azure Digital Twins usa [o Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para controlo de acesso baseado em funções (RBAC). Isto significa que antes de um utilizador poder fazer chamadas de avião de dados para a sua instância Azure Digital Twins, esse utilizador deve primeiro ser atribuído a uma função com permissões para o fazer.

Para a Azure Digital Twins, este papel é _**Azure Digital Twins Owner (Preview)**_. Pode ler mais sobre papéis e segurança em [*Soluções Concepts: Security for Azure Digital Twins.*](concepts-security.md)

Esta secção irá mostrar-lhe como criar uma atribuição de papel para um utilizador na instância Azure Digital Twins, através do seu e-mail associado ao inquilino AZure AD na sua assinatura Azure. Dependendo do seu papel e das suas permissões na sua subscrição Azure, irá configurar isto para si mesmo, ou configurar isto em nome de outra pessoa que estará a gerir o caso Azure Digital Twins.

### <a name="assign-the-role"></a>Atribuir o papel

Para dar permissão a um utilizador para gerir uma instância Azure Digital Twins, deve atribuir-lhes o papel _**de Azure Digital Twins Owner (Preview)**_ dentro do caso. 

> [!NOTE]
> Note que esta função é diferente da função Azure AD *Owner,* que também pode ser atribuída no âmbito da instância Azure Digital Twins. Estas são duas funções de gestão distintas, e a Azure AD *Owner* não concede acesso a funcionalidades de plano de dados que são concedidas com *a Azure Digital Twins Owner (Preview)*.

Utilize o seguinte comando para atribuir a função (deve ser gerido por um Proprietário da assinatura Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado deste comando é informação sobre a atribuição de funções que foi criada.

> [!TIP]
> Se obter um erro *400: BadRequest,* executar o seguinte comando para obter o *ObjectID* para o utilizador:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Em seguida, repita o comando de atribuição de funções utilizando o *ID* do objeto do utilizador no lugar do seu e-mail.

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo. Em seguida, irá configurar permissões para uma aplicação de clientes para aceder a ela.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicações de clientes

Uma vez configurado um exemplo de Azure Digital Twins, é comum interagir com esse caso através de uma aplicação de cliente que cria. Para isso, terá de se certificar de que a aplicação do cliente será capaz de autenticar contra a Azure Digital Twins. Isto é feito através da criação de um registo de **aplicações** [Azure Ative (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para a aplicação do seu cliente.

Este registo de aplicações é onde configura permissões de acesso às [APIs das Gémeas Digitais Azure.](how-to-use-apis-sdks.md) Posteriormente, a aplicação do cliente irá autenticar-se contra o registo da aplicação e, consequentemente, será-lhe concedidas as permissões de acesso configuradas às APIs.

>[!TIP]
> Como subscrição Proprietário, pode preferir configurar um novo registo de aplicações para cada nova instância Azure Digital Twins, *ou* fazê-lo apenas uma vez e estabelecer um registo de aplicações única que será partilhado entre todas as instâncias Azure Digital Twins na subscrição.

### <a name="create-the-registration"></a>Criar o registo

Para criar um registo de aplicações, você precisa fornecer os IDs de recursos para as APIs de Gémeos Digitais Azure, e as permissões de base para a API. No seu diretório de trabalho, abra um novo arquivo e introduza o seguinte corte JSON para configurar estes detalhes: 

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

Guarde este ficheiro à medida *quemanifest.jsligado*.

> [!NOTE] 
> Existem alguns locais onde uma corda "amigável", legível pelo homem `https://digitaltwins.azure.net` pode ser usada para o ID de recurso Azure Digital Twins em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos ao longo desta documentação definem a autenticação com a biblioteca MSAL, e a corda amigável pode ser usada para isso. No entanto, durante este passo de criação do registo da aplicação, é necessário o formulário GUID do ID tal como é mostrado acima. 

Na sua janela Cloud Shell, clique no ícone "Carregar/Descarregar ficheiros" e escolha "Upload".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da opção Upload":::
Navegue até ao *manifest.jsem* que acabou de criar e bater "Open".

Em seguida, executar o seguinte comando para criar um registo de aplicações (substituindo os espaços reservados, se necessário):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Aqui está um excerto da saída deste comando, mostrando informações sobre o registo que criou:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Saída cloud Shell do novo registo de aplicações AD AZure":::

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Em primeiro lugar, verifique se as definições do seu *manifest.js* carregado estavam corretamente definidas no registo. Para isso, selecione *Manifesto* da barra de menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure os campos a partir do *seumanifest.jsem* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Recolher valores importantes

Em seguida, *selecione Overview* da barra de menus para ver os detalhes do registo da aplicação:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Visão do portal dos valores importantes para o registo da aplicação":::

Tome nota do ID de *Aplicação (cliente)* e *Diretório (inquilino)* mostrado na **sua** página. Estes valores serão necessários mais tarde para [autenticar uma aplicação do cliente contra as APIs das Gémeas Digitais Azure.](how-to-authenticate-client.md) Se você não é a pessoa que vai escrever código para tais aplicações, você precisará compartilhar estes valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outros passos possíveis para a sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

Veja como ligar a aplicação do seu cliente ao seu caso escrevendo o código de autenticação da aplicação do cliente:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)
