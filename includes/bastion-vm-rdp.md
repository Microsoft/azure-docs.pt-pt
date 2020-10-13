---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978142"
---
1. Abra o [portal do Azure](https://portal.azure.com). Navegue para a máquina virtual a que pretende ligar e, em seguida, selecione **Connect**. **Selecione Bastion** a partir do dropdown.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Selecione Bastião" border="false":::

1. Depois de selecionar Bastion a partir do dropdown, aparece uma barra lateral que tem três separadores: RDP, SSH e Bastion. Como o Bastion foi previsto para a rede virtual, o separador Bastion está ativo por padrão. Selecione **Utilizar o Bastião**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Selecione Bastião" border="false":::

1. Na página 'Ligar' utilizando a página **Azure Bastion,** insira o nome de utilizador e a palavra-passe para a sua máquina virtual e, em seguida, selecione **Connect**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Selecione Bastião" border="false":::

1. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Selecione Bastião" border="false":::
