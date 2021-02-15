---
title: 'Quickstart: Criar um grupo de gestão com JavaScript'
description: Neste quickstart, você usa o JavaScript para criar um grupo de gestão para organizar os seus recursos numa hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 99a2ed406d4837f6fc346e68b3b400003feed38c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099154"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Quickstart: Criar um grupo de gestão com JavaScript

Os grupos de gestão são contentores que o ajudam a gerir o acesso, a política e o cumprimento em várias subscrições. Crie estes recipientes para construir uma hierarquia eficaz e eficiente que possa ser usada com [a Azure Policy](../policy/overview.md) e [os Controlos de Acesso Baseados em Funções Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](overview.md)

O primeiro grupo de gestão criado no diretório pode demorar até 15 minutos para ser concluído. Há processos que correm pela primeira vez para configurar o serviço de grupos de gestão dentro do Azure para o seu diretório. Recebe uma notificação quando o processo estiver concluído. Para mais informações, consulte [a configuração inicial dos grupos de gestão.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Antes de começar, certifique-se de que está instalada pelo menos a versão 12 de [Node.js.](https://nodejs.org/)

- Qualquer utilizador Azure AD no arrendatário pode criar um grupo de gestão sem que o grupo de gestão escreva a permissão atribuída a esse utilizador se a [proteção da hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver ativada. Este novo grupo de gestão torna-se filho do Grupo de Gestão de Raízes ou do [grupo de gestão padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e o criador recebe uma atribuição de função de "Proprietário". O serviço de grupo de gestão permite esta capacidade para que as atribuições de funções não sejam necessárias ao nível da raiz. Nenhum utilizadores tem acesso ao Grupo de Gestão de Raízes quando este é criado. Para evitar o obstáculo de encontrar os Azure AD Global Admins para começar a usar grupos de gestão, permitimos a criação dos grupos de gestão iniciais ao nível da raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Configuração da aplicação

Para permitir que o JavaScript gere grupos de gestão, o ambiente deve ser configurado. Esta configuração funciona onde o JavaScript pode ser utilizado, incluindo [a bash no Windows 10](/windows/wsl/install-win10).

1. Crie um novo projeto de Node.js executando o seguinte comando.

   ```bash
   npm init -y
   ```

1. Adicione uma referência ao módulo yargs.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência ao módulo Azure Resource Graph.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Adicione uma referência à biblioteca de autenticação Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verifique em _package.jsem é a_ versão `@azure/arm-managementgroups` **1.1.0** ou superior e `@azure/ms-rest-nodeauth` é a versão **3.0.5** ou superior.

## <a name="create-the-management-group"></a>Criar o grupo de gestão

1. Crie um novo ficheiro chamado _index.js_ e introduza o seguinte código.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Introduza o seguinte comando no terminal:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Certifique-se de substituir cada espaço reservado para cada `<>` suporte reservado ao seu grupo de _gestão_ e _nome amigável do grupo de gestão,_ respectivamente.

   À medida que o script tenta autenticar, uma mensagem semelhante à seguinte mensagem é exibida no terminal:

   > Para iniciar sposição, utilize um navegador web para abrir a página https://microsoft.com/devicelogin e introduza o código FGB56WJUGK para autenticar.

   Uma vez autenticado no navegador, o script continua a ser executado.

O resultado da criação do grupo de gestão é a saída para a consola.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as bibliotecas instaladas da sua aplicação, execute o seguinte comando.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um grupo de gestão para organizar a sua hierarquia de recursos. O grupo de gestão pode realizar subscrições ou outros grupos de gestão.

Para saber mais sobre grupos de gestão e como gerir a sua hierarquia de recursos, continue a:

> [!div class="nextstepaction"]
> [Gerir os seus recursos com grupos de gestão](./manage.md)