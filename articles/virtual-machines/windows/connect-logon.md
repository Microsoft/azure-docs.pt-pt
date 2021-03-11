---
title: Ligue-se a um VM do Servidor do Windows
description: Saiba como ligar e iniciar sê-lo num VM do Windows utilizando o portal Azure e o modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5d14160a47789e10f1881fa0e55afd4af122c990
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550761"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Como conectar e iniciar sê-lo numa máquina virtual Azure que executa o Windows
Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP) a partir de um ambiente de trabalho do Windows. Primeiro liga-se à máquina virtual e depois assina-se.

Para ligar a um VM do Windows a partir de um Mac, terá de instalar um cliente RDP para Mac, como o [Microsoft Remote Desktop](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Vá ao [portal Azure](https://portal.azure.com/) para ligar a um VM. Procure e selecione **máquinas Virtuais.**
2. Selecione a máquina virtual na lista.
3. No início da página de máquina virtual, selecione **Connect**.
4. Na página de **'Ligar à máquina virtual',** selecione **RDP** e, em seguida, selecione o **endereço IP** apropriado e o número **de porta**. Na maioria dos casos, o endereço IP predefinido e a porta devem ser utilizados. Selecione **Transferir Ficheiro RDP**. Se o VM tiver uma definição de política just-in-time, primeiro tem de selecionar o botão **de acesso 'Pedir'** para solicitar acesso antes de poder descarregar o ficheiro RDP. Para obter mais informações sobre a política just-in-time, consulte [Gerir o acesso à máquina virtual utilizando a política just in time](../../security-center/security-center-just-in-time.md).
5. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado. Receberá um aviso de que o `.rdp` ficheiro é de uma editora desconhecida. Isto era esperado. Na janela **"Ligação de Ambiente de Trabalho Remoto",** selecione **Connect** para continuar.
   
    ![Captura de ecrã de um aviso sobre um publicador desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Introduza as credenciais para uma conta na máquina virtual e, em seguida, selecione **OK**.
   
     **Conta local**: Este é normalmente o nome de utilizador da conta local e a palavra-passe que especificou quando criou a máquina virtual. Neste caso, o domínio é o nome da máquina virtual e é introduzido como *nomedavm*&#92;*nomedeutilizador*.  
   
    **Domínio aderido VM**: Se o VM pertencer a um domínio, insira o nome de utilizador no formato *Domínio*&#92;*Nome de Utilizador*. A conta também tem de estar no grupo Administradores ou terem sido concedidos privilégios de acesso remoto à VM.
   
    **Controlador de domínio**: Se o VM for um controlador de domínio, insira o nome de utilizador e a palavra-passe de uma conta de administrador de domínio para esse domínio.
4. Selecione **Sim** para verificar a identidade da máquina virtual e termine de iniciar sessão.
   
   ![Captura de ecrã que mostra uma mensagem sobre a confirmação da identidade da VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se o botão **Ligar** no portal estiver acinzentado e não estiver ligado ao Azure através de uma [ligação](../../expressroute/expressroute-introduction.md) VPN da Rota Expressa ou [do Local-a-Local,](../../vpn-gateway/tutorial-site-to-site-portal.md) terá de criar e atribuir ao seu VM um endereço IP público antes de poder utilizar o RDP. Para mais informações, consulte [endereços IP públicos em Azure.](../../virtual-network/public-ip-addresses.md)
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ligue-se à máquina virtual utilizando o PowerShell

 

Se estiver a utilizar o PowerShell e tiver o módulo Azure PowerShell instalado, também poderá ligar-se utilizando o `Get-AzRemoteDesktopFile` cmdlet, como mostrado abaixo.

Este exemplo lançará imediatamente a ligação RDP, levando-o através de instruções semelhantes à anterior.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Pode também guardar o ficheiro RDP para utilização futura.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passos seguintes
Se tiver dificuldade em ligar, consulte [as ligações de Ambiente de Trabalho remoto de resolução de problemas](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json).