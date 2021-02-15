---
title: 'Quickstart: Criar um grupo de gestão com Go'
description: Neste arranque rápido, você usa Go para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101775"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Quickstart: Criar um grupo de gestão com Go

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Um diretor de serviço da Azure, incluindo o _clienteId_ e _o clienteSecret._ Se não tiver um principal de serviço para uso com a Política Azure ou quiser criar um novo, consulte [as bibliotecas de gestão Azure para autenticação .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Salte o passo para instalar os pacotes .NET Core, pois vamos fazê-lo nos próximos passos.

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Adicione o pacote do grupo de gestão

Para permitir que o Go gere grupos de gestão, o pacote deve ser adicionado. Este pacote funciona onde quer que o Go possa ser utilizado, incluindo [a bash no Windows 10](/windows/wsl/install-win10) ou instalada localmente.

1. Verifique se o último Go está instalado (pelo menos **1.15**). Se ainda não estiver instalado, descarregue-o em [Golang.org](https://golang.org/dl/).

1. Verifique se o último CLI do Azure está instalado (pelo menos **2.5.1**). Se ainda não estiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > O Azure CLI é necessário para permitir que o Go utilize o `auth.NewAuthorizerFromCLI()` método no seguinte exemplo. Para obter informações sobre outras opções, consulte [Azure SDK para Go - Mais detalhes de autenticação](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autenticar através do Azure CLI.

   ```azurecli
   az login
   ```

1. No seu ambiente de eleição Go, instale os pacotes necessários para grupos de gestão:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Configuração da aplicação

Com os pacotes Go adicionados ao seu ambiente de eleição, é hora de configurar a aplicação Go que pode criar um grupo de gestão.

1. Crie a aplicação Go e guarde a seguinte fonte `mgCreate.go` como:

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Construa a aplicação Go:

   ```bash
   go build mgCreate.go
   ```

1. Criar um grupo de gestão utilizando a aplicação Go compilada. `<Name>`Substitua-o pelo nome do seu novo grupo de gestão:

   ```bash
   mgCreate "<Name>"
   ```

O resultado é um novo grupo de gestão no grupo de gestão de raiz.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as embalagens instaladas do ambiente Go, pode fazê-lo utilizando o seguinte comando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)