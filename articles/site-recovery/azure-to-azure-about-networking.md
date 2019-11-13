---
title: Sobre a rede na recuperação de desastre da VM do Azure com o Azure Site Recovery
description: Fornece uma visão geral da rede para replicação de VMs do Azure usando Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/22/2019
ms.author: sutalasi
ms.openlocfilehash: 09cd814ade25be438a17b83fb73e74b89c14e22f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954198"
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
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação do serviço de Site Recovery possa ocorrer na VM. Você pode usar o ' Site Recovery IP ' correspondente se o proxy de firewall oferecer suporte a IPs.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e de diagnóstico de Site Recovery possam ser gravados da VM. Você pode usar o ' Site Recovery Monitoring IP ' correspondente se o proxy de firewall oferecer suporte a IPs.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se você estiver usando um proxy de firewall baseado em IP ou regras NSG para controlar a conectividade de saída, esses intervalos de IP precisam ser permitidos.

- Todos os intervalos de endereços IP que correspondem às contas de armazenamento na região de origem
    - Crie uma regra de NSG baseada em [marca de serviço de armazenamento](../virtual-network/security-overview.md#service-tags) para a região de origem.
    - Permita esses endereços para que os dados possam ser gravados na conta de armazenamento de cache, da VM.
- Criar uma [etiquetas de serviço do Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) com base em regras NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
    - Se novos endereços são adicionados ao Azure Active Directory (AAD) no futuro, terá de criar novas regras NSG.
- Site Recovery endereços IP do ponto de extremidade de serviço-disponíveis em um [arquivo XML](https://aka.ms/site-recovery-public-ips) e dependem do local de destino. 
- Recomendamos que você crie as regras NSG necessárias em um NSG de teste e verifique se não há problemas antes de criar as regras em um NSG de produção.


Os intervalos de endereços IP Site Recovery são os seguintes:

   **Destino** | **Site Recovery IP** |  **IP de monitoramento de Site Recovery**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132 | 13.94.47.61
   Sudeste asiático | 52.187.58.193 | 13.76.179.223
   Índia Central | 52.172.187.37 | 104.211.98.185
   Sul da Índia | 52.172.46.220 | 104.211.224.190
   EUA Centro-Norte | 23.96.195.247 | 168.62.249.226
   Europa do Norte | 40.69.212.238 | 52.169.18.8
   Europa ocidental | 52.166.13.64 | 40.68.93.145
   EUA Leste | 13.82.88.226 | 104.45.147.24
   EUA Oeste | 40.83.179.48 | 104.40.26.199
   EUA Centro-Sul | 13.84.148.14 | 104.210.146.250
   EUA Central | 40.69.144.231 | 52.165.34.144
   EUA Leste 2 | 52.184.158.163 | 40.79.44.59
   Leste do Japão | 52.185.150.140 | 138.91.1.105
   Oeste do Japão | 52.175.146.69 | 138.91.17.38
   Sul do Brasil | 191.234.185.172 | 23.97.97.36
   Leste da Austrália | 104.210.113.114 | 191.239.64.144
   Sudeste da Austrália | 13.70.159.158 | 191.239.160.45
   Canadá Central | 52.228.36.192 | 40.85.226.62
   Leste do Canadá | 52.229.125.98 | 40.86.225.142
   EUA Centro-Oeste | 52.161.20.168 | 13.78.149.209
   EUA Oeste 2 | 52.183.45.166 | 13.66.228.204
   Reino Unido Oeste | 51.141.3.203 | 51.141.14.113
   Reino Unido Sul | 51.140.43.158 | 51.140.189.52
   Sul do Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte do Reino Unido | 51.142.209.167 | 13.87.102.68
   Coreia do Sul Central | 52.231.28.253 | 52.231.32.85
   Coreia do Sul | 52.231.198.185 | 52.231.200.144
   França Central | 52.143.138.106 | 52.143.136.55
   Sul de França | 52.136.139.227 |52.136.136.62
   Austrália Central| 20.36.34.70 | 20.36.46.142
   Austrália Central 2| 20.36.69.62 | 20.36.74.130
   Oeste da África do Sul | 102.133.72.51 | 102.133.26.128
   Norte da África do Sul | 102.133.160.44 | 102.133.154.128
   Gov (US) - Virginia | 52.227.178.114 | 23.97.0.197
   US Gov - Iowa | 13.72.184.23 | 23.97.16.186
   Gov (US) - Arizona | 52.244.205.45 | 52.244.48.85
   Gov (US) - Texas | 52.238.119.218 | 52.238.116.60
   US DoD Leste | 52.181.164.103 | 52.181.162.129
   US DoD Centro | 52.182.95.237 | 52.182.90.133
   China Norte | 40.125.202.254 | 42.159.4.151
   Norte da China 2 | 40.73.35.193 | 40.73.33.230
   Norte da China | 42.159.205.45 | 42.159.132.40
   Leste da China 2 | 40.73.118.52| 40.73.100.125
   Alemanha Centro-Oeste| 51.116.208.58| 51.116.58.128
   Alemanha Oeste-Central | 51.116.156.176 | 51.116.154.192
   Oeste da Suíça | 51.107.231.223| 51.107.154.128
   Norte da Suíça | 51.107.68.31| 51.107.58.128
   Leste da Noruega | 51.120.100.64| 51.120.98.128
   Oeste da Noruega | 51.120.220.65| 51.120.218.160

## <a name="example-nsg-configuration"></a>Exemplo de configuração de NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você estiver usando regras de NSG para controlar a conectividade de saída, use regras de "permitir HTTPS de saída" para a porta: 443 para todos os intervalos de endereços IP necessários.
- O exemplo supõe que o local de origem da VM é "leste dos EUA" e o local de destino é "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Eastus" no NSG, conforme mostrado na captura de tela abaixo.

      ![marca de armazenamento](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, conforme mostrado na captura de tela abaixo.

      ![AAD – marca](./media/azure-to-azure-about-networking/aad-tag.png)

3. Crie regras de saída HTTPS (443) para os IPs de Site Recovery que correspondem ao local de destino:

   **Localização** | **Endereço IP Site Recovery** |  **Endereço IP de monitoramento de Site Recovery**
    --- | --- | ---
   EUA Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras de NSG-EUA Central

Essas regras são necessárias para que a replicação possa ser habilitada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Centralus" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Crie regras de saída HTTPS (443) para os IPs de Site Recovery que correspondem ao local de origem:

   **Localização** | **Endereço IP Site Recovery** |  **Endereço IP de monitoramento de Site Recovery**
    --- | --- | ---
   EUA Leste | 13.82.88.226 | 104.45.147.24


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
