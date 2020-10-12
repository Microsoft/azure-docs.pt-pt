---
title: Ligue-se às suas máquinas virtuais Linux em Azure DevTest Labs
description: Saiba como se conectar com a sua máquina virtual Linux em um laboratório (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86532190"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Ligue-se a um Linux VM no seu laboratório (Azure DevTest Labs)
Este artigo mostra-lhe como ligar-se ao Linux VM no seu laboratório. 

## <a name="connect-to-a-linux-vm"></a>Ligar a uma VM do Linux
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Procure e selecione Laboratórios DevTest":::    
1. Da lista de laboratórios, selecione o seu **laboratório.**

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Procure e selecione Laboratórios DevTest":::            
1. Na página inicial do seu laboratório, selecione o seu VM Linux da lista **de máquinas virtuais.** 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Procure e selecione Laboratórios DevTest":::        
5. Na página **'Vista Geral',** pode ver o nome de domínio totalmente qualificado (FQDN) ou endereço IP do VM. Também pode ver a porta como mostrado na imagem seguinte.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Procure e selecione Laboratórios DevTest":::    

    Note que o botão **Connect** está acinzentado mesmo que o VM esteja iniciado. É por desígnio.
6.  Utilize o SSH para ligar ao seu LM Linux. O exemplo a seguir liga-se ao VM com o FQDN, `mydtl07172452621450000.eastus.cloudapp.azure.com` com o nome de utilizador de e `vmuser` `51637` porta. Introduza a palavra-passe para o utilizador ligar-se ao VM. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Pode utilizar ferramentas como [a Putty](https://www.putty.org/) ou qualquer outro cliente SSH para se ligar ao VM. 

    Depois de ligar utilizando o SSH, pode instalar e configurar um ambiente de ambiente de trabalho[(xfce)](https://www.xfce.org)e um ambiente de trabalho remoto[(xrdp).](http://xrdp.org)  Para obter informações detalhadas, consulte [instalar e configurar o Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Passos Seguintes
[Como: ligar a um VM do Windows](connect-windows-virtual-machine.md)
