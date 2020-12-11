---
title: Tutorial - Adicione recurso ao modelo
description: Descreve os passos para criar o seu primeiro modelo de Gestor de Recursos Azure (modelo ARM). Você aprende sobre a sintaxe do ficheiro do modelo e como implementar uma conta de armazenamento.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106976"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Tutorial: Adicione um recurso ao seu modelo ARM

No [tutorial anterior,](template-tutorial-create-first-template.md)aprendeu a criar um modelo de Gestor de Recursos Azure em branco (modelo ARM) e implantá-lo. Agora, está pronto para implementar um recurso. Neste tutorial, adicione uma conta de armazenamento. Leva cerca de **9 minutos** para completar este tutorial.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial introdutório sobre modelos,](template-tutorial-create-first-template.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="add-resource"></a>Adicionar recurso

Para adicionar uma definição de conta de armazenamento ao modelo existente, olhe para o JSON realçado no exemplo seguinte. Em vez de tentar copiar secções do modelo, copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

Substitua `{provide-unique-name}` e as chaves encaracoladas `{}` por um nome único de conta de armazenamento.

> [!IMPORTANT]
> O nome da conta do Storage tem de ser exclusivo em todo o Azure. O nome deve ter apenas letras ou números minúsculos. Não pode ter mais de 24 caracteres. Você pode tentar um padrão de nomeação como usar **a loja1** como um prefixo e, em seguida, adicionar as suas iniciais e a data de hoje. Por exemplo, o nome que usa pode parecer **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Adivinhar um nome único para uma conta de armazenamento não é fácil e não funciona bem para automatizar grandes implementações. Mais tarde nesta série tutorial, você usará funcionalidades de modelo que facilitam a criação de um nome único.

## <a name="resource-properties"></a>Propriedades de recursos

Pode estar a perguntar-se como encontrar as propriedades a utilizar para cada tipo de recurso. Pode utilizar a referência do [modelo ARM](/azure/templates/) para encontrar os tipos de recursos que pretende implantar.

Cada recurso que implementa tem pelo menos as seguintes três propriedades:

- `type`: Tipo de recurso. Este valor é uma combinação do espaço de nome do fornecedor de recursos e do tipo de recursos como `Microsoft.Storage/storageAccounts` .
- `apiVersion`: Versão da API REST a utilizar para a criação do recurso. Cada fornecedor de recursos publica as suas próprias versões API, pelo que este valor é específico do tipo.
- `name`: Nome do recurso.

A maioria dos recursos também tem um `location` imóvel, que define a região onde o recurso é implantado.

As outras propriedades variam de acordo com o tipo de recurso e a versão API. É importante entender a ligação entre a versão API e as propriedades disponíveis, por isso vamos entrar em mais detalhes.

Neste tutorial, adicionou uma conta de armazenamento ao modelo. Pode ver a versão API no [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Note que não adicionou todas as propriedades ao seu modelo. Muitas das propriedades são opcionais. O `Microsoft.Storage` fornecedor de recursos pode lançar uma nova versão API, mas a versão que está a implementar não tem de mudar. Pode continuar a usar essa versão e saber que os resultados da sua implementação serão consistentes.

Se você vê uma versão API mais antiga, como [armazenamentoAcounts 2016-05-01,](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)verá que um conjunto menor de propriedades estão disponíveis.

Se decidir alterar a versão API para um recurso, certifique-se de que avalia as propriedades dessa versão e ajusta o seu modelo adequadamente.

## <a name="deploy-template"></a>Implementar o modelo

Pode implementar o modelo para criar a conta de armazenamento. Dê ao seu destacamento um nome diferente para que possa encontrá-lo facilmente na história.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a `templateFile` variável para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

Duas possíveis falhas de implantação que poderá encontrar:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` não é um nome de conta de armazenamento válido. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas.

    No modelo, `{provide-unique-name}` substitua-o por um nome de conta de armazenamento único. Consulte [o recurso Add](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` já está tomada.

    No modelo, experimente um nome diferente da conta de armazenamento.

Esta implementação demora mais tempo do que a implementação do seu modelo em branco porque a conta de armazenamento é criada. Pode levar cerca de um minuto, mas normalmente é mais rápido.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Vê que foi implantada uma conta de armazenamento.
1. Note que a etiqueta de implantação agora diz: **Implementações: 2 Bem sucedidas**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Criou um modelo simples para implementar uma conta de armazenamento Azure. Nos tutoriais posteriores, aprende-se a adicionar parâmetros, variáveis, recursos e saídas a um modelo. Estas características são os blocos de construção para modelos muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](template-tutorial-add-parameters.md)
