---
title: Botão Implementar no Azure
description: Utilize o botão para implementar os modelos do Gestor de Recursos Azure a partir de um repositório GitHub.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 65891cace1cb17614abbfe091e1592d6f13feff4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185730"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Use um botão de implementação para implementar modelos do repositório GitHub

Este artigo descreve como usar o botão **Implementar para Azure** para implementar modelos a partir de um repositório GitHub. Pode adicionar o botão diretamente ao ficheiro README.md no seu repositório GitHub. Ou, pode adicionar o botão a uma página web que faz referência ao repositório.

O âmbito de implantação é determinado pelo esquema do modelo. Para obter mais informações, veja:

* [grupos de recursos](deploy-to-resource-group.md)
* [assinaturas](deploy-to-subscription.md)
* [grupos de gestão](deploy-to-management-group.md)
* [inquilinos](deploy-to-tenant.md)

## <a name="use-common-image"></a>Use imagem comum

Para adicionar o botão à sua página web ou repositório, utilize a seguinte imagem:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão Implementar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para implementar o modelo

Para criar o URL para o seu modelo, comece com o URL cru para o modelo no seu repo. Para ver o URL cru, selecione **Raw**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="selecionar Raw":::

O formato do URL é:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, converta o URL num valor codificado por URL. Pode utilizar um codificader on-line ou executar um comando. O exemplo seguinte do PowerShell mostra como codificar um valor url.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
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

Tipicamente, você hospeda o modelo em um repo público. Se utilizar um repo privado, deve incluir um símbolo para aceder ao conteúdo bruto do modelo. O símbolo gerado pelo GitHub é válido por apenas um curto período de tempo. Precisaria de atualizar frequentemente o link.

Se estiver a utilizar [Git com Azure Repos](/azure/devops/repos/git/) em vez de um repo GitHub, ainda pode utilizar o botão Implementar para Azure. Certifique-se de que o seu repo é público. Utilize a [operação Itens](/rest/api/azure/devops/git/items/get) para obter o modelo. O seu pedido deve estar no seguinte formato:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Codificar este URL de pedido.

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

Para Git com Azure repo, o botão está no formato:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Implementar o modelo

Para testar a solução completa, selecione o seguinte botão:

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

O portal apresenta um painel que lhe permite fornecer facilmente valores de parâmetros. Os parâmetros são pré-preenchidos com os valores predefinidos do modelo.

![Use o portal para implementar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os modelos, consulte [a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](template-syntax.md).
