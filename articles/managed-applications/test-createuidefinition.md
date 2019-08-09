---
title: Testar a definição da interface do usuário para aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve como testar a experiência do usuário para criar seu aplicativo gerenciado do Azure por meio do Portal.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 91dd661cf4900512390079751f400f6a9888c452
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845917"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testar a interface do portal para aplicativos gerenciados do Azure

Depois de [criar o arquivo createUiDefinition. JSON](create-uidefinition-overview.md) para seu aplicativo gerenciado, você precisará testar a experiência do usuário. Para simplificar o teste, use um ambiente de área restrita que carregue o arquivo no Portal. Você não precisa realmente implantar seu aplicativo gerenciado. A área restrita apresenta a interface do usuário na experiência atual do portal de tela inteira. Ou você pode usar um script para testar a interface. Ambas as abordagens são mostradas neste artigo. A área restrita é a maneira recomendada para visualizar a interface.

## <a name="prerequisites"></a>Pré-requisitos

* Um arquivo **createUiDefinition. JSON** . Se você não tiver esse arquivo, copie o [arquivo de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="use-sandbox"></a>Usar área restrita

1. Abra a [área restrita criar definição de interface do usuário](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Mostrar área restrita](./media/test-createuidefinition/show-sandbox.png)

1. Substitua a definição vazia pelo conteúdo do seu arquivo createUiDefinition. JSON. Selecione **Visualização**.

   ![Selecionar visualização](./media/test-createuidefinition/select-preview.png)

1. O formulário que você criou é exibido. Você pode percorrer a experiência do usuário e preencher os valores.

   ![Mostrar formulário](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se o formulário não for exibido após a seleção da **Visualização**, você poderá ter um erro de sintaxe. Procure o indicador vermelho na barra de rolagem direita e navegue até ele.

![Mostrar erro de sintaxe](./media/test-createuidefinition/show-syntax-error.png)

Se o formulário não for exibido e, em vez disso, você vir um ícone de uma nuvem com descarte solto, seu formulário terá um erro, como uma propriedade ausente. Abra o Ferramentas para Desenvolvedores da Web no navegador. O **console** exibe mensagens importantes sobre a interface.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Usar script de teste

Para testar sua interface no portal, copie um dos seguintes scripts para seu computador local:

* [Script de carregamento do PowerShell – módulo AZ](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script de carregamento do PowerShell-módulo do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [CLI do Azure script de carga lateral](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Para ver o arquivo de interface no portal, execute o script baixado. O script cria uma conta de armazenamento em sua assinatura do Azure e carrega o arquivo createUiDefinition. JSON na conta de armazenamento. A conta de armazenamento é criada na primeira vez em que você executar o script ou se a conta de armazenamento tiver sido excluída. Se a conta de armazenamento já existir em sua assinatura do Azure, o script a reutiliza. O script abre o portal e carrega o arquivo da conta de armazenamento.

Forneça um local para a conta de armazenamento e especifique a pasta que tem o arquivo createUiDefinition. JSON.

Para o PowerShell, utilize:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, utilize:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o arquivo createUiDefinition. JSON estiver na mesma pasta que o script e você já tiver criado a conta de armazenamento, não precisará fornecer esses parâmetros.

Para o PowerShell, utilize:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Para a CLI do Azure, utilize:

```azurecli
./sideload-createuidef.sh
```

O script abre uma nova guia no navegador. Ele exibe o portal com sua interface para criar o aplicativo gerenciado.

Forneça valores para os campos. Quando terminar, você verá os valores que são passados para o modelo.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Você pode usar esses valores como o arquivo de parâmetro para testar seu modelo de implantação.

Se o portal parar na tela de resumo, poderá haver um bug na seção de saída. Por exemplo, você pode ter referenciado um controle que não existe. Se um parâmetro na saída estiver vazio, o parâmetro poderá estar fazendo referência a uma propriedade que não existe. Por exemplo, a referência ao controle é válida, mas a referência de propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar seus arquivos de solução

Agora que você verificou que a interface do portal está funcionando conforme o esperado, é hora de validar que o arquivo createUiDefinition está adequadamente integrado ao seu arquivo MainTemplate. JSON. Você pode executar um teste de script de validação para testar o conteúdo dos seus arquivos de solução, incluindo o arquivo createUiDefinition. O script valida a sintaxe JSON, verifica expressões Regex em campos de texto e garante que os valores de saída da interface do portal correspondam aos parâmetros do seu modelo. Para obter informações sobre como executar esse script, consulte [executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passos Seguintes

Depois de validar sua interface do portal, saiba como tornar seu [aplicativo gerenciado do Azure disponível no Marketplace](publish-marketplace-app.md).
