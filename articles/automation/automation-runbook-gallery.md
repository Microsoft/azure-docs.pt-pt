---
title: Galerias de runbook e módulo para a automação do Azure
description: Os Runbooks e os módulos da Microsoft e da Comunidade estão disponíveis para você instalar e usar em seu ambiente de automação do Azure.  Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a galeria.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ef739a7053fffdd45c6d5e144ae736429597a4f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850500"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de runbook e módulo para a automação do Azure

Em vez de criar seus próprios runbooks e módulos na automação do Azure, você pode acessar cenários que já foram criados pela Microsoft e pela Comunidade.

Você pode obter runbooks e [módulos](#modules-in-powershell-gallery) do PowerShell dos runbooks Galeria do PowerShell e [Python](#python-runbooks) na galeria do script Center. Você também pode contribuir para a Comunidade compartilhando cenários que você desenvolve, consulte Adicionando um runbook à Galeria

## <a name="runbooks-in-powershell-gallery"></a>Runbooks em Galeria do PowerShell

O [Galeria do PowerShell](https://www.powershellgallery.com/packages) fornece uma variedade de Runbooks da Microsoft e da Comunidade que você pode importar para a automação do Azure. Para usar um, baixe um runbook da galeria ou você pode importar runbooks diretamente da galeria ou da sua conta de automação no portal do Azure.

Você só pode importar diretamente do Galeria do PowerShell usando o portal do Azure. Você não pode executar essa função usando o PowerShell.

> [!NOTE]
> Você deve validar o conteúdo de todos os runbooks obtidos da Galeria do PowerShell e ter muito cuidado ao instalá-los e executá-los em um ambiente de produção.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um runbook do PowerShell da Galeria de Runbooks com o portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Em **automação de processo**, clique em **Galeria de Runbooks**
3. Selecionar **origem: Galeria do PowerShell**.
4. Localize o item da Galeria desejado e selecione-o para exibir seus detalhes. À esquerda, você pode inserir parâmetros de pesquisa adicionais para o Publicador e o tipo.

   ![Procurar na Galeria](media/automation-runbook-gallery/browse-gallery.png)

5. Clique em **Exibir projeto de origem** para exibir o item no [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para exibir seus detalhes e, em seguida, clique no botão **importar** .

   ![Botão importar](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
8. O runbook aparece na guia **Runbooks** da conta de automação.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Adicionando um runbook do PowerShell à Galeria

A Microsoft incentiva você a adicionar runbooks ao Galeria do PowerShell que você considera úteis para outros clientes. O Galeria do PowerShell aceita módulos do PowerShell e scripts do PowerShell. Você pode adicionar um runbook [carregando-o no Galeria do PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Não há suporte para runbooks gráficos no Galeria do PowerShell.

## <a name="modules-in-powershell-gallery"></a>Módulos no Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que você pode instalar na automação do Azure estão disponíveis no [Galeria do PowerShell](https://www.powershellgallery.com). Você pode iniciar essa Galeria por meio do portal do Azure e instalá-los diretamente na automação do Azure. Você também pode baixá-los e instalá-los manualmente.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo da Galeria de módulos de automação com o portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **módulos** em **recursos compartilhados** para abrir a lista de módulos.
3. Clique em **procurar na Galeria** na parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página **procurar na Galeria** , você pode pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Tags
   * Autor
   * Nome do recurso de cmdlet/DSC

5. Localize um módulo no qual você esteja interessado e selecione-o para exibir seus detalhes.

   Ao analisar um módulo específico, você pode exibir mais informações. Essas informações incluem um link de volta para o Galeria do PowerShell, todas as dependências necessárias e todos os cmdlets ou recursos de DSC que o módulo contém.

   ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na automação do Azure, clique no botão **importar** .
7. Ao clicar no botão importar, no painel **importar** , você verá o nome do módulo que está prestes a importar. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Se você não tiver dependências, será necessário importar essas dependências antes de importar este módulo.
8. Na página **importar** , clique em **OK** para importar o módulo. Enquanto a automação do Azure importa um módulo para sua conta, ela extrai metadados sobre o módulo e os cmdlets. Essa ação pode levar alguns minutos, pois cada atividade precisa ser extraída.
9. Você recebe uma notificação inicial de que o módulo está sendo implantado e outra notificação quando ele é concluído.
10. Depois que o módulo for importado, você poderá ver as atividades disponíveis. Você pode usar seus recursos em seus runbooks e a configuração de estado desejado.

> [!NOTE]
> Os módulos que dão suporte apenas ao PowerShell Core não têm suporte na automação do Azure e não podem ser importados no portal do Azure ou implantados diretamente do Galeria do PowerShell.

## <a name="python-runbooks"></a>Runbooks do Python

Os Runbooks do Python estão disponíveis na [Galeria do script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Você pode contribuir com os runbooks do Python para a galeria do script Center clicando em **carregar uma contribuição**. Ao fazer isso, certifique-se de adicionar a marca **Python** ao carregar sua contribuição.

> [!NOTE]
> Para carregar conteúdo no [Script Center](https://gallery.technet.microsoft.com/scriptcenter) , é necessário um mínimo de 100 pontos.

## <a name="requesting-a-runbook-or-module"></a>Solicitando um runbook ou módulo

Você pode enviar solicitações para o [usuário Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um runbook ou tiver uma pergunta sobre o PowerShell, poste uma pergunta em nosso [Fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passos Seguintes

* Para começar a usar runbooks, consulte [gerenciar runbook na automação do Azure](manage-runbooks.md)
* Para entender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell com runbooks, consulte [Learning PowerShell Workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
