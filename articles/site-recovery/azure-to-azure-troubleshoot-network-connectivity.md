---
title: Conectividade de suposição de problemas para recuperação de desastres de Azure para Azure com recuperação do site Azure
description: Problemas de conectividade na recuperação de desastres da VM Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292016"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Problemas de resolução de problemas de conectividade da rede VM Azure-to-Azure

Este artigo descreve as questões comuns relacionadas com a conectividade da rede quando se replica e recupera as máquinas virtuais Azure de uma região para outra. Para obter mais informações sobre os requisitos de networking, consulte os requisitos de [conectividade para replicar VMs Azure](azure-to-azure-about-networking.md).

Para que a replicação da recuperação do site funcione, a conectividade de saída a URLs específicos ou intervalos IP é necessária a partir do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras do grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas.

**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessários para que os dados possam ser escritos na conta de armazenamento de cache na região fonte a partir do VM. Se você sabe todas as contas de armazenamento de cache para os seus VMs, você pode permitir listar a conta de armazenamento específica URLs (por exemplo, cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *.blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para os URLs do serviço de Recuperação do Local.
*.hypervrecoverymanager.windowsazure.com | Necessária para que a comunicação do serviço de recuperação do site possa ocorrer a partir do VM. Pode utilizar o 'IP de recuperação do site' correspondente se o seu proxy de firewall suportar IPs.
*.servicebus.windows.net | Necessário para que os dados de monitorização e diagnóstico de recuperação do site possam ser escritos a partir do VM. Pode utilizar o "IP de Monitorização de Recuperação do Site" correspondente se o seu proxy de firewall suportar IPs.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para URLs de Recuperação do Site ou intervalos IP (código de erro 151037 ou 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Edição 1: Falha no registo da máquina virtual Azure com recuperação do site (151195) </br>
- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais de recuperação do local devido a falha na resolução do DNS.
  - Esta situação é observada com maior frequência durante reaplicação da proteção, quando é efetuada a ativação pós-falha da máquina virtual, mas o servidor DNS não está acessível a partir da região de RD.

- **Resolução**
   - Se estiver a utilizar DNS personalizados, certifique-se de que o servidor DNS está acessível a partir da região de Recuperação de Desastres. Para verificar se tem um DNS personalizado vá à rede VM> De recuperação de desastres> servidores DNS. Tente aceder ao servidor DNS a partir da máquina virtual. Se não estiver acessível, torná-lo acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e dNS.

    ![erro com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração de recuperação do site falhou (151196)

> [!NOTE]
> Se as máquinas virtuais estiverem por trás do equilíbrio interno de carga **Standard,** não teria acesso a IPs O365 (isto é, login.microsoftonline.com) por padrão. Ou o altere para o tipo **básico** de equilíbrio interno de carga ou crie acesso de saída, conforme mencionado no [artigo](https://aka.ms/lboutboundrulescli).

- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais de autenticação e identidade ip4 do Office 365.

- **Resolução**
  - A Recuperação do Site Azure exigiu o acesso às gamas de IPs do Office 365 para autenticação.
    Se estiver a utilizar regras/proxy do grupo de segurança azure Network (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que permite a comunicação a O365 IPranges. Criar uma regra NSG baseada em placa de [serviço Azure Ative Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) para permitir o acesso a todos os endereços IP correspondentes ao Azure AD
      - Se no futuro forem adicionados novos endereços à Azure AD, terá de criar novas regras de NSG.

### <a name="example-nsg-configuration"></a>Exemplo de configuração NSG

Este exemplo mostra como configurar as regras de NSG para que um VM se reproduza.

- Se estiver a utilizar regras de NSG para controlar a conectividade de saída, utilize as regras "Permitir https outbound" para a porta:443 para todas as gamas de endereços IP necessários.
- O exemplo pressupõe que a localização da fonte vm é "East US" e a localização alvo é "Central US".

### <a name="nsg-rules---east-us"></a>Regras da NSG - Leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.EastUS" no NSG, como mostra a imagem abaixo.

      ![etiqueta de armazenamento](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, como mostra a imagem abaixo.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Criar regras HTTPS de saída (443) para os IPs de Recuperação do Site que correspondam à localização-alvo:

   **Localização** | **Endereço IP de recuperação do site** |  **Endereço IP de monitorização da recuperação do site**
    --- | --- | ---
   E.U.A. Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras do NSG - Centro dos EUA

Estas regras são necessárias para que a replicação possa ser ativada da região-alvo para a região-alvo após o fracasso:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Criar regras HTTPS de saída (443) para os IPs de Recuperação do Site que correspondam à localização de origem:

   **Localização** | **Endereço IP de recuperação do site** |  **Endereço IP de monitorização da recuperação do site**
    --- | --- | ---
   E.U.A. Central | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração de recuperação do site falhou (151197)
- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais do serviço de recuperação do local do Azure.

- **Resolução**
  - Acesso necessário do Azure Site Recovery aos [intervalos de IP do Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags), dependendo da região. Garanta que os intervalos de IP necessários estão acessíveis a partir da máquina virtual.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Edição 4: A replicação A2A falhou quando o tráfego de rede passa pelo servidor proxy no local (151072)
- **Possível causa** </br>
  - As configurações de procuração personalizadas são inválidas, e o agente do Serviço de Mobilidade de Recuperação do Site Azure não detetou automaticamente as definições de procuração do IE


- **Resolução**
  1. O agente do Serviço de Mobilidade deteta as definições de procuração a partir de IE no Windows e /etc/ambiente no Linux.
  2. Se preferir definir procuração apenas para o Serviço de Mobilidade de Recuperação do Site Azure, pode fornecer os detalhes proxy info.conf localizados em:</br>
     - ``/usr/local/InMage/config/``em ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``no ***Windows***
  3. O ProxyInfo.conf deve ter as definições de proxy no seguinte formato INI.</br>
                *[procuração]*</br>
                *Endereço=http://1.2.3.4*</br>
                *Porto=567*</br>
  4. O agente do Serviço de Mobilidade de Recuperação do Sítio Azure suporta apenas ***proxies não autenticados.***

### <a name="fix-the-problem"></a>Corrigir o problema
Para permitir [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou as [gamas IP necessárias,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga os passos no documento de [orientação](site-recovery-azure-to-azure-networking-guidance.md)de rede .


## <a name="next-steps"></a>Passos seguintes
[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
