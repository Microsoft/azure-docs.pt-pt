---
title: Criar um VHD compatível com o Azure para o Azure Marketplace | Documentos da Microsoft
description: Explica como criar um VHD para uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: bd085804f532811e71d421e3f683c50c1c3416cb
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575487"
---
# <a name="create-an-azure-compatible-vhd"></a>Criar um VHD compatível com o Azure

Este artigo descreve os passos necessários para criar um disco rígido virtual (VHD) para uma oferta de máquina virtual (VM) no Azure Marketplace.  Ele também inclui práticas recomendadas para diferentes aspetos, como utilizar o protocolo RDP (Remote Desktop), selecionar um tamanho para a VM, instalar as atualizações mais recentes do Windows e generalizar a imagem VHD.  As secções seguintes concentrar-se principalmente em VHDs baseados no windows; Para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [Linux em distribuições apoiadas pelo Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Recomenda-se vivamente que siga as orientações neste tópico para utilizar o Azure para criar uma VM que contém um sistema de operativo previamente configurado, apoiado.  Se isso não é compatível com a sua solução, em seguida, é possível criar e configurar uma VM no local com um sistema de operativo aprovado.  Em seguida, pode configurar e prepará-lo para carregamento, conforme descrito em [preparar um VHD do Windows ou o VHDX para carregar para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selecione uma base aprovada
O sistema operativo VHD da imagem de VM deve basear-se numa imagem base aprovada do Azure que contém o Windows Server ou SQL Server.
Para começar, crie uma VM a partir de um dos seguintes imagens, localizadas no portal do Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Se estiver a utilizar o portal do Azure atual ou o PowerShell, as imagens do Windows Server publicadas a 8 de Setembro de 2014 e versões posteriores são aprovadas.

Em alternativa, o Azure oferece uma gama de distribuições do Linux aprovadas.  Para obter uma lista atual, consulte [Linux em distribuições apoiadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure 

No Microsoft [portal do Azure](https://ms.portal.azure.com/), criar a imagem base através dos seguintes passos.

1. Inicie sessão no portal com a conta Microsoft para a subscrição do Azure que pretende publicar a sua oferta VM.
2. Criar um novo grupo de recursos e fornecer sua **nome do grupo de recursos**, **subscrição**, e **localização do grupo de recursos**.  Para obter mais orientações, veja [gerir grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Clique em **máquinas virtuais** na barra de menus esquerda para exibir a página de detalhes de máquinas virtuais. 
4. Nesta página nova, clique em **+ adicionar** para apresentar o **computação** painel.  Se não vir o tipo de VM na tela inicial, pode procurar o nome da sua VM base, por exemplo:

    ![Painel de VM nova computação](./media/publishvm_014.png)

5. Depois de selecionar a imagem virtual adequada, forneça os valores seguintes:
  * Sobre o **Noções básicas** painel, introduza um **nome** para a máquina virtual, entre 1 e 15 carateres de alfanuméricos. (Este exemplo utiliza `DemoVm009`.)
  * Introduza um **nome de utilizador** e uma forte **palavra-passe**, que é utilizado para criar uma conta local na VM.  (Aqui `adminUser` é usado.)  A palavra-passe tem de ter entre 8 e 123 carateres e cumprir três dos quatro requisitos de complexidade seguintes: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Para obter mais informações, consulte [requisitos de nome de utilizador e palavra-passe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
  * Selecione o grupo de recursos que criou (aqui `DemoResourceGroup`).
  * Selecione um Datacenter do Azure **localização** (aqui `West US`).
  * Clique em **OK** para guardar estes valores. 

6.  Selecione o tamanho da VM para implementar com as seguintes recomendações:
  * Se tenciona desenvolver o VHD no local, o tamanho não é relevante. Pondere utilizar uma das VMs mais pequenas.
  * Se tenciona desenvolver a imagem no Azure, pondere utilizar um dos tamanhos de VM recomendados para a imagem selecionada.
  * Para obter informações sobre preços, consulte a **escalões de preço recomendados** Seletor apresentado no portal. Isso exibirá os três tamanhos recomendados fornecidos pelo editor. (Aqui, o publicador é Microsoft.)

    ![Painel de tamanho de VM nova](./media/publishvm_015.png)

7. Na **definições** painel, definir o **disco gerido de utilização** a opção de **não**.  Isto permite-lhe gerir manualmente o novo VHD. (A **configurações** painel também lhe permite alterar a outra alteração as rede opções de armazenamento e, por exemplo, selecionando **Premium (SSD)** na **tipo de disco**.)  Clique em **OK** para continuar.

    ![Painel de definições de VM nova](./media/publishvm_016.png)

8. Clique em **Resumo** para rever as opções. Quando vir a mensagem **A validação passou**, clique em **OK**.

    ![Painel de resumo de VM nova](./media/publishvm_017.png)

Azure começa o aprovisionamento da máquina virtual que especificou.  Pode acompanhar o progresso clicando em **máquinas virtuais** separador no lado esquerdo.  Depois de criado, o estado será alterado para **em execução**.  Nesse ponto, pode [ligar à máquina virtual](./cpp-connect-vm.md).


## <a name="next-steps"></a>Passos Seguintes

Se tiver encontrado a dificuldade de criar o seu novo VHD baseado no Azure, veja [problemas comuns durante a criação do VHD](./cpp-common-vhd-creation-issues.md).  Caso contrário, em seguida, tem [ligar às VMs](./cpp-connect-vm.md) que criou no Azure. 
