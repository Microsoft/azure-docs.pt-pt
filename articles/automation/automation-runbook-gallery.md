---
title: Utilize livros e módulos de automação Azure na Galeria PowerShell
description: Este artigo diz como usar livros e módulos da Microsoft e da comunidade na PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: f1d60c19b05de218ab985b2087071733602c7f2c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743894"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Use livros e módulos na Galeria PowerShell

Em vez de criar os seus próprios livros e módulos na Azure Automation, pode aceder a cenários que já foram construídos pela Microsoft e pela comunidade. Você pode obter livros e [módulos](#modules-in-powershell-gallery) PowerShell da PowerShell Gallery e livros de [execução Python](#use-python-runbooks) da Galeria Script Center. Também pode contribuir para a comunidade partilhando [cenários que desenvolve.](#add-a-powershell-runbook-to-the-gallery) 

## <a name="runbooks-in-powershell-gallery"></a>Livros de execução na Galeria PowerShell

A [PowerShell Gallery](https://www.powershellgallery.com/packages) fornece uma variedade de livros de execução da Microsoft e da comunidade que você pode importar para a Automação Azure. Para utilizar um, faça o download de um livro de execução da galeria, ou pode importar diretamente livros de execução a partir da galeria, ou da sua conta De automação no portal Azure.

> [!NOTE]
> Os livros gráficos não são suportados na PowerShell Gallery.

Só é possível importar diretamente a partir da Galeria PowerShell utilizando o portal Azure. Não é possível executar esta função utilizando o PowerShell.

> [!NOTE]
> Deve validar o conteúdo de quaisquer livros de execução que obtenha da Galeria PowerShell e ter extrema cautela na instalação e execução num ambiente de produção.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria PowerShell

Os módulos PowerShell contêm cmdlets que pode utilizar nos seus livros de execução, e os módulos existentes que pode instalar na Azure Automation estão disponíveis na [PowerShell Gallery](https://www.powershellgallery.com). Pode lançar esta galeria a partir do portal Azure e instalá-las diretamente na Automação Azure. Também pode descarregá-los e instalá-los manualmente.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Cenários comuns disponíveis na Galeria PowerShell

A lista abaixo contém alguns livros que suportam cenários comuns. Para obter uma lista completa de livros de execução criados pela equipa azure Automation, consulte o [perfil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importa a versão mais recente de todos os módulos numa conta de Automação da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Configures Azure Diagnostics e Log Analytics para receber registos de Automação Azure contendo o estado do trabalho e fluxos de trabalho.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Copia um ficheiro remoto de uma máquina virtual Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Copia um ficheiro local para uma máquina virtual Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importar um livro de corridas PowerShell da galeria runbook com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **galeria Runbooks** em **Process Automation**.
3. Selecione **Fonte: PowerShell Gallery**.
4. Localize o item da galeria que deseja e selecione-o para ver os seus detalhes. À esquerda, pode introduzir parâmetros de pesquisa adicionais para o editor e escrever.

   ![Galeria de Procura](media/automation-runbook-gallery/browse-gallery.png)

5. Clique no **projeto De origem do Ver** para ver o item no [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para ver os seus dados e, em seguida, clique **em Importar**.

   ![Botão de importação](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do livro de execução e, em seguida, clique em **OK** para importar o livro de execução.
8. O livro de execução aparece no separador **Runbooks** para a conta Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Adicione um livro de corridas PowerShell à galeria

A Microsoft encoraja-o a adicionar livros de execução à PowerShell Gallery que pensa que seria útil para outros clientes. A PowerShell Gallery aceita módulos PowerShell e scripts PowerShell. Pode adicionar um livro de reprodução [enviando-o para a Galeria PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importar um módulo da galeria do módulo com o portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Módulos** em **Recursos Partilhados** para abrir a lista de módulos.
3. Clique na **galeria Browse** a partir do topo da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página da galeria Browse, pode pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Etiquetas
   * Autor
   * Nome de recurso Cmdlet/DSC

5. Localize um módulo que lhe interesse e selecione-o para ver os seus detalhes.

   Quando perfurar um módulo específico, pode ver mais informações. Estas informações incluem um link de volta à PowerShell Gallery, quaisquer dependências necessárias, e todos os recursos cmdlets ou DSC que o módulo contém.

   ![Detalhes do módulo PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na Automatização Azure, clique **em Importar**.
7. No painel de importação, pode ver o nome do módulo para importar. Se todas as dependências estiverem instaladas, o botão **OK** é ativado. Se está sem dependências, precisa importar essas dependências antes de importar este módulo.
8. No painel de importação, clique **ok** para importar o módulo. Enquanto a Azure Automation importa um módulo para a sua conta, extrai metadados sobre o módulo e os cmdlets. Esta ação pode demorar alguns minutos, uma vez que cada atividade precisa de ser extraída.
9. Recebe uma notificação inicial de que o módulo está a ser implantado e outra notificação quando este tiver sido concluído.
10. Depois de importado o módulo, pode ver as atividades disponíveis. Pode utilizar recursos de módulos nos seus livros de execução e recursos DSC.

> [!NOTE]
> Os módulos que suportam apenas o núcleo PowerShell não são suportados na Automação Azure e não podem ser importados no portal Azure, ou implantados diretamente a partir da PowerShell Gallery.

## <a name="use-python-runbooks"></a>Use livros de corridas Python

Os Livros de Execução Python estão disponíveis na galeria do [Script Center.](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=) Pode contribuir com os livros de execução python para a galeria do Script Center clicando em **Carregar uma contribuição**. Quando o fizer, certifique-se de que adiciona a etiqueta `Python` ao fazer o upload da sua contribuição.

> [!NOTE]
> Para fazer o upload de conteúdo para [script center,](https://gallery.technet.microsoft.com/scriptcenter)precisa de um mínimo de 100 pontos.

## <a name="request-a-runbook-or-module"></a>Solicite um livro de rés-do-que rés-do-mesmo ou

Pode enviar pedidos para [a Voz do Utilizador](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um livro de corridas ou tiver uma pergunta sobre o PowerShell, poste uma pergunta no nosso [fórum.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="next-steps"></a>Passos seguintes

* [Gerir livros de corridas na Automação Azure](manage-runbooks.md)
* [Conheça o fluxo de trabalho powerShell para a automação azure](automation-powershell-workflow.md)
* [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview)
