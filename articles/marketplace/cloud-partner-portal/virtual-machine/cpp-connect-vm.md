---
title: Ligar a sua máquina de virtual baseada no Azure da Microsoft | Documentos da Microsoft
description: Explica como se pode ligar à nova máquina virtual criada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744561"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Ligar à máquina virtual baseada no Azure

Este artigo explica como ligar a e inicie sessão nas máquinas virtuais (VMs) que criou no Azure.  Depois de ter ligado com êxito, pode trabalhar com a VM como se estivesse conectado localmente para o respetivo servidor de anfitrião. 

## <a name="connect-to-a-windows-based-vm"></a>Ligar a uma VM baseada no Windows

Irá utilizar o cliente de ambiente de trabalho remoto para ligar à VM com base em Windows alojada no Azure.  A maioria das versões do Windows contêm nativamente suporte para o protocolo de ambiente de trabalho remoto (RDP).  Para outras máquinas, pode encontrar mais informações sobre os clientes nos [clientes de ambiente de trabalho remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

O artigo seguinte fornece detalhes sobre como utilizar o suporte de RDP do Windows incorporado para ligar à sua VM: [Como ligar e iniciar sessão a uma máquina virtual do Azure a executar o Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Poderá receber avisos de segurança durante o processo, por exemplo que o arquivo. rdp é de um publicador desconhecido ou que não não possível verificar as credenciais de utilizador.  É seguro ignorar esses avisos.


## <a name="connect-to-a-linux-based-vm"></a>Ligar a uma VM baseada no Linux

Para ligar a VM baseado em Linux, tem de um cliente do secure shell (SSH) do protocolo.  Esta discussão, irá utilizar gratuitamente [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminal.

1. No **máquinas virtuais** painel da [portal do Azure](https://ms.portal.azure.com), selecione a VM que pretende ligar.  
2. **Iniciar** a VM se ainda não estiver em execução.
3. Clique no nome da VM para abrir as respetivas **descrição geral** página.
4. Tenha em atenção que o endereço IP público e o nome DNS da sua VM.  (Se esses valores não são definidos, deverá [criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Definições de descrição geral da VM](./media/publishvm_019.png)
 
5. Abra a aplicação PuTTY.  
6. Na caixa de diálogo Configuração do PuTTY, introduza o endereço IP ou nome DNS da sua VM. 

   ![Definições de terminal do puTTY](./media/publishvm_020.png)
 
7. Clique em **abra** para abrir um terminal PuTTY.  
8. Quando lhe for pedido, introduza o nome da conta e a palavra-passe da sua conta de VM do Linux. 

   Se estiver a ter problemas de ligação, consulte a documentação do seu cliente SSH, por exemplo [capítulo 10: Mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Para obter mais informações, incluindo como adicionar uma área de trabalho a uma VM do Linux aprovisionados, consulte [instalar e configurar o ambiente de trabalho remoto para ligar a uma VM do Linux no Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Parar VMs não utilizadas
Faturas do Azure para o alojamento de VM quando uma VM está em execução *ou Inativas*.  Por isso é melhor prática para parar VMs que atualmente não estão a ser utilizadas.  Por exemplo, teste, a cópia de segurança, ou VMs extintos são candidatos para encerramento. Para encerrar uma VM, execute os seguintes passos:

1. Sobre o **máquinas virtuais** painel, selecione a VM que pretende parar. 
2. Na barra de ferramentas junto à parte superior da página, clique nas **parar** botão.

   ![Parar uma VM](./media/publishvm_018.png)

Azure rapidamente para a VM num processo chamado *Desalocação*, que não só encerra o sistema operacional na VM, mas também liberta os recursos de hardware e de rede aprovisionados anteriormente para ela.

Se quiser reativar a uma VM parada mais tarde, selecione-o e clique nas **iniciar** botão.


## <a name="next-steps"></a>Passos Seguintes

Depois que estiverem ligados remotamente, estará pronto para [configurar a sua VM](./cpp-configure-vm.md).
