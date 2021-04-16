---
title: Teste o ficheiro de definição de UI
description: Descreve como testar a experiência do utilizador para criar a sua Aplicação Gerida Azure através do portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478901"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Teste a interface do portal para aplicações geridas azure

Depois de [criar o createUiDefinition.jsno ficheiro](create-uidefinition-overview.md) para a sua aplicação gerida, tem de testar a experiência do utilizador. Para simplificar os testes, utilize um ambiente de caixa de areia que carregue o seu ficheiro no portal. Não precisas de implementar a tua aplicação gerida. A caixa de areia apresenta a sua interface de utilizador na experiência atual do portal de ecrã completo. Ou pode usar um script para testar a interface. Ambas as abordagens são mostradas neste artigo. A caixa de areia é a forma recomendada de pré-visualizar a interface.

## <a name="prerequisites"></a>Pré-requisitos

* Um **createUiDefinition.jsarquivado.** Se não tiver este ficheiro, copie o [ficheiro da amostra.](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="use-sandbox"></a>Use caixa de areia

1. Abra a [Caixa de Areia Create UI Definition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Mostrar caixa de areia](./media/test-createuidefinition/show-sandbox.png)

1. Substitua a definição vazia pelo conteúdo do seu createUiDefinition.jsno ficheiro. Selecione **Pré-visualização**.

   ![Selecione pré-visualização](./media/test-createuidefinition/select-preview.png)

1. A forma que criou é exibida. Pode passar pela experiência do utilizador e preencher os valores.

   ![Mostrar forma](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se o seu formulário não for apresentado após a seleção **de Pré-visualização,** poderá ter um erro de sintaxe. Procure o indicador vermelho na barra de deslocal direita e navegue até ele.

![Mostrar erro de sintaxe](./media/test-createuidefinition/show-syntax-error.png)

Se o seu formulário não aparecer e, em vez disso, vir um ícone de uma nuvem com gota de lágrima, o seu formulário tem um erro, como uma propriedade em falta. Abra as Ferramentas de Desenvolvimento Web no seu browser. A **Consola** apresenta mensagens importantes sobre a sua interface.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Use o script de teste

Para testar a sua interface no portal, copie um dos seguintes scripts para a sua máquina local:

* [Script de carga lateral PowerShell - Módulo Az](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script de carga lateral PowerShell - Módulo Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de carga lateral Azure CLI](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Para ver o seu ficheiro de interface no portal, execute o seu script descarregado. O script cria uma conta de armazenamento na sua subscrição Azure e envia o seu createUiDefinition.jsno ficheiro para a conta de armazenamento. A conta de armazenamento é criada na primeira vez que executou o script ou se a conta de armazenamento foi eliminada. Se a conta de armazenamento já existir na sua subscrição Azure, o script reutiliza-a. O script abre o portal e carrega o seu ficheiro a partir da conta de armazenamento.

Forneça uma localização para a conta de armazenamento e especifique a pasta que tem o seu createUiDefinition.jsno ficheiro.

Para o PowerShell, utilize:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, utilize:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o seu createUiDefinition.jsficheiro estiver na mesma pasta que o script, e já tiver criado a conta de armazenamento, não precisa de fornecer esses parâmetros.

Para o PowerShell, utilize:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Para a CLI do Azure, utilize:

```bash
./sideload-createuidef.sh
```

O script abre um novo separador no seu navegador. Exibe o portal com a sua interface para criar a aplicação gerida.

Fornecer valores para os campos. Quando terminar, vê os valores que são passados para o modelo que pode ser encontrado na consola de ferramentas de desenvolvimento do seu navegador.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Pode utilizar estes valores como ficheiro de parâmetro para testar o seu modelo de implantação.

Se o portal estiver pendurado no ecrã do resumo, pode haver um erro na secção de saída. Por exemplo, pode ter referenciado um controlo que não existe. Se um parâmetro na saída estiver vazio, o parâmetro pode estar a referir-se a uma propriedade que não existe. Por exemplo, a referência ao controlo é válida, mas a referência da propriedade não é válida.

## <a name="test-your-solution-files"></a>Teste os seus ficheiros de solução

Agora que verificou que a interface do portal está a funcionar como esperado, é hora de validar que o seu ficheiro createUiDefinition está devidamente integrado com o seu mainTemplate.jsem arquivo. Pode executar um teste de script de validação para testar o conteúdo dos seus ficheiros de solução, incluindo o ficheiro createUiDefinition. O script valida a sintaxe JSON, verifica as expressões regex nos campos de texto e garante que os valores de saída da interface do portal correspondem aos parâmetros do seu modelo. Para obter informações sobre a execução deste script, consulte [Executar verificações de validação estática para modelos](https://aka.ms/arm-ttk).

## <a name="next-steps"></a>Passos seguintes

Depois de validar a interface do portal, saiba como disponibilizar a sua [aplicação gerida pelo Azure no Mercado.](../../marketplace/create-new-azure-apps-offer.md)
