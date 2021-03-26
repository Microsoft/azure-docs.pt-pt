---
title: Tutorial - adicione módulos ao ficheiro Bicep do Azure Resource Manager
description: Utilize módulos para encapsular detalhes complexos da declaração de recursos brutos.
author: mumian
ms.date: 03/25/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8c7ab1038cbe62d6f15faf56796193df12b38546
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568771"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar módulos ao ficheiro Bicep do Gestor de Recursos da Azure

No [tutorial anterior,](bicep-tutorial-use-parameter-file.md)aprendeu a usar um ficheiro de parâmetros para implementar o seu ficheiro Bicep. Neste tutorial, você aprende a usar módulos Bicep para encapsular detalhes complexos da declaração de recursos brutos. Os módulos podem ser partilhados e reutilizados dentro da sua solução.  Leva cerca de **12 minutos** para ser completado.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre o arquivo](bicep-tutorial-use-parameter-file.md)de parâmetros, mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Este ficheiro Bicep funciona bem. Mas para soluções maiores, pretende dividir o seu ficheiro Bicep em muitos módulos relacionados para que possa partilhar e reutilizar estes módulos. O ficheiro Bicep atual implementa uma conta de armazenamento, um plano de serviço de aplicações e um website.  Vamos separar a conta de armazenamento num módulo.

## <a name="create-bicep-module"></a>Criar módulo Bicep

Cada ficheiro Bicep pode ser consumido como um módulo, por isso não existe sintaxe específica para definir um módulo. Criar um ficheiro _storage.bicep_ com os seguintes conteúdos:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Este módulo contém o recurso da conta de armazenamento e os parâmetros e variáveis relacionados. Os valores para o parâmetro _de localização_ e os _parâmetros de recursosTags_ foram removidos. Estes valores serão passados a partir do ficheiro Bicep principal.

## <a name="consume-bicep-module"></a>Consumir módulo bicep

Substitua a definição de recursos de conta de armazenamento no _azuredeploy.bicep_ existente com os seguintes conteúdos Bicep:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **módulo**: Palavra-chave.
- **nome simbólico** (stg): Este é um identificador para o módulo.
- **ficheiro do módulo**: O caminho para o módulo neste exemplo é especificado utilizando um caminho relativo (./storage.bicep). Todos os caminhos em Bicep devem ser especificados utilizando o separador de diretório de corte dianteiro (/) para garantir uma plataforma transversal de compilação consistente. O backslash do Windows \) (o carácter não é suportado.

Para recuperar o ponto final de armazenamento, atualize a saída em _azuredeploy.bicep_ para o seguinte Bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

O azuredeploy.bicep completo tem o seguinte conteúdo:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implantar o gabarito.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---
> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando os grupos de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Vê os dois novos grupos de recursos que implementou neste tutorial.
1. Selecione qualquer um dos grupos de recursos e veja os recursos implantados. Note que correspondem aos valores especificados no seu ficheiro de parâmetros para esse ambiente.

## <a name="clean-up-resources"></a>Limpar os recursos

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**. Se tiver concluído esta série, tem três grupos de recursos para eliminar - **myResourceGroup,** **myResourceGroupDev** e **myResourceGroupProd**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Parabéns, terminou esta introdução para colocar ficheiros Bicep no Azure. Informe-nos se tiver algum comentário e sugestões na secção de comentários. Obrigado!

A próxima série tutorial vai para mais detalhes sobre a implementação de modelos.

> [!div class="nextstepaction"]
> [Implementar um modelo local](./deployment-tutorial-local-template.md)
