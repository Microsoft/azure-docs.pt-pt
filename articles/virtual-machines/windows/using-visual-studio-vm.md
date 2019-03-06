---
title: Com o Visual Studio numa máquina virtual do Azure | Documentos da Microsoft
description: Com o Visual Studio numa máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 02/19/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: b1b47b5353340da9c1d36d8d2035a6c9f5963d80
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449704"
---
# <a name="visual-studio-images-on-azure"></a>Imagens do Visual Studio no Azure
Com o Visual Studio numa máquina de virtual do Azure (VM) pré-configurada é uma maneira rápida e fácil para ir de nada para um ambiente de desenvolvimento de segurança e execução. Imagens do sistema com configurações diferentes do Visual Studio estão disponíveis no [do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=visual%20studio&page=1).

Novo no Azure? [Crie uma conta gratuita](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Quais versões e configurações estão disponíveis?
Imagens para as versões mais recentes principais para Visual Studio 2017 e o Visual Studio 2015, podem ser encontradas no Azure Marketplace.  Recentemente, adicionámos suporte para pré-visualizações a próxima versão principal - Visual Studio 2019.  Para cada versão de lançamento principais, consulte a versão (RTW) originalmente lançada e as mais recentes versões atualizadas.  Cada uma dessas versões oferece o Visual Studio Enterprise e as edições do Visual Studio Community.  Estas imagens são atualizadas, pelo menos, todos os meses para incluir as atualizações mais recentes do Visual Studio e do Windows.  Enquanto os nomes das imagens permanecem os mesmos, a descrição de cada imagem inclui a versão de produto instalado e "a partir de" data da imagem.

| Versão de lançamento                                              | Edições                     |     Versão do produto      |
|:------------------------------------------------------------:|:----------------------------:|:------------------------:|
|    Visual Studio 2019: Pré-visualização (pré-visualização 3)                   |           Enterprise         | Pré-visualização de versão 16.0.0 3 |
| Visual Studio 2017: Versão mais recente (versão 15.9)                    |    Enterprise, da Comunidade     |      Versão 15.9.7      |
|         Visual Studio 2017: RTW                              |    Enterprise, da Comunidade     |      Versão 15.0.20     |
|   Visual Studio 2015: Versão mais recente (atualização 3)                      |    Enterprise, da Comunidade     |  Versão 14.0.25431.01   |
|         Visual Studio 2015: RTW                              |             Nenhuma             | (Expirou para a manutenção)  |

> [!NOTE]
> Em conformidade com a Microsoft a política de manutenção, a versão de (RTW) originalmente lançamento do Visual Studio 2015 expirou para a manutenção. Visual Studio 2015 Update 3 é a única versão restante oferecida para a linha de produtos do Visual Studio 2015.

Para obter mais informações, consulte a [política de manutenção de Studio Visual](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Que funcionalidades estão instaladas?
Cada imagem contém o conjunto para que essa edição do Visual Studio de recursos recomendados. Em geral, a instalação inclui:

* Todas as cargas de trabalho disponíveis, incluindo cada carga de trabalho recomendado componentes opcionais
* O .NET 4.6.2 e o .NET 4.7 SDKs, pacotes de destino e ferramentas de programador
* VisualF#
* Extensão do GitHub para o Visual Studio
* LINQ to SQL ferramentas

Linha de comandos utilizada para instalar o Visual Studio ao criar as imagens é o seguinte:

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

Se as imagens não incluem um recurso do Visual Studio que necessita, fornece comentários por meio da ferramenta de comentários no canto superior direito da página.

## <a name="what-size-vm-should-i-choose"></a>Qual tamanho de VM devo escolher?
O Azure oferece uma grande variedade de tamanhos de máquinas virtuais. Como o Visual Studio é um aplicativo multithread poderosas, quer um tamanho de VM que inclui, pelo menos, dois processadores e 7 GB de memória. Recomendamos os seguintes tamanhos VM para as imagens do Visual Studio:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Para obter mais informações sobre os tamanhos de máquina mais recente, consulte [máquinas de virtuais de tamanhos para Windows no Azure](/azure/virtual-machines/windows/sizes).

Com o Azure, podem reequilibrar à sua escolha inicial ao redimensionar a VM. Pode aprovisionar uma VM nova com um tamanho mais apropriado, ou redimensionar a sua VM existente para um hardware subjacente diferente. Para obter mais informações, consulte [redimensionar uma VM do Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Depois da VM está em execução, o que se segue?
Visual Studio segue o modelo "traga a sua própria licença" no Azure. Tal como acontece com uma instalação de hardware do proprietário, uma das primeiras etapas é o licenciamento a instalação do Visual Studio. Para desbloquear o Visual Studio, optar por:
- Inicie sessão com uma conta Microsoft associada uma subscrição do Visual Studio 
- Desbloquear o Visual Studio com a chave de produto fornecida com a compra inicial

Para obter mais informações, consulte [inicie sessão no Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) e [como desbloquear o Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Como posso salvar o desenvolvimento de VM para futuro ou equipa usar?

O espectro de ambientes de desenvolvimento é enorme e há real custo associado ao criar os ambientes mais complexos. Independentemente da configuração do seu ambiente, pode guardar ou capturar, a VM configurada como uma "imagem base" para utilização futura ou para outros membros da sua equipa. Em seguida, quando se reinicia uma nova VM, aprovisioná-lo da imagem base, em vez da imagem do Azure Marketplace.

Um resumo rápido: Utilize a ferramenta de preparação do sistema (Sysprep) e encerre a VM em execução e, em seguida, capturar *(figura 1)* a VM como uma imagem através da IU no portal do Azure. Azure poupa o `.vhd` ficheiro que contém a imagem na conta de armazenamento à sua escolha. A nova imagem, em seguida, mostrado como um recurso de imagem na lista da sua subscrição de recursos.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Capture uma imagem através do portal do Azure da interface do Usuário.*</center>

Para obter mais informações, consulte [criar uma imagem gerida de uma VM generalizada no Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Não se esqueça de utilizar o Sysprep para preparar a VM. Se perder esse passo, o Azure não pode aprovisionar uma VM a partir da imagem.

> [!NOTE]
> Continuam a incorrer em custos de armazenamento das imagens, mas que o custo incremental pode ser insignificante em comparação com os custos gerais para recriar a VM a partir do zero para cada membro da Equipe que dela precise. Por exemplo, custa alguns dólares para criar e armazenar uma imagem de 127 GB durante um mês, que é reutilizável por toda a equipa. No entanto, esses custos são insignificantes em comparação com horas que cada funcionário investe para criar e validar uma caixa de programação corretamente configurado para utilização individual.

Além disso, as tarefas de desenvolvimento ou tecnologias poderão ter mais de escala, como uma variedade de configurações de desenvolvimento e várias configurações de máquina. Pode utilizar o Azure DevTest Labs para criar _receitas_ que automatizar a construção de seu "imagem dourada". Também pode utilizar o DevTest Labs para gerir políticas para VMs em execução de sua equipe. [Utilizar o Azure DevTest Labs para programadores](/azure/devtest-lab/devtest-lab-developer-lab) é a melhor fonte para obter mais informações sobre o DevTest Labs.

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe sobre as imagens pré-configuradas do Visual Studio, a próxima etapa é criar uma nova VM:

* [Criar uma VM através do portal do Azure](quick-create-portal.md)
* [Descrição geral das máquinas virtuais do Windows](overview.md)
