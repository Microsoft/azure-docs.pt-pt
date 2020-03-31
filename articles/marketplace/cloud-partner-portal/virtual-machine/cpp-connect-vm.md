---
title: Ligue-se à sua máquina virtual baseada no Microsoft Azure [ Mercado Azure
description: Explica como se conectar à nova máquina virtual criada no Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2b7eb6d321a64835254b684c8faeedc53645dffe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278063"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Ligue-se à sua máquina virtual baseada em Azure

Este artigo explica como se conectar e assinar nas máquinas virtuais (VMs) que criou no Azure.  Uma vez conectado com sucesso, pode trabalhar com o VM como se estivesse ligado localmente ao seu servidor anfitrião. 

## <a name="connect-to-a-windows-based-vm"></a>Ligue-se a um VM baseado no Windows

Utilizará o cliente remoto de desktop para se ligar ao VM baseado no Windows hospedado no Azure.  A maioria das versões do Windows contêm suporte para o protocolo remoto de desktop (RDP).  Para outras máquinas, pode encontrar mais informações sobre clientes em [clientes do Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

O seguinte artigo detalha como utilizar o suporte rdp do Windows incorporado para se ligar ao seu VM: [Como ligar e iniciar sessão a uma máquina virtual Azure que executa](../../../virtual-machines/windows/connect-logon.md)o Windows .  

>[!TIP]
> Pode obter avisos de segurança durante o processo, por exemplo, que o ficheiro .rdp é de um editor desconhecido ou que as suas credenciais de utilizador não podem ser verificadas.  É seguro ignorar estes avisos.


## <a name="connect-to-a-linux-based-vm"></a>Ligue-se a um VM baseado em Linux

Para ligar o VM baseado em Linux, precisa de um cliente seguro de protocolo de concha (SSH).  Esta discussão utilizará o terminal [PuTTY](https://www.ssh.com/ssh/putty/) SHH gratuito.

1. Vá ao [portal Azure.](https://ms.portal.azure.com) Procure e selecione **máquinas Virtuais**. 
2. Selecione o VM a que pretende ligar.  
3. **Inicie** o VM se ainda não estiver em funcionamento.
4. Clique no nome do VM para abrir a sua página **de visão geral.**
5. Note o endereço IP público e o nome DNS do seu VM.  (Se estes valores não estiverem definidos, então deve [criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Definições de visão geral vm](./media/publishvm_019.png)
 
6. Abra a aplicação PuTTY.  
7. No diálogo de configuração PuTTY, introduza o endereço IP ou o nome DNS do seu VM. 

   ![Definições de terminais PuTTY](./media/publishvm_020.png)
 
8. Clique **em Abrir** para abrir um terminal PuTTY.  
9. Quando for solicitado, introduza o nome da conta e a palavra-passe da sua conta VM Linux. 

Se tiver problemas de ligação, consulte a documentação para o seu cliente SSH, por exemplo [capítulo 10: Mensagens](https://www.ssh.com/ssh/putty/putty-manuals)de erro comuns .

Para mais informações, incluindo como adicionar um ambiente de trabalho a um VM Linux provisionado, consulte Instalar e configurar o [Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Parem vMs não utilizados
Contas azure para vm hospedagem quando um VM está em execução *ou inativo*.  Como tal, é melhor parar vMs que não estão sendo usados atualmente.  Por exemplo, os VMs de teste, de reserva ou aposentados são candidatos à paralisação. Para desligar um VM, complete os seguintes passos:

1. Na lâmina das **máquinas Virtuais,** selecione o VM que pretende parar. 
2. Na barra de ferramentas perto da parte superior da página, clique no botão **Stop.**

   ![Parar uma VM](./media/publishvm_018.png)

O Azure rapidamente para o VM num processo chamado *dedelocação*, que não só desliga o sistema operativo no VM, como também liberta os recursos de hardware e rede anteriormente previstos para o mesmo.

Se pretender reativar mais tarde um VM parado, selecione-o e clique no botão **Iniciar.**


## <a name="next-steps"></a>Passos seguintes

Depois de estar ligado remotamente, está pronto para [configurar o seu VM](./cpp-configure-vm.md).
