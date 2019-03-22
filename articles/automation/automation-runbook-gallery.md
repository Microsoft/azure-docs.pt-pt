---
title: Galerias de módulos e Runbooks de automatização do Azure
description: Runbooks e módulos da Microsoft e da Comunidade estão disponíveis para instalar e utilizar no seu ambiente de automatização do Azure.  Este artigo descreve como pode aceder a estes recursos e para contribuir com os runbooks na galeria.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 58f666d7ebf8ac02d393a42f55e00f08d82b8cae
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337093"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de módulos e Runbooks de automatização do Azure

Em vez de criar seus próprios runbooks e módulos na automatização do Azure, pode aceder a cenários que já foram criados pela Microsoft e pela Comunidade.

Pode obter [runbooks do PowerShell](#runbooks-in-runbook-gallery) e [módulos](#modules-in-powerShell-gallery) da galeria do PowerShell e [runbooks de Python](#python-runbooks) partir da galeria do Script Center. Também pode contribuir para a Comunidade por meio do compartilhamento de cenários que desenvolver, consulte [adicionar um runbook na Galeria](#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-powershell-gallery"></a>Runbooks na galeria do PowerShell

O [galeria do PowerShell](https://www.powershellgallery.com/packages) fornece uma variedade de runbooks da Microsoft e da Comunidade que pode ser importado para a automatização do Azure. Para utilizar um, transferir um runbook a partir da galeria ou diretamente pode importar runbooks da galeria, ou a partir de sua conta de automatização no portal do Azure.

Só é possível importar diretamente a partir da galeria do PowerShell com o portal do Azure. Não é possível executar esta função através do PowerShell.

> [!NOTE]
> Deve validar os conteúdos de todos os runbooks que obter a partir da galeria do PowerShell e que tenha muito cuidado na instalação e executá-los num ambiente de produção.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um runbook do PowerShell a partir da Galeria de Runbooks no portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Sob **automatização de processos**, clique em **Galeria de Runbooks**
3. Selecione **origem: Galeria do PowerShell**.
4. Localize o item de galeria que pretende e selecione-o para ver os respectivos detalhes. À esquerda, pode introduzir parâmetros adicionais de pesquisa para o publicador e o tipo.

   ![Procurar Galeria](media/automation-runbook-gallery/browse-gallery.png)

5. Clique em **projeto de código do modo de exibição** para ver o item no [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique na mesma para ver os detalhes e, em seguida, clique nas **importar** botão.

   ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do runbook e, em seguida, clique em **OK** para importar o runbook.
8. O runbook é apresentado no **Runbooks** separador para a conta de automatização.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Adicionar um runbook do PowerShell para a Galeria

A Microsoft incentiva a adicionar runbooks para a galeria do PowerShell que acredita que seria útil para outros clientes. A galeria do PowerShell aceita módulos do PowerShell e scripts do PowerShell. Pode adicionar um runbook pelo [carregá-lo para a galeria do PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Os runbooks gráficos não são suportados na galeria do PowerShell.

## <a name="modules-in-powershell-gallery"></a>Módulos na galeria do PowerShell

Módulos do PowerShell contêm cmdlets que podem ser utilizados nos runbooks e módulos existentes que podem instalar na automatização do Azure estão disponíveis no [galeria do PowerShell](https://www.powershellgallery.com). Pode iniciar esta galeria a partir do portal do Azure e instalá-los diretamente na automatização do Azure. Também pode transferi-los e instalá-los manualmente.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo a partir da galeria do módulo de automatização no portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **módulos** sob **recursos partilhados** para abrir a lista de módulos.
3. Clique em **Galeria de procura** da parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Sobre o **Galeria de procura** página, pode pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Etiquetas
   * Autor
   * Nome do cmdlet/DSC de recursos

5. Localize um módulo que está interessado e selecioná-lo para ver os detalhes.  

   Quando é fazer uma busca num módulo específico, pode ver mais informações. Estas informações incluem uma ligação para a galeria do PowerShell, necessários dependências e todos os cmdlets ou recursos de DSC que contém o módulo.

   ![Detalhes de módulos do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na automatização do Azure, clique nas **importação** botão.
7. Se clicar no botão de importação, o **importar** painel, verá o nome do módulo que está prestes a importar. Se estiverem instaladas todas as dependências, o **OK** botão é ativado. Se não tem dependências, terá de importar essas dependências antes de pode importar este módulo.
8. Sobre o **importe** página, clique em **OK** para importar o módulo. Embora a automatização do Azure importa um módulo para a sua conta, extrai metadados sobre o módulo e os cmdlets. Esta ação pode demorar alguns minutos, uma vez que cada atividade tem de ser extraídos.
9. Receberá uma notificação inicial que o módulo está a ser implementado e outra notificação se foi concluída.
10. Após a importação do módulo, pode ver as atividades disponíveis. Pode usar seus recursos nos seus runbooks e de Desired State Configuration.

> [!NOTE]
> Módulos que só suportam o PowerShell core não são suportados na automatização do Azure e não conseguem ser importados no portal do Azure ou implementado diretamente a partir da galeria do PowerShell.

## <a name="python-runbooks"></a>Python Runbooks

Runbooks de Python estão disponíveis no [Galeria de centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Pode contribuir com runbooks de Python para a Galeria de centro de scripts clicando **carregue uma contribuição**. Quando, certifique-se de que adicionar a marca **Python** ao carregar a sua contribuição.

> [!NOTE]
> Para carregar conteúdo para [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter) é necessário um mínimo de 100 pontos. 

## <a name="requesting-a-runbook-or-module"></a>Pedir um runbook ou o módulo

Pode enviar pedidos para [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda com a escrita de um runbook ou tem uma pergunta sobre o PowerShell, postar uma pergunta para nosso [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks, consulte [gerir runbook na automatização do Azure](manage-runbooks.md)
* Para compreender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell com os runbooks, consulte [fluxo de trabalho do PowerShell de aprendizagem](automation-powershell-workflow.md)