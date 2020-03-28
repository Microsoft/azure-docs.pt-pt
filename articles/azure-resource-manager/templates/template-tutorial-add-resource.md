---
title: Tutorial - Adicionar recurso ao modelo
description: Descreve os passos para criar o seu primeiro modelo de Gestor de Recursos Azure. Você aprende sobre a sintaxe de ficheiro sintaxe do modelo e como implementar uma conta de armazenamento.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 79e337b411f9d115d93050ebeee346a526913d39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371741"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Tutorial: Adicione um recurso ao seu modelo ARM

No [tutorial anterior,](template-tutorial-create-first-template.md)aprendeu a criar um modelo em branco e a implantá-lo. Agora, estás pronto para usar um recurso real. Neste tutorial, você adiciona uma conta de armazenamento. Leva cerca de **9 minutos** para completar este tutorial.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial introdutório sobre modelos,](template-tutorial-create-first-template.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="add-resource"></a>Adicionar recurso

Para adicionar uma definição de conta de armazenamento ao modelo existente, veja o JSON realçado no exemplo seguinte. Em vez de tentar copiar secções do modelo, copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

Substitua **{forneça um nome único}** por um nome único da conta de armazenamento.

> [!IMPORTANT]
> O nome da conta do Storage tem de ser exclusivo em todo o Azure. O nome deve ter apenas letras ou números minúsculos. Não pode ser mais do que 24 caracteres. Pode tentar um padrão de nomeação como usar a **loja1** como prefixo e, em seguida, adicionar as suas iniciais e a data de hoje. Por exemplo, o nome que usa pode parecer **loja1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Adivinhar um nome único para uma conta de armazenamento não é fácil e não funciona bem para automatizar grandes implantações. Mais tarde nesta série tutorial, você usará características de modelo que facilitam a criação de um nome único.

## <a name="resource-properties"></a>Propriedades de recursos

Pode estar a perguntar-se como encontrar as propriedades para cada tipo de recurso. Pode utilizar a referência do [modelo ARM](/azure/templates/) para encontrar os tipos de recursos que pretende implementar.

Cada recurso que implementa tem pelo menos as seguintes três propriedades:

- **tipo**: Tipo do recurso. Este valor é uma combinação do espaço de nome do fornecedor de recursos e do tipo de recursos (como Microsoft.Storage/storageAccounts).
- **apiVersão**: Versão da API REST para utilizar para criar o recurso. Cada fornecedor de recursos publicou as suas próprias versões API, pelo que este valor é específico do tipo.
- **nome**: Nome do recurso.

A maioria dos recursos também tem uma propriedade de **localização,** que define a região onde o recurso é implantado.

As outras propriedades variam por tipo de recurso e versão API. É importante entender a ligação entre a versão API e as propriedades disponíveis, então vamos saltar para mais detalhes.

Neste tutorial, você adicionou uma conta de armazenamento ao modelo. Pode ver que a versão API no [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Note que não adicionou todas as propriedades ao seu modelo. Muitas das propriedades são opcionais. O fornecedor de recursos Microsoft.Storage poderia lançar uma nova versão API, mas a versão que está a implementar não tem de mudar. Pode continuar a utilizar essa versão e saber que os resultados da sua implementação serão consistentes.

Se vir uma versão API mais antiga, como [armazenamentoAccounts 2016-05-01,](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)verá que um conjunto menor de propriedades estão disponíveis.

Se decidir alterar a versão API por um recurso, certifique-se de que avalia as propriedades para essa versão e ajuste o seu modelo adequadamente.

## <a name="deploy-template"></a>Implementar o modelo

Pode implementar o modelo para criar a conta de armazenamento. Dê à sua implantação um nome diferente para que possa encontrá-lo facilmente na história.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Duas possíveis falhas de implantação que poderá encontrar:

- Erro: Código=Nome de contaInválido; Message={fornecer-nome único} não é um nome de conta de armazenamento válido. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar números e letras minúsculas apenas.

    No modelo, substitua **{fornecer um nome único}** por um nome único da conta de armazenamento.  Ver [Adicionar recurso](#add-resource).

- Erro: Code=StorageAccount Já Tomada; Mensagem=A conta de armazenamento denominada loja 1abc09092019 já foi tomada.

    No modelo, experimente um nome de conta de armazenamento diferente.

Esta implementação demora mais tempo do que a implementação do modelo em branco porque a conta de armazenamento é criada. Pode levar cerca de um minuto, mas normalmente é mais rápido.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. Está a ver que foi implementada uma conta de armazenamento.
1. Note que o rótulo de implantação diz agora: **Implantações: 2 Bem sucedidas**.

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Criou um modelo simples para implementar uma conta de armazenamento Azure.  Nos tutoriais posteriores, aprende-se a adicionar parâmetros, variáveis, recursos e saídas a um modelo. Estas características são os blocos de construção para modelos muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](template-tutorial-add-parameters.md)