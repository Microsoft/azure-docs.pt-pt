---
title: Ligar a uma VM do Windows Server | Microsoft Docs
description: Saiba como ligar e iniciar sessão para uma VM do Windows com o portal do Azure e o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c14b6e16a16f325681722203eaa05a435b141208
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990613"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Como ligar e iniciar sessão máquina virtual do Azure a executar o Windows
Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP) a partir de um ambiente de trabalho do Windows. Ligue primeiro à máquina virtual e, em seguida, iniciar sessão.

Para ligar a uma VM do Windows a partir de um Mac, terá de instalar como um cliente RDP para Mac [ambiente de trabalho remoto](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. No topo da página para a máquina virtual, selecione **Connect**.
2. Sobre o **ligar à máquina virtual** , selecione o endereço IP adequado e a porta. Na maioria dos casos, o endereço IP predefinido e a porta devem ser utilizados. Selecione **Transferir ficheiro RDP**. Se a VM tem uma política just-in-time definida, primeiro tem de selecionar o **pedir acesso** botão para pedir acesso antes de pode transferir o ficheiro RDP. Para obter mais informações sobre a política de acesso just-in-time, consulte [gerir o acesso de máquina virtual utilizando o apenas na política de tempo](../../security-center/security-center-just-in-time.md).
2. Abra o ficheiro RDP transferido e selecione **Connect** quando lhe for pedido. 
2. Obterá um aviso de que o `.rdp` arquivo provém de um publicador desconhecido. Isto era esperado. Na **conexão de área de trabalho remoto** janela, selecione **Connect** para continuar.
   
    ![Captura de ecrã de um aviso sobre um publicador desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Introduza as credenciais de uma conta na máquina virtual e, em seguida, selecione **OK**.
   
     **Conta local**: Isto é normalmente o nome de utilizador de conta local e a palavra-passe que especificou quando criou a máquina virtual. Neste caso, o domínio é o nome da máquina virtual e é introduzido como *nomedavm*&#92;*nomedeutilizador*.  
   
    **VM associada ao domínio**: Se a VM pertence a um domínio, introduza o nome de utilizador no formato *domínio*&#92;*nome de utilizador*. A conta também tem de estar no grupo Administradores ou terem sido concedidos privilégios de acesso remoto à VM.
   
    **Controlador de domínio**: Se a VM for um controlador de domínio, introduza o nome de utilizador e palavra-passe de uma conta de administrador de domínio para esse domínio.
4. Selecione **Sim** para verificar a identidade da máquina virtual e concluir o início de sessão.
   
   ![Captura de ecrã que mostra uma mensagem sobre a confirmação da identidade da VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se o **Connect** botão no portal do está desativado e não estiver ligado ao Azure via um [Express Route](../../expressroute/expressroute-introduction.md) ou [VPN Site a Site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) ligação, terá de criar e Atribua à VM um endereço IP público, antes de poder utilizar o RDP. Para obter mais informações, consulte [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ligar à máquina virtual com o PowerShell

Se estiver a utilizar o PowerShell e o módulo de AzureRM instalado também podem se conectar usando o `Get-AzRemoteDesktopFile` cmdlet, conforme mostrado abaixo.

Neste exemplo imediatamente inicia a ligação de RDP, apresentar a a avisos semelhantes como acima.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Também pode guardar o ficheiro RDP para utilização futura.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passos Seguintes
Se tiver dificuldade em ligar, veja [Troubleshoot Remote Desktop connections](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

