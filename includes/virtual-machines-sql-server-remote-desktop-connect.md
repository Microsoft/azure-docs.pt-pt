---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184215"
---
1. Depois de a máquina virtual do Azure ser criada e estar em execução, clique no ícone Máquinas Virtuais no portal do Azure para ver as VMs.

1. Clique nas reticências, **... **, da nova VM.

1. Clique em **Ligar**.

   ![Ligar à VM no portal](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Abra o ficheiro **RDP** transferido pelo browser para a VM.

1. A Ligação ao Ambiente de Trabalho Remoto notifica-o de que não é possível identificar o publicador desta ligação remota. Clique em **Ligar** para continuar.

1. Na caixa de diálogo **Segurança do Windows**, clique em **Utilizar uma conta diferente**. Poderá ter de clicar em **Mais opções** para ver isto. Especifique o nome de utilizador e a palavra-passe que configurou quando criou a VM. Tem de adicionar uma barra invertida antes do nome de utilizador.

   ![Autenticação de ambiente de trabalho remoto](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. Clique em **OK** para ligar.
