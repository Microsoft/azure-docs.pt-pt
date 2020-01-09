---
title: Tutorial – adicionar recurso ao modelo
description: Descreve as etapas para criar seu primeiro modelo de Azure Resource Manager. Você aprende sobre a sintaxe do arquivo de modelo e como implantar uma conta de armazenamento.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 924c369465bf53ea5f58de906bd0894ce822cac3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472962"
---
# <a name="tutorial-add-a-resource-to-your-resource-manager-template"></a>Tutorial: adicionar um recurso ao seu modelo do Resource Manager

No [tutorial anterior](template-tutorial-create-first-template.md), você aprendeu como criar um modelo em branco e implantá-lo. Agora, você está pronto para implantar um recurso real. Neste tutorial, você adiciona uma conta de armazenamento. Leva cerca de **9 minutos** para concluir este tutorial.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial introdutório sobre modelos](template-tutorial-create-first-template.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Adicionar recurso

Para adicionar uma definição de conta de armazenamento ao modelo existente, examine o JSON realçado no exemplo a seguir. Em vez de tentar copiar as seções do modelo, copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

Substitua **{forneça um nome exclusivo}** por um nome de conta de armazenamento exclusivo. O nome da conta do Storage tem de ser exclusivo em todo o Azure. O nome deve ter apenas letras minúsculas ou números. Ele não pode ter mais de 24 caracteres. Você pode tentar um padrão de nomenclatura como usar **Store1** como um prefixo e, em seguida, adicionar suas iniciais e a data de hoje. Por exemplo, o nome que você usa pode ser semelhante a **store1abc09092019**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json?range=1-19&highlight=5-17)]

Adivinhar um nome exclusivo para uma conta de armazenamento não é fácil e não funciona bem para automatizar grandes implantações. Posteriormente nesta série de tutoriais, você usará recursos de modelo que facilitam a criação de um nome exclusivo.

## <a name="resource-properties"></a>Propriedades do recurso

Você pode estar se perguntando como localizar as propriedades a serem usadas para cada tipo de recurso. Você pode usar a [referência de modelo do Resource Manager](/azure/templates/) para localizar os tipos de recursos que deseja implantar.

Cada recurso que você implanta tem pelo menos as três propriedades a seguir:

- **tipo**: tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como Microsoft. Storage/storageAccounts).
- **apiVersion**: versão da API REST a ser usada para criar o recurso. Cada provedor de recursos publicou suas próprias versões de API, portanto, esse valor é específico para o tipo.
- **nome**: o nome do recurso.

A maioria dos recursos também tem uma propriedade **Location** , que define a região onde o recurso é implantado.

As outras propriedades variam por tipo de recurso e versão de API. É importante entender a conexão entre a versão da API e as propriedades disponíveis, portanto, vamos entrar em mais detalhes.

Neste tutorial, você adicionou uma conta de armazenamento ao modelo. Você pode ver essa versão da API em [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Observe que você não adicionou todas as propriedades ao seu modelo. Muitas das propriedades são opcionais. O provedor de recursos Microsoft. Storage pode liberar uma nova versão de API, mas a versão que você está implantando não precisa ser alterada. Você pode continuar usando essa versão e saber que os resultados da sua implantação serão consistentes.

Se você exibir uma versão de API mais antiga, como [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), verá que um conjunto menor de propriedades está disponível.

Se você decidir alterar a versão da API para um recurso, certifique-se de avaliar as propriedades dessa versão e ajustar o modelo adequadamente.

## <a name="deploy-template"></a>Implementar o modelo

Você pode implantar o modelo para criar a conta de armazenamento. Dê um nome diferente à sua implantação para que você possa encontrá-la com facilidade no histórico.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Duas possíveis falhas de implantação que você pode encontrar:

- Erro: código = AccountNameInvalid; A mensagem = {forneça um nome exclusivo} não é um nome de conta de armazenamento válido. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e usar apenas números e letras minúsculas.

    No modelo, substitua **{fornecer-Unique-Name}** por um nome de conta de armazenamento exclusivo.  Consulte [Adicionar recurso](#add-resource).

- Erro: código = StorageAccountAlreadyTaken; Mensagem = a conta de armazenamento denominada store1abc09092019 já está em uso.

    No modelo, tente um nome de conta de armazenamento diferente.

Essa implantação leva mais tempo do que a implantação de modelo em branco porque a conta de armazenamento é criada. Pode levar cerca de um minuto, mas geralmente é mais rápido.

## <a name="verify-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. Você verá que uma conta de armazenamento foi implantada.
1. Observe que o rótulo de implantação agora diz: **implantações: 2 com êxito**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Você criou um modelo simples para implantar uma conta de armazenamento do Azure.  Nos tutoriais posteriores, você aprenderá a adicionar parâmetros, variáveis, recursos e saídas a um modelo. Esses recursos são os blocos de construção para modelos muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](template-tutorial-add-parameters.md)