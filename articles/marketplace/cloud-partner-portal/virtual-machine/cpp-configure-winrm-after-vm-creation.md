---
title: Configurar o WinRM após a criação da máquina virtual do Azure | Azure Marketplace
description: Explica como configurar o Gerenciamento Remoto do Windows (WinRM) após a criação de uma máquina virtual hospedada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: ae5a55c6d640852cbd873bc6b36e502b5fe17165
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817942"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurar o WinRM após a criação da máquina virtual

Este artigo explica como configurar uma VM (máquina virtual) hospedada pelo Azure existente para habilitar o WinRM por HTTPS.  Essa configuração se aplica somente a VMs baseadas em Windows e requer o seguinte processo de duas etapas:

1. Habilite o tráfego de porta para o protocolo WinRM sobre HTTPS.  Você definirá essa configuração para sua VM no portal do Azure.
2. Configure a VM para habilitar o WinRM executando os scripts do PowerShell fornecidos.


## <a name="enabling-port-traffic"></a>Habilitando o tráfego de porta

O protocolo WinRM via HTTPS usa a porta 5896, que não está habilitada por padrão em VMs pré-configuradas do Windows oferecidas no Azure Marketplace. Para habilitar esse protocolo, use as etapas a seguir para adicionar uma nova regra ao NSG (grupo de segurança de rede) com o [portal do Azure](https://portal.azure.com).  Para obter mais informações sobre NSGs, consulte [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Navegue até a folha **máquinas virtuais >**   <*nome da VM*>   **> configurações/rede**.
2.  Clique no nome do NSG (neste exemplo, **testvm11002**) para exibir suas propriedades:

    ![Propriedades do grupo de segurança de rede](./media/nsg-properties.png)
 
3. Em **configurações**, selecione **regras de segurança de entrada** para exibir esta folha.
4. Clique em **+ Adicionar** para criar uma nova regra chamada `WinRM_HTTPS` para a porta TCP 5986.

    ![Adicionar regra de segurança de rede de entrada](./media/nsg-new-rule.png)

5. Clique em **OK** quando terminar de fornecer valores.  A lista de regras de segurança de entrada deve conter as novas entradas a seguir.

    ![Lista de regras de segurança de rede de entrada](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurar a VM para habilitar o WinRM 

Use as etapas a seguir para habilitar e configurar o recurso Gerenciamento Remoto do Windows em sua VM do Windows.   

1. Estabeleça uma conexão de Área de Trabalho Remota com a VM hospedada no Azure.  Para obter mais informações, consulte [como se conectar e entrar em uma máquina virtual do Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  As etapas restantes serão executadas em sua VM.
2. Baixe os seguintes arquivos e salve-os em uma pasta em sua VM:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [MakeCert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Abra o **console do PowerShell** com privilégios elevados (**Executar como administrador**). 
4. Execute o comando a seguir, fornecendo o parâmetro obrigatório: o FQDN (nome de domínio totalmente qualificado) para sua VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script de configuração do PowerShell 1](./media/powershell-file1.png)

    Esse script depende dos outros dois arquivos que estão na mesma pasta.


## <a name="next-steps"></a>Passos seguintes

Depois de configurar o WinRM, você estará pronto para [implantar sua VM de seus VHDs constituintes](./cpp-deploy-vm-vhd.md).
