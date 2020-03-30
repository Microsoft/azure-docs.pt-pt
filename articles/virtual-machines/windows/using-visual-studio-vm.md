---
title: Usando o Estúdio Visual numa máquina virtual Azure
description: Usando o Estúdio Visual numa máquina virtual Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: estúdio visual
ms.openlocfilehash: 2977fb6f14468429eb651e4f8a6034c060cdee2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895952"
---
# <a name="visual-studio-images-on-azure"></a>Imagens do Estúdio Visual no Azure
Usar o Visual Studio numa máquina virtual Azure (VM) reconfigurada é uma maneira rápida e fácil de passar do nada para um ambiente de desenvolvimento em alta e funcionamento. Imagens do sistema com diferentes configurações do Estúdio Visual estão disponíveis no [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Novo no Azure? [Crie uma conta Azure gratuita.](https://azure.microsoft.com/free)

> [!NOTE]
> Nem todas as subscrições são elegíveis para implementar imagens do Windows 10. Para mais informações consulte [Use Windows cliente em Azure para cenários de dev/teste](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Que configurações e versões estão disponíveis?
As imagens das mais recentes versões principais, Visual Studio 2019, Visual Studio 2017 e Visual Studio 2015, podem ser encontradas no Azure Marketplace.  Para cada versão principal lançada, vê a versão originalmente "lançada para web" (RTW) e as versões mais recentes atualizadas.  Cada uma destas versões oferece as edições Visual Studio Enterprise e Visual Studio Community.  Estas imagens são atualizadas pelo menos todos os meses para incluir as mais recentes atualizações do Visual Studio e Windows.  Embora os nomes das imagens permaneçam os mesmos, a descrição de cada imagem inclui a versão do produto instalada e a data "a partir" da imagem.

| Versão de lançamento                                                                                                                                                | Edições              | Versão do Produto   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Estúdio Visual 2019: Mais recente (Versão 16.4)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, Comunidade | Versão 16.4.0    |
| [Estúdio Visual 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Enterprise, Comunidade | Versão 16.0.9    |
| [Estúdio Visual 2017: Mais recente (Versão 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Enterprise, Comunidade | Versão 15.9.17   |
| [Estúdio Visual 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Enterprise, Comunidade | Versão 15.0.27  |
| [Estúdio Visual 2015: Última (Atualização 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Enterprise, Comunidade | Versão 14.0.25431.01 |

> [!NOTE]
> De acordo com a política de manutenção da Microsoft, a versão originalmente lançada (RTW) do Visual Studio 2015 expirou para ser servida. Visual Studio 2015 Update 3 é a única versão restante oferecida para a linha de produtos Visual Studio 2015.

Para mais informações, consulte a [Política de Manutenção do Estúdio Visual.](https://www.visualstudio.com/productinfo/vs-servicing-vs)

## <a name="what-features-are-installed"></a>Quais as funcionalidades instaladas?
Cada imagem contém o conjunto de funcionalidades recomendado para a edição do Estúdio Visual. Geralmente, a instalação inclui:

* Todas as cargas de trabalho disponíveis, incluindo os componentes opcionais recomendados de cada carga de trabalho
* .NET 4.6.2 e .NET 4.7 SDKs, Pacotes de Segmentação e Ferramentas de Desenvolvimento
* Visual F #
* Extensão GitHub para estúdio visual
* Ferramentas LINQ para SQL

A linha de comando utilizada para instalar o Estúdio Visual ao construir as imagens é a seguinte:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Se as imagens não incluirem uma funcionalidade do Estúdio Visual que você precisa, forneça feedback através da ferramenta de feedback no canto superior direito da página.

## <a name="what-size-vm-should-i-choose"></a>Que tamanho vM devo escolher?
O Azure oferece uma gama completa de tamanhos de máquinas virtuais. Como o Visual Studio é uma aplicação poderosa e multi-threaded, você quer um tamanho VM que inclui pelo menos dois processadores e 7 GB de memória. Recomendamos os seguintes tamanhos VM para as imagens do Estúdio Visual:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Para obter mais informações sobre os tamanhos mais recentes da máquina, consulte [Sizes para máquinas virtuais Windows em Azure](/azure/virtual-machines/windows/sizes).

Com o Azure, pode reequilibrar a sua escolha inicial redimensionando o VM. Pode fornecer um novo VM com um tamanho mais adequado, ou redimensionar o seu VM existente para diferentes hardware subjacentes. Para mais informações, consulte [Redimensionar um VM do Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Depois do VM estar a funcionar, o que vem a seguir?
A Visual Studio segue o modelo "traga a sua própria licença" em Azure. Tal como acontece com uma instalação em hardware proprietário, um dos primeiros passos é licenciar a instalação do Estúdio Visual. Para desbloquear o Estúdio Visual, também:
- Inscreva-se numa conta microsoft que esteja associada a uma subscrição do Visual Studio 
- Desbloqueie o Visual Studio com a chave do produto que veio com a sua compra inicial

Para mais informações, consulte [O Estúdio Visual](/visualstudio/ide/signing-in-to-visual-studio) e Como desbloquear o Estúdio [Visual](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Como posso guardar o desenvolvimento vm para uso futuro ou em equipa?

O espectro de ambientes de desenvolvimento é enorme, e há um custo real associado à construção de ambientes mais complexos. Independentemente da configuração do seu ambiente, pode guardar ou capturar o seu VM configurado como uma "imagem base" para uso futuro ou para outros membros da sua equipa. Depois, ao iniciar um novo VM, você o provisão da imagem base em vez da imagem azure Marketplace.

Um resumo rápido: Utilize a ferramenta de preparação do sistema (Sysprep) e desligue o VM em execução e, em seguida, capture *(Figura 1)* o VM como uma imagem através da UI no portal Azure. O Azure `.vhd` guarda o ficheiro que contém a imagem na conta de armazenamento à sua escolha. A nova imagem aparece então como um recurso image na lista de recursos da sua subscrição.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Capture uma imagem através do portal Azure UI.*</center>

Para mais informações, consulte [Criar uma imagem gerida de um VM generalizado em Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Não se esqueça de usar sysprep para preparar o VM. Se perder esse passo, o Azure não pode fornecer um VM da imagem.

> [!NOTE]
> Ainda incorre em algum custo para o armazenamento das imagens, mas esse custo incremental pode ser insignificante em comparação com os custos gerais para reconstruir o VM do zero para cada membro da equipa que precisa de um. Por exemplo, custa alguns dólares criar e armazenar uma imagem de 127 GB por um mês que é reutilizável por toda a sua equipa. No entanto, estes custos são insignificantes em comparação com as horas que cada colaborador investe para construir e validar uma caixa dev devidamente configurada para a sua utilização individual.

Além disso, as suas tarefas ou tecnologias de desenvolvimento podem necessitar de mais escala, como variedades de configurações de desenvolvimento e configurações múltiplas de máquinas. Você pode usar Azure DevTest Labs para criar _receitas_ que automatizam a construção da sua "imagem dourada". Também pode usar o DevTest Labs para gerir as políticas para os VMs da sua equipa. [Usar o Azure DevTest Labs para desenvolvedores](/azure/devtest-lab/devtest-lab-developer-lab) é a melhor fonte para mais informações sobre os Laboratórios DevTest.

## <a name="next-steps"></a>Passos seguintes
Agora que sabe sobre as imagens do Estúdio Visual reconfigurados, o próximo passo é criar um novo VM:

* [Crie um VM através do portal Azure](quick-create-portal.md)
* [Visão geral das máquinas virtuais do Windows](overview.md)
