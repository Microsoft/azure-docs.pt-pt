---
title: Criar um VHD compatível com o Azure para o Azure Marketplace
description: Explica como criar um VHD para uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: pabutler
ms.openlocfilehash: 37fecb8100ec40ace02960a4f3390420a8bfc735
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816808"
---
# <a name="create-an-azure-compatible-vhd"></a>Criar um VHD compatível com o Azure

Este artigo detalha as etapas necessárias para criar um VHD (disco rígido virtual) para uma oferta de VM (máquina virtual) no Azure Marketplace.  Ele também inclui as práticas recomendadas para vários aspectos, como usar o protocolo RDP (RDP), selecionar um tamanho para a VM, instalar as atualizações mais recentes do Windows e generalizar a imagem VHD.  As seções a seguir se concentram principalmente em VHDs baseados no Windows; para obter mais informações sobre como criar VHDs baseados em Linux, consulte [Linux em distribuições endossadas pelo Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> É altamente recomendável que você siga as diretrizes neste tópico para usar o Azure para criar uma VM que contém um sistema operacional previamente configurado e endossado.  Se isso não for compatível com sua solução, será possível criar e configurar uma VM local usando um sistema operacional aprovado.  Em seguida, você pode configurar e prepará-lo para upload, conforme descrito em [preparar um VHD do Windows ou VHDX para carregar no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selecionar uma base aprovada
O VHD do sistema operacional para sua imagem de VM deve ser baseado em uma imagem base aprovada pelo Azure que contém o Windows Server ou SQL Server.
Para começar, crie uma VM com base em uma das imagens a seguir, localizadas na portal do Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Se você estiver usando o portal do Azure atual ou o PowerShell, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriores serão aprovadas.

Como alternativa, o Azure oferece uma variedade de distribuições do Linux aprovadas.  Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure 

No Microsoft [portal do Azure](https://ms.portal.azure.com/), crie a imagem base usando as etapas a seguir.

1. Entre no portal com o conta Microsoft para a assinatura do Azure na qual você deseja publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça o **nome do grupo de recursos**, a **assinatura**e o local do **grupo de recursos**.  Para obter mais diretrizes, consulte [gerenciar grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Clique em **máquinas virtuais** na barra de menus à esquerda para exibir a página de detalhes de máquinas virtuais. 
4. Nessa nova página, clique em **+ Adicionar** para exibir a folha **computação** .  Se você não vir o tipo de VM na tela inicial, poderá pesquisar o nome da VM de base, por exemplo:

    ![Folha de computação da nova VM](./media/publishvm_014.png)

5. Depois de selecionar a imagem virtual apropriada, forneça os seguintes valores:
   * Na folha **noções básicas** , insira um **nome** para a máquina virtual, entre 1-15 caracteres alfanuméricos. (Este exemplo usa `DemoVm009`.)
   * Insira um **nome de usuário** e uma **senha**forte, que são usados para criar uma conta local na VM.  (Aqui `adminUser` é usado.)  A senha deve ter de 3 a 8-123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Para obter mais informações, consulte [requisitos de nome de usuário e senha](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Selecione o grupo de recursos que você criou (aqui `DemoResourceGroup`).
   * Selecione um **local** de datacenter do Azure (aqui `West US`).
   * Clique em **OK** para salvar esses valores. 

6. Selecione o tamanho da VM a ser implantado usando as seguintes recomendações:
   * Se você planeja desenvolver o VHD local, o tamanho não importa. Pondere utilizar uma das VMs mais pequenas.
   * Se tenciona desenvolver a imagem no Azure, pondere utilizar um dos tamanhos de VM recomendados para a imagem selecionada.
   * Para obter informações sobre preços, consulte o seletor de **tipos de preço recomendado** exibido no Portal. Ele exibirá os três tamanhos recomendados fornecidos pelo Publicador. (Aqui, o Publicador é a Microsoft.)

   ![Folha de tamanho da nova VM](./media/publishvm_015.png)

7. Na folha **configurações** , defina a opção **usar disco gerenciado** como **não**.  Isso permite que você gerencie manualmente o novo VHD. (A folha **configurações** também permite que você altere outra alteração nas opções de armazenamento e rede, por exemplo, selecionando **Premium (SSD)** em **tipo de disco**.)  Clique em **OK** para continuar.

    ![Folha configurações da nova VM](./media/publishvm_016.png)

8. Clique em **Resumo** para rever as opções. Quando vir a mensagem **A validação passou**, clique em **OK**.

    ![Folha de resumo da nova VM](./media/publishvm_017.png)

O Azure começa o provisionamento da máquina virtual que você especificou.  Você pode acompanhar seu progresso clicando na guia **máquinas virtuais** à esquerda.  Depois de criado, o status será alterado para **em execução**.  Nesse ponto, você pode [se conectar à máquina virtual](./cpp-connect-vm.md).


## <a name="next-steps"></a>Passos seguintes

Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [problemas comuns durante a criação do VHD](./cpp-common-vhd-creation-issues.md).  Caso contrário, em seguida, você deve [se conectar às VMs](./cpp-connect-vm.md) que criou no Azure. 
