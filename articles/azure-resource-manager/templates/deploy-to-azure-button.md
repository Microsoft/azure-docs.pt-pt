---
title: Botão Implementar no Azure
description: Utilize o botão para implementar os modelos do Gestor de Recursos Azure a partir de um repositório GitHub.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 62a0a8b0336d9a7fcf00efb172775b9606bcef98
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675396"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Use um botão de implementação para implementar modelos do repositório GitHub

Este artigo descreve como usar o botão **Implementar para Azure** para implementar modelos a partir de um repositório GitHub. Pode adicionar o botão diretamente ao ficheiro README.md no seu repositório GitHub. Ou, pode adicionar o botão a uma página web que faz referência ao repositório.

O âmbito de implantação é determinado pelo esquema do modelo. Para obter mais informações, consulte:

* [grupos de recursos](deploy-to-resource-group.md)
* [assinaturas](deploy-to-subscription.md)
* [grupos de gestão](deploy-to-management-group.md)
* [inquilinos](deploy-to-tenant.md).

## <a name="use-common-image"></a>Use imagem comum

Para adicionar o botão à sua página web ou repositório, utilize a seguinte imagem:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão Implementar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para implementar o modelo

Para criar o URL para o seu modelo, comece com o URL cru para o modelo no seu repo. Para ver o URL cru, selecione **Raw** .

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="selecionar Raw":::

O formato do URL é:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, converta o URL num valor codificado por URL. Pode utilizar um codificader on-line ou executar um comando. O exemplo seguinte do PowerShell mostra como codificar um valor url.

```powershell
[uri]::EscapeDataString($url)
```

O URL de exemplo tem o seguinte valor quando o URL codificado.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Cada ligação começa com o mesmo URL base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adicione o seu link de modelo codificado por URL ao fim do URL base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Tem o URL completo para a ligação.

## <a name="create-deploy-to-azure-button"></a>Criar implementar para o botão Azure

Finalmente, junte o link e a imagem.

Para adicionar o botão com Markdown no ficheiro README.md no seu repositório GitHub ou numa página web, utilize:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Para HTML, utilize:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Implementar o modelo

Para testar a solução completa, selecione o seguinte botão:

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

O portal apresenta um painel que lhe permite fornecer facilmente valores de parâmetros. Os parâmetros são pré-preenchidos com os valores predefinidos do modelo.

![Use o portal para implementar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os modelos, consulte [a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](template-syntax.md).
