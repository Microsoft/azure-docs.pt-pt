---
title: Criar artefactos personalizados para a máquina virtual do DevTest Labs | Documentos da Microsoft
description: Saiba como criar seu próprio artefactos para utilizar com o Azure DevTest Labs.
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
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: e4fd8d226ebe1cb993e11fb5c9617df3eef08bdc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532581"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Criar artefactos personalizados para a máquina virtual do DevTest Labs

Veja o vídeo seguinte para obter uma descrição geral dos passos descritos neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Descrição geral
Pode usar *artefactos* para implementar e configurar a aplicação depois de aprovisionar uma VM. Um artefato consiste num arquivo de definição de artefacto e outros ficheiros de script são armazenados numa pasta num repositório Git. Ficheiros de definição de artefacto consistem em JSON e expressões que pode utilizar para especificar o que deseja instalar numa VM. Por exemplo, pode definir o nome de um artefato, um comando para executar e parâmetros que estão disponíveis quando o comando é executado. Pode consultar outros arquivos de script dentro do arquivo de definição de artefacto por nome.

## <a name="artifact-definition-file-format"></a>Formato de ficheiro de definição de artefacto
O exemplo seguinte mostra as secções que compõem a estrutura básica de um ficheiro de definição:

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
| $schema |Não |Localização do ficheiro de esquema JSON. O ficheiro de esquema JSON pode ajudar a testar a validade do arquivo de definição. |
| título |Sim |Nome do artefacto apresentado no laboratório. |
| descrição |Sim |Descrição do artefacto apresentado no laboratório. |
| iconUri |Não |URI do ícone apresentado no laboratório. |
| targetOsType |Sim |Sistema operativo da VM onde está instalado o artefacto. Opções suportadas são Windows e Linux. |
| parâmetros |Não |Valores que são fornecidos quando o comando de instalação do artefacto é executado numa máquina. Isto ajuda a personalizar o artefacto. |
| runCommand |Sim |Comando que é executado numa VM de instalação de artefacto. |

### <a name="artifact-parameters"></a>Parâmetros de artefacto
Na secção de parâmetros do ficheiro de definição, especifique os valores que um utilizador pode introduzir a instalação de um artefacto. Pode consultar estes valores no comando de instalação de artefacto.

Para definir os parâmetros, utilize a seguinte estrutura:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| tipo |Sim |Tipo de valor do parâmetro. Veja a seguir uma lista de tipos permitidos. |
| displayName |Sim |Nome do parâmetro que é apresentado a um utilizador no laboratório. | |
| descrição |Sim |Descrição do parâmetro que é apresentado no laboratório. |

Tipos permitidos são:

* a cadeia de caracteres (qualquer cadeia JSON válida)
* Int (qualquer JSON número inteiro válido)
* bool (qualquer JSON booleanos válidos)
* matriz (qualquer matriz JSON válido)

## <a name="artifact-expressions-and-functions"></a>Expressões de artefacto e funções
Pode utilizar expressões e funções para construir o artefacto de comando de instalação.
As expressões são colocadas entre parênteses Retos ([e]) e são avaliadas quando o artefacto que está instalado. As expressões podem aparecer em qualquer lugar num valor de cadeia de caracteres do JSON. Expressões sempre retornam um valor JSON diferente. Se precisar de utilizar uma cadeia literal que começa com um colchete ([]), tem de utilizar dois Retos ([[).
Normalmente, usar expressões com as funções para construir um valor. Tal como no JavaScript, chamadas de função são formatadas como **functionName (arg1, arg2, arg3)**.

A lista seguinte mostra as funções comuns:

* **parameters(parameterName)**: Devolve um valor de parâmetro que é fornecido quando o comando de artefacto é executado.
* **concat (arg1, arg2, arg3,...)** : Combina vários valores de cadeia de caracteres. Essa função pode ter vários argumentos.

O exemplo seguinte mostra como utilizar expressões e funções para construir um valor:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Criar um artefacto personalizado

1. Instale um editor de JSON. É necessário um editor de JSON para trabalhar com arquivos de definição de artefacto. Recomendamos que utilize [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e OS X.
2. Obter um ficheiro de definição de artifactfile.json de exemplo. Confira os artefactos criados pela equipe do DevTest Labs no nosso [repositório do GitHub](https://github.com/Azure/azure-devtestlab). Criámos uma biblioteca completa de artefatos que podem ajudar a criar artefactos da sua própria. Transferir um ficheiro de definição de artefacto e alterá-la para criar artefactos da sua própria.
3. Certifique-se de usar do IntelliSense. Use o IntelliSense para ver os elementos válidos que pode utilizar para construir um arquivo de definição de artefacto. Também pode ver as diferentes opções para os valores de um elemento. Por exemplo, ao editar a **targetOsType** elemento, o IntelliSense mostra-lhe duas opções, para Windows ou Linux.
4. Store o artefacto no [público repositório de Git para o DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou [seu próprio repositório de Git](devtest-lab-add-artifact-repo.md). No repositório público, pode ver os artefactos partilhados por outros utilizadores, que pode utilizar diretamente ou personalize-os para satisfazer as suas necessidades.
   
   1. Crie um diretório separado para cada artefato. O nome do diretório deve ser igual ao nome de artefacto.
   2. Store o ficheiro de definição de artefacto (artifactfile.json) no diretório que criou.
   3. Store os scripts que são referenciados a partir do comando de instalação do artefacto.
      
      Eis um exemplo de como pode ser uma pasta de artefacto:
      
      ![Exemplo de pasta de artefacto](./media/devtest-lab-artifact-author/git-repo.png)
5. Se estiver a utilizar o seu próprio repositório para armazenar artefactos, adicione o repositório para o laboratório ao seguir as instruções no artigo: [Adicionar um repositório de Git de artefactos e modelos](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefactos no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [adicionar um repositório de Git de artefactos a um laboratório](devtest-lab-add-artifact-repo.md).
