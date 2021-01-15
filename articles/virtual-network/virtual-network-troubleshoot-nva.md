---
title: Problemas de rede de problemas de aparelhos virtuais em Azure | Microsoft Docs
description: Problemas problemas de prescrição de aparelhos virtuais de rede (NVA) em Azure e validar os requisitos básicos da Plataforma Azure para as configurações da NVA.
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
ms.openlocfilehash: 18f2128b6869b4047cc6f35e1638aca81233a014
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219288"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemas de aparelhos virtuais em rede em Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode experimentar problemas e erros de conectividade VM ou VPN ao utilizar um aparelho virtual de rede de terceiros (NVA) no Microsoft Azure. Este artigo fornece passos básicos para ajudá-lo a validar os requisitos básicos da Plataforma Azure para configurações de NVA.

O suporte técnico para NVAs de terceiros e a sua integração com a plataforma Azure são fornecidos pelo fornecedor NVA.

> [!NOTE]
> Se tiver um problema de conectividade ou encaminhamento que envolva um NVA, deverá contactar diretamente [o fornecedor da NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de verificação para resolução de problemas com o fornecedor NVA

- Atualizações de software para software NVA VM
- Configuração e funcionalidade da Conta de Serviço
- Rotas definidas pelo utilizador (UDRs) em sub-redes de rede virtuais que direcionam o tráfego para a NVA
- UDRs em sub-redes de rede virtuais que direcionam o tráfego da NVA
- Tabelas e regras de encaminhamento dentro do NVA (por exemplo, do NIC1 ao NIC2)
- Rastreio em NICs NVA para verificar a receção e envio de tráfego de rede
- Ao utilizar um SKU standard e iPs públicos, deve haver um NSG criado e uma regra explícita para permitir que o tráfego seja encaminhado para a NVA.

## <a name="basic-troubleshooting-steps"></a>Etapas básicas de resolução de problemas

- Verifique a configuração básica
- Verifique o desempenho da NVA
- Resolução avançada de problemas da rede

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Verifique os requisitos mínimos de configuração para NVAs em Azure

Cada NVA tem requisitos básicos de configuração para funcionar corretamente no Azure. A secção seguinte fornece os passos para verificar estas configurações básicas. Para mais informações, [contacte o fornecedor da NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

**Verifique se o encaminhamento IP está ativado na NVA**

Utilizar o portal do Azure

1. Localizar o recurso NVA no [portal Azure,](https://portal.azure.com)selecione Networking e, em seguida, selecione a interface 'Rede'.
2. Na página de interface da Rede, selecione a configuração IP.
3. Certifique-se de que o encaminhamento IP está ativado.

Utilizar o PowerShell

1. Abra a PowerShell e, em seguida, inscreva-se na sua conta Azure.
2. Executar o seguinte comando (substituir os valores agrupados pelas suas informações):

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

**Verifique se o NSG ao utilizar o IP Standard SKU Pubilc** Ao utilizar um SKU standard e iPs públicos, deve haver um NSG criado e uma regra explícita para permitir o tráfego para a NVA.

**Verifique se o tráfego pode ser encaminhado para a NVA**

1. No [portal Azure](https://portal.azure.com), open **Network Watcher,** selecione **Next Hop**.
2. Especifique um VM configurado para redirecionar o tráfego para a NVA, e um endereço IP de destino para visualizar o próximo salto. 
3. Se o NVA não estiver listado como o **próximo salto,** verifique e atualize as tabelas de rota Azure.

**Verifique se o tráfego pode chegar à NVA**

1. No [portal Azure](https://portal.azure.com), abra **o Observador de Rede** e, em seguida, selecione IP Flow **Check**. 
2. Especifique o VM e o endereço IP do NVA e, em seguida, verifique se o tráfego está bloqueado por quaisquer grupos de segurança da Rede (NSG).
3. Se houver uma regra NSG que bloqueie o tráfego, localize o NSG em regras **de segurança eficazes** e, em seguida, atualize-a para permitir que o tráfego passe. Em seguida, executar **o fluxo IP Verifique** novamente e utilize a resolução de problemas de **ligação** para testar as comunicações TCP de VM para o seu endereço IP interno ou externo.

**Verifique se as NVA e vMs estão a ouvir o tráfego esperado**

1. Ligue-se à NVA utilizando RDP ou SSH e, em seguida, executar o seguinte comando:

    Para Windows:

    ```console
   netstat -an
    ```

    Para Linux:

    ```console
   netstat -an | grep -i listen
    ```
2. Se não vir a porta TCP que é utilizada pelo software NVA que está listado nos resultados, tem de configurar a aplicação na NVA e VM para ouvir e responder ao tráfego que chega a essas portas. [Contacte o fornecedor NVA para obter assistência, se necessário.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="check-nva-performance"></a>Verifique o desempenho da NVA

### <a name="validate-vm-cpu"></a>Validar CPU VM

Se o uso do CPU se aproximar dos 100%, poderá experimentar problemas que afetam as quedas de pacotes de rede. O seu VM reporta cpu médio por um período de tempo específico no portal Azure. Durante um pico de CPU, investigue qual o processo no VM convidado que está a causar o alto CPU, e atenuá-lo, se possível. Também pode ter de redimensionar o VM para um tamanho SKU maior ou, para conjunto de escala de máquina virtual, aumentar a contagem de instâncias ou definir para escala automática na utilização do CPU. Para qualquer uma destas questões, [contacte o fornecedor NVA para obter assistência,](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)se necessário.

### <a name="validate-vm-network-statistics"></a>Validar estatísticas da Rede VM

Se a rede VM utilizar picos ou mostrar períodos de alta utilização, também poderá ter de aumentar o tamanho SKU do VM para obter capacidades de produção mais elevadas. Também pode recolocar o VM com ativação de rede acelerada. Para verificar se o NVA suporta a funcionalidade de Rede Acelerada, [contacte o fornecedor NVA para obter assistência,](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)se necessário.

## <a name="advanced-network-administrator-troubleshooting"></a>Resolução avançada de problemas do administrador de rede

### <a name="capture-network-trace"></a>Rastrear vestígios de rede
Capture um traço de rede simultâneo na origem VM, na NVA e no VM de destino enquanto corre **[PsPing](/sysinternals/downloads/psping)** ou **Nmap**, e depois pare o rastreio.

1. Para capturar um rastreio de rede simultâneo, executar o seguinte comando:

   **Para janelas**

   captura de início de traço de netsh=sim tracefile=c:\server_IP.etl scenario=netconnection

   **Para Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Utilize **PsPing** ou **Nmap** da fonte VM para o destino VM (por exemplo: `PsPing 10.0.0.4:80` ou `Nmap -p 80 10.0.0.4` ).
3. Abra os vestígios de rede a partir do VM de destino utilizando [o Monitor de Rede](https://download.cnet.com/s/network-monitor) ou tcpdump. Aplique um filtro de visualização para o IP do VM de origem a que executou **PsPing** ou **Nmap,** tais como `IPv4.address==10.0.0.4 (Windows netmon)` ou `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analisar vestígios

Se não vir os pacotes a chegar ao traço VM de backend, é provável que uma interferência de NSG ou UDR ou as tabelas de encaminhamento NVA estejam incorretas.

Se vir que os pacotes estão a ser enviados, mas que não há resposta, poderá ter de resolver um problema da aplicação de VM ou de firewall. Para qualquer uma destas questões, [contacte o fornecedor NVA para obter assistência, se necessário.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)