---
title: Sobre networking em Azure VM recuperação de desastres com recuperação do local de Azure
description: Fornece uma visão geral da rede para replicação de VMs Azure usando Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370445"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Sobre networking em Azure VM recuperação de desastres



Este artigo fornece orientação de networking quando você está replicando e recuperando VMs Azure de uma região para outra, usando [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)

## <a name="typical-network-infrastructure"></a>Infraestrutura típica de rede

O diagrama a seguir retrata um ambiente típico de Azure, para aplicações em execução em VMs Azure:

![Diagrama que retrata um ambiente típico de Azure para aplicações em execução em VMs Azure.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se estiver a utilizar a Azure ExpressRoute ou uma ligação VPN da sua rede no local para a Azure, o ambiente é o seguinte:

![ambiente cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, as redes são protegidas utilizando firewalls e grupos de segurança de rede (NSGs). As etiquetas de serviço devem ser utilizadas para controlar a conectividade da rede. Os NSGs devem permitir que várias tags de serviço controlem a conectividade de saída.

>[!IMPORTANT]
> A utilização de um representante autenticado para controlar a conectividade da rede não é suportada pela Recuperação do Site, e a replicação não pode ser ativada.

>[!NOTE]
>- A filtragem baseada em endereços IP não deve ser realizada para controlar a conectividade de saída.
>- Os endereços IP de recuperação do local de Azure não devem ser adicionados na tabela de encaminhamento Azure para controlar a conectividade de saída.

## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a utilizar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita que estes URLs de Recuperação do Site:

**URL** | **Detalhes**
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser escritos na conta de armazenamento de cache na região de origem a partir do VM. Se souber de todas as contas de armazenamento de cache dos seus VMs, pode permitir o acesso aos URLs de conta de armazenamento específico (Ex: cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *.blob.core.windows.net
login.microsoftonline.com | Requerida autorização e autenticação para os URLs do serviço de Recuperação do Local.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação do serviço de recuperação do local possa ocorrer a partir do VM.
*.servicebus.windows.net | Necessário para que os dados de monitorização e diagnóstico da Recuperação do Local possam ser escritos a partir do VM.
*.vault.azure.net | Permite o acesso para permitir a replicação de máquinas virtuais ativadas por ADE através do portal
*.automation.ext.azure.com | Permite permitir a atualização automática do agente de mobilidade para um item replicado via portal

## <a name="outbound-connectivity-using-service-tags"></a>Conectividade de saída usando tags de serviço

Ao utilizar o NSG para controlar a conectividade de saída, estas etiquetas de serviço precisam de ser permitidas.

- Para as contas de armazenamento na região de origem:
    - Crie uma regra NSG baseada em [serviço de armazenamento](../virtual-network/network-security-groups-overview.md#service-tags) para a região de origem.
    - Permitir estes endereços para que os dados possam ser escritos na conta de armazenamento de cache, a partir do VM.
- Criar uma regra NSG baseada em etiquetas de [serviço Azure Ative (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) para permitir o acesso a todos os endereços IP correspondentes ao AAD
- Crie uma regra NSG baseada em tags do EventsHub para a região alvo, permitindo o acesso à monitorização da Recuperação do Local.
- Crie uma regra NSG baseada em etiquetas de serviço AzureSiteRecovery para permitir o acesso ao serviço de Recuperação de Locais em qualquer região.
- Crie uma regra NSG baseada em etiquetas AzureKeyVault. Isto é necessário apenas para permitir a replicação de máquinas virtuais ativadas por ADE via portal.
- Crie uma regra NSG baseada em etiquetas de serviço GuestAndHybrid. Isto é necessário apenas para permitir a atualização automática do agente de mobilidade para um item replicado via portal.
- Recomendamos que crie as regras NSG necessárias num teste NSG e verifique se não existem problemas antes de criar as regras sobre uma NSG de produção.

## <a name="example-nsg-configuration"></a>Configuração NSG exemplo

Este exemplo mostra como configurar as regras NSG para um VM replicar.

- Se estiver a utilizar as regras NSG para controlar a conectividade de saída, utilize as regras "Permitir a saída HTTPS" para a porta:443 para todas as gamas de endereços IP necessárias.
- O exemplo pressupõe que a localização da origem VM é "East US" e que a localização alvo é "Central US".

### <a name="nsg-rules---east-us"></a>Regras NSG - Leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.EastUS" no NSG, como mostrado na imagem abaixo.

      ![Screenshot mostra adicionar regra de segurança de saída para um grupo de segurança de rede para o ponto de armazenamento East U. S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, como mostrado na imagem abaixo.

      ![Screenshot mostra Adicionar regra de segurança de saída para um grupo de segurança de rede para Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. Semelhante às regras de segurança acima, crie uma regra de segurança HTTPS (443) para "EventHub.CentralUS" no NSG que corresponda à localização do alvo. Isto permite o acesso à monitorização da Recuperação do Local.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação de Sítios em qualquer região.

### <a name="nsg-rules---central-us"></a>Regras NSG - Central US

Estas regras são necessárias para que a replicação possa ser ativada da região-alvo até à região de origem após o fracasso:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Semelhante às regras de segurança acima, crie uma regra de segurança HTTPS (443) para "EventHub.EastUS" no NSG que corresponda à localização da fonte. Isto permite o acesso à monitorização da Recuperação do Local.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação de Sítios em qualquer região.

## <a name="network-virtual-appliance-configuration"></a>Configuração de aparelho virtual de rede

Se estiver a utilizar aparelhos virtuais de rede (NVAs) para controlar o tráfego de rede de saída a partir de VMs, o aparelho pode ser estrangulado se todo o tráfego de replicação passar pelo NVA. Recomendamos a criação de um ponto final de serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não vá para a NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Criar ponto final de serviço de rede para armazenamento
Pode criar um ponto final de serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite do Azure.

- Selecione a sua rede virtual Azure e clique em 'Pontos finais de serviço'

    ![ponto final de armazenamento](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Clique em 'Adicionar' e 'Adicionar pontos finais' abre
- Selecione 'Microsoft.Storage' em 'Serviço' e as sub-redes necessárias no campo 'Subnetas' e clique em 'Adicionar'

>[!NOTE]
>Não restringir o acesso à rede virtual às suas contas de armazenamento utilizadas para ASR. Deve permitir o acesso a partir de 'Todas as redes'

### <a name="forced-tunneling"></a>Túnel forçado

Pode sobrepor a rota do sistema padrão da Azure para o prefixo de endereço 0.0.0.0/0 com uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para um aparelho virtual de rede no local (NVA), mas esta configuração não é recomendada para a replicação da Recuperação do Local. Se estiver a utilizar rotas personalizadas, deverá [criar um ponto final de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) na sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite Azure.

## <a name="next-steps"></a>Passos seguintes
- Comece a proteger as suas cargas de trabalho [replicando máquinas virtuais Azure](./azure-to-azure-quickstart.md).
- Saiba mais sobre [a retenção de endereços IP](site-recovery-retain-ip-azure-vm-failover.md) para falha de falha da máquina virtual Azure.
- Saiba mais sobre a recuperação de desastres das [máquinas virtuais Azure com o ExpressRoute.](azure-vm-disaster-recovery-with-expressroute.md)