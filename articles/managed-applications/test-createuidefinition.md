---
title: Testar a definição de IU para aplicações geridas do Azure | Documentos da Microsoft
description: Descreve como testar a experiência do usuário para criar a sua aplicação gerida do Azure através do portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: b1392c29881a9077e26baafc8972148800d03d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746324"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testar a interface de portal do Azure para a aplicação gerida
Após [criar o ficheiro Createuidefinition](create-uidefinition-overview.md) para a sua aplicação gerida do Azure, terá de testar a experiência do usuário. Para simplificar o teste, utilize um script que carrega o ficheiro no portal. Não precisa de implementar, na verdade, seu aplicativo gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

* R **Createuidefinition** ficheiro. Se não tiver este ficheiro, copie os [ficheiro de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) e guarde-o localmente.

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="download-test-script"></a>Transferir o script de teste

Para testar sua interface no portal, tem de copiar um dos seguintes scripts no seu computador local:

* [Script de sideload do PowerShell](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de sideload da CLI do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Executar script

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

## <a name="test-your-interface"></a>Testar sua interface do utilizador

O script abre um novo separador no browser. É apresentado no portal com sua interface para criar a aplicação gerida.

![Ver portal](./media/test-createuidefinition/view-portal.png)

Antes de preencher os campos, abra as ferramentas de desenvolvimento da Web no seu browser. O **consola** exibe mensagens importantes sobre a sua interface.

![Selecione consola](./media/test-createuidefinition/select-console.png)

Se a definição de interface tem um erro, verá a descrição na consola do.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

Fornece valores para os campos. Quando terminar, verá os valores que são transmitidos para o modelo.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Pode utilizar estes valores como o ficheiro de parâmetros para o modelo de implementação de teste.

## <a name="troubleshooting-the-interface"></a>A interface de resolução de problemas

Alguns erros comuns que poderá ver são:

* O portal não carregar sua interface. Em vez disso, ela mostra um ícone de uma nuvem com o menu de subdivisão. Normalmente, veem este ícone quando existe um erro de sintaxe no ficheiro. Abra o ficheiro no VS Code (ou outro editor de JSON que tenha a validação do esquema) e procure erros de sintaxe.

* O portal paradas na tela de resumida. Normalmente, este interrupção ocorre quando existe um bug na secção de saída. Por exemplo, poderá ter referenciado um controle que não existe.

* Um parâmetro na saída está vazio. O parâmetro pode fazer referência uma propriedade que não existe. Por exemplo, a referência ao controle é válida, mas a referência de propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar seus arquivos de solução

Agora que verificar que sua interface do portal está a funcionar conforme esperado, chegou a hora para validar que o ficheiro createUiDefinition corretamente é integrado ao seu ficheiro maintemplate. JSON. Pode executar um script de validação do teste para teste o conteúdo dos seus ficheiros de solução, incluindo o ficheiro createUiDefinition. O script valida a sintaxe JSON, verifica a existência de expressões de regex em campos de texto e certifica-se de que os valores de saída de interface do portal de corresponder aos parâmetros do seu modelo. Para informações sobre como executar este script, consulte [executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passos Seguintes

Depois de confirmar sua interface do portal, saiba mais sobre como efetuar sua [do Azure geridos aplicação disponível no Marketplace](publish-marketplace-app.md).
