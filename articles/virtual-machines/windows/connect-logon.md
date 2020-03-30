---
title: Ligar a um VM do Servidor do Windows
description: Saiba como ligar e iniciar sessão num Windows VM utilizando o portal Azure e o modelo de implementação do Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 152df830f11cd5a73235559c5c5d65ced44f22fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266770"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows
Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP) a partir de um ambiente de trabalho do Windows. Primeiro ligas-te à máquina virtual, e depois assinas.

Para se ligar a um VM do Windows a partir de um Mac, terá de instalar um cliente RDP para Mac, como [o Microsoft Remote Desktop](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Vá ao [portal Azure](https://portal.azure.com/) para ligar a um VM. Procure e selecione **máquinas Virtuais**.
2. Selecione a máquina virtual na lista.
3. No início da página da máquina virtual, selecione **Connect**.
4. Na página **Connect to virtual machine,** selecione **RDP**, e, em seguida, selecione o **endereço IP** apropriado e o número da **porta**. Na maioria dos casos, o endereço IP padrão e a porta devem ser utilizados. Selecione **Download RDP File**. Se o VM tiver um conjunto de políticas just-in-time, primeiro precisa selecionar o botão de **acesso do Pedido** para solicitar acesso antes de poder descarregar o ficheiro RDP. Para obter mais informações sobre a política just-in-time, consulte Gerir o acesso virtual à [máquina utilizando a política de tempo justo.](../../security-center/security-center-just-in-time.md)
5. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado. Receberá um aviso de `.rdp` que o ficheiro é de uma editora desconhecida. Isto era esperado. Na janela **Remote Desktop Connection,** selecione **Connect** para continuar.
   
    ![Captura de ecrã de um aviso sobre um publicador desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Introduza as credenciais para uma conta na máquina virtual e, em seguida, selecione **OK**.
   
     **Conta local**: Este é geralmente o nome de utilizador da conta local e a palavra-passe que especificou quando criou a máquina virtual. Neste caso, o domínio é o nome da máquina virtual e é introduzido como *nomedavm*&#92;*nomedeutilizador*.  
   
    **Domínio unido VM**: Se o VM pertencer a um domínio, introduza o nome de utilizador no formato *Domínio*&#92;Nome de *utilizador*. A conta também tem de estar no grupo Administradores ou terem sido concedidos privilégios de acesso remoto à VM.
   
    **Controlador de domínio**: Se o VM for um controlador de domínio, introduza o nome de utilizador e a palavra-passe de um administrador de domínio para esse domínio.
4. Selecione **Sim** para verificar a identidade da máquina virtual e terminar o login.
   
   ![Captura de ecrã que mostra uma mensagem sobre a confirmação da identidade da VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se o botão **Connect** no portal estiver acinzentado e não estiver ligado ao Azure através de uma [rota expressa](../../expressroute/expressroute-introduction.md) ou ligação [VPN site-to-site,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) terá de criar e atribuir ao seu VM um endereço IP público antes de poder utilizar rdp. Para mais informações, consulte [endereços IP públicos em Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ligue-se à máquina virtual utilizando powerShell

 

Se estiver a utilizar o PowerShell e tiver instalado o módulo `Get-AzRemoteDesktopFile` PowerShell Azure, também poderá ligar-se utilizando o cmdlet, como mostrado abaixo.

Este exemplo lançará imediatamente a ligação RDP, levando-o através de indicações semelhantes às anteriores.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Também pode guardar o ficheiro RDP para utilização futura.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passos seguintes
Se tiver dificuldade em ligar, consulte as ligações de ambiente de [trabalho remoto de Resolução](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de Problemas . 

