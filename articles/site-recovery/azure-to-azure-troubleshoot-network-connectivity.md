---
title: Conectividade de resolução de problemas para Azure para recuperação de desastres Azure com recuperação do local de Azure
description: Problemas de conectividade na recuperação de desastres da Azure VM
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652267"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Problemas de resolução Azure-to-Azure Problemas de conectividade da rede VM

Este artigo descreve as questões comuns relacionadas com a conectividade da rede quando replica e recupera máquinas virtuais Azure (VM) de uma região para outra região. Para obter mais informações sobre os requisitos de networking, consulte os [requisitos de conectividade para a replicação dos VMs Azure](azure-to-azure-about-networking.md).

Para que a replicação da Recuperação do Local funcione, é necessária conectividade de saída a URLs específicos ou gamas IP do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas.

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Necessário para que os dados possam ser escritos na conta de armazenamento de cache na região de origem a partir do VM. Se souber de todas as contas de armazenamento de cache dos seus VMs, pode utilizar uma lista de admissões para os URLs de conta de armazenamento específicos. Por exemplo, `cache1.blob.core.windows.net` e `cache2.blob.core.windows.net` em vez `*.blob.core.windows.net` de. . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Requerida autorização e autenticação para os URLs do serviço de Recuperação do Local. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Necessário para que a comunicação do serviço de recuperação do local possa ocorrer a partir do VM. Pode utilizar o _IP de recuperação do site_ correspondente se o seu representante de firewall suportar IPs. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Necessário para que os dados de monitorização e diagnóstico da Recuperação do Local possam ser escritos a partir do VM. Pode utilizar o IP de _Monitorização de Recuperação do Local_ correspondente se o seu representante de firewall suportar IPs. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para URLs de Recuperação de Sítios ou intervalos IP (código de erro 151037 ou 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Edição 1: Falha no registo da máquina virtual Azure com recuperação do local (151195)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais de recuperação do local devido a uma falha de resolução do Sistema de Nome de Domínio (DNS). Este problema é mais comum durante a reproteção quando falhou sobre o VM, mas o servidor DNS não é alcançável a partir da região de recuperação de desastres (DR).

#### <a name="resolution"></a>Resolução

Se estiver a utilizar DNS personalizado, certifique-se de que o servidor DNS está acessível a partir da região de recuperação de desastres.

Para verificar se o VM utiliza uma definição de DNS personalizada:

1. Abra **as máquinas Virtuais** e selecione o VM.
1. Navegue para as **Definições VMs** e selecione **Networking**.
1. Na **rede virtual/sub-rede,** selecione o link para abrir a página de recursos da rede virtual.
1. Vá a **Definições** e selecione **servidores DNS**.

Tente aceder ao servidor DNS a partir da máquina virtual. Se o servidor DNS não estiver acessível, torne-o acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-erro":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração da recuperação do local falhou (151196)

> [!NOTE]
> Se os VMs estiverem por trás de um balanceador de carga interno **Standard,** por padrão, não teria acesso aos IPs da Microsoft 365, tais como `login.microsoftonline.com` . Altere-o para o tipo **básico** de balançador interno de carga ou crie acesso de saída, conforme mencionado no artigo [Configure as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando o Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration).

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação com os pontos finais ip4 de autenticação e identidade do Microsoft 365.

#### <a name="resolution"></a>Resolução

- A recuperação do site Azure requer acesso às gamas IP da Microsoft 365 para autenticação.
- Se estiver a utilizar as regras do Grupo de Segurança da Rede Azure (NSG) /proxy de firewall para controlar a conectividade da rede de saída no VM, certifique-se de que permite a comunicação às gamas IP da Microsoft 365. Crie uma regra NSG baseada em etiquetas de [serviço Azure Ative (Azure AD)](../virtual-network/network-security-groups-overview.md#service-tags) que permita o acesso a todos os endereços IP correspondentes ao Azure AD.
- Se novos endereços forem adicionados à AZure AD no futuro, você precisa criar novas regras NSG.

### <a name="example-nsg-configuration"></a>Configuração NSG exemplo

Este exemplo mostra como configurar as regras NSG para um VM replicar.

- Se estiver a utilizar as regras NSG para controlar a conectividade de saída, utilize **as regras de saída HTTPS** para a porta 443 para todas as gamas de endereços IP necessárias.
- O exemplo pressupõe que a localização da origem VM é **a Leste dos EUA** e que a localização alvo é a Central **US.**

#### <a name="nsg-rules---east-us"></a>Regras NSG - Leste dos EUA

1. Crie uma regra de segurança de saída HTTPS para o NSG, como mostrado na imagem seguinte. Este exemplo utiliza a **etiqueta de serviço destino**: **Gamas portuárias** _De Armazenamento.EastUS_ e Destino : _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="A screenshot mostra um painel de regras de segurança de saída para uma regra de segurança para o ponto de armazenamento East U.S.":::

1. Crie uma regra de segurança de saída HTTPS para o NSG, como mostrado na imagem seguinte. Este exemplo utiliza a **etiqueta de serviço destino**: _AzureActiveDirectory_ and **Destination port:** _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="A screenshot mostra um painel de regras de segurança de saída para uma regra de segurança para o Azure Ative Directory.":::

1. Semelhante às regras de segurança acima, crie uma regra de segurança HTTPS (443) para "EventHub.CentralUS" no NSG que corresponda à localização do alvo. Isto permite o acesso à monitorização da Recuperação do Local.
1. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação de Sítios em qualquer região.

#### <a name="nsg-rules---central-us"></a>Regras NSG - Central US

Por exemplo, estas regras do NSG são necessárias para que a replicação possa ser ativada desde a região-alvo até à região de origem após o failover:

1. Criar uma regra de segurança de saída HTTPS para _armazenamento.CentralUS:_

   - **Etiqueta de serviço de destino**: _Storage.CentralUS_
   - **Gamas portuárias de destino**: _443_

1. Crie uma regra de segurança de saída HTTPS para _a AzureActiveDirectory_.

   - **Etiqueta de serviço de destino**: _AzureActiveDirectory_
   - **Gamas portuárias de destino**: _443_

1. Semelhante às regras de segurança acima, crie uma regra de segurança HTTPS (443) para "EventHub.EastUS" no NSG que corresponda à localização da fonte. Isto permite o acesso à monitorização da Recuperação do Local.
1. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isto permite o acesso ao Serviço de Recuperação de Sítios em qualquer região.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração da recuperação do local falhou (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação com os pontos finais do serviço de recuperação do local de Azure.

#### <a name="resolution"></a>Resolução

Se estiver a utilizar uma regra/procuração do Azure Network Security Group (NSG) para controlar a conectividade da rede de saída na máquina, existem várias tags de serviço que precisam de ser permitidas. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Edição 4: A replicação Azure-to-Azure falhou quando o tráfego da rede passa pelo servidor proxy no local (151072)

#### <a name="possible-cause"></a>Causa possível

As definições de procuração personalizadas são inválidas e o agente de serviço de mobilidade de recuperação do local Azure não conseguiu automaticamente as definições de procuração do Internet Explorer (IE).

#### <a name="resolution"></a>Resolução

1. O agente de serviço de mobilidade deteta as definições de procuração do IE no Windows e `/etc/environment` no Linux.
1. Se preferir configurar apenas procuração para o serviço de Mobilidade de Recuperação do Local Azure, pode fornecer os detalhes do proxy em _ProxyInfo.conf_ localizado em:

   - **Linux:**`/usr/local/InMage/config/`
   - **Janelas:**`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo.conf_ deve ter as definições de procuração no seguinte formato _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> O agente de serviço de mobilidade de recuperação do local Azure suporta apenas **proxies não autenticados**.

### <a name="fix-the-problem"></a>Corrigir o problema

Para permitir [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos IP necessários,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga os passos no [documento de orientação de rede](./azure-to-azure-about-networking.md).

## <a name="next-steps"></a>Passos seguintes

[Replicar VMs do Azure para outra região do Azure](azure-to-azure-how-to-enable-replication.md)