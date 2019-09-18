---
title: Solucionando problemas de solução de virtualização de rede no Azure | Microsoft Docs
description: Saiba como solucionar problemas de solução de virtualização de rede no Azure.
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
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056823"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemas de solução de virtualização de rede no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode experimentar problemas de conectividade de VM ou VPN e erros ao usar uma solução de virtualização de rede de terceiros (NVA) em Microsoft Azure. Este artigo fornece etapas básicas para ajudá-lo a validar os requisitos básicos da plataforma Azure para as configurações do NVA.

O suporte técnico para NVAs de terceiros e sua integração com a plataforma do Azure é fornecido pelo fornecedor do NVA.

> [!NOTE]
> Se você tiver um problema de conectividade ou de roteamento que envolve um NVA, deverá [entrar em contato diretamente com o fornecedor do NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de verificação para solução de problemas com o fornecedor do NVA

- Atualizações de software para o software de VM NVA
- Configuração e funcionalidade da conta de serviço
- UDRs (rotas definidas pelo usuário) em sub-redes de rede virtual que direcionam o tráfego para o NVA
- UDRs em sub-redes de rede virtual que direcionam o tráfego do NVA
- Tabelas de roteamento e regras dentro do NVA (por exemplo, de NIC1 para NIC2)
- Rastreamento em NICs NVA para verificar o recebimento e o envio do tráfego de rede
- Ao usar um SKU Standard e IPs públicos, deve haver um NSG criado e uma regra explícita para permitir que o tráfego seja roteado para o NVA.

## <a name="basic-troubleshooting-steps"></a>Etapas básicas de solução de problemas

- Verificar a configuração básica
- Verificar o desempenho do NVA
- Solução de problemas de rede avançada

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Verifique os requisitos mínimos de configuração do NVAs no Azure

Cada NVA tem requisitos básicos de configuração para funcionar corretamente no Azure. A seção a seguir fornece as etapas para verificar essas configurações básicas. Para obter mais informações, [entre em contato com o fornecedor do NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Verifique se o encaminhamento de IP está habilitado em NVA**

Utilizar o portal do Azure

1. Localize o recurso NVA no [portal do Azure](https://portal.azure.com), selecione rede e, em seguida, selecione a interface de rede.
2. Na página interface de rede, selecione configuração de IP.
3. Verifique se o encaminhamento de IP está habilitado.

Utilizar o PowerShell

1. Abra o PowerShell e entre em sua conta do Azure.
2. Execute o seguinte comando (substitua os valores entre colchetes com suas informações):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Verifique a propriedade **EnableIPForwarding** .
4. Se o encaminhamento de IP não estiver habilitado, execute os seguintes comandos para habilitá-lo:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Verificar NSG ao usar o IP PUBILC SKU padrão** Ao usar um SKU Standard e IPs públicos, deve haver um NSG criado e uma regra explícita para permitir o tráfego para o NVA.

**Verifique se o tráfego pode ser roteado para o NVA**

1. Em [portal do Azure](https://portal.azure.com), abra **observador de rede**, selecione **próximo salto**.
2. Especifique uma VM que esteja configurada para redirecionar o tráfego para o NVA e um endereço IP de destino no qual exibir o próximo salto. 
3. Se o NVA não estiver listado como o **próximo salto**, verifique e atualize as tabelas de rotas do Azure.

**Verifique se o tráfego pode alcançar o NVA**

1. Em [portal do Azure](https://portal.azure.com), abra o **observador de rede**e selecione **verificação de fluxo de IP**. 
2. Especifique a VM e o endereço IP do NVA e, em seguida, verifique se o tráfego está bloqueado por qualquer NSG (grupos de segurança de rede).
3. Se houver uma regra NSG que bloqueie o tráfego, localize o NSG em regras de **segurança em vigor** e, em seguida, atualize-o para permitir que o tráfego passe. Em seguida, execute a **verificação de fluxo de IP** novamente e use a solução de problemas de **conexão** para testar as comunicações TCP da VM para seu endereço IP interno ou externo.

**Verificar se NVA e VMs estão ouvindo o tráfego esperado**

1. Conecte-se ao NVA usando RDP ou SSH e, em seguida, execute o seguinte comando:

    Para Windows:

        netstat -an

    Para Linux:

        netstat -an | grep -i listen
2. Se você não vir a porta TCP usada pelo software NVA listado nos resultados, deverá configurar o aplicativo no NVA e na VM para escutar e responder ao tráfego que atinge essas portas. [Entre em contato com o fornecedor do NVA para obter assistência, conforme necessário](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Verificar o desempenho do NVA

### <a name="validate-vm-cpu"></a>Validar CPU da VM

Se o uso da CPU ficar perto de 100%, você poderá enfrentar problemas que afetam os descartes de pacotes de rede. Sua VM relata a média de CPU para um período de tempo específico no portal do Azure. Durante um pico de CPU, investigue qual processo na VM convidada está causando a alta CPU e atenue-a, se possível. Você também pode precisar redimensionar a VM para um tamanho de SKU maior ou, para o conjunto de dimensionamento de máquinas virtuais, aumentar a contagem de instâncias ou definir como dimensionamento automático no uso da CPU. Para qualquer um desses problemas, [entre em contato com o fornecedor do NVA para obter assistência](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), conforme necessário.

### <a name="validate-vm-network-statistics"></a>Validar estatísticas de rede VM

Se a rede VM usar picos ou mostrar períodos de alto uso, talvez você também precise aumentar o tamanho do SKU da VM para obter recursos de taxa de transferência mais altos. Você também pode reimplantar a VM com a rede acelerada habilitada. Para verificar se o NVA dá suporte ao recurso de rede acelerada, [entre em contato com o fornecedor do NVA para obter assistência](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), conforme necessário.

## <a name="advanced-network-administrator-troubleshooting"></a>Solução de problemas de administrador de rede avançado

### <a name="capture-network-trace"></a>Capturar rastreamento de rede
Capture um rastreamento de rede simultâneo na VM de origem, o NVA e a VM de destino enquanto você executa **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** ou **nmap**e, em seguida, interrompe o rastreamento.

1. Para capturar um rastreamento de rede simultâneo, execute o seguinte comando:

   **Para Windows**

   netsh trace Start Capture = Sim TraceFile = cenário c:\server_IP.etl = NetConnection

   **Para Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Use **PsPing** ou **nmap** da VM de origem para a VM de destino (por exemplo `PsPing 10.0.0.4:80` : `Nmap -p 80 10.0.0.4`ou).
3. Abra o rastreamento de rede da VM de destino usando [Monitor de rede](https://www.microsoft.com/download/details.aspx?id=4865) ou tcpdump. Aplique um filtro de exibição para o IP da VM de origem que você executou **PsPing** ou **nmap** `IPv4.address==10.0.0.4 (Windows netmon)` , como `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` ou (Linux).

### <a name="analyze-traces"></a>Analisar rastreamentos

Se você não vir os pacotes de entrada para o rastreamento de VM de back-end, provavelmente haverá um NSG ou UDR interferindo ou as tabelas de roteamento NVA estão incorretas.

Se vir que os pacotes estão a ser enviados, mas que não há resposta, poderá ter de resolver um problema da aplicação de VM ou de firewall. Para qualquer um desses problemas, [entre em contato com o fornecedor do NVA para obter assistência, conforme necessário](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).