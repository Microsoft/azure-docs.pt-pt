---
title: Ligar a uma VM do Windows Server | Microsoft Docs
description: Saiba como se conectar e entrar em uma VM do Windows usando o portal do Azure e o modelo de implantação do Gerenciador de recursos.
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
ms.openlocfilehash: aa56cfafdcca163d3bdb3f339f84ceeaa37f3dc5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079931"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows
Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP) a partir de um ambiente de trabalho do Windows. Primeiro, conecte-se à máquina virtual e, em seguida, faça logon.

Para se conectar a uma VM do Windows de um Mac, será necessário instalar um cliente RDP para Mac, como [área de trabalho remota da Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Na parte superior da página da máquina virtual, selecione **conectar**.
2. Na página **conectar à máquina virtual** , selecione o endereço IP e a porta apropriados. Na maioria dos casos, o endereço IP e a porta padrão devem ser usados. Selecione **Transferir ficheiro RDP**. Se a VM tiver um conjunto de políticas just-in-time, primeiro você precisará selecionar o botão **solicitar acesso** para solicitar acesso para poder baixar o arquivo RDP. Para obter mais informações sobre a política just-in-time, consulte [gerenciar o acesso à máquina virtual usando a política just in time](../../security-center/security-center-just-in-time.md).
2. Abra o arquivo RDP baixado e selecione **conectar** quando solicitado. 
2. Você receberá um aviso de que `.rdp` o arquivo é de um editor desconhecido. Isto era esperado. Na janela **conexão de área de trabalho remota** , selecione **conectar** para continuar.
   
    ![Captura de ecrã de um aviso sobre um publicador desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Insira as credenciais para uma conta na máquina virtual e, em seguida, selecione **OK**.
   
     **Conta local**: Normalmente, esse é o nome de usuário e a senha da conta local que você especificou quando criou a máquina virtual. Neste caso, o domínio é o nome da máquina virtual e é introduzido como *nomedavm*&#92;*nomedeutilizador*.  
   
    **VM ingressada no domínio**: Se a VM pertencer a um domínio, insira o nome de usuário no formato *domínio*&#92;*nome*de usuários. A conta também tem de estar no grupo Administradores ou terem sido concedidos privilégios de acesso remoto à VM.
   
    **Controlador de domínio**: Se a VM for um controlador de domínio, insira o nome de usuário e a senha de uma conta de administrador de domínio para esse domínio.
4. Selecione **Sim** para verificar a identidade da máquina virtual e concluir o logon.
   
   ![Captura de ecrã que mostra uma mensagem sobre a confirmação da identidade da VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se o botão **conectar** no portal estiver esmaecido e você não estiver conectado ao Azure por meio de uma [rota expressa](../../expressroute/expressroute-introduction.md) ou conexão [VPN site a site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , você precisará criar e atribuir à VM um endereço IP público antes de poder usar o RDP. Para obter mais informações, consulte [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Conectar-se à máquina virtual usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se você estiver usando o PowerShell e tiver o módulo do PowerShell do Azure instalado, você também `Get-AzRemoteDesktopFile` poderá se conectar usando o cmdlet, conforme mostrado abaixo.

Este exemplo iniciará imediatamente a conexão RDP, levando você por meio de prompts semelhantes, como acima.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Você também pode salvar o arquivo RDP para uso futuro.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passos Seguintes
Se você tiver dificuldade para se conectar, consulte [solucionar problemas de conexões área de trabalho remota](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

