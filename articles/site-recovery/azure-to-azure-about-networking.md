---
title: Sobre a rede na recuperação de desastre da VM do Azure com o Azure Site Recovery
description: Fornece uma visão geral da rede para replicação de VMs do Azure usando Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/23/2020
ms.author: sutalasi
ms.openlocfilehash: aeab1960b065538635fdd63c43d779287f8cd9ee
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759828"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Sobre a rede na recuperação de desastre da VM do Azure



Este artigo fornece diretrizes de rede quando você está replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestrutura de rede típica

O diagrama a seguir ilustra um ambiente típico do Azure, para aplicativos em execução em VMs do Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se você estiver usando o Azure ExpressRoute ou uma conexão VPN da sua rede local para o Azure, o ambiente será o seguinte:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, as redes são protegidas usando firewalls e NSGs (grupos de segurança de rede). Os firewalls usam a URL ou a lista de permissões baseada em IP para controlar a conectividade de rede. NSGs fornecem regras que usam intervalos de endereços IP para controlar a conectividade de rede.

>[!IMPORTANT]
> O uso de um proxy autenticado para controlar a conectividade de rede não tem suporte pelo Site Recovery, e a replicação não pode ser habilitada.


## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita estas Site Recovery URLs:


**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados na conta de armazenamento de cache na região de origem da VM. Se você souber todas as contas de armazenamento em cache para suas VMs, poderá permitir o acesso às URLs de conta de armazenamento específicas (ex: cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *. blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação do serviço de Site Recovery possa ocorrer na VM.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e de diagnóstico de Site Recovery possam ser gravados da VM.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a usar um NSG para controlar a conectividade de saída, estas etiquetas de serviço precisam de ser permitidas.

- Todos os intervalos de endereços IP que correspondem às contas de armazenamento na região de origem
    - Crie uma regra de NSG baseada em [marca de serviço de armazenamento](../virtual-network/security-overview.md#service-tags) para a região de origem.
    - Permita esses endereços para que os dados possam ser gravados na conta de armazenamento de cache, da VM.
- Criar uma [etiquetas de serviço do Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) com base em regras NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
- Crie uma regra de NSG baseada na marca de serviço EventsHub para a região de destino, permitindo o acesso ao monitoramento de Site Recovery.
- Crie uma regra de NSG baseada na marca de serviço AzureSiteRecovery para permitir acesso ao Site Recovery Service em qualquer região.
- Recomendamos que você crie as regras NSG necessárias em um NSG de teste e verifique se não há problemas antes de criar as regras em um NSG de produção.

## <a name="example-nsg-configuration"></a>Exemplo de configuração de NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você estiver usando regras de NSG para controlar a conectividade de saída, use regras de "permitir HTTPS de saída" para a porta: 443 para todos os intervalos de endereços IP necessários.
- O exemplo supõe que o local de origem da VM é "leste dos EUA" e o local de destino é "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Eastus" no NSG, conforme mostrado na captura de tela abaixo.

      ![marca de armazenamento](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, conforme mostrado na captura de tela abaixo.

      ![AAD – marca](./media/azure-to-azure-about-networking/aad-tag.png)

3. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Centralus" no NSG que corresponde ao local de destino. Isso permite o acesso ao monitoramento de Site Recovery.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

### <a name="nsg-rules---central-us"></a>Regras de NSG-EUA Central

Essas regras são necessárias para que a replicação possa ser habilitada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Centralus" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Eastus" no NSG que corresponde ao local de origem. Isso permite o acesso ao monitoramento de Site Recovery.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

## <a name="network-virtual-appliance-configuration"></a>Configuração da solução de virtualização de rede

Se você estiver usando NVAs (soluções de virtualização de rede) para controlar o tráfego de rede de saída de VMs, o dispositivo poderá ficar limitado se todo o tráfego de replicação passar pelo NVA. É recomendável criar um ponto de extremidade de serviço de rede em sua rede virtual para "armazenamento" para que o tráfego de replicação não vá para o NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Criar ponto de extremidade de serviço de rede para armazenamento
Você pode criar um ponto de extremidade de serviço de rede em sua rede virtual para "armazenamento" para que o tráfego de replicação não saia do limite do Azure.

- Selecione sua rede virtual do Azure e clique em ' pontos de extremidade de serviço '

    ![armazenamento-ponto de extremidade](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Clique na guia "Adicionar" e "adicionar pontos de extremidade de serviço" é aberta
- Selecione ' Microsoft. Storage ' em ' serviço ' e as sub-redes necessárias no campo ' sub-redes ' e clique em ' Adicionar '

>[!NOTE]
>Não restrinja o acesso à rede virtual às suas contas de armazenamento usadas para o ASR. Você deve permitir o acesso de ' todas as redes '

### <a name="forced-tunneling"></a>Túnel forçado

Você pode substituir a rota do sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 por uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego da VM para uma NVA (solução de virtualização de rede) local, mas essa configuração não é recomendada para replicação de site Recovery. Se você estiver usando rotas personalizadas, deverá [criar um ponto de extremidade de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para "armazenamento" para que o tráfego de replicação não deixe o limite do Azure.

## <a name="next-steps"></a>Passos seguintes
- Comece a proteger suas cargas de trabalho [replicando as máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
- Saiba mais sobre a [retenção de endereço IP](site-recovery-retain-ip-azure-vm-failover.md) para o failover de máquina virtual do Azure.
- Saiba mais sobre a recuperação de desastre de [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
