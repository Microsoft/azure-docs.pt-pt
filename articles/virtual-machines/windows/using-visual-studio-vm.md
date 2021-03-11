---
title: Usando o Estúdio Visual em uma máquina virtual Azure
description: Utilização do Estúdio Visual numa máquina virtual Azure.
author: cathysull
manager: cathys
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: cathys
keywords: visuaistudio
ms.openlocfilehash: a6acf27743f17941e14adc3f6deea903f4f5ca87
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562083"
---
# <a name="visual-studio-images-on-azure"></a>Imagens do Estúdio Visual em Azure
Usar o Visual Studio numa máquina virtual Azure pré-configurada (VM) é uma maneira rápida e fácil de ir do nada para um ambiente de desenvolvimento em funcionamento. Imagens de sistema com diferentes configurações do Estúdio Visual estão disponíveis no [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Novo no Azure? [Crie uma conta Azure gratuita.](https://azure.microsoft.com/free)

> [!NOTE]
> Nem todas as subscrições são elegíveis para implementar imagens do Windows 10. Para mais informações consulte [o cliente do Windows em Azure para cenários dev/teste](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Que configurações e versões estão disponíveis?
As imagens para as versões mais recentes, Visual Studio 2019, Visual Studio 2017 e Visual Studio 2015, podem ser encontradas no Azure Marketplace.  Para cada versão principal lançada, vê a versão originalmente "lançada para web" (RTW) e as versões mais recentes atualizadas.  Cada uma destas versões oferece as edições Visual Studio Enterprise e Visual Studio Community.  Estas imagens são atualizadas pelo menos todos os meses para incluir as mais recentes atualizações do Visual Studio e Windows.  Embora os nomes das imagens permaneçam os mesmos, a descrição de cada imagem inclui a versão do produto instalado e a data "a partir" da imagem.

| Versão de lançamento                                                                                                                                                | Edições              | Versão do Produto   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Mais recente (Versão 16.8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Empresa, Comunidade | Versão 16.8.0    |
| Visual Studio 2019: RTW                         | Grandes Empresas | Versão 16.0.20    |
| Visual Studio 2017: Mais recente (Versão 15.9)           | Empresa, Comunidade | Versão 15.9.29   |
| Visual Studio 2017: RTW                             | Empresa, Comunidade | Versão 15.0.28  |
| Visual Studio 2015: Mais recente (Atualização 3)               | Empresa, Comunidade | Versão 14.0.25431.01 |

> [!NOTE]
> De acordo com a política de manutenção da Microsoft, a versão originalmente lançada (RTW) do Visual Studio 2015 expirou para manutenção. Visual Studio 2015 Update 3 é a única versão restante oferecida para a linha de produtos Visual Studio 2015.

Para mais informações, consulte a [Política de Manutenção do Estúdio Visual](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Quais as funcionalidades instaladas?
Cada imagem contém o conjunto de funcionalidades recomendado para a edição do Visual Studio. Geralmente, a instalação inclui:

* Todas as cargas de trabalho disponíveis, incluindo os componentes opcionais recomendados por cada carga de trabalho
* .NET 4.6.2 e .NET 4.7 SDKs, Pacotes de Destino e Ferramentas de Desenvolvimento
* Visual F #
* Extensão gitHub para estúdio visual
* Ferramentas LINQ para SQL

A linha de comando utilizada para instalar o Visual Studio ao construir as imagens é a seguinte:

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

Se as imagens não incluirem uma funcionalidade de Estúdio Visual que você necessita, forneça feedback através da ferramenta de feedback no canto superior direito da página.

## <a name="what-size-vm-should-i-choose"></a>Qual o tamanho da VM que devo escolher?
O Azure oferece uma gama completa de tamanhos de máquinas virtuais. Como o Visual Studio é uma aplicação potente e multi-roscada, você quer um tamanho VM que inclui pelo menos dois processadores e 7 GB de memória. Recomendamos os seguintes tamanhos VM para as imagens do Estúdio Visual:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Para obter mais informações sobre os tamanhos mais recentes da máquina, consulte [tamanhos para máquinas virtuais windows em Azure.](../sizes.md)

Com o Azure, pode reequilibrar a sua escolha inicial redimensionando o VM. Pode providenciar um novo VM com um tamanho mais adequado, ou redimensionar o seu VM existente para diferentes hardware subjacentes. Para obter mais informações, consulte [Redimensionar um VM do Windows](./resize-vm.md).

## <a name="after-the-vm-is-running-whats-next"></a>Depois do VM estar a funcionar, o que vem a seguir?
O Visual Studio segue o modelo "traga a sua própria licença" em Azure. Tal como acontece com uma instalação em hardware proprietário, um dos primeiros passos é licenciar a sua instalação visual Studio. Para desbloquear o Visual Studio, também:
- Inscreva-se com uma conta microsoft associada a uma subscrição do Visual Studio 
- Desbloqueie o Visual Studio com a chave do produto que veio com a sua compra inicial

Para mais informações, consulte [Iniciar s-seção no Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) e como desbloquear o Visual [Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Como guardo o VM de desenvolvimento para uso futuro ou de equipa?

O espectro de ambientes de desenvolvimento é enorme, e há um custo real associado à construção de ambientes mais complexos. Independentemente da configuração do seu ambiente, pode guardar ou capturar o seu VM configurado como uma "imagem base" para uso futuro ou para outros membros da sua equipa. Em seguida, ao iniciar um novo VM, você o forja a partir da imagem base em vez da imagem do Azure Marketplace.

Um resumo rápido: Utilize a ferramenta de preparação do sistema (Sysprep) e desligue o VM em funcionamento e, em seguida, capture *(Figura 1)* o VM como uma imagem através da UI no portal Azure. O Azure guarda o `.vhd` ficheiro que contém a imagem na conta de armazenamento da sua escolha. A nova imagem aparece então como um recurso de Imagem na lista de recursos da sua subscrição.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Capture uma imagem através do portal Azure UI.*</center>

Para obter mais informações, consulte [Criar uma imagem gerida de um VM generalizado em Azure.](./capture-image-resource.md)

> [!IMPORTANT]
> Não se esqueça de usar o Sysprep para preparar o VM. Se perderes esse passo, o Azure não pode providenciar um VM da imagem.

> [!NOTE]
> Ainda incorrem em algum custo de armazenamento das imagens, mas esse custo incremental pode ser insignificante em comparação com os custos gerais para reconstruir o VM de raiz para cada membro da equipa que precisa de um. Por exemplo, custa alguns dólares criar e armazenar uma imagem de 127 GB durante um mês que seja reutilizável por toda a sua equipa. No entanto, estes custos são insignificantes em comparação com as horas que cada colaborador investe para construir e validar uma caixa dev devidamente configurada para o seu uso individual.

Além disso, as suas tarefas ou tecnologias de desenvolvimento podem necessitar de mais escala, como variedades de configurações de desenvolvimento e múltiplas configurações de máquinas. Você pode usar Azure DevTest Labs para criar _receitas_ que automatizam a construção da sua "imagem dourada". Também pode usar a DevTest Labs para gerir políticas para os VMs da sua equipa. [Usar a Azure DevTest Labs para desenvolvedores](../../devtest-labs/devtest-lab-developer-lab.md) é a melhor fonte para mais informações sobre a DevTest Labs.

## <a name="next-steps"></a>Passos seguintes
Agora que sabe sobre as imagens pré-configuradas do Visual Studio, o próximo passo é criar um novo VM:

* [Criar um VM através do portal Azure](quick-create-portal.md)
* [Visão geral das máquinas virtuais do Windows](overview.md)