---
title: Solução de problemas de Azure Site Recovery para problemas e erros de conectividade de rede do Azure para o Azure | Microsoft Docs
description: Solucionar problemas e erros ao replicar máquinas virtuais do Azure para recuperação de desastre
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 5ed57c93f73eb9a9e972a683f1068692a5963e54
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816968"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Solucionar problemas de conectividade de rede de VM do Azure para Azure

Este artigo descreve os problemas comuns relacionados à conectividade de rede quando você replica e recupera máquinas virtuais do Azure de uma região para outra região. Para obter mais informações sobre os requisitos de rede, consulte os [requisitos de conectividade para replicar VMs do Azure](azure-to-azure-about-networking.md).

Para replicação do Site Recovery para o trabalho, a conectividade de saída para URLs ou IP específicos a intervalos é necessária da VM. Se a VM estiver protegido por uma firewall ou utiliza regras de grupo (NSG) de segurança de rede para controlar a conectividade de saída, poderá deparar-se com um desses problemas.

**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados na conta de armazenamento de cache na região de origem da VM. Se você souber todas as contas de armazenamento em cache para suas VMs, poderá permitir-listar as URLs de conta de armazenamento específicas (por exemplo, cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *. blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação do serviço de Site Recovery possa ocorrer na VM. Você pode usar o ' Site Recovery IP ' correspondente se o proxy de firewall oferecer suporte a IPs.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e de diagnóstico de Site Recovery possam ser gravados da VM. Você pode usar o ' Site Recovery Monitoring IP ' correspondente se o proxy de firewall oferecer suporte a IPs.

# <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs do Site Recovery (código de erro 151037 ou 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: Falha ao registrar a máquina virtual do Azure com o Site Recovery (151195) </br>
- **Causa possível** </br>
  - A conexão não pode ser estabelecida com Site Recovery pontos de extremidade devido à falha na resolução do DNS.
  - Isso é mais frequentemente visto durante a reavaliação de proteção, quando tem a ativação pós-falha da máquina virtual, mas o servidor DNS não está acessível a partir da região de DR.

- **Resolução**
   - Se você estiver usando o DNS personalizado, verifique se o servidor DNS está acessível na região de recuperação de desastre. Para verificar se tem um DNS personalizado, vá para a VM > rede de recuperação após desastre > servidores DNS. Tente acessar o servidor DNS da máquina virtual. Se não estiver acessível, torne-o acessível ao fazer o failover do servidor DNS ou criar a linha de site entre a rede de DR e o DNS.

    ![Erro de com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: Falha na configuração do Site Recovery (151196)

> [!NOTE]
> Se as máquinas virtuais estiverem atrás do Load balancer interno **padrão** , ele não teria acesso aos IPS do O365 (ou seja, login.microsoftonline.com) por padrão. Altere-o para o tipo **básico** de balanceador de carga interno ou crie acesso de saída conforme mencionado no [artigo](https://aka.ms/lboutboundrulescli).

- **Causa possível** </br>
  - Não é possível estabelecer a ligação para a autenticação do Office 365 e os pontos finais de IPv4 de identidade.

- **Resolução**
  - O Azure Site Recovery necessário acesso aos intervalos de IPs do Office 365 para a autenticação.
    Se estiver a utilizar o proxy de firewall/regras de grupo (NSG) de segurança de rede do Azure para controlar a conectividade de rede de saída na VM, certifique-se de que permite a comunicação com IPranges do Office 365. Criar Azure Active Directory uma regra de NSG baseada na [marca de serviço (AD do Azure)](../virtual-network/security-overview.md#service-tags) para permitir acesso a todos os endereços IP correspondentes ao Azure AD
      - Se novos endereços forem adicionados ao Azure AD no futuro, você precisará criar novas regras de NSG.

### <a name="example-nsg-configuration"></a>Exemplo de configuração de NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você estiver usando regras de NSG para controlar a conectividade de saída, use regras de "permitir HTTPS de saída" para a porta: 443 para todos os intervalos de endereços IP necessários.
- O exemplo supõe que o local de origem da VM é "leste dos EUA" e o local de destino é "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Eastus" no NSG, conforme mostrado na captura de tela abaixo.

      ![marca de armazenamento](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG, conforme mostrado na captura de tela abaixo.

      ![AAD – marca](./media/azure-to-azure-about-networking/aad-tag.png)

3. Crie regras de saída HTTPS (443) para os IPs de Site Recovery que correspondem ao local de destino:

   **Location** | **Endereço IP Site Recovery** |  **Endereço IP de monitoramento de Site Recovery**
    --- | --- | ---
   EUA Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras de NSG-EUA Central

Essas regras são necessárias para que a replicação possa ser habilitada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage. Centralus" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Crie regras de saída HTTPS (443) para os IPs de Site Recovery que correspondem ao local de origem:

   **Location** | **Endereço IP Site Recovery** |  **Endereço IP de monitoramento de Site Recovery**
    --- | --- | ---
   EUA Central | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: Falha na configuração do Site Recovery (151197)
- **Causa possível** </br>
  - Não é possível estabelecer ligação a pontos finais de serviço do Azure Site Recovery.

- **Resolução**
  - O Azure Site Recovery precisavam acessá [intervalos de IP de recuperação de Site](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) consoante a região. Certifique-se de que necessária intervalos ip estão acessíveis a partir da máquina virtual.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Falha na replicação do A2A quando o tráfego de rede passa pelo servidor proxy local (151072)
- **Causa possível** </br>
  - As configurações de proxy personalizadas são inválidas e Azure Site Recovery agente do serviço de mobilidade não detectou automaticamente as configurações de proxy do IE


- **Resolução**
  1. Agente do serviço de mobilidade Deteta as definições de proxy do IE no Windows e /etc/environment no Linux.
  2. Se preferir definir proxy somente para Azure Site Recovery serviço de mobilidade, você poderá fornecer os detalhes do proxy em ProxyInfo. conf localizado em:</br>
     - ``/usr/local/InMage/config/`` no ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` no ***Windows***
  3. O ProxyInfo.conf deve ter as definições de proxy no seguinte formato INI.</br>
                *[proxy]*</br>
                *Endereço =http://1.2.3.4*</br>
                *Porta = 567*</br>
  4. Azure Site Recovery agente do serviço de mobilidade dá suporte apenas a ***proxies não autenticados***.

### <a name="fix-the-problem"></a>Corrigir o problema
Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as etapas no [documento diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Passos seguintes
[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
