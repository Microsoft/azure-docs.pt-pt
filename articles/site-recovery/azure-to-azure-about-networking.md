---
title: Sobre o networking em recuperação de desastres azure VM com a Recuperação do Site Azure
description: Fornece uma visão geral da rede para a replicação de VMs Azure utilizando a Recuperação do Site Azure.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: sutalasi
ms.openlocfilehash: 5dcae83714ee3693288abf54afe8df7bb55dd578
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371448"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Sobre networking na recuperação de desastres azure VM



Este artigo fornece orientação de networking quando está a replicar e recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)

## <a name="typical-network-infrastructure"></a>Infraestrutura de rede típica

O diagrama seguinte retrata um ambiente típico de Azure, para aplicações em execução em VMs Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se estiver a utilizar o Azure ExpressRoute ou uma ligação VPN da sua rede no local para o Azure, o ambiente é o seguinte:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, as redes são protegidas usando firewalls e grupos de segurança de rede (NSGs). As firewalls utilizam a lista branca baseada em URL ou IP para controlar a conectividade da rede. Os NSGs fornecem regras que utilizam gamas de endereços IP para controlar a conectividade da rede.

>[!IMPORTANT]
> A utilização de um proxy autenticado para controlar a conectividade da rede não é suportada pela Recuperação do Site, e a replicação não pode ser ativada.


## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a utilizar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita estes URLs de Recuperação do Site:


**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessários para que os dados possam ser escritos na conta de armazenamento de cache na região fonte a partir do VM. Se você sabe todas as contas de armazenamento de cache para os seus VMs, você pode permitir o acesso à conta de armazenamento específica URLs (Ex: cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *.blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para os URLs do serviço de Recuperação do Local.
*.hypervrecoverymanager.windowsazure.com | Necessária para que a comunicação do serviço de recuperação do site possa ocorrer a partir do VM.
*.servicebus.windows.net | Necessário para que os dados de monitorização e diagnóstico de recuperação do site possam ser escritos a partir do VM.
*.vault.azure.net | Permite o acesso para permitir a replicação de máquinas virtuais ativadas pela ADE através do portal
*.automation.ext.azure.com | Permite permitir a tualização automática do agente de mobilidade para um item replicado via portal

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a usar um NSG para controlar a conectividade de saída, estas etiquetas de serviço precisam de ser permitidas.

- Todas as gamas de endereços IP que correspondem às contas de armazenamento na região fonte
    - Crie uma regra NSG baseada em etiqueta de [armazenamento](../virtual-network/security-overview.md#service-tags) para a região de origem.
    - Permita que estes endereços possam ser escritos na conta de armazenamento de cache, a partir do VM.
- Criar uma regra NSG baseada em placa de [serviço Azure Ative Diretório (AAD)](../virtual-network/security-overview.md#service-tags) para permitir o acesso a todos os endereços IP correspondentes ao AAD
- Crie uma regra NSG baseada em etiquetas de serviço EventsHub para a região alvo, permitindo o acesso à monitorização da Recuperação do Site.
- Crie uma regra NSG baseada em placa de serviço AzureSiteRecovery para permitir o acesso ao serviço de Recuperação do Site em qualquer região.
- Crie uma regra NSG baseada em etiqueta de serviço AzureKeyVault. Isto só é necessário para permitir a replicação de máquinas virtuais ativadas pela ADE através do portal.
- Crie uma regra NSG baseada em etiqueta de serviço GuestAndHybridManagement. Isto é necessário apenas para permitir a atualização automática do agente de mobilidade para um item replicado via portal.
- Recomendamos que crie as regras de NSG necessárias num teste NSG, e verifique se não há problemas antes de criar as regras de um NSG de produção.

## <a name="example-nsg-configuration"></a>Exemplo de configuração NSG

Este exemplo mostra como configurar as regras de NSG para que um VM se reproduza.

- Se estiver a utilizar regras de NSG para controlar a conectividade de saída, utilize as regras "Permitir https outbound" para a porta:443 para todas as gamas de endereços IP necessários.
- O exemplo pressupõe que a localização da fonte vm é "East US" e a localização alvo é "Central US".

### <a name="nsg-rules---east-us"></a>Regras da NSG - Leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.EastUS" no NSG, como mostra a imagem abaixo.

      ![etiqueta de armazenamento](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, como mostra a imagem abaixo.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Semelhante às regras de segurança acima, crie uma regra de segurança DE saída HTTPS (443) para "EventHub.CentralUS" no NSG que corresponda à localização alvo. Isto permite o acesso à monitorização da Recuperação do Local.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação do Site em qualquer região.

### <a name="nsg-rules---central-us"></a>Regras do NSG - Centro dos EUA

Estas regras são necessárias para que a replicação possa ser ativada da região-alvo para a região-alvo após o fracasso:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Semelhante às regras de segurança acima, crie a regra de segurança DE saída HTTPS (443) para "EventHub.EastUS" no NSG que corresponda à localização de origem. Isto permite o acesso à monitorização da Recuperação do Local.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação do Site em qualquer região.

## <a name="network-virtual-appliance-configuration"></a>Configuração de aparelho virtual de rede

Se estiver a utilizar aparelhos virtuais de rede (NVAs) para controlar o tráfego de rede de saída a partir de VMs, o aparelho poderá ser estrangulado se todo o tráfego de replicação passar pela NVA. Recomendamos a criação de um ponto final do serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não vá para o NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Criar ponto final do serviço de rede para armazenamento
Pode criar um ponto final de serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não deixe o limite azure.

- Selecione a sua rede virtual Azure e clique em 'Pontos finais de serviço'

    ![ponto final de armazenamento](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Clique em 'Adicionar' e 'Adicionar pontos finais de serviço' abre
- Selecione 'Microsoft.Storage' em 'Service' e as subredes necessárias no campo 'Subnets' e clique em 'Adicionar'

>[!NOTE]
>Não restrinja o acesso virtual à rede às suas contas de armazenamento utilizadas para a ASR. Deve permitir o acesso a partir de 'Todas as redes'

### <a name="forced-tunneling"></a>Túnel forçado

Pode substituir a rota do sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego de VM para um aparelho virtual de rede no local (NVA), mas esta configuração não é recomendada para a replicação da Recuperação do Local. Se estiver a utilizar rotas personalizadas, deverá [criar um ponto final](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) de serviço de rede virtual na sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite Azure.

## <a name="next-steps"></a>Passos seguintes
- Comece a proteger as suas cargas de trabalho [replicando máquinas virtuais Azure](site-recovery-azure-to-azure.md).
- Saiba mais sobre a retenção de [endereços IP](site-recovery-retain-ip-azure-vm-failover.md) para a falha da máquina virtual Azure.
- Saiba mais sobre a recuperação de desastres das [máquinas virtuais Azure com expressRoute.](azure-vm-disaster-recovery-with-expressroute.md)
