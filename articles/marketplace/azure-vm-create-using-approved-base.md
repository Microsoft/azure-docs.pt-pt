---
title: Criar uma oferta de máquina virtual Azure (VM) a partir de uma base aprovada, Azure Marketplace
description: Saiba como criar uma oferta de máquina virtual (VM) a partir de uma base aprovada.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 94d21cb82290e59ebb003969a566c1bfc877713e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200450"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Como criar uma máquina virtual usando uma base aprovada

Este artigo descreve como usar o Azure para criar uma máquina virtual (VM) contendo um sistema operativo pré-configurado e endossado. Se isto não for compatível com a sua solução, é possível [criar e configurar um VM no local utilizando](azure-vm-create-using-own-image.md) um sistema operativo aprovado.

> [!NOTE]
> Antes de iniciar este procedimento, reveja os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) para as ofertas de VM Azure, incluindo os requisitos de disco rígido virtual (VHD).

## <a name="select-an-approved-base-image"></a>Selecione uma imagem base aprovada

Selecione uma das seguintes imagens Windows ou Linux como base.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

A Azure oferece uma gama de distribuições linux aprovadas. Para obter uma lista atual, consulte [o Linux sobre as distribuições endossadas pela Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Criar VM no portal Azure

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. **Selecione + Adicione** para abrir o ecrã de máquina **virtual.**
4. Selecione a imagem da lista de dropdown ou **selecione Navegue em todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
5. Para criar um **Gen 2** VM, vá ao separador **Advanced** e selecione a opção **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho do VM para implementar.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione um tamanho VM recomendado para a imagem selecionada.":::

7. Forneça os outros detalhes necessários para criar o VM.
8. Selecione **Review + crie** para rever as suas escolhas. Quando aparecer a mensagem **de validação,** selecione  **Criar**.

O Azure começa a aturar a máquina virtual especificada. Acompanhe o seu progresso selecionando o separador **Máquinas Virtuais** no menu esquerdo. Depois de criado, o estado da Máquina Virtual muda para **executar**.

## <a name="configure-the-vm"></a>Configurar a VM

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="connect-to-your-vm"></a>Ligar à VM

Consulte a seguinte documentação para ligar ao seu [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM.

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passos seguintes

- Passo seguinte recomendado: [Teste a sua imagem em VM](azure-vm-image-test.md) para garantir que satisfaz os requisitos de publicação do Azure Marketplace. Isto é opcional.
- Se não quiser testar a sua imagem em VM, inscreva-se no [Partner Center](https://partner.microsoft.com/) para publicar a sua imagem.
- Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [a VM FAQ para o Azure Marketplace](azure-vm-create-faq.md).
