---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521543"
---
1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar e, em seguida, selecione **Connect**. **Selecione Bastion** a partir do dropdown.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Selecione Bastião":::

1. Depois de selecionar Bastion a partir do dropdown, aparece uma barra lateral que tem três separadores: RDP, SSH e Bastion. Como o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Selecione **Utilizar o Bastião**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Selecione Utilizar Bastião":::

1. Na página 'Ligar' utilizando a página **Azure Bastion,** insira o nome de utilizador e a palavra-passe para a sua máquina virtual e, em seguida, selecione **Connect**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Ligar":::

1. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Ligar usando a porta 443":::
