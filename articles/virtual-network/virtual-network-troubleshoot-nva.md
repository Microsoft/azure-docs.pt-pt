---
title: Problemas de problemas com problemas na rede de resolução de problemas de aparelhos virtuais em Azure Microsoft Docs
description: Aprenda a resolver problemas com os problemas com os problemas com o aparelho virtual da rede em Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056823"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemas de aparelhos virtuais em rede em Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode experimentar problemas e erros de conectividade VM ou VPN ao utilizar um aparelho virtual de rede de terceiros (NVA) no Microsoft Azure. Este artigo fornece passos básicos para ajudá-lo a validar os requisitos básicos da Plataforma Azure para configurações de NVA.

O suporte técnico para os NVAs de terceiros e a sua integração com a plataforma Azure é fornecido pelo fornecedor NVA.

> [!NOTE]
> Se tiver um problema de conectividade ou encaminhamento que envolva um NVA, deverá contactar diretamente [o fornecedor da NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de verificação para resolução de problemas com fornecedor nVA

- Atualizações de software para software VM NVA
- Configuração e funcionalidade da Conta de Serviço
- Rotas definidas pelo utilizador (UDRs) em subredes de rede virtual que direcionam o tráfego para a NVA
- UDRs em redes virtuais subnets que direcionam o tráfego da NVA
- Quadros e regras de encaminhamento dentro da NVA (por exemplo, de NIC1 a NIC2)
- Rastreio em NICs nva para verificar o tráfego de rede de receção e envio
- Ao utilizar um SKU padrão e iPs públicos, deve haver um NSG criado e uma regra explícita para permitir que o tráfego seja encaminhado para o NVA.

## <a name="basic-troubleshooting-steps"></a>Passos básicos de resolução de problemas

- Verifique a configuração básica
- Verifique o desempenho da NVA
- Resolução avançada de problemas de rede

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Verifique os requisitos mínimos de configuração para NVAs no Azure

Cada NVA tem requisitos básicos de configuração para funcionar corretamente no Azure. A secção seguinte fornece os passos para verificar estas configurações básicas. Para mais informações, [contacte o fornecedor da NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Verifique se o reencaminhamento ip está ativado no NVA**

Utilizar o portal do Azure

1. Localize o recurso NVA no [portal Azure,](https://portal.azure.com)selecione Networking e, em seguida, selecione a interface rede.
2. Na página da interface da Rede, selecione a configuração IP.
3. Certifique-se de que o encaminhamento ip está ativado.

Utilizar o PowerShell

1. Abra a PowerShell e, em seguida, inscreva-se na sua conta Azure.
2. Executar o seguinte comando (substitua os valores com suporte sinuoso com as suas informações):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Verifique a propriedade **EnableIPForwarding.**
4. Se o encaminhamento IP não estiver ativado, execute os seguintes comandos para o ativar:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Verifique se o NSG utiliza o Standard SKU Pubilc IP** Ao utilizar um SKU padrão e iPs públicos, deve haver um NSG criado e uma regra explícita para permitir o tráfego para a NVA.

**Verifique se o tráfego pode ser encaminhado para a NVA**

1. No [portal Azure,](https://portal.azure.com)open **Network Watcher,** selecione **Next Hop**.
2. Especifique um VM configurado para redirecionar o tráfego para a NVA, e um endereço IP de destino para ver o próximo salto. 
3. Se a NVA não estiver listada como o **próximo lúpulo,** verifique e atualize as tabelas de rotas do Azure.

**Verifique se o tráfego pode chegar à NVA**

1. No [portal Azure,](https://portal.azure.com)abra **o Observador**de Rede e, em seguida, selecione IP **Flow Check**. 
2. Especifique o VM e o endereço IP do NVA e verifique se o tráfego está bloqueado por quaisquer grupos de segurança da Rede (NSG).
3. Se houver uma regra nsg que bloqueie o tráfego, localize o NSG em regras de **segurança eficazes** e, em seguida, atualize-o para permitir que o tráfego passe. Em seguida, executar **IP Flow Verifique** novamente e utilize a sessão de problemas da **Ligação** para testar as comunicações TCP da VM para o seu endereço IP interno ou externo.

**Verifique se nva e VMs estão a ouvir o tráfego esperado**

1. Ligue-se ao NVA utilizando RDP ou SSH e, em seguida, executar o seguinte comando:

    Para Windows:

        netstat -an

    Para Linux:

        netstat -an | grep -i listen
2. Se não vir a porta TCP que é utilizada pelo software NVA que está listada nos resultados, deve configurar a aplicação na NVA e VM para ouvir e responder ao tráfego que chega a essas portas. [Contacte o fornecedor da NVA para obter assistência necessária](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Verifique o desempenho da NVA

### <a name="validate-vm-cpu"></a>Validar cpu VM

Se o uso do CPU chegar perto de 100%, poderá experimentar problemas que afetam as quedas de pacotes de rede. O seu VM reporta cpU médio por um período de tempo específico no portal Azure. Durante um pico de CPU, investigue qual o processo no VM convidado que está a causar a alta CPU, e atenuá-lo, se possível. Também pode ter de redimensionar o VM para um tamanho SKU maior ou, para um conjunto de escala de máquina virtual, aumentar a contagem de instâncias ou definir para a escala automática na utilização do CPU. Para qualquer uma destas questões, [contacte o fornecedor da NVA para obter assistência,](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)se necessário.

### <a name="validate-vm-network-statistics"></a>Validar as estatísticas da Rede VM

Se a rede VM utilizar picos ou mostrar períodos de alta utilização, poderá também ter de aumentar o tamanho SKU do VM para obter maiores capacidades de entrada. Também pode reimplantar o VM com a ativação da Rede Acelerada. Para verificar se a NVA suporta a funcionalidade de rede acelerada, [contacte o fornecedor NVA para obter assistência,](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)se necessário.

## <a name="advanced-network-administrator-troubleshooting"></a>Resolução avançada de problemas do administrador de rede

### <a name="capture-network-trace"></a>Capturar vestígios de rede
Capture um vestígio de rede simultâneo na fonte VM, na NVA e no destino VM enquanto executa **[O PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** ou **Nmap,** e depois pare o rasto.

1. Para capturar um vestígio de rede simultâneo, executar o seguinte comando:

   **Para Windows**

   netsh trace iniciar captura=sim tracefile=c:\server_IP.etl cenário=netconnection

   **Para Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Utilize **o PsPing** ou **o Nmap** desde a fonte `PsPing 10.0.0.4:80` VM até ao destino VM (por exemplo: ou `Nmap -p 80 10.0.0.4`).
3. Abra o rastreio de rede a partir do destino VM utilizando o Monitor de [Rede](https://www.microsoft.com/download/details.aspx?id=4865) ou o tcpdump. Aplique um filtro de visualização para o IP do VM fonte `IPv4.address==10.0.0.4 (Windows netmon)` `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` do seu comando, de onde executou **O PsPing** ou **O Nmap,** como ou (Linux).

### <a name="analyze-traces"></a>Analisar vestígios

Se não vir os pacotes a chegar ao traço VM traseiro, é provável que haja um interferor DE NSG ou UDR ou as mesas de encaminhamento nVA estão incorretas.

Se vir que os pacotes estão a ser enviados, mas que não há resposta, poderá ter de resolver um problema da aplicação de VM ou de firewall. Para qualquer uma destas questões, [contacte o fornecedor da NVA para obter assistência necessária.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)