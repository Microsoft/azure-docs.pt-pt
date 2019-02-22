---
title: Resolução de problemas de aplicação virtual de rede no Azure | Documentos da Microsoft
description: Aprenda a solucionar os problemas de aplicação virtual de rede no Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 40e034a563074e10a2dfbee36b6792a095022057
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649634"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemas de aplicação virtual de rede no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poderá notar VM ou problemas de conectividade VPN e de erros quando a utilizar uma aplicação de rede Virtual (NVA) no Microsoft Azure de terceiros. Este artigo fornece os passos básicos para o ajudar a validar requisitos básicos de plataforma do Azure para configurações de NVA.

Suporte técnico para as NVAs de terceiros e sua integração com a plataforma do Azure é fornecido pelo fabricante do NVA.

> [!NOTE]
> Se tiver uma conectividade ou o problema de encaminhamento que envolve uma NVA, deve [contacte o fornecedor da NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) diretamente.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de verificação para resolução de problemas com o fornecedor da NVA

- Atualizações de software para o software de VM de NVA
- Configuração de conta de serviço e funcionalidade
- Definido pelo utilizador rotas (UDRs) em sub-redes da rede virtual que direcionar o tráfego para a NVA
- UDRs em sub-redes da rede virtual que direcionar o tráfego de NVA
- Tabelas de roteamento e regras da NVA (por exemplo, a partir de NIC1 para NIC2)
- Rastreio em NICs de NVA para verificar a receber e enviar o tráfego de rede
- Quando utilizar um Standard SKU e IPs públicos, deve haver um NSG criado e uma regra de explícita para permitir o tráfego encaminhado para a NVA.

## <a name="basic-troubleshooting-steps"></a>Passos de resolução de problemas básicos

- Verifique a configuração básica
- Verificar o desempenho da NVA
- Resolução de problemas de rede avançada

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Verifique os requisitos mínimos de configuração para NVAs no Azure

Cada NVA tem requisitos de configuração básica para funcionar corretamente no Azure. A secção seguinte fornece os passos para verificar essas configurações básicas. Para obter mais informações, [contacte o fornecedor da NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Verifique se o reencaminhamento de IP está ativado na NVA**

Utilizar o portal do Azure

1. Localize o recurso NVA na [portal do Azure](https://portal.azure.com), selecione o sistema de rede e, em seguida, selecione a interface de rede.
2. Na página de interface de rede, selecione a configuração de IP.
3. Certifique-se de que o reencaminhamento de IP está ativado.

Utilizar o PowerShell

1. Abra o PowerShell e, em seguida, inicie sessão na sua conta do Azure.
2. Execute o seguinte comando (substitua os valores entre parênteses com as suas informações):

   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. Verifique os **EnableIPForwarding** propriedade.
4. Se o reencaminhamento de IP não estiver ativado, execute os seguintes comandos para ativá-la:

   US $nic2 = Get-AzNetworkInterface - ResourceGroupName <ResourceGroupName> -nome <NicName> us $nic2. EnableIPForwarding = 1 conjunto AzNetworkInterface - NetworkInterface us $nic2 executar: $nic2 #and verificar um resultado esperado: EnableIPForwarding: True NetworkSecurityGroup : null

**Verificar a existência de NSG ao utilizar o IP de Pubilc de SKU Standard** quando utilizar um Standard SKU e IPs públicos, tem de existir um NSG criado e uma regra de explícita para permitir o tráfego para a NVA.

**Verifique se o tráfego pode ser encaminhado para a NVA**

1. No [portal do Azure](https://portal.azure.com), abra **observador de rede**, selecione **próximo salto**.
2. Especifique uma VM que está configurada para redirecionar o tráfego para a NVA e um endereço IP de destino no qual pretende ver o próximo salto. 
3. Se a NVA não estiver listada como o **próximo salto**, verificar e atualizar as tabelas de rota do Azure.

**Verifique se o tráfego pode chegar a NVA**

1. Na [portal do Azure](https://portal.azure.com), abra **observador de rede**e, em seguida, selecione **IP fluxo verificar**. 
2. Especifique a VM e o endereço IP da NVA e, em seguida, verifique se o tráfego é bloqueado por quaisquer grupos de segurança de rede (NSG).
3. Se houver uma regra NSG que bloqueia o tráfego, localize o NSG pela **segurança efetivas** regras e, em seguida, atualizá-la para permitir o tráfego para passar. Em seguida, execute **IP fluxo verificar** novamente e usar **resolver problemas de ligação** para testar as comunicações de TCP de VM para o seu endereço IP interno ou externo.

**Verifique se NVA e as VMs estão à escuta para o tráfego esperado**

1. Ligue-se para a NVA utilizando RDP ou SSH e, em seguida, execute o comando a seguir:

    Para Windows:

        netstat -an

    Para Linux:

        netstat -an | grep -i listen
2. Se não vir a porta TCP utilizada pelo software NVA está listado nos resultados da tem de configurar a aplicação na NVA e VM para escutar e responder ao tráfego que atinge essas portas. [Contacte o fornecedor NVA para obter assistência, conforme necessário](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Verificar o desempenho da NVA

### <a name="validate-vm-cpu"></a>Validar a CPU da VM

Se a utilização da CPU chega perto de 100 por cento, podem ocorrer problemas que afetam as quedas de pacotes de rede. Os relatórios VM média da CPU para um intervalo de tempo específico no portal do Azure. Durante um pico de CPU, que processar no convidado que VM está a causar a CPU elevada de investigar e mitigá-lo, se possível. Também poderá ter de redimensionar a VM para um tamanho SKU superior ou, para o conjunto de dimensionamento de máquina virtual, aumentar a contagem de instâncias ou definir para se dimensionarem automaticamente na utilização da CPU. Para qualquer um desses problemas, [contacte o fornecedor da NVA para assistência](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), conforme necessário.

### <a name="validate-vm-network-statistics"></a>Validar as estatísticas de rede VM

Se utilizar a rede VM picos ou mostra períodos de elevada utilização, que também poderá ter de aumentar o tamanho SKU de VM para obter capacidades de débito mais elevadas. Também pode Reimplementar a VM ao ter redes aceleradas ativada. Para verificar se a NVA suporta a funcionalidade de redes aceleradas, [contacte o fornecedor da NVA para assistência](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), conforme necessário.

## <a name="advanced-network-administrator-troubleshooting"></a>Administrador de rede avançadas de resolução de problemas

### <a name="capture-network-trace"></a>Captura de rastreio de rede
Capturar um rastreio de rede simultâneo na VM de origem, a NVA e o VM de destino enquanto executa **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** ou **Nmap**e, em seguida, pare o rastreio.

1. Para capturar um rastreio de rede simultâneas, execute o seguinte comando:

   **Para Windows**

   rastreio Netsh Iniciar captura = cenário do yes tracefile = netconnection

   **Para Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Uso **PsPing** ou **Nmap** da VM de origem para a VM de destino (por exemplo: `PsPing 10.0.0.4:80` ou `Nmap -p 80 10.0.0.4`).
3. Abra o rastreio de rede de VM de destino, utilizando [Monitor de rede](https://www.microsoft.com/download/details.aspx?id=4865) ou com tcpdump. Aplicar um filtro de apresentação para o IP da VM de origem tiver executado **PsPing** ou **Nmap** , como `IPv4.address==10.0.0.4 (Windows netmon)` ou `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analisar rastreios

Se não vir os pacotes para o rastreio da VM de back-end, é provável que um NSG ou UDR esteja a interferir ou as tabelas de roteamentos da NVA estão incorretas.

Se vir que os pacotes estão a ser enviados, mas que não há resposta, poderá ter de resolver um problema da aplicação de VM ou de firewall. Para qualquer um desses problemas, [contacte o fornecedor NVA para obter assistência, conforme necessário](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).