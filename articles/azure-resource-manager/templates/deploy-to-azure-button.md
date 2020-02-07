---
title: Botão implantar no Azure
description: Utilize o botão para implantar modelos do Gestor de Recursos Azure a partir de um repositório GitHub.
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cf22203b843f4483f495b6eba9a522267c010a79
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050593"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Use um botão de implementação para implementar modelos do repositório GitHub

Este artigo descreve como usar o botão **Deploy to Azure** para implementar modelos a partir de um repositório GitHub. Pode adicionar o botão diretamente ao ficheiro README.md no seu repositório GitHub ou a uma página web que referencia o repositório.

## <a name="use-common-image"></a>Use imagem comum

Para adicionar o botão à sua página web ou repositório, utilize a seguinte imagem:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão implantar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para implementar modelo

Para criar o URL para o seu modelo, comece com o URL cru para o modelo no seu repo:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, URL codifica-lo. Pode utilizar um codificador on-line ou executar um comando. O exemplo do PowerShell que se segue mostra como codificar um valor.

```powershell
[uri]::EscapeDataString($url)
```

O URL de exemplo tem o seguinte valor quando o URL codificado.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Cada link começa com o mesmo URL base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adicione o seu modelo codificado por URL na extremidade do URL base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Tem a url completa para o link.

## <a name="create-deploy-to-azure-button"></a>Criar o botão Deploy para Azure

Finalmente, junte o link e a imagem. Pode adicionar este HTML ao ficheiro README.md no seu repositório GitHub ou a uma página web.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Implementar o modelo

Para testar a solução completa, selecione o seguinte botão:
<br><br>
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>

O portal exibe um painel que lhe permite fornecer facilmente valores de parâmetros. Os parâmetros são pré-preenchidos com os valores predefinidos do modelo.

![Usar portal para implantar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os modelos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)
