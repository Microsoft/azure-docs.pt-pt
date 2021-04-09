---
title: Utilize os runbooks e módulos da Azure Automation na PowerShell Gallery
description: Este artigo diz como usar livros e módulos da Microsoft e da comunidade na PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452786"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Utilizar runbooks e módulos na Galeria do PowerShell

Em vez de criar os seus próprios livros e módulos na Azure Automation, pode aceder a cenários que já foram construídos pela Microsoft e pela comunidade. Você pode obter livros e [módulos](#modules-in-powershell-gallery) PowerShell da PowerShell Gallery e [livros python](#use-python-runbooks) da organização Azure Automation GitHub. Também pode contribuir para a comunidade partilhando [cenários que desenvolve.](#add-a-powershell-runbook-to-the-gallery)

> [!NOTE]
> O Centro de Scripts TechNet vai reformar-se. Todos os guiões do Script Center na galeria Runbook foram transferidos para a nossa [organização Automation GitHub](https://github.com/azureautomation) Para mais informações, consulte [aqui.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="runbooks-in-powershell-gallery"></a>Runbooks na PowerShell Gallery

A [PowerShell Gallery](https://www.powershellgallery.com/packages) fornece uma variedade de runbooks da Microsoft e da comunidade que você pode importar para a Azure Automation. Para utilizar um, faça o download de um livro de contas da galeria, ou pode importar diretamente livros de execução da galeria ou da sua conta Automation no portal Azure.

> [!NOTE]
> Os livros gráficos não são suportados na PowerShell Gallery.

Só pode importar diretamente da Galeria PowerShell utilizando o portal Azure. Não é possível executar esta função utilizando o PowerShell.

> [!NOTE]
> Deve validar o conteúdo de quaisquer livros que obtenha da PowerShell Gallery e tenha muito cuidado na instalação e execução em ambiente de produção.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria PowerShell

Os módulos PowerShell contêm cmdlets que pode utilizar nos seus livros de execução. Os módulos existentes que pode instalar na Azure Automation estão disponíveis na [PowerShell Gallery.](https://www.powershellgallery.com) Pode lançar esta galeria a partir do portal Azure e instalar os módulos diretamente na Azure Automation, ou pode descarregar e instalar manualmente.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Cenários comuns disponíveis na PowerShell Gallery

A lista abaixo contém alguns livros que suportam cenários comuns. Para obter uma lista completa de runbooks criados pela equipa da Azure Automation, consulte o [perfil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importa a versão mais recente de todos os módulos numa conta de Automação da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Configura a Azure Diagnostics e o Log Analytics para receber registos de Automação Azure contendo o estado do trabalho e os fluxos de trabalho.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Copia um ficheiro remoto a partir de uma máquina virtual Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Copia um ficheiro local para uma máquina virtual Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importe um runbook PowerShell da galeria de runbook com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
1. Selecione **a galeria Runbooks** em **Process Automation**.
1. **Selecione Fonte: PowerShell Gallery**. Isto mostra uma lista de livros disponíveis que pode navegar.
1. Pode utilizar a caixa de pesquisa acima da lista para estreitar a lista, ou pode utilizar os filtros para estreitar o ecrã por editor, tipo e classificação. Localize o item da galeria que deseja e selecione-o para ver os seus detalhes.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Navegando na galeria de runbook." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Para importar um item, clique em **Importar** na lâmina de detalhes.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Mostre um detalhe de item de galeria de runbook." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Opcionalmente, mude o nome do livro de bordo e, em seguida, clique **em OK** para importar o runbook.
1. O livro de execuções aparece no **separador Runbooks** para a conta Automação.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Importe um runbook PowerShell do GitHub com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
1. Selecione **a galeria Runbooks** em **Process Automation**.
1. **Selecione Fonte: GitHub**.
1. Pode utilizar os filtros acima da lista para reduzir o ecrã por editor, tipo e classificação. Localize o item da galeria que deseja e selecione-o para ver os seus detalhes.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Navegando na galeria GitHub." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Para importar um item, clique em **Importar** na lâmina de detalhes.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Vista detalhada de um livro de bordo da galeria GitHub." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Opcionalmente, mude o nome do livro de bordo e, em seguida, clique **em OK** para importar o runbook.
1. O livro de execuções aparece no **separador Runbooks** para a conta Automação.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Adicione um livro de corrida PowerShell à galeria

A Microsoft encoraja-o a adicionar livros de execução à PowerShell Gallery que pensa que seriam úteis a outros clientes. A PowerShell Gallery aceita módulos PowerShell e scripts PowerShell. Pode adicionar um livro de execução [enviando-o para a PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importe um módulo da galeria do módulo com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
1. Selecione **Módulos** em **Recursos Partilhados** para abrir a lista de módulos.
1. Clique **na galeria Browse** a partir do topo da página.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vista para a galeria do módulo." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Na página da galeria Browse, pode utilizar a caixa de pesquisa para encontrar fósforos em qualquer um dos seguintes campos:

   * Nome do Módulo
   * Etiquetas
   * Autor
   * Nome do recurso Cmdlet/DSC

1. Localize um módulo em que esteja interessado e selecione-o para ver os seus detalhes.

   Quando perfurar um módulo específico, pode visualizar mais informações. Esta informação inclui uma ligação de volta à PowerShell Gallery, quaisquer dependências necessárias, e todos os cmdlets ou recursos DSC que o módulo contém.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Vista detalhada de um módulo da galeria." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Para instalar o módulo diretamente na Azure Automation, clique em **Importar**.
1. No painel de importação, pode ver o nome do módulo para importar. Se todas as dependências estiverem instaladas, o botão **OK** é ativado. Se está a perder dependências, tem de importar essas dependências antes de poder importar este módulo.
1. No painel de importação, clique **em OK** para importar o módulo. Enquanto a Azure Automation importa um módulo para a sua conta, extrai metadados sobre o módulo e os cmdlets. Esta ação pode demorar alguns minutos, uma vez que cada atividade precisa de ser extraída.
1. Recebe uma notificação inicial de que o módulo está a ser implantado e outra notificação quando tiver terminado.
1. Depois de importado o módulo, pode ver as atividades disponíveis. Pode utilizar recursos de módulos nos seus livros de execução e recursos DSC.

> [!NOTE]
> Os módulos que suportam apenas o núcleo PowerShell não são suportados na Azure Automation e não podem ser importados no portal Azure, nem implantados diretamente a partir da Galeria PowerShell.

## <a name="use-python-runbooks"></a>Use livros de python

Os Python Runbooks estão disponíveis na [organização Azure Automation GitHub.](https://github.com/azureautomation) Quando você contribuir para o nosso repo GitHub, adicione a etiqueta **(GitHub Topic) : Python3** quando você carregar a sua contribuição.

## <a name="request-a-runbook-or-module"></a>Solicite um livro de execução ou módulo

Pode enviar pedidos para a [Voz do Utilizador.](https://feedback.azure.com/forums/246290-azure-automation/)  Se precisar de ajuda para escrever um livro de aplicação ou tiver uma pergunta sobre o PowerShell, publique uma pergunta na nossa [página de perguntas microsoft Q&A](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Passos seguintes

* Para começar com um livro de execução PowerShell, consulte [Tutorial: Crie um livro de execução PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para trabalhar com runbooks, consulte [Gerir os runbooks na Azure Automation.](manage-runbooks.md)
* Para mais detalhes sobre o PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
