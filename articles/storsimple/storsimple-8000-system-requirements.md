---
title: Os requisitos do sistema de série StorSimple 8000 | Microsoft Docs
description: Descreve software, networking e elevados requisitos de disponibilidade e boas práticas para uma solução Microsoft Azure StorSimple.
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
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382859"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software de série StorSimple 8000, alta disponibilidade e requisitos de networking

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Bem-vindos ao Microsoft Azure StorSimple. Este artigo descreve requisitos importantes do sistema e boas práticas para o seu dispositivo StorSimple e para os clientes de armazenamento que acedem ao dispositivo. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu sistema StorSimple e, em seguida, volte a fazê-lo conforme necessário durante a implementação e posterior funcionamento.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** - descreve os sistemas operativos suportados e quaisquer requisitos adicionais para esses sistemas operativos.
* **Requisitos de networking para o dispositivo StorSimple** - fornece informações sobre as portas que precisam de estar abertas na sua firewall para permitir iSCSI, cloud ou tráfego de gestão.
* **Requisitos de alta disponibilidade para StorSimple** - descreve requisitos de alta disponibilidade e boas práticas para o seu dispositivo StorSimple e computador anfitrião.

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento

Os seguintes requisitos de software são para os clientes de armazenamento que acedem ao seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Requisitos/notas adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Os volumes StorSimple iSCSI são suportados para utilização apenas nos seguintes tipos de discos Windows:<ul><li>Volume simples no disco básico</li><li>Volume simples e espelhado no disco dinâmico</li></ul>Apenas os iniciadores iSCSI de software presentes no sistema operativo são suportados de forma nativa. Os iniciadores iSCSI de hardware não são suportados.<br></br>O Windows Server 2012 e 2016 são funcionalidades finas e ODX suportadas se estiver a utilizar um volume StorSimple iSCSI.<br><br>A StorSimple pode criar volumes pouco abastetados e totalmente abastetados. Não pode criar volumes parcialmente a provisionados.<br><br>Reformatar um volume mal aprovisionado pode demorar muito tempo. Recomendamos que elimine o volume e, em seguida, crie um novo em vez de o reformatar. No entanto, se preferir reformatar um volume:<ul><li>Execute o seguinte comando antes de o reformatar para evitar atrasos de recuperação de espaço:  <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Após a conclusão da formatação, utilize o seguinte comando para ativar novamente a recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique o hotfix do Windows Server 2012 como descrito no [KB 2878635](https://support.microsoft.com/kb/2870270) no seu computador Windows Server.</li></ul></li></ul></ul> Se estiver a configurar o StorSimple Snapshot Manager ou o Adaptador StorSimple para o SharePoint, aceda aos [requisitos de Software para componentes opcionais](#software-requirements-for-optional-components). <br> Se o seu cliente Do Windows Server estiver a utilizar o protocolo SMB para aceder ao dispositivo StorSimple, vá à [sintonização de desempenho para servidores de ficheiros SMB](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) para obter orientação sobre o aumento do processamento paralelo.|
| VMware ESX |5.5 e 6.0 |Suportado com VMware vSphere como cliente iSCSI. A funcionalidade de bloco VAAI é suportada com VMware vSphere em dispositivos StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para clientes Linux iSCSI com versões de iniciador open-iSCSI 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> A IBM AIX não é atualmente apoiada pela StorSimple.


## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

Os seguintes requisitos de software são para os componentes StorSimple opcionais (StorSimple Snapshot Manager e Adaptador StorSimple para SharePoint).

| Componente | Plataforma de anfitrião | Requisitos/notas adicionais |
| --- | --- | --- |
| Snapshot Manager do StorSimple |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A utilização do StorSimple Snapshot Manager no Windows Server é necessária para cópia de segurança/restauro de discos dinâmicos espelhados e para quaisquer cópias de segurança consistentes com aplicações.<br> O StorSimple Snapshot Manager é suportado apenas no Windows Server 2008 R2 SP1 (64-bit), Windows Server 2012 R2 e Windows Server 2012.<ul><li>Se estiver a utilizar o Windows Server 2012, tem de instalar .NET 3.5-4.5 antes de instalar o StorSimple Snapshot Manager.</li><li>Se estiver a utilizar o Windows Server 2008 R2 SP1, tem de instalar o Windows Management Framework 3.0 antes de instalar o StorSimple Snapshot Manager.</li></ul> |
| Adaptador do StorSimple para o SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>O adaptador StorSimple para SharePoint só é suportado no SharePoint 2010 e no SharePoint 2013.</li><li>O RBS requer a SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para o seu dispositivo StorSimple

O seu dispositivo StorSimple é um dispositivo bloqueado. No entanto, as portas precisam de ser abertas na sua firewall para permitir o tráfego iSCSI, nuvem e gestão. A tabela que se segue lista as portas que precisam de ser abertas na sua firewall. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que entra solicita o acesso ao seu dispositivo. *Para fora* ou *para fora* refere-se à direção em que o seu dispositivo StorSimple envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

| Porto nº<sup>1,2</sup> | Dentro ou fora | Âmbito portuário | Necessário | Notas |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |No |<ul><li>A porta de saída é utilizada para o acesso à Internet para recuperar atualizações.</li><li>O representante da web de saída é configurável pelo utilizador.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para o controlador de IPs fixos.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Yes |<ul><li>A porta de saída é utilizada para aceder a dados na nuvem.</li><li>O representante da web de saída é configurável pelo utilizador.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para o controlador de IPs fixos.</li><li>Esta porta também é usada em ambos os controladores para recolha de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Yes |A porta de saída é utilizada pelo dispositivo StorSimple para comunicar com o serviço StorSimple Device Manager. |
| 3260 (iSCSI) |Em |LAN |No |Esta porta é utilizada para aceder a dados sobre o iSCSI. |
| 5985 |Em |LAN |No |A porta de entrada é utilizada pelo StorSimple Snapshot Manager para comunicar com o dispositivo StorSimple.<br>Esta porta também é utilizada quando liga remotamente ao Windows PowerShell para StorSimple em HTTP. |
| 5986 |Em |LAN |No |Esta porta é utilizada quando liga remotamente ao Windows PowerShell para StorSimple em HTTPS. |

<sup>1</sup> Não é necessário abrir portas de entrada na Internet pública.

<sup>2</sup> Se várias portas transportarem uma configuração de gateway, a ordem de tráfego rotato de saída será determinada com base na ordem de encaminhamento da porta descrita no [encaminhamento do Porto](#routing-metric), abaixo.

<sup>3</sup> O controlador de IPs fixo no seu dispositivo StorSimple deve ser encaminhado e capaz de se ligar diretamente à Internet ou através do representante web configurado. Os endereços IP fixos são utilizados para a manutenção das atualizações do dispositivo e para recolha de lixo. Se os controladores do dispositivo não conseguirem ligar-se à Internet através dos IPs fixos, não será possível atualizar o seu dispositivo StorSimple e a recolha de lixo não funcionará corretamente.

> [!IMPORTANT]
> Certifique-se de que a firewall não modifica ou desencripta qualquer tráfego TLS entre o dispositivo StorSimple e o Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem frequentemente configurar regras avançadas de firewall com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O seu dispositivo StorSimple e o serviço StorSimple Device Manager dependem de outras aplicações da Microsoft, tais como Azure Service Bus, Azure Ative Directory Access Control, contas de armazenamento e servidores Microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Isto, por sua vez, exigirá que o administrador da rede monitorize e atualize as regras de firewall para o seu StorSimple conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos StorSimple, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas. Os IPs de destino devem ser definidos para [as gamas IP do datacenter Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para portal Azure

| Padrão url | Componente/Funcionalidade | IPs do dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acessos<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede ativadas pela nuvem |
| `https://*.backup.windowsazure.com` |Registo de dispositivo |APENAS DADOS 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação do certificado |Interfaces de rede ativadas pela nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento Azure e monitorização |Interfaces de rede ativadas pela nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores microsoft update<br> |Apenas iPs fixos do controlador |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Apenas iPs fixos do controlador |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de apoio |Interfaces de rede ativadas pela nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para portal do Governo Azure

| Padrão url | Componente/Funcionalidade | IPs do dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acessos<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede ativadas pela nuvem |
| `https://*.backup.windowsazure.us` |Registo de dispositivo |APENAS DADOS 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação do certificado |Interfaces de rede ativadas pela nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento Azure e monitorização |Interfaces de rede ativadas pela nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores microsoft update<br> |Apenas iPs fixos do controlador |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Apenas iPs fixos do controlador |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de apoio |Interfaces de rede ativadas pela nuvem |

### <a name="routing-metric"></a>Métrica de encaminhamento

Uma métrica de encaminhamento está associada com as interfaces e o gateway que encaminham os dados para as redes especificadas. A métrica de encaminhamento é usada pelo protocolo de encaminhamento para calcular o melhor caminho para um determinado destino, se aprender que existem vários caminhos para o mesmo destino. Quanto mais baixa for a métrica de encaminhamento, maior a preferência.

No contexto do StorSimple, se várias interfaces de rede e gateways estiverem configurados para canalizar o tráfego, as métricas de encaminhamento entrarão em jogo para determinar a ordem relativa em que as interfaces serão utilizadas. As métricas de encaminhamento não podem ser alteradas pelo utilizador. No entanto, pode utilizar o `Get-HcsRoutingTable` cmdlet para imprimir a tabela de encaminhamento (e métricas) no seu dispositivo StorSimple. Mais informações sobre Get-HcsRoutingTable cmdlet na [resolução de problemas da implementação StorSimple](./storsimple-8000-troubleshoot-deployment.md).

O algoritmo métrico de encaminhamento utilizado para a atualização 2 e versões posteriores pode ser explicado da seguinte forma.

* Um conjunto de valores pré-determinados foram atribuídos às interfaces de rede.
* Considere uma tabela de exemplo mostrada abaixo com valores atribuídos às várias interfaces de rede quando estão ativadas na nuvem ou com desativação na nuvem, mas com um gateway configurado. Note que os valores aqui atribuídos são apenas valores de exemplo.

    | Interface de rede | Ativado por nuvens | Com deficiência em nuvem com gateway |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem pela qual o tráfego em nuvem será encaminhado através das interfaces de rede é:
  
    *Dados 0 > Dados 1 > Data 2 > Dados 3 > Dados 4 > Dados 5*
  
    Isto pode ser explicado pelo seguinte exemplo.
  
    Considere um dispositivo StorSimple com duas interfaces de rede ativadas pela nuvem, Dados 0 e Dados 5. Os dados 1 a Data 4 são desativados na nuvem, mas têm uma porta de entrada configurada. A ordem em que o tráfego será encaminhado para este dispositivo será:
  
    *Dados 0 (1) > Dados 5 (6) > Dados 1 (20) > Dados 2 (30) > Dados 3 (40) > Dados 4 (50)*
  
    *Os números em parênteses indicam as respetivas métricas de encaminhamento.*
  
    Se o Data 0 falhar, o tráfego na nuvem será encaminhado através do Data 5. Dado que um gateway está configurado em todas as outras redes, se ambos os Dados 0 e Dados 5 falharem, o tráfego na nuvem passará pelos Dados 1.
* Se uma interface de rede ativada pela nuvem falhar, então são 3 recaídas com um atraso de 30 segundos para ligar à interface. Se todas as recauchuções falharem, o tráfego é encaminhado para a próxima interface disponível ativada pela nuvem, conforme determinado pela tabela de encaminhamento. Se todas as interfaces de rede ativadas pela nuvem falharem, o dispositivo falhará no outro controlador (não será reiniciado neste caso).
* Se houver uma falha VIP para uma interface de rede ativada pelo iSCSI, haverá 3 retrações com um atraso de 2 segundos. Este comportamento manteve-se o mesmo dos lançamentos anteriores. Se todas as interfaces de rede iSCSI falharem, ocorrerá uma falha no controlador (acompanhada de um reboot).
* Um alerta também é levantado no seu dispositivo StorSimple quando há uma falha VIP. Para mais informações, consulte a [referência rápida.](storsimple-8000-manage-alerts.md)
* Em termos de retrações, o iSCSI terá precedência sobre a nuvem.
  
    Considere o seguinte exemplo: Um dispositivo StorSimple tem duas interfaces de rede ativadas, Dados 0 e Dados 1. Os dados 0 estão ativados na nuvem, enquanto os dados 1 são ambos ativados pela nuvem e pelo iSCSI. Nenhuma outra interface de rede neste dispositivo está ativada para cloud ou iSCSI.
  
    Se o Data 1 falhar, dado que é a última interface de rede iSCSI, isto resultará numa falha do controlador para os Dados 1 do outro controlador.

### <a name="networking-best-practices"></a>Boas práticas em rede

Para além dos requisitos de networking acima referidos, para o melhor desempenho da sua solução StorSimple, respeite as seguintes boas práticas:

* Certifique-se de que o seu dispositivo StorSimple tem sempre uma largura de banda dedicada de 40 Mbps (ou mais) disponível. Esta largura de banda não deve ser partilhada (ou a atribuição deve ser garantida através da utilização de políticas QoS) com quaisquer outras aplicações.
* Certifique-se de que a conectividade da rede com a Internet está sempre disponível. As ligações esporádicas ou não confiáveis da Internet aos dispositivos, incluindo a falta de conectividade da Internet, resultarão numa configuração não apoiada.
* Isole o iSCSI e o tráfego em nuvem, tendo interfaces de rede dedicadas no seu dispositivo para acesso iSCSI e cloud. Para obter mais informações, consulte como [modificar as interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces) no seu dispositivo StorSimple.
* Não utilize uma configuração do Protocolo de Controlo de Agregação de Ligações (LACP) para as suas interfaces de rede. Esta é uma configuração não suportada.

## <a name="high-availability-requirements-for-storsimple"></a>Elevados requisitos de disponibilidade para StorSimple

A plataforma de hardware que está incluída na solução StorSimple tem funcionalidades de disponibilidade e fiabilidade que fornecem uma base para uma infraestrutura de armazenamento altamente disponível e tolerante a falhas no seu datacenter. No entanto, existem requisitos e boas práticas que deve cumprir para ajudar a garantir a disponibilidade da sua solução StorSimple. Antes de implementar storSimple, reveja cuidadosamente os seguintes requisitos e boas práticas para o dispositivo StorSimple e computadores anfitriões conectados.

Para obter mais informações sobre a monitorização e manutenção dos componentes de hardware do seu dispositivo StorSimple, vá utilizar [o serviço StorSimple Device Manager para monitorizar os componentes de hardware e o estado](storsimple-8000-monitor-hardware-status.md) e a [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos e procedimentos de alta disponibilidade para o seu dispositivo StorSimple

Reveja cuidadosamente as seguintes informações para garantir a elevada disponibilidade do seu dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Os dispositivos StorSimple incluem módulos de potência e arrefecimento redundantes e permutáveis (PCMs). Cada PCM tem capacidade suficiente para prestar serviço para todo o chassis. Para garantir uma elevada disponibilidade, ambos os PCMs devem ser instalados.

* Ligue os seus PCMs a diferentes fontes de energia para fornecer disponibilidade se uma fonte de energia falhar.
* Se um PCM falhar, solicite uma substituição imediatamente.
* Remova um PCM falhado apenas quando tiver a substituição e estiver pronto para instalá-lo.
* Não remova ambos os PCMs simultaneamente. O módulo PCM inclui o módulo de bateria de reserva. A remoção de ambos os PCM resultará numa paragem sem proteção da bateria e o estado do dispositivo não será guardado. Para obter mais informações sobre a bateria, vá para [manter o módulo de bateria de reserva](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos controladores

Os dispositivos StorSimple incluem módulos controladores redundantes e permutáveis a quente. Os módulos controladores funcionam de forma ativa/passiva. A qualquer momento, um módulo controlador está ativo e está a prestar serviço, enquanto o outro módulo controlador é passivo. O módulo controlador passivo é ligado e fica operacional se o módulo controlador ativo falhar ou for removido. Cada módulo controlador tem capacidade suficiente para fornecer serviço para todo o chassis. Ambos os módulos controladores devem ser instalados para garantir uma elevada disponibilidade.

* Certifique-se de que ambos os módulos controladores estão sempre instalados.
* Se um módulo controlador falhar, solicite uma substituição imediatamente.
* Remova um módulo de controlador falhado apenas quando tiver a substituição e estiver pronto para instalá-lo. A remoção de um módulo por períodos prolongados afetará o fluxo de ar e, consequentemente, o arrefecimento do sistema.
* Certifique-se de que as ligações de rede a ambos os módulos controladores são idênticas e que as interfaces de rede ligadas têm uma configuração de rede idêntica.
* Se um módulo controlador falhar ou precisar de ser substituído, certifique-se de que o outro módulo do controlador está num estado ativo antes de substituir o módulo controlador falhado. Para verificar se um controlador está ativo, vá [identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova ambos os módulos controladores ao mesmo tempo. Se um controlador falhar, não desligue o módulo do controlador de espera nem o retire do chassis.
* Depois de um controlador falhar, aguarde pelo menos cinco minutos antes de remover qualquer módulo do controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

Os módulos de controlador de dispositivo storSimple têm quatro 1 Gigabit e duas interfaces de rede Ethernet de 10 Gigabit.

* Certifique-se de que as ligações de rede a ambos os módulos controladores são idênticas e que as interfaces de rede que as interfaces do módulo controlador estão ligadas têm uma configuração de rede idêntica.
* Quando possível, implemente ligações de rede em diferentes comutadores para garantir a disponibilidade do serviço em caso de falha no dispositivo de rede.
* Ao desligar a única ou última interface ativada pelo iSCSI (com os IPs atribuídos), desative primeiro a interface e, em seguida, desligue os cabos da ficha. Se a interface for desligada primeiro, então fará com que o controlador ativo falhe no controlador passivo. Se o controlador passivo também tiver as interfaces correspondentes desligadas, então ambos os controladores reiniciarão várias vezes antes de se instalarem num controlador.
* Ligue pelo menos duas interfaces DATA à rede a partir de cada módulo controlador.
* Se tiver ativado as duas interfaces de 10 GbE, coloque-as em diferentes comutadores.
* Quando possível, utilize o MPIO nos servidores para garantir que os servidores podem tolerar uma falha de ligação, rede ou interface.

Para obter mais informações sobre a ligação em rede do seu dispositivo para uma elevada disponibilidade e desempenho, vá [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Os dispositivos StorSimple incluem discos de estado sólido (SSDs) e discos rígidos (HDDs) que são protegidos usando espaços espelhados. A utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais SSDs ou HDDs.

* Certifique-se de que todos os módulos SSD e HDD estão instalados.
* Se um SSD ou HDD falhar, solicite uma substituição imediatamente.
* Se um SSD ou HDD falhar ou necessitar de substituição, certifique-se de que remove apenas o SSD ou o HDD que requer substituição.
* Não remova mais de um SSD ou HDD do sistema em qualquer momento.
  Uma falha de 2 ou mais discos de determinado tipo (HDD, SSD) ou falha consecutiva num curto espaço de tempo pode resultar em mau funcionamento do sistema e perda de dados potenciais. Se isto ocorrer, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter assistência.
* Durante a substituição, monitorize os **componentes Partilhados** na lâmina de saúde do **Hardware** para as unidades nos SSDs e HDDs. Um estado de verificação verde indica que os discos são saudáveis ou OK, enquanto um ponto de exclamação vermelho indica um SSD ou HDD falhado.
* Recomendamos que configures imagens em nuvem para todos os volumes que necessites de proteger em caso de falha do sistema.

#### <a name="ebod-enclosure"></a>Recinto EBOD

O modelo de dispositivo StorSimple 8600 inclui um invólucro de discos estendidos (EBOD) para além do recinto primário. Um EBOD contém controladores EBOD e discos rígidos (HDDs) que são protegidos usando espaços espelhados. A utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais HDDs. O recinto EBOD está ligado ao recinto primário através de cabos SAS redundantes.

* Certifique-se de que ambos os módulos controladores de caixa EBOD, ambos os cabos SAS e todos os discos rígidos estão sempre instalados.
* Se um módulo de controlador de recinto EBOD falhar, solicite uma substituição imediatamente.
* Se um módulo de controlador de recinto EBOD falhar, certifique-se de que o outro módulo do controlador está ativo antes de substituir o módulo falhado. Para verificar se um controlador está ativo, vá [identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante a substituição de um módulo controlador EBOD, monitorize continuamente o estado do componente no serviço StorSimple Device Manager acedendo à saúde do  >  **Hardware** do Monitor .
* Se um cabo SAS falhar ou necessitar de substituição (o Microsoft Support deve estar envolvido para fazer tal determinação), certifique-se de que remove apenas o cabo SAS que necessita de substituição.
* Não remova simultaneamente ambos os cabos SAS do sistema em nenhum momento.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de alta disponibilidade para os seus computadores anfitriões

Reveja cuidadosamente estas boas práticas para garantir a elevada disponibilidade de anfitriões ligados ao seu dispositivo StorSimple.

* Configurar o StorSimple com [configurações de conjunto de servidores de ficheiros de dois nós][1]. Ao remover pontos únicos de falha e construir em redundância do lado anfitrião, toda a solução torna-se altamente disponível.
* Utilize ações continuamente disponíveis (CA) disponíveis com o Windows Server 2012 (SMB 3.0) para uma elevada disponibilidade durante a falha dos controladores de armazenamento. Para obter informações adicionais para configurar clusters de servidores de ficheiros e partilhas continuamente disponíveis com o Windows Server 2012, consulte esta [demonstração de vídeo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os limites do sistema StorSimple](storsimple-8000-limits.md).
* [Saiba como implementar a sua solução StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)