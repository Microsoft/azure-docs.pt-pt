---
title: Crie artefactos personalizados para a sua máquina virtual DevTest Labs [ DevTest Labs] Microsoft Docs
description: Aprenda a autorar os seus próprios artefactos para usar com o Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399208"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Crie artefactos personalizados para a sua máquina virtual DevTest Labs

Veja o seguinte vídeo para uma visão geral dos passos descritos neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Descrição geral
Pode utilizar *artefactos* para implantar e configurar a sua aplicação depois de fornecer um VM. Um artefacto consiste num ficheiro de definição de artefacto e outros ficheiros de script que são armazenados numa pasta num repositório Git. Os ficheiros de definição de artefactos consistem em JSON e expressões que pode utilizar para especificar o que pretende instalar num VM. Por exemplo, pode definir o nome de um artefacto, um comando a executar e parâmetros disponíveis quando o comando é executado. Pode consultar outros ficheiros de script dentro do ficheiro de definição de artefacto por nome.

## <a name="artifact-definition-file-format"></a>Formato de ficheiro de definição de artefacto
O exemplo que se segue mostra as secções que compõem a estrutura básica de um ficheiro de definição:

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

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| $schema |Não |Localização do ficheiro de esquemas da JSON. O ficheiro schema JSON pode ajudá-lo a testar a validade do ficheiro de definição. |
| título |Sim |Nome do artefacto exibido no laboratório. |
| descrição |Sim |Descrição do artefacto exibido no laboratório. |
| iconUri |Não |URI do ícone exibido no laboratório. |
| targetOsType |Sim |Sistema operativo do VM onde o artefacto está instalado. As opções suportadas são Windows e Linux. |
| parâmetros |Não |Valores fornecidos quando o comando de instalação do artefacto é executado numa máquina. Isto ajuda a personalizar o seu artefacto. |
| executarComando |Sim |Instala o comando de instalação de artefactos que é executado num VM. |

### <a name="artifact-parameters"></a>Parâmetros de artefacto
Na secção de parâmetros do ficheiro de definição, especifique quais os valores que um utilizador pode inserir quando instala um artefacto. Pode consultar-se a estes valores no comando de instalação de artefactos.

Para definir parâmetros, utilize a seguinte estrutura:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| tipo |Sim |Tipo de valor de parâmetro. Consulte a seguinte lista para os tipos permitidos. |
| displayName |Sim |Nome do parâmetro que é apresentado a um utilizador no laboratório. |
| descrição |Sim |Descrição do parâmetro que está exposto no laboratório. |

Os tipos permitidos são:

* cadeia (qualquer corda JSON válida)
* int (qualquer inteiro JSON válido)
* bool (qualquer JSON Boolean válido)
* matriz (qualquer matriz JSON válida)

## <a name="secrets-as-secure-strings"></a>Segredos como cordas seguras
Declare segredos como cordas seguras. Aqui está a sintaxe para declarar um `parameters` parâmetro de corda seguro dentro da secção do ficheiro **artifactfile.json:**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Para o comando de instalação de artefactos, execute o script PowerShell que toma a corda segura criada utilizando o comando ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Para o exemplo completo de artifactfile.json e o artifact.ps1 (script PowerShell), consulte [esta amostra no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Outro ponto importante a notar é não registar segredos na consola, uma vez que a saída é capturada para depuração do utilizador. 

## <a name="artifact-expressions-and-functions"></a>Expressões e funções de artefacto
Pode utilizar expressões e funções para construir o comando de instalação do artefacto.
As expressões são incluídas em suportes (e], e são avaliadas quando o artefacto é instalado. Expressões podem aparecer em qualquer lugar num valor de corda JSON. As expressões devolvem sempre outro valor JSON. Se precisar de utilizar uma corda literal que comece com um suporte ([), deve utilizar dois suportes ([[).]
Normalmente, você usa expressões com funções para construir um valor. Tal como no JavaScript, as chamadas de função são formatadas como **funname (arg1, arg2, arg3)**.

A lista que se segue mostra funções comuns:

* **parâmetros (parâmetronome)**: Devolve um valor de parâmetro que é fornecido quando o comando do artefacto é executado.
* **concat (arg1, arg2, arg3,..... )**: Combina múltiplos valores de cordas. Esta função pode ter uma variedade de argumentos.

O exemplo que se segue mostra como usar expressões e funções para construir um valor:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Criar um artefacto personalizado

1. Instale um editor da JSON. Precisa de um editor da JSON para trabalhar com ficheiros de definição de artefactos. Recomendamos a utilização do [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e OS X.
2. Obtenha um ficheiro de definição de artefactos de amostra.json. Confira os artefactos criados pela equipa de DevTest Labs no nosso [repositório GitHub.](https://github.com/Azure/azure-devtestlab) Criámos uma rica biblioteca de artefactos que podem ajudá-lo a criar os seus próprios artefactos. Descarregue um ficheiro de definição de artefacto e faça alterações para criar os seus próprios artefactos.
3. Faça uso do IntelliSense. Utilize o IntelliSense para ver elementos válidos que pode utilizar para construir um ficheiro de definição de artefacto. Você também pode ver as diferentes opções para valores de um elemento. Por exemplo, ao editar o elemento **targetOsType,** o IntelliSense mostra-lhe duas opções, para Windows ou Linux.
4. Guarde o artefacto no [repositório público git para DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou [o seu próprio repositório Git](devtest-lab-add-artifact-repo.md). No repositório público, pode ver artefactos partilhados por outros que pode usar diretamente ou personalizá-los de acordo com as suas necessidades.
   
   1. Crie um diretório separado para cada artefacto. O nome do diretório deve ser o mesmo que o nome do artefacto.
   2. Guarde o ficheiro de definição de artefacto (artifactfile.json) no diretório que criou.
   3. Guarde os scripts referenciados a partir do comando de instalação de artefactos.
      
      Aqui está um exemplo de como uma pasta de artefactos pode parecer:
      
      ![Exemplo de pasta de artefactos](./media/devtest-lab-artifact-author/git-repo.png)
5. Se estiver a usar o seu próprio repositório para armazenar artefactos, adicione o repositório ao laboratório seguindo as instruções do artigo: [Adicione um repositório Git para artefactos e modelos](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefactos em Laboratórios DevTest](devtest-lab-troubleshoot-artifact-failure.md)
* [Junte-se a um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório de artefactos Git a um laboratório.](devtest-lab-add-artifact-repo.md)
