---
title: Ativar a extensão de VM a partir do portal Azure
description: Este artigo descreve como implementar extensões de máquinas virtuais para O Arco Azure habilitados a funcionar em ambientes de nuvem híbrida a partir do portal Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: dcab7cb441c329a60b2c6fa3256aeedb2bb5b33d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462938"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Ativar extensões VM do Azure a partir do portal Azure

Este artigo mostra-lhe como implementar e desinstalar extensões Azure VM, suportadas por servidores Azure Arc, para uma máquina híbrida Linux ou Windows através do portal Azure.

## <a name="enable-extensions-from-the-portal"></a>Permitir extensões a partir do portal

As extensões VM podem ser aplicadas ao seu Arco para máquina gerida pelo servidor através do portal Azure.

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

2. No portal, navegue pelos **Servidores - Azure Arc** e selecione a sua máquina híbrida da lista.

3. Escolha **extensões**e, em seguida, **selecione Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente. Neste exemplo, implementaremos a extensão VM do Log Analytics.

    ![Selecione extensão VM para máquina selecionada](./media/manage-vm-extensions/add-vm-extensions.png)

    O exemplo a seguir mostra a instalação da extensão VM log analytics a partir do portal Azure:

    ![Instalar extensão VM do Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Para completar a instalação, é-lhe exigido que forneça o ID do espaço de trabalho e a chave primária. Se não estiver familiarizado com a forma de encontrar esta informação, consulte [a identificação do espaço de trabalho e a chave.](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

4. Depois de confirmar as informações necessárias fornecidas, selecione **Criar**. É apresentado um resumo da implantação e pode rever o estado da implantação.

>[!NOTE]
>Embora as extensões múltiplas possam ser emgrurentadas e processadas, são instaladas em série. Uma vez concluída a primeira instalação de extensão, tentará-se a instalação da próxima extensão.

## <a name="uninstall-extension"></a>Desinstalar extensão

Pode remover uma ou mais extensões de um servidor ativado pelo Arco do portal Azure. Execute os seguintes passos para remover uma extensão.

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

2. No portal, navegue pelos **Servidores - Azure Arc** e selecione a sua máquina híbrida da lista.

3. Escolha **extensões**e, em seguida, selecione uma extensão da lista de extensões instaladas.

4. Selecione **Desinstalar** e quando for solicitado para verificar, selecione **Sim** para prosseguir.

## <a name="next-steps"></a>Passos seguintes

- Pode implementar, gerir e remover extensões VM utilizando os modelos [Azure CLI,](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).

- As informações relativas à resolução de problemas podem ser encontradas no guia de [extensões VM de resolução de problemas.](troubleshoot-vm-extensions.md)