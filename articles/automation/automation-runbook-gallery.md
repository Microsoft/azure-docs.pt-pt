---
title: Utilize os runbooks e módulos da Azure Automation na PowerShell Gallery
description: Este artigo diz como usar livros e módulos da Microsoft GitHub e da PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030684"
---
# <a name="use-existing-runbooks-and-modules"></a>Utilize livros e módulos existentes

Em vez de criar os seus próprios livros e módulos na Azure Automation, pode aceder a cenários que já foram construídos pela Microsoft e pela comunidade. Você pode obter livros powershell e Python relacionados com Azure na Galeria Runbook no portal Azure, e [módulos](#modules-in-the-powershell-gallery) e [runbooks](#runbooks-in-the-powershell-gallery) (que podem ou não ser específicos de Azure) da PowerShell Gallery. Também pode contribuir para a comunidade partilhando [cenários que desenvolve.](#contribute-to-the-community)

> [!NOTE]
> O Centro de Scripts TechNet vai reformar-se. Todos os guiões do Script Center na galeria Runbook foram transferidos para a nossa [organização Automation GitHub](https://github.com/azureautomation) Para obter mais informações, consulte [os Runbooks da Automação Azure que se deslocam para o GitHub.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importar runbooks do GitHub com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **a galeria Runbooks** em **Process Automation**.
3. **Selecione Fonte: GitHub**.
4. Pode utilizar os filtros acima da lista para reduzir o ecrã por editor, tipo e classificação. Localize o item da galeria que deseja e selecione-o para ver os seus detalhes.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Galeria de runbook de navegação." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Para importar um item, clique em **Importar** na página de detalhes.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Importação de artigos de galeria.":::

6. Opcionalmente, altere o nome do livro de recortes na lâmina de importação e, em seguida, clique **em OK** para importar o livro de recortes.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Lâmina de importação de artigo de galeria.":::

7. O livro de execuções aparece no **separador Runbooks** para a conta Automação.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooks na Galeria PowerShell

> [!IMPORTANT]
> Deve validar o conteúdo de quaisquer livros que obtenha na Galeria PowerShell. Tenha muito cuidado ao instalá-los e acordá-los num ambiente de produção.

A [PowerShell Gallery](https://www.powershellgallery.com/packages) fornece vários runbooks da Microsoft e da comunidade que pode importar para a Azure Automation. Para utilizar um, faça o download de um livro de contas da galeria, ou pode importar diretamente livros de execução da galeria ou da sua conta Automation no portal Azure.

> [!NOTE]
> Os livros gráficos não são suportados na PowerShell Gallery.

Só pode importar diretamente da Galeria PowerShell utilizando o portal Azure. Não é possível executar esta função utilizando o PowerShell. O procedimento é o mesmo que mostrado nos [livros de importação do GitHub com o portal Azure,](#import-runbooks-from-github-with-the-azure-portal)exceto que a **Fonte** será **a PowerShell Gallery**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Mostrando a seleção de fontes de galeria de runbook." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Módulos na Galeria PowerShell

Os módulos PowerShell contêm cmdlets que pode utilizar nos seus livros de execução. Os módulos existentes que pode instalar na Azure Automation estão disponíveis na [PowerShell Gallery.](https://www.powershellgallery.com) Pode lançar esta galeria a partir do portal Azure e instalar os módulos diretamente na Azure Automation, ou pode descarregar e instalar manualmente.

Também pode encontrar módulos para importar no portal Azure. Estão listados para a sua Conta de Automação na **galeria Módulos** com **recursos partilhados.**

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Cenários comuns disponíveis na Galeria PowerShell

A lista abaixo contém alguns livros que suportam cenários comuns. Para obter uma lista completa de runbooks criados pela equipa da Azure Automation, consulte o [perfil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importa a versão mais recente de todos os módulos numa conta de Automação da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Configura a Azure Diagnostics e o Log Analytics para receber registos de Automação Azure contendo o estado do trabalho e os fluxos de trabalho.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Copia um ficheiro remoto a partir de uma máquina virtual Windows Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Copia um ficheiro local para uma máquina virtual Azure.

## <a name="contribute-to-the-community"></a>Contribuir para a comunidade

Encorajamo-lo fortemente a contribuir e a ajudar a crescer a comunidade Azure Automation. Partilhe os incríveis livros que construiu com a comunidade. Suas contribuições serão apreciadas!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Adicione um livro de corridas à galeria GitHub Runbook

Pode adicionar novos livros powerShell ou Python à galeria Runbook com este fluxo de trabalho GitHub.

1. Crie um repositório público no GitHub e adicione o livro de recortes e quaisquer outros ficheiros necessários (como readme.md, descrição, e assim por diante).
1. Adicione o tópico `azureautomationrunbookgallery` para garantir que o repositório é descoberto pelo nosso serviço, para que possa ser exibido na galeria Automation Runbook.
1. Se o livro de execução que criou for um fluxo de trabalho PowerShell, adicione o tópico `PowerShellWorkflow` . Se for um livro python 3, `Python3` adicione. Não são necessários outros tópicos específicos para os runbooks do Azure, mas encorajamo-lo a adicionar outros tópicos que podem ser usados para categorização e pesquisa na Galeria Runbook.

   >[!NOTE]
   >Consulte os livros existentes na galeria para ver coisas como formatação, cabeçalhos e etiquetas existentes que poderá utilizar (como `Azure Automation` ou). `Linux Azure Virtual Machines`

Para sugerir alterações a um livro de execução existente, apresente um pedido de retirada contra ele. 

Se decidir clonar e editar um livro de execução existente, a melhor prática é dar-lhe um nome diferente. Se reutilizar o nome antigo, aparecerá duas vezes na listagem da galeria Runbook.

>[!NOTE]
>Por favor, permita pelo menos 12 horas para sincronização entre o GitHub e a Automation Runbook Gallery, tanto para novos livros atualizados como para novos.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Adicione um livro de execução PowerShell à galeria PowerShell

A Microsoft encoraja-o a adicionar livros de execução à PowerShell Gallery que pensa que seriam úteis a outros clientes. A PowerShell Gallery aceita módulos PowerShell e scripts PowerShell. Pode adicionar um livro de execução [enviando-o para a PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importar um módulo da galeria módulos no portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
1. Em **Recursos Partilhados,** selecione a galeria **de Módulos** para abrir a lista de módulos.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vista para a galeria do módulo." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Na página da galeria Browse, pode pesquisar pelos seguintes campos:

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

## <a name="request-a-runbook-or-module"></a>Solicite um livro de execução ou módulo

Pode enviar pedidos para a [Voz do Utilizador.](https://feedback.azure.com/forums/246290-azure-automation/)  Se precisar de ajuda para escrever um livro de aplicação ou tiver uma pergunta sobre o PowerShell, publique uma pergunta na nossa [página de perguntas microsoft Q&A](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Passos seguintes

* Para começar com os livros powerShell, consulte [Tutorial: Crie um livro de execução PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para trabalhar com runbooks, consulte [Gerir os runbooks na Azure Automation.](manage-runbooks.md)
* Para obter mais informações sobre scripts PowerShell, consulte [PowerShell Docs](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
