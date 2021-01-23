---
title: Converter modelo de portal para especificação de modelo
description: Descreve como converter um modelo existente na galeria do portal Azure para uma especificação de modelo.
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739139"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Converter galeria de modelos no portal para especificações de modelo

O portal Azure fornece uma forma de armazenar modelos de Gestor de Recursos Azure (modelos ARM) na sua conta. **Esta característica está a ser depreciada.** Para continuar a utilizar modelos nesta galeria, converta-os em [especificações de modelo.](template-specs.md)

Este artigo mostra como converter os modelos existentes na galeria do modelo para as especificações do modelo.

No portal, a funcionalidade a ser depreciada chama-se **Modelos (Pré-visualização)**. Para ver se tem algum modelo para converter, veja a [galeria do modelo no portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Estes modelos têm o tipo de `Microsoft.Gallery/myareas/galleryitems` recurso.

## <a name="deprecation-of-portal-feature"></a>Depreciação do recurso portal

A galeria de modelos do portal será depreterida no dia 21 de janeiro de 2021. Pode continuar a usá-lo até 21 de fevereiro. A partir de 22 de fevereiro, não é possível criar novos modelos na galeria do portal, mas ainda pode ver e implementar modelos existentes.

No dia 22 de junho, a funcionalidade será removida do portal e todas as operações da API serão bloqueadas. Não poderá visualizar ou implementar quaisquer modelos da galeria.

Antes de 22 de junho, deve migrar quaisquer modelos que pretenda continuar a utilizar. Pode utilizar um dos métodos mostrados neste artigo para migrar os modelos. Depois de a funcionalidade ter sido removida, terá de abrir um caso de suporte para obter quaisquer modelos que não tenha migrado.

## <a name="convert-with-powershell-script"></a>Converter com script PowerShell

Para simplificar os modelos de conversão na galeria de modelos, use um script PowerShell do repo de modelos Azure Quickstart. Ao executar o script, pode criar uma nova especificação de modelo para cada modelo ou descarregar um modelo que cria a especificação do modelo. O script não apaga o modelo da galeria do modelo.

1. Copie o roteiro de [migração.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1) Guarde uma cópia local com o nome *Migrate-GalleryItems.ps1*.
1. Para criar novas especificações de modelo, forneça valores para os `-ResourceGroupName` parâmetros e `-Location` parâmetros. 

   Configurar `ItemsToExport` `MyGalleryItems` para exportar os seus modelos. Desaça-o `AllGalleryItems` para exportar todos os modelos a que tenha acesso.

   O exemplo a seguir cria novas especificações de modelo para cada modelo num grupo de recursos chamado **migratedRG**. O script cria o grupo de recursos se não existir.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Para descarregar modelos que pode usar para criar as especificações do modelo, não forneça valores para o grupo de recursos ou localização. Em vez disso, especifique `-ExportToFile` . O modelo não é o mesmo que o seu modelo na galeria. Em vez disso, contém um recurso de especificação de modelo que cria a especificação do modelo para o seu modelo.

   O exemplo seguinte descarrega os modelos sem criar especificações de modelo.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Para aprender a implementar o modelo que cria a especificação do modelo, consulte [Quickstart: Criar e implementar especificações de modelo (Preview)](quickstart-create-template-specs.md).

Para obter mais informações sobre o script e seus parâmetros, consulte [Criar ModeloSpecs a partir de modelos de galeria de modelos.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)

## <a name="manually-convert-through-portal"></a>Converter manualmente através do portal

Pode copiar manualmente modelos da galeria para uma nova especificação do modelo.

1. Abra os [Modelos (pré-visualização)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) no portal.
1. Selecione o modelo para migrar.
1. Selecione **o modelo de visualização**.
1. Copie o conteúdo do modelo.
1. Na barra de pesquisa do portal, procure **as especificações do Modelo.** Selecione esta opção.
1. Selecione **Criar a especificação do modelo**.
1. Fornecer valores para o nome, subscrição, grupo de recursos, localização e versão.
1. Selecione **Seguinte: Editar o modelo**.
1. Para o conteúdo do modelo, cole o modelo que copiou da galeria do modelo.
1. Selecione **Rever + Criar**.
1. Após a validação concluída com sucesso, selecione **Criar**.

Se precisar de partilhar a especificação do modelo com outros utilizadores da sua organização, [desafete](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) o controlo de acesso baseado em funções ao grupo ou utilizadores que necessitem de acesso.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as especificações do modelo, consulte [Criar e implementar especificações de modelo.](template-specs.md)
