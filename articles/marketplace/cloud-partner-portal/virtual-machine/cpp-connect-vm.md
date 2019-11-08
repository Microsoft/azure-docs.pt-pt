---
title: Conectar-se à sua máquina virtual baseada em Microsoft Azure | Azure Marketplace
description: Explica como se conectar à nova máquina virtual criada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 256952d06e8b417be9ab2bc3c6c8c6857d50e2ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817373"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Conectar-se à sua máquina virtual baseada no Azure

Este artigo explica como se conectar e entrar nas máquinas virtuais (VMs) que você criou no Azure.  Depois de se conectar com êxito, você poderá trabalhar com a VM como se você tivesse feito logon localmente em seu servidor host. 

## <a name="connect-to-a-windows-based-vm"></a>Conectar-se a uma VM baseada no Windows

Você usará o cliente de área de trabalho remota para se conectar à VM baseada em Windows hospedada no Azure.  A maioria das versões do Windows nativamente contêm suporte para o protocolo RDP (área de trabalho remota).  Para outros computadores, você pode encontrar mais informações sobre clientes em [clientes área de trabalho remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

O artigo a seguir fornece detalhes sobre como usar o suporte interno do Windows RDP para se conectar à sua VM: [como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Você pode receber avisos de segurança durante o processo, por exemplo, que o arquivo. rdp é de um editor desconhecido ou que suas credenciais de usuário não podem ser verificadas.  É seguro ignorar esses avisos.


## <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma VM baseada em Linux

Para conectar a VM baseada em Linux, você precisa de um cliente SSH (Secure Shell Protocol).  Esta discussão usará o terminal [shh de](https://www.ssh.com/ssh/putty/) saída gratuito.

1. Na folha **máquinas virtuais** da [portal do Azure](https://ms.portal.azure.com), selecione a VM à qual você deseja se conectar.  
2. **Inicie** a VM se ela ainda não estiver em execução.
3. Clique no nome da VM para abrir sua página de **visão geral** .
4. Anote o endereço IP público e o nome DNS da sua VM.  (Se esses valores não estiverem definidos, você deverá [criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Configurações de visão geral da VM](./media/publishvm_019.png)
 
5. Abra o aplicativo de saída.  
6. Na caixa de diálogo configuração de saída, insira o endereço IP ou o nome DNS da sua VM. 

   ![Configurações de terminal de saída](./media/publishvm_020.png)
 
7. Clique em **abrir** para abrir um terminal de reemitida.  
8. Quando solicitado, insira o nome da conta e a senha da sua conta da VM do Linux. 

   Se você estiver tendo problemas de conexão, consulte a documentação do cliente SSH, por exemplo, [capítulo 10: mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Para obter mais informações, incluindo como adicionar uma área de trabalho a uma VM do Linux provisionada, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Parar VMs não utilizadas
O Azure cobra pela hospedagem de VM quando uma VM está em execução *ou ociosa*.  Portanto, é uma prática recomendada parar as VMs que não estão sendo usadas no momento.  Por exemplo, as VMs de teste, backup ou desativadas são candidatas para desligamento. Para desligar uma VM, execute as seguintes etapas:

1. Na folha **máquinas virtuais** , selecione a VM que você deseja parar. 
2. Na barra de ferramentas próxima à parte superior da página, clique no botão **parar** .

   ![Parar uma VM](./media/publishvm_018.png)

O Azure interrompe rapidamente a VM em um processo chamado *desalocação*, que não apenas desliga o sistema operacional na VM, mas também libera os recursos de hardware e rede provisionados anteriormente para ele.

Se você quiser reativar mais tarde uma VM parada, selecione-a e clique no botão **Iniciar** .


## <a name="next-steps"></a>Passos seguintes

Depois de conectar-se remotamente, você estará pronto para [configurar sua VM](./cpp-configure-vm.md).
