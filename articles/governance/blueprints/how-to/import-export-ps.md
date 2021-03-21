---
title: Plantas de importação e exportação com PowerShell
description: Aprenda a trabalhar com as definições do seu projeto como código. Partilhar, controlar a fonte e geri-los utilizando os comandos de exportação e importação.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919991"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Definições de planta de importação e exportação com PowerShell

As Plantas Azure podem ser totalmente geridas através do portal Azure. À medida que as organizações avançam na sua utilização de Azure Blueprints, devem começar a pensar em definições de planta como código gerido. Este conceito é frequentemente referido como Infraestrutura como Código (IAC). Tratar as definições do seu projeto como código oferece vantagens adicionais para além do que o portal Azure oferece. As vantagens incluem:

- Partilha de definições de projeto
- Backup das definições do seu projeto
- Reutilização de definições de projeto em diferentes inquilinos ou assinaturas
- Colocação das definições de planta no controlo de origem
  - Teste automatizado de definições de plantas em ambientes de teste
  - Suporte a gasodutos de integração contínua e de implantação contínua (CI/CD)

Quaisquer que sejam as suas razões, gerir as definições do seu projeto como código tem benefícios. Este artigo mostra como usar o `Import-AzBlueprintWithArtifact` e `Export-AzBlueprintWithArtifact` os comandos no módulo [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe um conhecimento moderado de trabalho da Azure Blueprints. Se ainda não o fez, trabalhe nos seguintes artigos:

- [Criar uma planta no portal](../create-blueprint-portal.md)
- Leia sobre [as fases de implantação](../concepts/deployment-stages.md) e [o ciclo de vida da planta](../concepts/lifecycle.md)
- [Criar](../create-blueprint-powershell.md) e [gerir](./manage-assignments-ps.md) definições e atribuições de projetos com a PowerShell

Se ainda não estiver instalado, siga as instruções do [módulo Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** da Galeria PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Estrutura de pasta de uma definição de planta

Antes de olhar para as plantas de exportação e importação, vamos ver como os ficheiros que compõem a definição de planta são estruturados. Uma definição de planta deve ser armazenada na sua própria pasta.

> [!IMPORTANT]
> Se não for transmitido qualquer valor para o parâmetro **Nome** do `Import-AzBlueprintWithArtifact` cmdlet, é utilizado o nome da pasta em que a definição de planta é armazenada.

Juntamente com a definição de planta, que deve ser `blueprint.json` nomeada, estão os artefactos de que a definição de planta é composta. Cada artefacto deve estar na sub-página chamada `artifacts` .
Junta, a estrutura da sua definição de planta como ficheiros JSON nas pastas deve ser a seguinte:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Exporte a sua definição de planta

Os passos para exportar a sua definição de planta são simples. Exportar a definição de planta pode ser útil para partilhar, fazer backup ou colocar no controlo de fontes.

- **Planta** [necessária]
  - Especifica a definição de planta
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **OutputPath** [obrigatório]
  - Especifica o caminho para salvar a definição de planta ficheiros JSON para
  - Os ficheiros de saída estão numa sub-dobradeira com o nome da definição de planta
- **Versão** (opcional)
  - Especifica a versão para saída se o objeto de referência **Blueprint** contiver referências a mais de uma versão.

1. Obtenha uma referência à definição de projeto para exportar da subscrição representada como `{subId}` :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Utilize o `Export-AzBlueprintWithArtifact` cmdlet para exportar a definição de planta especificada:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importe a sua definição de planta

Uma vez que tenha uma [definição de planta exportada](#export-your-blueprint-definition) ou tenha uma definição de planta criada manualmente na [estrutura de pastas necessária,](#folder-structure-of-a-blueprint-definition)pode importar essa definição de planta para um grupo de gestão ou subscrição diferente.

Por exemplo, definições de plantas incorporadas, consulte o [repo GitHub da Blueprint Azure](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)Blueprint .

- **Nome** [obrigatório]
  - Especifica o nome para a nova definição de planta
- **InputPath** [obrigatório]
  - Especifica o caminho para criar a definição de planta a partir de
  - Deve corresponder à [estrutura de pasta necessária](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcional)
  - O ID do grupo de gestão para guardar a definição de planta para, se não o padrão de contexto atual
  - Ou **ManagementGroupId** quer **SubscriçãoId** devem ser especificados
- **SubscriçãoId** (opcional)
  - O ID de subscrição para guardar a definição de planta para, se não o padrão de contexto atual
  - Ou **ManagementGroupId** quer **SubscriçãoId** devem ser especificados

1. Utilize o `Import-AzBlueprintWithArtifact` cmdlet para importar a definição de planta especificada:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Uma vez importada a definição da planta, [atribua-a à PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Para obter informações sobre a criação de definições avançadas de plantas, consulte os seguintes artigos:

- Utilize [parâmetros estáticos e dinâmicos.](../concepts/parameters.md)
- Personalize a [ordem de sequenciação da planta](../concepts/sequencing-order.md).
- Proteger as implementações com [o bloqueio de recursos de plantas](../concepts/resource-locking.md).
- [Gerir plantas como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).