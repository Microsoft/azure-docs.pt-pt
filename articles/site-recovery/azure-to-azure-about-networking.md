---
title: Sobre o funcionamento em rede na recuperação após desastre do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Fornece uma descrição geral do funcionamento em rede para a replicação de VMs do Azure com o Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: fb80af9df58fba41e5ece060099eb98e3d2f90fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883152"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Sobre o funcionamento em rede no Azure para replicação



Este artigo fornece orientações de rede, ao replicar e recuperar as VMs do Azure de uma região para outro, utilizando [do Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece a recuperação após desastre para [este cenário](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestrutura de rede típica

O diagrama seguinte mostra um ambiente típico do Azure, para aplicações em execução em VMs do Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se estiver a utilizar do Azure ExpressRoute ou uma ligação VPN a partir da rede no local para o Azure, o ambiente é o seguinte:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, redes estão protegidas através de firewalls e os grupos de segurança de rede (NSGs). Firewalls utilizam URL ou IP com base em listas de permissões para controlar a conectividade de rede. Os NSGs fornecem regras que utilizam intervalos de endereços IP para controlar a conectividade de rede.

>[!IMPORTANT]
> Utilizar um proxy autenticado para conectividade de rede de controle não é suportado pelo Site Recovery e não é possível ativar a replicação.


## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a utilizar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita estes URLs do Site Recovery:


**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | É necessário para que os dados podem ser escritos para a conta de armazenamento de cache na região de origem da VM.
login.microsoftonline.com | Necessário para autorização e autenticação para os URLs do serviço Site Recovery.
*.hypervrecoverymanager.windowsazure.com | É necessário para que a comunicação de serviço de recuperação de Site pode ocorrer a partir da VM.
*.servicebus.windows.net | É necessário para que os dados de monitorização e diagnóstico do Site Recovery podem ser escritos da VM.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a utilizar um proxy de firewall baseado em IP ou regras do NSG para controlar a conectividade de saída, estes intervalos IP têm de ser permitidos.

- Todos os intervalos de endereços IP que correspondem às contas de armazenamento na região de origem
    - Criar uma [etiquetas de serviço de armazenamento](../virtual-network/security-overview.md#service-tags) com base em regras NSG para a região de origem.
    - Permitir que estes endereços, de modo a que os dados podem ser escritos para a conta de armazenamento de cache, a partir da VM.
- Criar uma [etiquetas de serviço do Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) com base em regras NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
    - Se novos endereços são adicionados ao Azure Active Directory (AAD) no futuro, terá de criar novas regras NSG.
- Site Recovery service endpoint endereços IP - disponíveis numa [arquivo XML](https://aka.ms/site-recovery-public-ips) e dependem de sua localização de destino.
- Recomendamos que crie as regras do NSG necessárias num NSG de teste e certifique-se de que não existem problemas antes de criar as regras num NSG de produção.


Intervalos de endereços de IP de recuperação de site são os seguintes:

   **Target** | **Recuperação de site IP** |  **Recuperação de site IP de monitorização**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132 | 13.94.47.61
   Sudeste Asiático | 52.187.58.193 | 13.76.179.223
   Índia Central | 52.172.187.37 | 104.211.98.185
   Sul da Índia | 52.172.46.220 | 104.211.224.190
   EUA Centro-Norte | 23.96.195.247 | 168.62.249.226
   Europa do Norte | 40.69.212.238 | 52.169.18.8
   Europa Ocidental | 52.166.13.64 | 40.68.93.145
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
   Sul do Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte do Reino Unido | 51.142.209.167 | 13.87.102.68
   Coreia do Sul Central | 52.231.28.253 | 52.231.32.85
   Coreia do Sul | 52.231.298.185 | 52.231.200.144
   França Central | 52.143.138.106 | 52.143.136.55
   Sul de França | 52.136.139.227 |52.136.136.62
   Austrália central| 20.36.34.70 | 20.36.46.142
   Austrália Central 2| 20.36.69.62 | 20.36.74.130

## <a name="example-nsg-configuration"></a>Exemplo de configuração de NSG

Este exemplo mostra como configurar regras NSG para uma VM a replicar.

- Se estiver a utilizar as regras do NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para a porta: 443 para todo os necessários intervalos de endereços IP.
- O exemplo parte do princípio de que a localização de origem da VM é "Leste E.u.a." e a localização de destino é "E.u.a. Central".

### <a name="nsg-rules---east-us"></a>Regras do NSG - E.U.A. leste

1. Crie uma regra de segurança de saída HTTPS (443) para "Eastus" no NSG conforme mostrado na captura de ecrã abaixo.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança de saída HTTPS (443) para "AzureActiveDirectory" no NSG conforme mostrado na captura de ecrã abaixo.

      ![etiqueta de aad](./media/azure-to-azure-about-networking/aad-tag.png)

3. Crie regras de saída HTTPS (443) para os IPs de recuperação de Site que correspondem à localização de destino:

   **Localização** | **Endereço de IP de recuperação de site** |  **Endereço IP de monitorização de recuperação de site**
    --- | --- | ---
   EUA Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras do NSG - E.U.A. Central

Estas regras são necessárias para que os replicação pode ser ativada a partir da região de destino para a publicação de região de origem-ativação pós-falha:

1. Crie uma regra de segurança de saída HTTPS (443) para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança de saída HTTPS (443) para "AzureActiveDirectory" no NSG.

3. Crie regras de saída HTTPS (443) para os IPs de recuperação de Site que correspondem à localização de origem:

   **Localização** | **Endereço de IP de recuperação de site** |  **Endereço IP de monitorização de recuperação de site**
    --- | --- | ---
   EUA Central | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Configuração de aplicação virtual de rede

Se estiver a utilizar aplicações virtuais de rede (NVAs) para controlar o tráfego de rede de saída das VMs, a aplicação poderá ficar bloqueada se todo o tráfego de replicação passa através da NVA. Recomendamos que crie um ponto de extremidade do serviço de rede na sua rede virtual para "Armazenamento", para que o tráfego de replicação não é transmitido para a NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Criar o ponto final de serviço de rede para armazenamento
Pode criar um ponto de extremidade do serviço de rede na sua rede virtual para "Armazenamento", para que o tráfego de replicação não deixam o limite do Azure.

- Selecione a rede virtual do Azure e clique em "Pontos finais de serviço"

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Clique em "Adicionar" e "Adicionar pontos finais de serviço" separador abre-se
- Selecione "Microsoft. Storage" em "Service" e as sub-redes necessárias em campo "Sub-redes" e clique em "Adicionar"

>[!NOTE]
>Não restringir o acesso de rede virtual às contas de armazenamento utilizadas para o ASR. Deve permitir acesso de "Todas as redes"

### <a name="forced-tunneling"></a>Túnel forçado

Pode substituir a rota de sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com um [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para uma aplicação de virtual de rede de no local (NVA), mas esta configuração não é recomendada para o Site Recovery replicação. Se estiver a utilizar rotas personalizadas, deve [criar um ponto de final de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) no seu virtual de rede para "Armazenamento", para que o tráfego de replicação não deixam o limite do Azure.

## <a name="next-steps"></a>Passos Seguintes
- Começar a proteger as cargas de trabalho por [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
- Saiba mais sobre [retenção de endereço IP](site-recovery-retain-ip-azure-vm-failover.md) para ativação pós-falha da máquina virtual do Azure.
- Saiba mais sobre a recuperação após desastre de [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
