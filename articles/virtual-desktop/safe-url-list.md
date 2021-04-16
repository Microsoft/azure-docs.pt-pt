---
title: Windows Virtual Desktop lista de URL necessária - Azure
description: Uma lista de URLs que deve desbloquear para garantir que a sua implementação virtual do Windows funciona como pretendido.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514990"
---
# <a name="required-url-list"></a>Lista de URL necessária

Para implementar e utilizar o Windows Virtual Desktop, tem de desbloquear certos URLs para que as suas máquinas virtuais (VMs) possam aceder a esses ambientes a qualquer momento. Este artigo lista os URLs necessários para desbloquear para que o Windows Virtual Desktop funcione corretamente. 

>[!IMPORTANT]
>O Windows Virtual Desktop não suporta implementações que bloqueiem os URLs listados neste artigo.

## <a name="required-url-check-tool"></a>Ferramenta de verificação de URL necessária

A ferramenta de verificação de URL necessária validará URLs e mostrará se os URLs que a máquina virtual precisa para funcionar estão acessíveis. Caso contrário, a ferramenta listará os URLs inacessíveis para que possa desbloqueá-los, se necessário.

É importante ter em mente as seguintes coisas:

- Só pode utilizar a ferramenta de verificação de URL necessária para implantações em nuvens comerciais.
- A ferramenta de verificação de URL requerida não pode verificar URLs com wildcards, por isso certifique-se de desbloquear os URLs primeiro.

### <a name="requirements"></a>Requisitos

Necessita das seguintes coisas para utilizar a ferramenta de verificação de URL necessária:

- O seu VM deve ter um quadro .NET 4.6.2
- Versão RDAgent 1.0.2944.400 ou superior
- O ficheiro WVDAgentUrlTool.exe deve estar na mesma pasta que o ficheiro WVDAgentUrlTool.config

### <a name="how-to-use-the-required-url-check-tool"></a>Como utilizar a ferramenta de verificação de URL necessária

Para utilizar a ferramenta de verificação de URL necessária:

1. Abra um pedido de comando como administrador no seu VM.
2. Executar o seguinte comando para alterar o diretório para a mesma pasta que o agente de construção:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Execute o seguinte comando:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Assim que executar o ficheiro, verá uma lista de URLs acessíveis e inacessíveis.

    Por exemplo, a imagem que se segue mostra um cenário em que é necessário desbloquear dois URLs não wildcard necessários:

    > [!div class="mx-imgBorder"]
    > ![Screenshot de saída de URLs não acessíveis.](media/noaccess.png)
    
    Aqui está como deve ser a saída depois de desbloquear todos os URLs não wildcard necessários:

    > [!div class="mx-imgBorder"]
    > ![Screenshot de saída de URLs acessíveis.](media/access.png)

## <a name="virtual-machines"></a>Máquinas virtuais

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso aos seguintes URLs na nuvem comercial Azure:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Tráfego de agentes|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Tráfego de agentes|AzureCloud|
|*xt.blob.core.windows.net|443|Tráfego de agentes|AzureCloud|
|*eh.servicebus.windows.net|443|Tráfego de agentes|AzureCloud|
|*xt.table.core.windows.net|443|Tráfego de agentes|AzureCloud|
|*xt.queue.core.windows.net|443|Tráfego de agentes|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Ativação do Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Atualizações da pilha de agente e SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Suporte ao portal Azure|AzureCloud|
| 169.254.169.254 | 80 | [Ponto final do serviço de metadados Azure Instance](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitorização de sessão de saúde anfitrião](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

>[!IMPORTANT]
>O Windows Virtual Desktop suporta agora a tag FQDN. Para obter mais informações, consulte [o Azure Firewall para proteger as implementações do Ambiente de Trabalho Virtual da Janela](../firewall/protect-windows-virtual-desktop.md).
>
>Recomendamos que utilize tags FQDN ou tags de serviço em vez de URLs para evitar problemas de serviço. Os URLs e tags listados apenas correspondem a sites e recursos virtuais do Windows Virtual Desktop. Não incluem URLs para outros serviços como o Azure Ative Directory.

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso aos seguintes URLs na nuvem do Governo Azure:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.wvd.microsoft.us|443|Tráfego de serviço|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Tráfego de agentes|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Ativação do Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Atualizações da pilha de agente e SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Suporte ao portal Azure|AzureCloud|
| 169.254.169.254 | 80 | [Ponto final do serviço de metadados Azure Instance](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitorização de sessão de saúde anfitrião](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

A tabela que se segue lista URLs opcionais aos que as suas máquinas virtuais Azure podem ter acesso:

|Endereço|Porta TCP de saída|Objetivo|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticação para serviços online da Microsoft|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Serviço de Telemetria|Nenhum|
|www.msftconnecttest.com|443|Deteta se o SO está ligado à internet|Nenhum|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nenhum|
|login.windows.net|443|Iniciar sôms nos Serviços Online da Microsoft, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Atualizações para software de cliente OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Verificação de revogação de certificados|Nenhum|
|*.azure-dns.com|443|Resolução do Azure DNS|Nenhum|
|*.azure-dns.net|443|Resolução do Azure DNS|Nenhum|

>[!NOTE]
>O Windows Virtual Desktop não tem atualmente uma lista de intervalos de endereços IP que possa desbloquear para permitir o tráfego de rede. Só apoiamos o desbloqueio de URLs específicos neste momento.
>
>Se estiver a utilizar um Firewall de Próxima Geração (NGFW), terá de utilizar uma lista dinâmica especificamente feita para iPs Azure para se certificar de que pode ligar-se.
>
>Para obter uma lista de URLs seguros relacionados com o Office, incluindo urls relacionados com o Azure Ative Directory, consulte [os intervalos de URLs e endereços IP do Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Deve utilizar o caractere wildcard (*) para URLs que envolvam tráfego de serviço. Se preferir não usar * para tráfego relacionado com agentes, eis como encontrar os URLs sem wildcards:
>
>1. Registe as suas máquinas virtuais na piscina de anfitriões virtual do Windows Desktop.
>2. Espectador **de Evento Aberto**, em seguida, vá ao Windows **logs**  >  **Application**  >  **WVD-Agent** e procure o ID do evento 3701.
>3. Desbloqueie os URLs que encontrar no âmbito do Evento ID 3701. Os URLs no âmbito do ID 3701 do evento são específicos da região. Terá de repetir o processo de desbloqueio com os URLs relevantes para cada região em que pretende implantar as suas máquinas virtuais.

## <a name="remote-desktop-clients"></a>Clientes de Ambiente de Trabalho Remoto

Quaisquer clientes de Ambiente de Trabalho remoto que utilize devem ter acesso aos seguintes URLs:

|Endereço|Porta TCP de saída|Objetivo|Clientes(s)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|Todos|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Dados de resolução de problemas|Todos|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Todos|Nenhum|
|aka.ms|443|Encurtador de URL da Microsoft|Todos|Nenhum|
|docs.microsoft.com|443|Documentação|Todos|Nenhum|
|privacy.microsoft.com|443|Declaração de privacidade|Todos|Nenhum|
|query.prod.cms.rt.microsoft.com|443|Atualizações de clientes|Ambiente de Trabalho do Windows|Nenhum|

>[!IMPORTANT]
>A abertura destes URLs é essencial para uma experiência confiável do cliente. O bloqueio do acesso a estes URLs não é suportado e irá afetar a funcionalidade do serviço.
>
>Estes URLs apenas correspondem a sites e recursos de clientes. Esta lista não inclui URLs para outros serviços como o Azure Ative Directory. Os URLs do Diretório Ativo Azure podem ser encontrados no ID 56 nos [intervalos de endereços UrLs e IP do Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
