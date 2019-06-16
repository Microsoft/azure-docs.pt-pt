---
title: Configurar o WinRM após a criação da máquina virtual do Azure | O Azure Marketplace
description: Explica como configurar a gestão remota do Windows (WinRM) após a criação de uma máquina de virtual alojado no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 4a4248efcfda76dfd8907069e167fdfa144d0365
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938508"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurar o WinRM após a criação da máquina virtual

Este artigo explica como configurar uma existente alojado no Azure máquina virtual (VM) para ativar o WinRM por HTTPS.  Esta configuração aplica-se apenas a VMs baseadas no Windows e requer que o processo de dois passos seguintes:

1. Ative o tráfego de porta para o WinRM através do protocolo HTTPS.  Irá configurar esta definição para a sua VM no portal do Azure.
2. Configure a VM para ativar o WinRM através da execução de scripts do PowerShell fornecidos.


## <a name="enabling-port-traffic"></a>Ativar o tráfego de porta

O WinRM através do protocolo HTTPS utiliza a porta 5896, que não está ativada por predefinição em VMs de Windows pré-configuradas oferecidos no Azure Marketplace. Para ativar este protocolo, utilize os seguintes passos para adicionar uma nova regra para o grupo de segurança de rede (NSG) com o [portal do Azure](https://portal.azure.com).  Para obter mais informações sobre NSGs, consulte [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Navegue para o painel **máquinas virtuais >**   <*nome da vm*>   **> definições/rede**.
2.  Clique no nome do NSG (neste exemplo, **testvm11002**) para apresentar as respetivas propriedades:

    ![Propriedades do grupo de segurança de rede](./media/nsg-properties.png)
 
3. Sob **configurações**, selecione **regras de segurança de entrada** para apresentar neste painel.
4. Clique em **+ adicionar** para criar uma nova regra chamada `WinRM_HTTPS` para a porta TCP 5986.

    ![Adicionar regra de segurança de rede de entrada](./media/nsg-new-rule.png)

5. Clique em **OK** quando tiver terminado de fornecer valores.  A lista de regras de segurança de entrada deve conter as seguintes entradas de novo.

    ![Lista de regras de segurança de rede de entrada](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurar a VM para ativar o WinRM 

Utilize os seguintes passos para ativar e configurar a funcionalidade de gestão remota do Windows na sua VM do Windows.   

1. Estabelece uma ligação de ambiente de trabalho remoto à sua VM alojado no Azure.  Para obter mais informações, consulte [como ligar e iniciar sessão na máquina virtual do Azure a executar o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Os passos restantes serão executados na sua VM.
2. Transferir os seguintes ficheiros e salvá-los para uma pasta na sua VM:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Abra o **consola do PowerShell** com privilégios elevados (**executar como administrador**). 
4. Execute o seguinte comando, fornecendo o parâmetro necessário: o nome de domínio completamente qualificado (FQDN) para a sua VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell script de configuração 1](./media/powershell-file1.png)

    Este script depende de outros dois arquivos que está a ser na mesma pasta.


## <a name="next-steps"></a>Passos Seguintes

Assim que tiver configurado o WinRM, está pronto para [implementar a sua VM a partir do seu VHDs constituintes](./cpp-deploy-vm-vhd.md).
