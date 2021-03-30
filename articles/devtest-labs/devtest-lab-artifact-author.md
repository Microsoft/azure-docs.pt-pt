---
title: Crie artefactos personalizados para a sua máquina virtual DevTest Labs | Microsoft Docs
description: Aprenda a criar artefactos para usar com a Azure DevTest Labs para implementar e configurar aplicações depois de providenciar uma máquina virtual.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270687"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Crie artefactos personalizados para a sua máquina virtual DevTest Labs

Veja o seguinte vídeo para uma visão geral dos passos descritos neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Descrição Geral
Pode utilizar *artefactos* para implantar e configurar a sua aplicação depois de ter previsto um VM. Um artefacto consiste num ficheiro de definição de artefactos e outros ficheiros de script que são armazenados numa pasta num repositório de Git. Os ficheiros de definição de artefactos consistem em JSON e expressões que pode utilizar para especificar o que pretende instalar num VM. Por exemplo, pode definir o nome de um artefacto, um comando a executar, e parâmetros que estão disponíveis quando o comando é executado. Pode consultar outros ficheiros de script dentro do ficheiro de definição de artefactos pelo nome.

## <a name="artifact-definition-file-format"></a>Formato de ficheiro de definição de artefacto
O exemplo a seguir mostra as secções que compõem a estrutura básica de um ficheiro de definição:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Nome do elemento | Necessário? | Description |
| --- | --- | --- |
| $schema |No |Localização do ficheiro de esquema JSON. O ficheiro de esquema JSON pode ajudá-lo a testar a validade do ficheiro de definição. |
| título |Yes |O nome do artefacto exposto no laboratório. |
| descrição |Yes |Descrição do artefacto exibido no laboratório. |
| iconUri |No |URI do ícone exibido no laboratório. |
| targetOsType |Yes |Sistema operativo do VM onde o artefacto está instalado. As opções suportadas são Windows e Linux. |
| parâmetros |No |Valores fornecidos quando o comando de instalação do artefacto é executado numa máquina. Isto ajuda-o a personalizar o seu artefacto. |
| runCommand |Yes |O comando de instalação de artefactos que é executado num VM. |

### <a name="artifact-parameters"></a>Parâmetros dos artefactos
Na secção de parâmetros do ficheiro de definição, especifique quais os valores que um utilizador pode inserir quando instala um artefacto. Pode consultar estes valores no comando de instalação de artefactos.

Para definir parâmetros, utilize a seguinte estrutura:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Nome do elemento | Necessário? | Description |
| --- | --- | --- |
| tipo |Yes |Tipo de valor do parâmetro. Consulte a seguinte lista para os tipos permitidos. |
| displayName |Yes |Nome do parâmetro que é apresentado a um utilizador em laboratório. |
| descrição |Yes |Descrição do parâmetro que é apresentado no laboratório. |

Os tipos permitidos são:

* corda (qualquer cadeia JSON válida)
* int (qualquer número inteiro válido JSON)
* bool (qualquer JSON Boolean válido)
* matriz (qualquer matriz JSON válida)

## <a name="secrets-as-secure-strings"></a>Segredos como cordas seguras
Declare segredos como cordas seguras. Aqui está a sintaxe para declarar um parâmetro de corda seguro dentro `parameters` da secção doartifactfile.js **em** arquivo:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Para o comando de instalação de artefactos, execute o script PowerShell que toma a cadeia de segurança criada utilizando o comando ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Para o exemplo completo artifactfile.jse o artifact.ps1 (script PowerShell), consulte [esta amostra no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Outro ponto importante a notar é não registar segredos para a consola, uma vez que a saída é capturada para depurar o utilizador. 

## <a name="artifact-expressions-and-functions"></a>Expressões e funções de artefactos
Pode utilizar expressões e funções para construir o comando de instalação de artefactos.
As expressões são fechadas com suportes (e], e são avaliadas quando o artefacto é instalado. Expressões podem aparecer em qualquer lugar no valor de cadeia JSON. As expressões devolvem sempre outro valor JSON. Se precisar de usar uma corda literal que comece com um suporte ([), deve utilizar dois suportes ([).
Normalmente, usa-se expressões com funções para construir um valor. Tal como no JavaScript, as chamadas de função são formatadas como **funçãoName (arg1, arg2, arg3)**.

A lista que se segue mostra funções comuns:

* **parâmetros(parâmetroName)**: Devolve um valor de parâmetro que é fornecido quando o comando do artefacto é executado.
* **concat(arg1, arg2, arg3,..... )**: Combina múltiplos valores de cordas. Esta função pode ter uma variedade de argumentos.

O exemplo a seguir mostra como usar expressões e funções para construir um valor:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Criar um artefacto personalizado

1. Instale um editor JSON. Precisa de um editor da JSON para trabalhar com ficheiros de definição de artefactos. Recomendamos a utilização [do Código do Estúdio Visual,](https://code.visualstudio.com/)que está disponível para Windows, Linux e OS X.
2. Obtenha uma amostra artifactfile.jsno ficheiro de definição. Confira os artefactos criados pela equipa da DevTest Labs no nosso [repositório GitHub.](https://github.com/Azure/azure-devtestlab) Criámos uma rica biblioteca de artefactos que pode ajudá-lo a criar os seus próprios artefactos. Descarregue um ficheiro de definição de artefactos e faça alterações para criar os seus próprios artefactos.
3. Faça uso do IntelliSense. Use o IntelliSense para ver elementos válidos que pode usar para construir um ficheiro de definição de artefactos. Também pode ver as diferentes opções para valores de um elemento. Por exemplo, quando edita o elemento **TargetOsType,** o IntelliSense mostra-lhe duas opções, para Windows ou Linux.
4. Guarde o artefacto no [repositório de Git público para devTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou [o seu próprio repositório de Git.](devtest-lab-add-artifact-repo.md) No repositório público, pode ver artefactos partilhados por outros que pode usar diretamente ou personalizá-los de acordo com as suas necessidades.
   
   1. Crie um diretório separado para cada artefacto. O nome do diretório deve ser o mesmo que o nome do artefacto.
   2. Guarde o ficheiro de definição de artefacto (artifactfile.js) no diretório que criou.
   3. Guarde os scripts que são referenciados a partir do comando de instalação de artefactos.
      
      Aqui está um exemplo de como uma pasta de artefactos pode parecer:
      
      ![Exemplo de pasta de artefactos](./media/devtest-lab-artifact-author/git-repo.png)
5. Se estiver a utilizar o seu próprio repositório para armazenar artefactos, adicione o repositório ao laboratório seguindo as instruções do artigo: [Adicione um repositório git para artefactos e modelos](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefactos em Laboratórios DevTest](devtest-lab-troubleshoot-artifact-failure.md)
* [Junte um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório de artefactos Git a um laboratório.](devtest-lab-add-artifact-repo.md)
