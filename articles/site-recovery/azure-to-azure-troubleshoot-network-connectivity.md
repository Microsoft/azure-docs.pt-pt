---
title: Conectividade de suposição de problemas para recuperação de desastres de Azure para Azure com recuperação do site Azure
description: Problemas de conectividade na recuperação de desastres da VM Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804865"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Problemas de resolução de problemas de conectividade da rede VM Azure-to-Azure

Este artigo descreve as questões comuns relacionadas com a conectividade da rede quando se replica e recupera as máquinas virtuais Azure (VM) de uma região para outra região. Para obter mais informações sobre os requisitos de networking, consulte os requisitos de [conectividade para replicar VMs Azure](azure-to-azure-about-networking.md).

Para que a replicação da recuperação do site funcione, a conectividade de saída a URLs específicos ou intervalos IP é necessária a partir do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras do grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas.

| **URL** | **Detalhes** |
| --- | --- |
| `*.blob.core.windows.net` | Necessários para que os dados possam ser escritos na conta de armazenamento de cache na região fonte a partir do VM. Se você sabe todas as contas de armazenamento de cache para os seus VMs, você pode usar uma lista de espera para os URLs da conta de armazenamento específico. Por `cache1.blob.core.windows.net` exemplo, `cache2.blob.core.windows.net` e `*.blob.core.windows.net`em vez de. |
| `login.microsoftonline.com` | Necessário para autorização e autenticação para os URLs do serviço de Recuperação do Local. |
| `*.hypervrecoverymanager.windowsazure.com` | Necessária para que a comunicação do serviço de recuperação do site possa ocorrer a partir do VM. Pode utilizar o IP de _Recuperação_ do Site correspondente se o seu proxy de firewall suportar IPs. |
| `*.servicebus.windows.net` | Necessário para que os dados de monitorização e diagnóstico de recuperação do site possam ser escritos a partir do VM. Pode utilizar o IP de Monitorização de _Recuperação_ do Site correspondente se o seu proxy de firewall suportar IPs. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para URLs de Recuperação do Site ou intervalos IP (código de erro 151037 ou 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Edição 1: Falha no registo da máquina virtual Azure com recuperação do site (151195)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais de recuperação do site devido a uma falha de resolução do Sistema de Nome de Domínio (DNS). Este problema é mais comum durante a reproteção quando se falhou sobre o VM, mas o servidor DNS não é acessível a partir da região de recuperação de desastres (DR).

#### <a name="resolution"></a>Resolução

Se estiver a utilizar DNS personalizados, certifique-se de que o servidor DNS está acessível a partir da região de recuperação de desastres.

Para verificar se o VM utiliza uma definição dNS personalizada:

1. Abra **as máquinas virtuais** e selecione o VM.
1. Navegue para as **Definições** de VMs e selecione **Networking**.
1. Na **rede Virtual/sub-rede,** selecione o link para abrir a página de recursos da rede virtual.
1. Vá a **Definições** e selecione **servidores DNS**.

Tente aceder ao servidor DNS a partir da máquina virtual. Se o servidor DNS não estiver acessível, torná-lo acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="erro com":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração de recuperação do site falhou (151196)

> [!NOTE]
> Se os VMs estiverem por detrás de um equilibrador de carga interna **Standard,** por `login.microsoftonline.com`padrão, não teria acesso aos IPs do Office 365, tais como . Ou o altere para o tipo **básico** de equilíbrio interno de carga ou crie acesso de saída, conforme mencionado no artigo Configure regras de equilíbrio de carga e saída no Equilíbrio de [Carga Padrão utilizando o Azure CLI](/azure/load-balancer/configure-load-balancer-outbound-cli).

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais de autenticação e identidade do Office 365.

#### <a name="resolution"></a>Resolução

- A Recuperação do Site Azure requer acesso às gamas IP do Office 365 para autenticação.
- Se estiver a utilizar regras/proxy do grupo de segurança azure Network (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que permite a comunicação às gamas IP do Office 365. Crie uma regra NSG baseada em placa de [serviço Azure Ative Directory (Azure AD)](/azure/virtual-network/security-overview#service-tags) que permita o acesso a todos os endereços IP correspondentes ao Azure AD.
- Se no futuro forem adicionados novos endereços à Azure AD, terá de criar novas regras de NSG.

### <a name="example-nsg-configuration"></a>Exemplo de configuração NSG

Este exemplo mostra como configurar as regras de NSG para que um VM se reproduza.

- Se estiver a utilizar regras de NSG para controlar a conectividade de saída, utilize as regras de **saída do HTTPS** para a porta 443 para todas as gamas de endereços IP necessários.
- O exemplo pressupõe que a localização da fonte vm é **os EUA Orientais** e a localização alvo é **central dos EUA.**

#### <a name="nsg-rules---east-us"></a>Regras da NSG - Leste dos EUA

1. Crie uma regra de segurança de saída HTTPS para o NSG, como mostra a seguinte imagem. Este exemplo utiliza a etiqueta de **serviço Destination:** _Storage.EastUS_ e **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="etiqueta de armazenamento":::

1. Crie uma regra de segurança de saída HTTPS para o NSG, como mostra a seguinte imagem. Este exemplo utiliza a etiqueta de **serviço Destination**: _AzureActiveDirectory_ and **Destination ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Crie regras de saída da porta HTTPS 443 para os IPs de Recuperação do Site que correspondam à localização-alvo:

   | **Localização** | **Endereço IP de recuperação do site** |  **Endereço IP de monitorização da recuperação do site** |
   | --- | --- | --- |
   | E.U.A. Central | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Regras do NSG - Centro dos EUA

Para este exemplo, estas regras de NSG são necessárias para que a replicação possa ser ativada da região-alvo para a região-alvo após o fracasso:

1. Crie uma regra de segurança de saída HTTPS para _Storage.CentralUS:_

   - **Etiqueta de serviço**de destino : _Storage.CentralUS_
   - **Gamas de porta de destino**: _443_

1. Crie uma regra de segurança de saída HTTPS para _o AzureActiveDirectory_.

   - **Etiqueta de serviço**de destino : _AzureActiveDirectory_
   - **Gamas de porta de destino**: _443_

1. Criar regras de saída da porta HTTPS 443 para os IPs de Recuperação do Site que correspondam à localização de origem:

   |**Localização** | **Endereço IP de recuperação do site** |  **Endereço IP de monitorização da recuperação do site** |
   | --- | --- | --- |
   | E.U.A. Leste | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração de recuperação do site falhou (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais do serviço de recuperação do site Azure.

#### <a name="resolution"></a>Resolução

Acesso necessário do Azure Site Recovery aos [intervalos de IP do Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), dependendo da região. Certifique-se de que as gamas IP necessárias são acessíveis a partir do VM.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Edição 4: A replicação Azure-to-Azure falhou quando o tráfego da rede passa pelo servidor proxy no local (151072)

#### <a name="possible-cause"></a>Causa possível

As definições de procuração personalizadas são inválidas e o agente de serviço de Mobilidade de Recuperação do Site Azure não detetou automaticamente as definições de procuração do Internet Explorer (IE).

#### <a name="resolution"></a>Resolução

1. O agente de serviço de Mobilidade deteta `/etc/environment` as definições de procuração do IE no Windows e no Linux.
1. Se preferir definir procuração apenas para o serviço de mobilidade de recuperação do site Azure, pode fornecer os detalhes proxy em _ProxyInfo.conf_ localizado em:

   - **Linux:**`/usr/local/InMage/config/`
   - **Janelas:**`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo.conf_ deve ter as definições de procuração no seguinte formato _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. O agente de serviço de recuperação de locais Azure suporta apenas **proxies não autenticados.**

### <a name="fix-the-problem"></a>Corrigir o problema

Para permitir [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou as [gamas IP necessárias,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga os passos no documento de [orientação](site-recovery-azure-to-azure-networking-guidance.md)de rede .

## <a name="next-steps"></a>Passos seguintes

[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
