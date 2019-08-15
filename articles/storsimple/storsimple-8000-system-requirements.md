---
title: Requisitos de sistema do StorSimple 8000 Series | Microsoft Docs
description: Descreve os requisitos de software, de rede e de alta disponibilidade e as práticas recomendadas para uma solução de Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 2e7c1eedf02c8a7783ee90f403dbd77ec2ee53ea
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963336"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software da série StorSimple 8000, alta disponibilidade e requisitos de rede

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos de sistema e as práticas recomendadas importantes para seu dispositivo StorSimple e para os clientes de armazenamento que acessam o dispositivo. Recomendamos que você revise as informações cuidadosamente antes de implantar o sistema StorSimple e, em seguida, reveja-as conforme necessário durante a implantação e a operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** – descreve os sistemas operacionais com suporte e quaisquer requisitos adicionais para esses sistemas operacionais.
* **Requisitos de rede para o dispositivo StorSimple** – fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego de iSCSI, nuvem ou gerenciamento.
* **Requisitos de alta disponibilidade para o StorSimple** -descreve os requisitos de alta disponibilidade e as práticas recomendadas para o dispositivo StorSimple e o computador host.

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento

Os seguintes requisitos de software são para os clientes de armazenamento que acessam seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Requisitos/observações adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Os volumes iSCSI do StorSimple têm suporte para uso somente nos seguintes tipos de disco do Windows:<ul><li>Volume simples no disco básico</li><li>Volume simples e espelhado em disco dinâmico</li></ul>Somente os iniciadores iSCSI de software presentes nativamente no sistema operacional têm suporte. Não há suporte para iniciadores iSCSI de hardware.<br></br>Os recursos de provisionamento e ODX do Windows Server 2012 e 2016 são suportados se você estiver usando um volume do StorSimple iSCSI.<br><br>O StorSimple pode criar volumes com provisionamento dinâmico e provisionamento total. Ele não pode criar volumes parcialmente provisionados.<br><br>A reformatação de um volume com provisionamento dinâmico pode levar muito tempo. É recomendável excluir o volume e, em seguida, criar um novo, em vez de reformatar. No entanto, se você ainda preferir reformatar um volume:<ul><li>Execute o comando a seguir antes de reformatar para evitar atrasos de reclamação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Após a conclusão da formatação, use o seguinte comando para reabilitar a reclamação do espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique o hotfix do Windows Server 2012 conforme descrito em [KB 2878635](https://support.microsoft.com/kb/2870270) para seu computador Windows Server.</li></ul></li></ul></ul> Se você estiver configurando o StorSimple Snapshot Manager ou o adaptador StorSimple para SharePoint, acesse [requisitos de software para componentes opcionais](#software-requirements-for-optional-components). |
| VMware ESX |5,5 e 6,0 |Com suporte com VMware vSphere como cliente iSCSI. O recurso VAAI-Block tem suporte com VMware vSphere em dispositivos StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para clientes iSCSI Linux com iniciador do Open-iSCSI versões 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Atualmente, o IBM AIX não tem suporte com o StorSimple.


## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

Os seguintes requisitos de software são para os componentes opcionais do StorSimple (StorSimple Snapshot Manager e o adaptador StorSimple para SharePoint).

| Componente | Plataforma de host | Requisitos/observações adicionais |
| --- | --- | --- |
| Snapshot Manager do StorSimple |Windows Server 2008 R2 SP1, 2012, 2012 R2 |O uso do StorSimple Snapshot Manager no Windows Server é necessário para backup/restauração de discos dinâmicos espelhados e para qualquer backup consistente com o aplicativo.<br> O StorSimple Snapshot Manager tem suporte apenas no Windows Server 2008 R2 SP1 (64 bits), no Windows Server 2012 R2 e no Windows Server 2012.<ul><li>Se você estiver usando o Windows Server 2012, deverá instalar o .NET 3.5 – 4.5 antes de instalar o StorSimple Snapshot Manager.</li><li>Se você estiver usando o Windows Server 2008 R2 SP1, deverá instalar o Windows Management Framework 3,0 antes de instalar o StorSimple Snapshot Manager.</li></ul> |
| Adaptador do StorSimple para o SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Só há suporte para o adaptador StorSimple para SharePoint no SharePoint 2010 e no SharePoint 2013.</li><li>O RBS requer a edição SQL Server Enterprise, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para seu dispositivo StorSimple

Seu dispositivo StorSimple é um dispositivo bloqueado. No entanto, as portas precisam ser abertas no firewall para permitir o tráfego de gerenciamento, de nuvem e iSCSI. A tabela a seguir lista as portas que precisam ser abertas no firewall. Nesta tabela, entrada ou *entrada* refere-se à direção da qual as solicitações de entrada do cliente acessam seu dispositivo. *Out* ou *Outbound* refere-se à direção na qual o dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, de saída para a Internet.

| N º da porta<sup>1, 2</sup> | Entrada ou saída | Escopo da porta | Requerido | Notas |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |Não |<ul><li>A porta de saída é usada para acesso à Internet para recuperar atualizações.</li><li>O proxy da Web de saída é configurável pelo usuário.</li><li>Para permitir atualizações do sistema, essa porta também deve estar aberta para os IPs fixos do controlador.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Sim |<ul><li>A porta de saída é usada para acessar dados na nuvem.</li><li>O proxy da Web de saída é configurável pelo usuário.</li><li>Para permitir atualizações do sistema, essa porta também deve estar aberta para os IPs fixos do controlador.</li><li>Essa porta também é usada em ambos os controladores para coleta de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte observações. |Essa porta será necessária somente se você estiver usando um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte observações. |Essa porta será necessária somente se você estiver usando um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Sim |A porta de saída é usada pelo dispositivo StorSimple para se comunicar com o serviço StorSimple Device Manager. |
| 3260 (iSCSI) |Em |LAN |Não |Essa porta é usada para acessar dados por iSCSI. |
| 5985 |Em |LAN |Não |A porta de entrada é usada pelo Snapshot Manager do StorSimple para se comunicar com o dispositivo StorSimple.<br>Essa porta também é usada quando você se conecta remotamente ao Windows PowerShell para StorSimple via HTTP. |
| 5986 |Em |LAN |Não |Essa porta é usada quando você se conecta remotamente ao Windows PowerShell para StorSimple via HTTPS. |

<sup>1</sup> nenhuma porta de entrada precisa ser aberta na Internet pública.

<sup>2</sup> se várias portas carregarem uma configuração de gateway, a ordem de tráfego roteado de saída será determinada com base na ordem de roteamento de porta descrita no [Roteamento de porta](#routing-metric)abaixo.

<sup>3</sup> os IPs fixos do controlador em seu dispositivo StorSimple devem ser roteáveis e podem se conectar à Internet diretamente ou por meio do proxy da Web configurado. Os endereços IP fixos são usados para atender as atualizações para o dispositivo e para a coleta de lixo. Se os controladores de dispositivo não puderem se conectar à Internet por meio de IPs fixos, você não poderá atualizar o dispositivo StorSimple e a coleta de lixo não funcionará corretamente.

> [!IMPORTANT]
> Certifique-se de que o firewall não modifique nem descriptografe nenhum tráfego SSL entre o dispositivo StorSimple e o Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. O dispositivo StorSimple e o serviço StorSimple Device Manager dependem de outros aplicativos da Microsoft, como o barramento de serviço do Azure, o controle de acesso Azure Active Directory, as contas de armazenamento e os servidores Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall para seu StorSimple como e quando necessário.

Recomendamos que você defina suas regras de firewall para o tráfego de saída, com base em endereços IP fixos do StorSimple, livremente na maioria dos casos. No entanto, você pode usar as informações abaixo para definir regras de firewall avançadas que são necessárias para criar ambientes seguros.

> [!NOTE]
> Os IPs do dispositivo (origem) devem sempre ser definidos para todas as interfaces de rede habilitadas. Os IPs de destino devem ser definidos para [intervalos de IP do datacenter do Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para portal do Azure

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço do Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Azure Service Bus<br>Serviço de Autenticação |Interfaces de rede habilitadas para nuvem |
| `https://*.backup.windowsazure.com` |Registo de dispositivo |Somente dados 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |Interfaces de rede habilitadas para nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitoramento e contas de armazenamento do Azure |Interfaces de rede habilitadas para nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores Microsoft Update<br> |Somente IPs fixos pelo controlador |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |Somente IPs fixos pelo controlador |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede habilitadas para nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal do Azure governamental

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Serviço do Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Azure Service Bus<br>Serviço de Autenticação |Interfaces de rede habilitadas para nuvem |
| `https://*.backup.windowsazure.us` |Registo de dispositivo |Somente dados 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |Interfaces de rede habilitadas para nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitoramento e contas de armazenamento do Azure |Interfaces de rede habilitadas para nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores Microsoft Update<br> |Somente IPs fixos pelo controlador |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |Somente IPs fixos pelo controlador |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede habilitadas para nuvem |

### <a name="routing-metric"></a>Métrica de roteamento

Uma métrica de roteamento é associada às interfaces e ao gateway que roteia os dados para as redes especificadas. A métrica de roteamento é usada pelo protocolo de roteamento para calcular o melhor caminho para um determinado destino, se ele aprende que vários caminhos existem para o mesmo destino. Quanto menor for a métrica de roteamento, maior será a preferência.

No contexto do StorSimple, se vários gateways e interfaces de rede estiverem configurados para o tráfego de canal, as métricas de roteamento entrarão em jogo para determinar a ordem relativa na qual as interfaces serão usadas. As métricas de roteamento não podem ser alteradas pelo usuário. No entanto, você `Get-HcsRoutingTable` pode usar o cmdlet para imprimir a tabela de roteamento (e as métricas) em seu dispositivo StorSimple. Mais informações sobre o cmdlet Get-HcsRoutingTable em [solução de problemas de implantação do StorSimple](storsimple-troubleshoot-deployment.md).

O algoritmo de métrica de roteamento usado para a atualização 2 e versões posteriores pode ser explicado da seguinte maneira.

* Um conjunto de valores predeterminados foi atribuído às interfaces de rede.
* Considere uma tabela de exemplo mostrada abaixo com valores atribuídos a várias interfaces de rede quando eles forem habilitados para nuvem ou desabilitados na nuvem, mas com um gateway configurado. Observe que os valores atribuídos aqui são apenas valores de exemplo.

    | Interface de rede | Habilitado para nuvem | Desabilitado para a nuvem com gateway |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem na qual o tráfego de nuvem será roteado através das interfaces de rede é:
  
    *Data 0 > data 1 > Data 2 > dados 3 > dados 4 > data 5*
  
    Isso pode ser explicado pelo exemplo a seguir.
  
    Considere um dispositivo StorSimple com duas interfaces de rede habilitadas para nuvem, data 0 e data 5. Os dados 1 a 4 são desabilitados na nuvem, mas têm um gateway configurado. A ordem na qual o tráfego será roteado para este dispositivo será:
  
    *Data 0 (1) > data 5 (6) > dados 1 (20) > Data 2 (30) > Data 3 (40) > data 4 (50)*
  
    *Os números entre parênteses indicam as respectivas métricas de roteamento.*
  
    Se os dados 0 falharem, o tráfego de nuvem será roteado por meio de dados 5. Considerando que um gateway está configurado em todas as outras redes, se o data 0 e o data 5 falharem, o tráfego de nuvem passará por data 1.
* Se uma interface de rede habilitada para nuvem falhar, serão 3 repetições com um atraso de 30 segundos para se conectar à interface. Se todas as tentativas falharem, o tráfego será roteado para a próxima interface habilitada para a nuvem disponível, conforme determinado pela tabela de roteamento. Se todas as interfaces de rede habilitadas para nuvem falharem, o dispositivo fará failover para o outro controlador (sem reinicialização nesse caso).
* Se houver uma falha de VIP para uma interface de rede habilitada para iSCSI, haverá 3 repetições com um atraso de 2 segundos. Esse comportamento permaneceu da mesma das versões anteriores. Se todas as interfaces de rede iSCSI falharem, um failover do controlador ocorrerá (acompanhado de uma reinicialização).
* Um alerta também é gerado em seu dispositivo StorSimple quando há uma falha de VIP. Para obter mais informações, vá para [alerta referência rápida](storsimple-8000-manage-alerts.md).
* Em termos de novas tentativas, o iSCSI terá precedência sobre a nuvem.
  
    Considere o seguinte exemplo: Um dispositivo StorSimple tem duas interfaces de rede habilitadas, data 0 e data 1. Data 0 é habilitado para a nuvem, enquanto data 1 é habilitado para a nuvem e para o iSCSI. Nenhuma outra interface de rede neste dispositivo está habilitada para nuvem ou iSCSI.
  
    Se o data 1 falhar, Considerando que ele é a última interface de rede iSCSI, isso resultará em um failover do controlador para dados 1 no outro controlador.

### <a name="networking-best-practices"></a>Práticas recomendadas de rede

Além dos requisitos de rede acima, para o desempenho ideal de sua solução StorSimple, siga as seguintes práticas recomendadas:

* Verifique se o dispositivo StorSimple tem uma largura de banda de 40 Mbps dedicada (ou mais) disponível em todos os momentos. Essa largura de banda não deve ser compartilhada (ou a alocação deve ser garantida por meio do uso de políticas de QoS) com outros aplicativos.
* Verifique se a conectividade de rede com a Internet está disponível o tempo todo. Conexões de Internet esporádicas ou não confiáveis para os dispositivos, incluindo nenhuma conectividade com a Internet, resultarão em uma configuração sem suporte.
* Isole o tráfego iSCSI e na nuvem tendo interfaces de rede dedicadas em seu dispositivo para acesso iSCSI e na nuvem. Para obter mais informações, consulte como [Modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces) em seu dispositivo StorSimple.
* Não use uma configuração de LACP (protocolo de controle de agregação de link) para suas interfaces de rede. Esta é uma configuração sem suporte.

## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidade para o StorSimple

A plataforma de hardware incluída com a solução StorSimple tem recursos de disponibilidade e confiabilidade que fornecem uma base para uma infraestrutura de armazenamento altamente disponível e tolerante a falhas em seu datacenter. No entanto, há requisitos e práticas recomendadas que você deve obedecer para ajudar a garantir a disponibilidade da solução StorSimple. Antes de implantar o StorSimple, examine atentamente os requisitos e as práticas recomendadas a seguir para o dispositivo StorSimple e os computadores host conectados.

Para obter mais informações sobre como monitorar e manter os componentes de hardware do seu dispositivo StorSimple, acesse [usar o serviço StorSimple Device Manager para monitorar os componentes de hardware e o status](storsimple-8000-monitor-hardware-status.md) e a [substituição de componentes de hardware do storsimple ](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos e procedimentos de alta disponibilidade para seu dispositivo StorSimple

Examine atentamente as informações a seguir para garantir a alta disponibilidade do seu dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Os dispositivos StorSimple incluem módulos de energia e refrigeração redundantes e intercambiáveis (PCMs). Cada PCM tem capacidade suficiente para fornecer serviço para todo o chassi. Para garantir a alta disponibilidade, ambos os PCMs devem ser instalados.

* Conecte seu PCMs a fontes de alimentação diferentes para fornecer disponibilidade se uma fonte de alimentação falhar.
* Se um PCM falhar, solicite uma substituição imediatamente.
* Remova um PCM com falha somente quando você tiver a substituição e estiver pronto para instalá-lo.
* Não remova ambos os PCMs simultaneamente. O módulo PCM inclui o módulo de bateria de backup. Remover ambos os PCMs resultará em um desligamento sem proteção da bateria e o estado do dispositivo não será salvo. Para obter mais informações sobre a bateria, acesse [manter o módulo de bateria de backup](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos do controlador

Os dispositivos StorSimple incluem módulos redundantes e intercambiáveis do controlador. Os módulos do controlador operam de maneira ativa/passiva. A qualquer momento, um módulo do controlador está ativo e está fornecendo serviço, enquanto o outro módulo do controlador é passivo. O módulo do controlador passivo está ligado e se torna operacional se o módulo do controlador ativo falhar ou for removido. Cada módulo do controlador tem capacidade suficiente para fornecer serviço para todo o chassi. Ambos os módulos do controlador devem ser instalados para garantir a alta disponibilidade.

* Certifique-se de que ambos os módulos do controlador estejam sempre instalados.
* Se um módulo do controlador falhar, solicite uma substituição imediatamente.
* Remova um módulo do controlador com falha somente quando você tiver a substituição e estiver pronto para instalá-lo. A remoção de um módulo por períodos estendidos afetará o fluxo de ar e, portanto, o resfriamento do sistema.
* Verifique se as conexões de rede com ambos os módulos do controlador são idênticas e se as interfaces de rede conectadas têm uma configuração de rede idêntica.
* Se um módulo do controlador falhar ou precisar de substituição, verifique se o outro módulo do controlador está em um estado ativo antes de substituir o módulo do controlador com falha. Para verificar se um controlador está ativo, acesse [identificar o controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova ambos os módulos do controlador ao mesmo tempo. Se um failover do controlador estiver em andamento, não desligue o módulo do controlador em espera ou remova-o do chassi.
* Após um failover do controlador, aguarde pelo menos cinco minutos antes de remover o módulo do controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

Cada módulo do controlador de dispositivo StorSimple tem quatro interfaces de rede Gigabit Ethernet de 1 Gigabit e 2 10.

* Verifique se as conexões de rede com ambos os módulos do controlador são idênticas e se as interfaces de rede às quais as interfaces do módulo do controlador estão conectadas têm uma configuração de rede idêntica.
* Quando possível, implante conexões de rede entre diferentes opções para garantir a disponibilidade do serviço em caso de falha do dispositivo de rede.
* Ao desconectar a única ou a última interface habilitada para iSCSI (com os IPs atribuídos), desabilite a interface primeiro e, em seguida, desconecte os cabos. Se a interface for desconectada primeiro, isso fará com que o controlador ativo faça failover para o controlador passivo. Se o controlador passivo também tiver suas interfaces correspondentes desconectadas, ambos os controladores serão reinicializados várias vezes antes de serem liquidados em um controlador.
* Conecte pelo menos duas interfaces de dados à rede a partir de cada módulo do controlador.
* Se você tiver habilitado as interfaces de 2 10 GbE, implante-as em diferentes comutadores.
* Quando possível, use o MPIO em servidores para garantir que os servidores possam tolerar uma falha de conexão, rede ou interface.

Para obter mais informações sobre a rede de seu dispositivo para alta disponibilidade e desempenho, acesse [instalar o dispositivo storsimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar o dispositivo storsimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Os dispositivos StorSimple incluem discos de estado sólido (SSDs) e unidades de disco rígido (HDDs) que são protegidos usando espaços espelhados. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais SSDs ou HDDs.

* Verifique se todos os módulos SSD e HDD estão instalados.
* Se um SSD ou HDD falhar, solicite uma substituição imediatamente.
* Se um SSD ou HDD falhar ou exigir substituição, certifique-se de remover somente o SSD ou HDD que requer substituição.
* Não remova mais de um SSD ou HDD do sistema a qualquer momento.
  Uma falha de 2 ou mais discos de determinado tipo (HDD, SSD) ou falha consecutiva em um curto período de tempo pode resultar em mau funcionamento do sistema e potencial perda de dados. Se isso ocorrer, [entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para obter assistência.
* Durante a substituição, monitore os **componentes compartilhados** na folha **integridade de hardware** das unidades no SSDs e HDDs. Um status de verificação verde indica que os discos estão íntegros ou OK, enquanto um ponto de exclamação vermelho indica um SSD ou HDD com falha.
* Recomendamos que você configure instantâneos de nuvem para todos os volumes que você precisa proteger em caso de falha do sistema.

#### <a name="ebod-enclosure"></a>Compartimento EBOD

O modelo de dispositivo StorSimple 8600 inclui um compartimento de EBOD (vários discos estendidos) Além do compartimento primário. Um EBOD contém controladores EBOD e HDDs (unidades de disco rígido) que são protegidos usando espaços espelhados. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais HDDs. O compartimento EBOD está conectado ao compartimento principal por meio de cabos SAS redundantes.

* Certifique-se de que ambos os módulos do controlador de compartimento EBOD, ambos os cabos SAS e todas as unidades de disco rígido estejam sempre instalados.
* Se um módulo do controlador de compartimento EBOD falhar, solicite uma substituição imediatamente.
* Se um módulo do controlador de compartimento EBOD falhar, verifique se o outro módulo do controlador está ativo antes de substituir o módulo com falha. Para verificar se um controlador está ativo, acesse [identificar o controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante uma substituição do módulo do controlador Ebod, Monitore continuamente o status do componente no serviço StorSimple Device Manager acessando **monitorar** > **integridade do hardware**.
* Se um cabo SAS falhar ou exigir substituição (Suporte da Microsoft deve estar envolvido para fazer essa determinação), certifique-se de remover apenas o cabo SAS que requer substituição.
* Não remova simultaneamente os dois cabos SAS do sistema a qualquer momento.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de alta disponibilidade para seus computadores host

Examine atentamente essas práticas recomendadas para garantir a alta disponibilidade de hosts conectados ao seu dispositivo StorSimple.

* Configure o StorSimple com [configurações de cluster de servidor de arquivos de dois nós][1]. Ao remover pontos únicos de falha e criar redundância no lado do host, toda a solução se torna altamente disponível.
* Use os compartilhamentos continuamente disponíveis (CA) disponíveis com o Windows Server 2012 (SMB 3,0) para alta disponibilidade durante o failover dos controladores de armazenamento. Para obter informações adicionais sobre como configurar clusters de servidor de arquivos e compartilhamentos continuamente disponíveis com o Windows Server 2012, consulte esta [demonstração em vídeo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os limites do sistema StorSimple](storsimple-8000-limits.md).
* [Saiba como implantar sua solução StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
