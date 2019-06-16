---
title: Testar a definição de IU para aplicações geridas do Azure | Documentos da Microsoft
description: Descreve como testar a experiência do usuário para criar a sua aplicação gerida do Azure através do portal.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257616"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testar a sua interface do portal para aplicações geridas do Azure

Após [criar o ficheiro Createuidefinition](create-uidefinition-overview.md) para seu aplicativo gerenciado, precisa testar a experiência do usuário. Para simplificar o teste, utilize um ambiente de área de segurança que carrega o ficheiro no portal. Não precisa de implementar, na verdade, seu aplicativo gerenciado. A área de segurança apresenta sua interface do usuário da experiência do portal atual, a tela inteira. Em alternativa, pode utilizar um script do PowerShell para testar a interface, mas utiliza uma vista de legado do portal. Ambas as abordagens são apresentadas neste artigo. A área de segurança é a forma recomendada para a interface de pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

* R **Createuidefinition** ficheiro. Se não tiver este ficheiro, copie os [ficheiro de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="use-sandbox"></a>Utilize o sandbox

1. Abra o [criar Sandbox de definição de interface do Usuário](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Mostrar a área de segurança](./media/test-createuidefinition/show-sandbox.png)

1. Substitua a definição vazia com o conteúdo do ficheiro Createuidefinition. Selecione **pré-visualização**.

   ![Selecione pré-visualização](./media/test-createuidefinition/select-preview.png)

1. O formulário que criou é apresentado. Pode percorrer a experiência do usuário e preencha os valores.

   ![Mostrar formulário](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Resolução de problemas

Se o seu formulário não exibe depois de selecionar **pré-visualização**, pode ter um erro de sintaxe. Procure o indicador de vermelho na barra de rolagem certo e navegar até ele.

![Mostrar Erro de sintaxe](./media/test-createuidefinition/show-syntax-error.png)

Se não Exibe seu formulário e, em vez disso, verá um ícone de uma nuvem com o menu de subdivisão, o formulário tem um erro, como uma propriedade em falta. Abra as ferramentas de desenvolvimento da Web no seu browser. O **consola** exibe mensagens importantes sobre a sua interface.

![Mostrar Erro](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Utilizar script de teste

Para testar sua interface no portal, tem de copiar um dos seguintes scripts no seu computador local:

* [Script de sideload do PowerShell](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de sideload da CLI do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Para ver o ficheiro de interface no portal, execute o seu script transferido. O script cria uma conta de armazenamento na sua subscrição do Azure e carrega o ficheiro Createuidefinition para a conta de armazenamento. A conta de armazenamento é criada na primeira vez que executa o script ou se a conta de armazenamento foi eliminada. Se a conta de armazenamento já existe na sua subscrição do Azure, o script reutiliza-lo. O script abre o portal e carrega o ficheiro da conta de armazenamento.

Fornece um local para a conta de armazenamento e especifique a pasta que contém o ficheiro Createuidefinition.

Para o PowerShell, utilize:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, utilize:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o ficheiro Createuidefinition está na mesma pasta que o script e já criou a conta de armazenamento, não precisa de fornecer esses parâmetros.

Para o PowerShell, utilize:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Para a CLI do Azure, utilize:

```azurecli
./sideload-createuidef.sh
```

O script abre um novo separador no browser. É apresentado no portal com sua interface para criar a aplicação gerida.

![Ver portal](./media/test-createuidefinition/view-portal.png)

Fornece valores para os campos. Quando terminar, verá os valores que são transmitidos para o modelo.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Pode utilizar estes valores como o ficheiro de parâmetros para o modelo de implementação de teste.

Se o portal pára no ecrã resumo, poderá haver um bug na secção de saída. Por exemplo, poderá ter referenciado um controle que não existe. Se um parâmetro na saída estiver vazio, o parâmetro pode referenciar uma propriedade que não existe. Por exemplo, a referência ao controle é válida, mas a referência de propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar seus arquivos de solução

Agora que verificar que sua interface do portal está a funcionar conforme esperado, chegou a hora para validar que o ficheiro createUiDefinition corretamente é integrado ao seu ficheiro maintemplate. JSON. Pode executar um script de validação do teste para teste o conteúdo dos seus ficheiros de solução, incluindo o ficheiro createUiDefinition. O script valida a sintaxe JSON, verifica a existência de expressões de regex em campos de texto e certifica-se de que os valores de saída de interface do portal de corresponder aos parâmetros do seu modelo. Para informações sobre como executar este script, consulte [executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passos Seguintes

Depois de confirmar sua interface do portal, saiba mais sobre como efetuar sua [do Azure geridos aplicação disponível no Marketplace](publish-marketplace-app.md).
