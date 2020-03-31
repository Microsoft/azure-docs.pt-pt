---
title: Plantas de importação e exportação com powerShell
description: Aprenda a trabalhar com as definições de plantas como código. Partilhar, controlar a fonte e geri-los utilizando os comandos de exportação e importação.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873204"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Definições de plantas de importação e exportação com powerShell

As Plantas Azure podem ser totalmente geridas através do portal Azure. À medida que as organizações avançam na sua utilização de Blueprints, devem começar a pensar nas definições de plantas como código gerido. Este conceito é muitas vezes referido como Infraestrutura como Código (IaC). Tratar as definições do seu projeto como código oferece vantagens adicionais para além do que o portal Azure oferece. Estes benefícios incluem:

- Partilha de definições de plantas
- Apoiando as definições de projeto
- Reutilizar definições de plantas em diferentes inquilinos ou subscrições
- Colocando as definições de planta no controlo de origem
  - Teste automatizado das definições de plantas em ambientes de ensaio
  - Apoio a gasodutos de integração contínua e de implantação contínua (CI/CD)

Quaisquer que sejam as suas razões, gerir as definições do seu projeto como código tem benefícios. Este artigo mostra como `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` usar o e comandos no módulo [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume um conhecimento de trabalho moderado das Plantas Azure. Se ainda não o fez, trabalhe nos seguintes artigos:

- [Criar uma planta no portal](../create-blueprint-portal.md)
- Leia sobre [as fases](../concepts/deployment-stages.md) de implantação e o ciclo de [vida da planta](../concepts/lifecycle.md)
- [Criação](../create-blueprint-powershell.md) e [gestão](./manage-assignments-ps.md) de definições e atribuições de plantas com powerShell

Se ainda não estiver instalado, siga as instruções em [Adicionar o módulo Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** da Galeria PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Estrutura de pasta de uma definição de planta

Antes de olhar para as plantas de exportação e importação, vamos ver como os ficheiros que compõem a definição de planta são estruturados. Uma definição de planta deve ser armazenada na sua própria pasta.

> [!IMPORTANT]
> Se não for passado **Name** qualquer valor `Import-AzBlueprintWithArtifact` para o parâmetro nome do cmdlet, o nome da pasta em que a definição de planta é armazenada é utilizado.

Juntamente com a definição `blueprint.json`de planta, que deve ser nomeada, são os artefactos de que a definição de planta é composta. Cada artefacto deve estar na `artifacts`subpasta denominada .
Junta, a estrutura da definição de projeto como ficheiros JSON em pastas deve ser a seguinte:

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

## <a name="export-your-blueprint-definition"></a>Exporte a sua definição de projeto

Os passos para exportar a sua definição de projeto são simples. Exportar a definição de planta pode ser útil para partilha, cópia de segurança ou colocação no controlo de origem.

- **Planta** [necessária]
  - Especifica a definição de planta
  - Usar `Get-AzBlueprint` para obter o objeto de referência
- **OutputPath** [necessário]
  - Especifica o caminho para salvar a definição de planta Ficheiros JSON para
  - Os ficheiros de saída estão numa subpasta com o nome da definição de planta
- **Versão** (opcional)
  - Especifica a versão para a saída se o objeto de referência **Blueprint** contiver referências a mais de uma versão.

1. Obtenha uma referência à definição de projeto `{subId}`para exportar a partir da subscrição representada como:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Utilize `Export-AzBlueprintWithArtifact` o cmdlet para exportar a definição de projeto especificada:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importar a sua definição de projeto

Uma vez que tenha uma definição de [planta exportada](#export-your-blueprint-definition) ou tenha uma definição de planta criada manualmente na [estrutura de pasta necessária,](#folder-structure-of-a-blueprint-definition)pode importar essa definição de projeto para um grupo de gestão ou subscrição diferente.

Por exemplo, de definições de plantas incorporadas, consulte o [repo Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Nome** [obrigatório]
  - Especifica o nome para a nova definição de projeto
- **InputPath** [obrigatório]
  - Especifica o caminho para criar a definição de planta a partir de
  - Deve corresponder à [estrutura de pasta necessária](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcional)
  - O ID do grupo de gestão para salvar a definição de projeto para, se não o padrão de contexto atual
  - **O ManagementGroupId** ou o **SubscriptionId** devem ser especificados
- **Subscrição Id** (opcional)
  - O ID de subscrição para salvar a definição de planta para, se não o padrão de contexto atual
  - **O ManagementGroupId** ou o **SubscriptionId** devem ser especificados

1. Utilize `Import-AzBlueprintWithArtifact` o cmdlet para importar a definição de projeto especificada:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Uma vez importada a definição de projeto, [atribua-a à PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Para obter informações sobre a criação de definições avançadas de plantas, consulte os seguintes artigos:

- Utilize [parâmetros estáticos e dinâmicos.](../concepts/parameters.md)
- Personalize a ordem de sequenciação da [planta.](../concepts/sequencing-order.md)
- Proteja as implementações com [o bloqueio](../concepts/resource-locking.md)do recurso de planta .
- [Gerir as plantas como Código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

- Conheça o ciclo de vida da [planta.](../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).