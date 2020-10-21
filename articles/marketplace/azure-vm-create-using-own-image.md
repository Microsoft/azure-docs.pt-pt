---
title: Crie uma oferta de máquina virtual Azure no Azure Marketplace usando a sua própria imagem
description: Aprenda a publicar uma oferta de máquina virtual ao Azure Marketplace usando a sua própria imagem.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284807"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Como criar uma máquina virtual usando a sua própria imagem

Este artigo descreve como criar e implementar uma imagem de máquina virtual (VM) fornecida pelo utilizador.

> [!NOTE]
> Antes de iniciar este procedimento, reveja os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) para as ofertas de VM Azure, incluindo os requisitos de disco rígido virtual (VHD).

Para utilizar uma imagem base aprovada, siga as instruções na [Criar uma imagem VM a partir de uma base aprovada](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurar a VM

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="size-the-vhds"></a>Tamanho dos VHDs

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Faça o upload do VHD para Azure

Configure e prepare o VM para ser carregado como descrito no [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ou [Criar e Carregar um Linux VHD](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Extrair o VHD da imagem (se utilizar serviços de construção de imagens)

Se estiver a utilizar um serviço de construção de imagens como [o Packer,](https://www.packer.io/)poderá ter de extrair o VHD da imagem. Não há forma direta de fazer isto. Terá de criar um VM e extrair o VHD do disco VM.

### <a name="create-the-vm-on-the-azure-portal"></a>Criar o VM no portal Azure

Siga estes passos para criar a imagem VM base no [portal Azure](https://ms.portal.azure.com/).

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. **Selecione + Adicione** para abrir o ecrã de máquina **virtual.**
4. Selecione a imagem da lista de dropdown ou **selecione Navegue em todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
5. Para criar um **Gen 2** VM, vá ao separador **Advanced** e selecione a opção **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho do VM para implementar.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione Gen 1 ou Gen 2.":::

7. Forneça os outros detalhes necessários para criar o VM.
8. Selecione **Review + crie** para rever as suas escolhas. Quando aparecer a mensagem **de validação,** selecione **Criar**.

O Azure começa a aturar a máquina virtual especificada. Acompanhe o seu progresso selecionando o separador **Máquinas Virtuais** no menu esquerdo. Depois de criado, o estado das alterações da Máquina Virtual para **executar**.

### <a name="connect-to-your-vm"></a>Ligar à VM

Consulte a seguinte documentação para ligar ao seu [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passos seguintes

- Passo seguinte recomendado: [Teste a sua imagem em VM](azure-vm-image-test.md) para garantir que satisfaz os requisitos de publicação do Azure Marketplace. Isto é opcional.
- Se não testar a sua imagem em VM, continue com [Generate the SAS URI](azure-vm-get-sas-uri.md).
- Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [a VM FAQ para o Azure Marketplace](azure-vm-create-faq.md).
