---
title: Usando o Visual Studio em uma máquina virtual do Azure
description: Usando o Visual Studio em uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 09/24/2019
ms.author: phillee
keywords: VisualStudio
ms.openlocfilehash: 4b3b36f69387a6ae10a2b76154056f322fd4a41a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170921"
---
# <a name="visual-studio-images-on-azure"></a>Imagens do Visual Studio no Azure
Usar o Visual Studio em uma VM (máquina virtual) do Azure pré-configurada é uma maneira rápida e fácil de passar de nada para um ambiente de desenvolvimento ativo e em execução. As imagens do sistema com diferentes configurações do Visual Studio estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Novo no Azure? [Crie uma conta gratuita](https://azure.microsoft.com/free).

> [!NOTE]
> Nem todas as assinaturas estão qualificadas para implantar imagens do Windows 10. Para obter mais informações, consulte [usar o cliente do Windows no Azure para cenários de desenvolvimento/teste](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Quais configurações e versões estão disponíveis?
As imagens para as versões principais mais recentes, o Visual Studio 2019, o Visual Studio 2017 e o Visual Studio 2015, podem ser encontradas no Azure Marketplace.  Para cada versão principal lançada, você verá a versão originalmente "lançada para a Web" (RTW) e as versões atualizadas mais recentes.  Cada uma dessas versões oferece a Visual Studio Enterprise e as edições da Comunidade do Visual Studio.  Essas imagens são atualizadas pelo menos a cada mês para incluir as atualizações mais recentes do Visual Studio e do Windows.  Embora os nomes das imagens permaneçam os mesmos, a descrição de cada imagem inclui a versão do produto instalada e a data "a partir de" da imagem.

| Versão de lançamento                                                                                                                                                | Edições              | Versão do produto   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: mais recente (versão 16,3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, comunidade | 16.3.9 da versão    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Enterprise, comunidade | 16.0.9 da versão    |
| [Visual Studio 2017: mais recente (versão 15,9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Enterprise, comunidade | 15.9.17 da versão   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Enterprise, comunidade | 15.0.27 da versão  |
| [Visual Studio 2015: mais recente (atualização 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Enterprise, comunidade | 14.0.25431.01 da versão |

> [!NOTE]
> De acordo com a política de serviço da Microsoft, a versão lançada originalmente (RTW) do Visual Studio 2015 expirou para manutenção. O Visual Studio 2015 atualização 3 é a única versão restante oferecida para a linha de produtos Visual Studio 2015.

Para obter mais informações, consulte a [política de serviço do Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Quais recursos estão instalados?
Cada imagem contém o conjunto de recursos recomendado para a edição do Visual Studio. Em geral, a instalação inclui:

* Todas as cargas de trabalho disponíveis, incluindo os componentes opcionais recomendados de cada carga de trabalho
* SDKs .NET 4.6.2 e .NET 4,7, pacotes de direcionamento e Ferramentas para Desenvolvedores
* VisualizarF#
* Extensão do GitHub para Visual Studio
* Ferramentas de LINQ to SQL

A linha de comando usada para instalar o Visual Studio ao criar as imagens é a seguinte:

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

Se as imagens não incluírem um recurso do Visual Studio que você precisa, forneça comentários por meio da ferramenta de comentários no canto superior direito da página.

## <a name="what-size-vm-should-i-choose"></a>Qual tamanho de VM devo escolher?
O Azure oferece uma gama completa de tamanhos de máquina virtual. Como o Visual Studio é um aplicativo multithread e poderoso, você deseja um tamanho de VM que inclua pelo menos dois processadores e 7 GB de memória. Recomendamos os seguintes tamanhos de VM para as imagens do Visual Studio:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Para obter mais informações sobre os tamanhos de máquina mais recentes, consulte [tamanhos de máquinas virtuais do Windows no Azure](/azure/virtual-machines/windows/sizes).

Com o Azure, você pode reequilibrar sua escolha inicial redimensionando a VM. Você pode provisionar uma nova VM com um tamanho mais apropriado ou redimensionar sua VM existente para um hardware subjacente diferente. Para obter mais informações, consulte [redimensionar uma VM do Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Depois que a VM estiver em execução, o que vem a seguir?
O Visual Studio segue o modelo "Traga sua própria licença" no Azure. Como com uma instalação no hardware proprietário, uma das primeiras etapas é licenciar a instalação do Visual Studio. Para desbloquear o Visual Studio, seja:
- Entre com um conta Microsoft associado a uma assinatura do Visual Studio 
- Desbloquear o Visual Studio com a chave do produto que veio com sua compra inicial

Para obter mais informações, consulte [entrar no Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) e [como desbloquear o Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Como fazer salvar a VM de desenvolvimento para uso futuro ou de equipe?

O espectro de ambientes de desenvolvimento é enorme, e há um custo real associado à criação de ambientes mais complexos. Independentemente da configuração do seu ambiente, você pode salvar ou capturar sua VM configurada como uma "imagem base" para uso futuro ou para outros membros da sua equipe. Em seguida, ao inicializar uma nova VM, você a provisiona a partir da imagem base em vez da imagem do Azure Marketplace.

Um resumo rápido: Use a ferramenta de preparação do sistema (Sysprep) e desligue a VM em execução e, em seguida, Capture *(Figura 1)* a VM como uma imagem por meio da interface do usuário no portal do Azure. O Azure salva o arquivo de `.vhd` que contém a imagem na conta de armazenamento de sua escolha. A nova imagem é exibida como um recurso de imagem na lista de recursos da sua assinatura.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center> *(Figura 1) Capture uma imagem por meio da interface do usuário do portal do Azure.* </center>

Para obter mais informações, consulte [criar uma imagem gerenciada de uma VM generalizada no Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Não se esqueça de usar o Sysprep para preparar a VM. Se você perder essa etapa, o Azure não poderá provisionar uma VM a partir da imagem.

> [!NOTE]
> Você ainda tem algum custo para o armazenamento das imagens, mas esse custo incremental pode ser insignificante em comparação com os custos de sobrecarga para recriar a VM do zero para cada membro da equipe que precisa de uma. Por exemplo, ele custa alguns dólares para criar e armazenar uma imagem de 127 GB por um mês que é reutilizável por toda a sua equipe. No entanto, esses custos são insignificantes em comparação com as horas que cada funcionário investe para criar e validar uma caixa de desenvolvimento configurada corretamente para uso individual.

Além disso, suas tarefas ou tecnologias de desenvolvimento podem precisar de mais escala, como variedades de configurações de desenvolvimento e várias configurações de computador. Você pode usar Azure DevTest Labs para criar _receitas_ que automatizam a construção de sua "imagem dourada". Você também pode usar o DevTest Labs para gerenciar políticas para as VMs em execução da sua equipe. [Usar Azure DevTest Labs para desenvolvedores](/azure/devtest-lab/devtest-lab-developer-lab) é a melhor fonte para obter mais informações sobre o DevTest Labs.

## <a name="next-steps"></a>Passos seguintes
Agora que você conhece as imagens pré-configuradas do Visual Studio, a próxima etapa é criar uma nova VM:

* [Criar uma VM por meio do portal do Azure](quick-create-portal.md)
* [Visão geral de Máquinas Virtuais do Windows](overview.md)
