---
title: Criar um VHD compatível com Azure para o Mercado Azure
description: Explica como criar um VHD para uma oferta de máquina virtual no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 99d2bc95c1dd837bfc3bcabcead28777b7e6f746
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273941"
---
# <a name="create-an-azure-compatible-vhd"></a>Criar um VHD compatível com Azure

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da sua Máquina Virtual Azure ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create your Azure Virtual Machine ativos técnicos](https://aka.ms/AzureVMTechAsset) para gerir as suas ofertas migradas.

Este artigo detalha os passos necessários para criar um disco rígido virtual (VHD) para uma oferta de máquina virtual (VM) no Mercado Azure.  Também inclui as melhores práticas para vários aspetos, tais como a utilização do Protocolo de Ambiente de Trabalho Remoto (RDP), a seleção de um tamanho para o VM, a instalação das mais recentes atualizações do Windows e a generalização da imagem VHD.  As seguintes secções focam-se principalmente em VHDs baseados em janelas; para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [o Linux nas distribuições endossadas pelo Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Recomenda-se vivamente que siga a orientação deste tópico para utilizar o Azure para criar um VM contendo um sistema operativo pré-configurado e endossado.  Se isto não for compatível com a sua solução, então é possível criar e configurar um VM no local utilizando um sistema operativo aprovado.  Em seguida, pode configurá-lo e prepará-lo para upload conforme descrito em [Prepare um Windows VHD ou VHDX para fazer o upload para Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selecione uma base aprovada
O VHD do sistema operativo para a sua imagem VM deve basear-se numa imagem de base aprovada pelo Azure que contenha o Windows Server ou o SQL Server.
Para começar, crie um VM a partir de uma das seguintes imagens, localizada no portal Microsoft Azure:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Se estiver a utilizar o portal Atual Azure ou PowerShell, as imagens do Windows Server publicadas a 8 de setembro de 2014 e posteriormente são aprovadas.

Alternativamente, o Azure oferece uma gama de distribuições de Linux aprovadas.  Para obter uma lista atual, consulte [o Linux nas distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal Azure 

No portal Microsoft [Azure, crie](https://ms.portal.azure.com/)a imagem base utilizando os seguintes passos.

1. Faça sessão no portal com a conta Microsoft para a subscrição do Azure que pretende publicar a sua oferta VM.
2. Crie um novo grupo de recursos e forneça o nome do **grupo Recursos,** **Subscrição,** e localização do **grupo Derecursos.**  Para obter mais orientação, consulte [Gerir grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Clique em **máquinas virtuais** na barra de menus esquerda para mostrar a página de detalhes das máquinas virtuais. 
4. Nesta nova página, clique em **+Adicionar** para exibir a lâmina **Compute.**  Se não vir o tipo VM no ecrã inicial, pode procurar o nome do seu VM base, por exemplo:

    ![Lâmina de computação do novo VM](./media/publishvm_014.png)

5. Depois de selecionar a imagem virtual adequada, forneça os seguintes valores:
   * Na lâmina **Basics,** introduza um **nome** para a máquina virtual, entre 1-15 caracteres alfanuméricos. (Este exemplo `DemoVm009`usa .)
   * Introduza um **nome de utilizador** e uma **palavra-passe**forte, que são usadas para criar uma conta local no VM.  (Aqui `adminUser` é usado.)  A palavra-passe deve ter 8-123 caracteres de comprimento e satisfazer três dos quatro seguintes requisitos de complexidade: um personagem de minúscula, um personagem de maiúscula, um número e um personagem especial. Para mais informações, consulte os requisitos de [username e password](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Selecione o grupo `DemoResourceGroup`Derecursos que criou (aqui).
   * Selecione um **Local** azure datacenter (aqui `West US`).
   * Clique **em OK** para guardar estes valores. 

6. Selecione o tamanho do VM para implementar usando as seguintes recomendações:
   * Se planeia desenvolver o VHD no local, o tamanho não importa. Considere usar um dos VMs menores.
   * Se planeia desenvolver a imagem em Azure, considere utilizar um dos tamanhos vm recomendados para a imagem selecionada.
   * Para obter informações sobre preços, consulte o seletor de **preços recomendado** apresentado no portal. Apresentará os três tamanhos recomendados fornecidos pela editora. (Aqui, a editora é a Microsoft.)

   ![Lâmina de tamanho do novo VM](./media/publishvm_015.png)

7. Na lâmina **Definições,** desloque a opção **Use Managed Disk** para **No**.  Isto permite-lhe gerir manualmente o novo VHD. (A lâmina **Definições** também permite alterar outras alterações nas opções de armazenamento e rede, por exemplo, selecionando **Premium (SSD)** no **tipo disco**.)  Clique **em OK** para continuar.

    ![Lâmina de configurações de Novo VM](./media/publishvm_016.png)

8. Clique em **Resumo** para rever as opções. Quando vir a mensagem **A validação passou**, clique em **OK**.

    ![Lâmina sumária de Novo VM](./media/publishvm_017.png)

O Azure começa a fornecer a máquina virtual que especificou.  Pode acompanhar o seu progresso clicando no separador **Máquinas Virtuais** à esquerda.  Depois de criado, o estado mudará para **Running**.  Nessa altura, pode [ligar-se à máquina virtual.](./cpp-connect-vm.md)


## <a name="next-steps"></a>Passos seguintes

Se encontrou dificuldades em criar o seu novo VHD baseado em Azure, consulte [questões comuns durante a criação de VHD](./cpp-common-vhd-creation-issues.md).  Caso contrário, em seguida, deve [ligar-se aos VMs](./cpp-connect-vm.md) que criou no Azure. 
