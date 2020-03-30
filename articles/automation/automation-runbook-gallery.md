---
title: Runbook and module galleries for Azure Automation (Galerias de runbooks e módulos para a Automatização do Azure)
description: Os livros e módulos da Microsoft e da comunidade estão disponíveis para que possa instalar e utilizar no seu ambiente De Automação Azure.  Este artigo descreve como pode aceder a estes recursos e contribuir com os seus livros para a galeria.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421492"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook and module galleries for Azure Automation (Galerias de runbooks e módulos para a Automatização do Azure)

Em vez de criar os seus próprios livros e módulos na Azure Automation, pode aceder a cenários que já foram construídos pela Microsoft e pela comunidade.

Você pode obter livros e [módulos](#modules-in-powershell-gallery) PowerShell da PowerShell Gallery e livros de [execução Python](#python-runbooks) da Galeria Script Center. Você também pode contribuir para a comunidade partilhando cenários que você desenvolve, ver Adicionar um livro de corridas à galeria

## <a name="runbooks-in-powershell-gallery"></a>Livros de execução na Galeria PowerShell

A [PowerShell Gallery](https://www.powershellgallery.com/packages) fornece uma variedade de livros de execução da Microsoft e da comunidade que você pode importar para a Automação Azure. Para utilizar um, faça o download de um livro de execução da galeria, ou pode importar diretamente livros de execução a partir da galeria, ou da sua Conta de Automação no portal Azure.

Só é possível importar diretamente a partir da Galeria PowerShell utilizando o portal Azure. Não é possível executar esta função utilizando o PowerShell.

> [!NOTE]
> Deve validar o conteúdo de quaisquer livros de execução que obtenha da Galeria PowerShell e ter extrema cautela na instalação e execução num ambiente de produção.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um livro de execução powerShell da Galeria Runbook com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Under **Process Automation**, clique na galeria **Runbooks**
3. Selecione **Fonte: PowerShell Gallery**.
4. Localize o item da galeria que deseja e selecione-o para ver os seus detalhes. À esquerda, pode introduzir parâmetros de pesquisa adicionais para o editor e escrever.

   ![Galeria de Procura](media/automation-runbook-gallery/browse-gallery.png)

5. Clique no **projeto De origem do Ver** para ver o item no [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para ver os seus detalhes e, em seguida, clique no botão **Import.**

   ![Botão de importação](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do livro de execução e, em seguida, clique em **OK** para importar o livro de execução.
8. O livro de execução aparece no separador **'Livros de Execução'** para a Conta de Automação.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Adicionar um livro de corridas PowerShell à galeria

A Microsoft encoraja-o a adicionar livros de execução à PowerShell Gallery que pensa que seria útil para outros clientes. A PowerShell Gallery aceita módulos PowerShell e scripts PowerShell. Pode adicionar um livro de reprodução [enviando-o para a Galeria PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Os livros gráficos não são suportados na PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria PowerShell

Os módulos PowerShell contêm cmdlets que pode utilizar nos seus livros de execução, e os módulos existentes que pode instalar na Azure Automation estão disponíveis na [PowerShell Gallery](https://www.powershellgallery.com). Pode lançar esta galeria a partir do portal Azure e instalá-las diretamente na Automação Azure. Também pode descarregá-los e instalá-los manualmente.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo da Galeria do Módulo de Automação com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Módulos** em **Recursos Partilhados** para abrir a lista de módulos.
3. Clique na **galeria Browse** a partir do topo da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página da **galeria Browse,** pode pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Etiquetas
   * Autor
   * Nome de recurso Cmdlet/DSC

5. Localize um módulo que lhe interesse e selecione-o para ver os seus detalhes.

   Quando perfurar um módulo específico, pode ver mais informações. Estas informações incluem um link de volta à PowerShell Gallery, quaisquer dependências necessárias, e todos os recursos cmdlets ou DSC que o módulo contém.

   ![Detalhes do módulo PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na Automatização Azure, clique no botão **Import.**
7. Quando clicar no botão Import, no painel **import,** vê o nome do módulo que está prestes a importar. Se todas as dependências estiverem instaladas, o botão **OK** é ativado. Se está sem dependências, precisa importar essas dependências antes de importar este módulo.
8. Na página **import,** **clique** ok para importar o módulo. Enquanto a Azure Automation importa um módulo para a sua conta, extrai metadados sobre o módulo e os cmdlets. Esta ação pode demorar alguns minutos, uma vez que cada atividade precisa de ser extraída.
9. Recebe uma notificação inicial de que o módulo está a ser implantado e outra notificação quando este tiver sido concluído.
10. Depois de importado o módulo, pode ver as atividades disponíveis. Pode utilizar os seus recursos nos seus livros de execução e configuração de Estado Desejada.

> [!NOTE]
> Os módulos que suportam apenas o núcleo PowerShell não são suportados na Automação Azure e não podem ser importados no portal Azure, ou implantados diretamente a partir da PowerShell Gallery.

## <a name="python-runbooks"></a>Livros de Execução Python

Os Livros de Execução Python estão disponíveis na galeria do [Script Center.](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=) Pode contribuir com os livros de execução python para a galeria do Script Center clicando em **Carregar uma contribuição**. Quando o fizer, certifique-se de que adiciona a etiqueta **Python** ao fazer o upload da sua contribuição.

> [!NOTE]
> Para fazer o upload do conteúdo para script [center](https://gallery.technet.microsoft.com/scriptcenter) é necessário um mínimo de 100 pontos.

## <a name="requesting-a-runbook-or-module"></a>Solicitando um livro ou módulo

Pode enviar pedidos para [a Voz do Utilizador](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um livro de corridas ou tiver uma pergunta sobre o PowerShell, poste uma pergunta no nosso [fórum.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Soluções comuns disponíveis na galeria runbook

A lista abaixo contém alguns livros que fornecem soluções para cenários comuns. Para obter uma lista completa de livros de execução criados pela equipa azure Automation, consulte o [perfil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importa a versão mais recente na PowerShell Gallery de todos os módulos numa conta Automation.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Este script configura o Azure Diagnostics e o Log Analytics para receber registos de Automação Azure contendo o estado do trabalho e fluxos de trabalho.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Este livro de execução copia um ficheiro remoto de uma máquina virtual Windows Azure.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Este livro de execução copia um ficheiro local para uma máquina virtual Azure.

## <a name="next-steps"></a>Passos seguintes

* Para começar com livros de corridas, consulte Gerir o livro de [corridas na Automação Azure](manage-runbooks.md)
* Para compreender as diferenças entre powerShell e PowerShell Workflow com livros de execução, consulte [Learning PowerShell workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
