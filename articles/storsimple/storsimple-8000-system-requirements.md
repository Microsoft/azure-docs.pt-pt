---
title: Requisitos do sistema da série StorSimple 8000 / Microsoft Docs
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
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297714"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software da série StorSimple 8000, alta disponibilidade e requisitos de networking

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve requisitos importantes do sistema e boas práticas para o seu dispositivo StorSimple e para os clientes de armazenamento que acedem ao dispositivo. Recomendamos que reveja a informação cuidadosamente antes de implementar o seu sistema StorSimple e, em seguida, remeta-a de volta para ela conforme necessário durante a implementação e posterior operação.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes** de armazenamento - descreve os sistemas operativos suportados e quaisquer requisitos adicionais para esses sistemas operativos.
* **Requisitos de rede para o dispositivo StorSimple** - fornece informações sobre as portas que precisam de estar abertas na sua firewall para permitir o tráfego iSCSI, cloud ou gestão.
* **Requisitos de alta disponibilidade para o StorSimple** - descreve requisitos de alta disponibilidade e boas práticas para o seu dispositivo StorSimple e computador anfitrião.

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento

Os seguintes requisitos de software são para os clientes de armazenamento que acedem ao seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Requisitos/notas adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Os volumes StorSimple iSCSI são suportados para utilização apenas nos seguintes tipos de disco Windows:<ul><li>Volume simples no disco básico</li><li>Volume simples e espelhado no disco dinâmico</li></ul>Apenas são suportados os iniciadores de software iSCSI presentes de forma nativa no sistema operativo. Os iniciadores do hardware iSCSI não são suportados.<br></br>As funcionalidades de fornecimento fino do Windows Server 2012 e 2016 e as funcionalidades ODX são suportadas se estiver a utilizar um volume StorSimple iSCSI.<br><br>O StorSimple pode criar volumes aprovisionados e totalmente provisionados. Não pode criar volumes parcialmente provisionados.<br><br>A reformatação de um volume pouco provisionado pode demorar muito tempo. Recomendamos a eliminar o volume e, em seguida, criar um novo em vez de se reformar. No entanto, se ainda preferir reformar um volume:<ul><li>Executar o seguinte comando antes do reformat para evitar atrasos na recuperação do espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Depois de a formatação estar completa, utilize o seguinte comando para reativar a recuperação do espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique o hotfix do Windows Server 2012, conforme descrito no [KB 2878635](https://support.microsoft.com/kb/2870270) no seu computador Windows Server.</li></ul></li></ul></ul> Se estiver a configurar o StorSimple Snapshot Manager ou o StorSimple Adapter para o SharePoint, vá aos [requisitos de Software para componentes opcionais](#software-requirements-for-optional-components). |
| VMware ESX |5.5 e 6.0 |Suportado com vSphere VMware como cliente iSCSI. A função vaai-block é suportada com VMware vSphere em dispositivos StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para clientes Linux iSCSI com versões de iniciação open-iSCSI 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> A IBM AIX não é atualmente suportada pela StorSimple.


## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

Os seguintes requisitos de software são para os componentes Opcionais StorSimple (StorSimple Snapshot Manager e StorSimple Adapter para SharePoint).

| Componente | Plataforma anfitriã | Requisitos/notas adicionais |
| --- | --- | --- |
| Snapshot Manager do StorSimple |Windows Server 2008 R2 SP1, 2012, 2012 R2 |A utilização do StorSimple Snapshot Manager no Windows Server é necessária para cópia de segurança/restauro de discos dinâmicos espelhados e para quaisquer cópias de segurança consistentes com aplicações.<br> O StorSimple Snapshot Manager é suportado apenas no Windows Server 2008 R2 SP1 (64-bit), Windows Server 2012 R2 e Windows Server 2012.<ul><li>Se estiver a utilizar o Window Server 2012, tem de instalar .NET 3.5-4.5 antes de instalar o StorSimple Snapshot Manager.</li><li>Se estiver a utilizar o Windows Server 2008 R2 SP1, tem de instalar o Windows Management Framework 3.0 antes de instalar o StorSimple Snapshot Manager.</li></ul> |
| Adaptador do StorSimple para o SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>O Adaptador StorSimple para O SharePoint só é suportado no SharePoint 2010 e no SharePoint 2013.</li><li>O RBS requer a SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para o seu dispositivo StorSimple

O seu dispositivo StorSimple é um dispositivo de bloqueio. No entanto, as portas precisam de ser abertas na sua firewall para permitir o tráfego iSCSI, cloud e gestão. A tabela seguinte lista as portas que precisam de ser abertas na sua firewall. Nesta tabela, *dentro* ou *à entrada* refere-se à direção a partir da qual o cliente que chega acede ao seu dispositivo. *out* or *out refere-se* à direção em que o seu dispositivo StorSimple envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

| Porta nº<sup>1,2</sup> | Dentro ou fora | Âmbito do porto | Necessário | Notas |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |Não |<ul><li>A porta de saída é utilizada para o acesso à Internet para recuperar atualizações.</li><li>O proxy web de saída é configurável pelo utilizador.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para os IPs fixos do controlador.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Sim |<ul><li>A porta de saída é utilizada para aceder a dados na nuvem.</li><li>O proxy web de saída é configurável pelo utilizador.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para os IPs fixos do controlador.</li><li>Esta porta também é utilizada em ambos os controladores para recolha de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Sim |A porta de saída é utilizada pelo dispositivo StorSimple para comunicar com o serviço StorSimple Device Manager. |
| 3260 (iSCSI) |Entrada |LAN |Não |Esta porta é utilizada para aceder a dados sobre o iSCSI. |
| 5985 |Entrada |LAN |Não |A porta de entrada é utilizada pelo StorSimple Snapshot Manager para comunicar com o dispositivo StorSimple.<br>Esta porta também é utilizada quando liga remotamente ao Windows PowerShell para StorSimple em HTTP. |
| 5986 |Entrada |LAN |Não |Esta porta é utilizada quando liga remotamente ao Windows PowerShell para StorSimple over HTTPS. |

<sup>1</sup> Não é necessário abrir portas de entrada na Internet pública.

<sup>2</sup> Se várias portas não oporem a uma configuração de gateway, a ordem de tráfego direcionada para a saída será determinada com base na ordem de encaminhamento portuário descrita no [encaminhamento](#routing-metric)do Porto , abaixo.

<sup>3</sup> O controlador fixiu os IPs no seu dispositivo StorSimple deve ser repreensível e capaz de se ligar diretamente à Internet ou através do proxy web configurado. Os endereços IP fixos são utilizados para a manutenção das atualizações no dispositivo e para recolha de lixo. Se os controladores do dispositivo não conseguirem ligar-se à Internet através dos IPs fixos, não será possível atualizar o seu dispositivo StorSimple e a recolha de lixo não funcionará corretamente.

> [!IMPORTANT]
> Certifique-se de que a firewall não modifica nem desencripta qualquer tráfego TLS entre o dispositivo StorSimple e o Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede podem configurar frequentemente regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e saída. O seu dispositivo StorSimple e o serviço StorSimple Device Manager dependem de outras aplicações da Microsoft, como o Azure Service Bus, o Azure Ative Directory Access Control, as contas de armazenamento e os servidores microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Isto, por sua vez, exigirá que o administrador de rede monitorize e atualize as regras de firewall para o seu StorSimple conforme e quando necessário.

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos StorSimple, liberalmente na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas. Os IPs de destino devem ser definidos para [as gamas IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)do centro de dados Azure .


#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para portal Azure

| Padrão URL | Componente/Funcionalidade | IPs do dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede ativadas por nuvem |
| `https://*.backup.windowsazure.com` |Registo de dispositivo |DATA 0 apenas |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação do certificado |Interfaces de rede ativadas por nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento e monitorização azure |Interfaces de rede ativadas por nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores de Atualização microsoft<br> |Controlador iPs fixos apenas |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Controlador iPs fixos apenas |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de apoio |Interfaces de rede ativadas por nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para portal do Governo Azure

| Padrão URL | Componente/Funcionalidade | IPs do dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede ativadas por nuvem |
| `https://*.backup.windowsazure.us` |Registo de dispositivo |DATA 0 apenas |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação do certificado |Interfaces de rede ativadas por nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento e monitorização azure |Interfaces de rede ativadas por nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores de Atualização microsoft<br> |Controlador iPs fixos apenas |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Controlador iPs fixos apenas |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de apoio |Interfaces de rede ativadas por nuvem |

### <a name="routing-metric"></a>Métrica de encaminhamento

Uma métrica de encaminhamento está associada às interfaces e ao portal que encaminha os dados para as redes especificadas. A métrica de encaminhamento é usada pelo protocolo de encaminhamento para calcular o melhor caminho para um determinado destino, se descobrir que existem múltiplos caminhos para o mesmo destino. Quanto mais baixa a métrica de encaminhamento, maior é a preferência.

No contexto do StorSimple, se várias interfaces de rede e gateways estiverem configurados para canalizar o tráfego, as métricas de encaminhamento entrarão em jogo para determinar a ordem relativa em que as interfaces serão utilizadas. As métricas de encaminhamento não podem ser alteradas pelo utilizador. No entanto, `Get-HcsRoutingTable` pode utilizar o cmdlet para imprimir a tabela de encaminhamento (e métricas) no seu dispositivo StorSimple. Mais informações sobre o cmdlet Get-HcsRouttableTable na [implementação StorSimple](storsimple-troubleshoot-deployment.md)de resolução de problemas .

O algoritmo métrico de encaminhamento utilizado para atualização 2 e versões posteriores pode ser explicado da seguinte forma.

* Um conjunto de valores pré-determinados foram atribuídos às interfaces de rede.
* Considere uma tabela de exemplo saem abaixo com valores atribuídos às várias interfaces de rede quando são ativadas por nuvens ou com deficiência em nuvem, mas com um gateway configurado. Note que os valores aqui atribuídos são apenas valores de exemplo.

    | Interface de rede | Ativado por nuvens | Com deficiência em nuvem com porta de entrada |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem em que o tráfego em nuvem será encaminhado através das interfaces da rede é:
  
    *Dados 0 > Data 1 > Data 2 > Data 3 > Data 4 > Data 5*
  
    Isto pode ser explicado pelo seguinte exemplo.
  
    Considere um dispositivo StorSimple com duas interfaces de rede ativadas pela nuvem, Dados 0 e Dados 5. Os dados 1 através do Data 4 são desativados em nuvem, mas têm um gateway configurado. A ordem em que o tráfego será encaminhado para este dispositivo será:
  
    *Dados 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Os números em parênteses indicam as respetivas métricas de encaminhamento.*
  
    Se o Data 0 falhar, o tráfego na nuvem será encaminhado através do Data 5. Dado que um portal está configurado em todas as outras redes, se ambos os Dados 0 e Dados 5 falharem, o tráfego na nuvem passará pelos Dados 1.
* Se uma interface de rede ativada pela nuvem falhar, então são 3 repetições com um atraso de 30 segundos para ligar à interface. Se todas as tentativas falharem, o tráfego é encaminhado para a próxima interface disponível ativada pela nuvem, conforme determinado pela tabela de encaminhamento. Se todas as interfaces de rede ativadas pela nuvem falharem, então o dispositivo falhará no outro controlador (sem reinicialização neste caso).
* Se houver uma falha VIP para uma interface de rede ativada pelo iSCSI, haverá 3 repetições com um atraso de 2 segundos. Este comportamento manteve-se o mesmo dos lançamentos anteriores. Se todas as interfaces de rede iSCSI falharem, ocorrerá uma falha do controlador (acompanhada de um reboot).
* Um alerta também é levantado no seu dispositivo StorSimple quando há uma falha VIP. Para mais informações, consulte [a referência rápida](storsimple-8000-manage-alerts.md).
* Em termos de retries, o iSCSI terá precedência sobre a nuvem.
  
    Considere o seguinte exemplo: Um dispositivo StorSimple tem duas interfaces de rede ativadas, Dados 0 e Dados 1. Os dados 0 são ativados pela nuvem, enquanto os Dados 1 são simultaneamente em nuvem e iSCSI habilitados. Nenhuma outra interface de rede neste dispositivo está ativada para nuvem ou iSCSI.
  
    Se o Data 1 falhar, dado que é a última interface de rede iSCSI, isto resultará numa falha no controlador do Data 1 no outro controlador.

### <a name="networking-best-practices"></a>Redes de boas práticas

Para além dos requisitos de networking acima referidos, para o desempenho ideal da sua solução StorSimple, respeite as seguintes boas práticas:

* Certifique-se de que o seu dispositivo StorSimple tem sempre uma largura de banda dedicada de 40 Mbps (ou mais) disponível. Esta largura de banda não deve ser partilhada (ou a atribuição deve ser garantida através da utilização de políticas QoS) com quaisquer outras aplicações.
* Certifique-se de que a conectividade da rede com a Internet está sempre disponível. Ligações esporádicas ou pouco fiáveis à Internet aos dispositivos, incluindo nenhuma conectividade da Internet, resultarão numa configuração não suportada.
* Isole o iSCSI e o tráfego na nuvem por ter interfaces de rede dedicadas no seu dispositivo para iSCSI e acesso na nuvem. Para mais informações, consulte como [modificar as interfaces](storsimple-8000-modify-device-config.md#modify-network-interfaces) de rede no seu dispositivo StorSimple.
* Não utilize uma configuração do Protocolo de Controlo de Agregação de Ligações (LACP) para as suas interfaces de rede. Esta é uma configuração não suportada.

## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidade para StorSimple

A plataforma de hardware que está incluída com a solução StorSimple tem funcionalidades de disponibilidade e fiabilidade que fornecem uma base para uma infraestrutura de armazenamento altamente disponível e tolerante a falhas no seu datacenter. No entanto, existem requisitos e boas práticas que deve cumprir para ajudar a garantir a disponibilidade da sua solução StorSimple. Antes de implementar o StorSimple, reveja cuidadosamente os seguintes requisitos e as melhores práticas para o dispositivo StorSimple e computadores anfitriões conectados.

Para obter mais informações sobre a monitorização e manutenção dos componentes de hardware do seu dispositivo StorSimple, vá utilizar [o serviço StorSimple Device Manager para monitorizar componentes de hardware e estado e](storsimple-8000-monitor-hardware-status.md) substituição de componentes de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos e procedimentos de alta disponibilidade para o seu dispositivo StorSimple

Reveja cuidadosamente as seguintes informações para garantir a elevada disponibilidade do seu dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Os dispositivos StorSimple incluem módulos de potência e arrefecimento redundantes e permutáveis a quente (PCMs). Cada PCM tem capacidade suficiente para prestar serviço a todo o chassis. Para garantir uma elevada disponibilidade, ambos os PCMs devem ser instalados.

* Ligue os seus PCMs a diferentes fontes de energia para fornecer disponibilidade se uma fonte de energia falhar.
* Se um PCM falhar, solicite imediatamente uma substituição.
* Retire um PCM falhado apenas quando tiver a substituição e esteja pronto para instalá-lo.
* Não remova ambas as PCMs simultaneamente. O módulo PCM inclui o módulo de bateria de reserva. A remoção de ambos os PCMs resultará numa paragem sem proteção da bateria e o estado do dispositivo não será guardado. Para mais informações sobre a bateria, vá manter [o módulo da bateria de reserva](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos controladores

Os dispositivos StorSimple incluem módulos controladores redundantes e permutáveis a quente. Os módulos controladores funcionam de forma ativa/passiva. A qualquer momento, um módulo de controlador está ativo e está a prestar serviço, enquanto o outro módulo de controlador é passivo. O módulo passivo do controlador é ligado e torna-se operacional se o módulo de controlador ativo falhar ou for removido. Cada módulo de comando tem capacidade suficiente para prestar serviço a todo o chassis. Ambos os módulos do controlador devem ser instalados para garantir uma elevada disponibilidade.

* Certifique-se de que ambos os módulos do controlador estão sempre instalados.
* Se um módulo controlador falhar, solicite uma substituição imediatamente.
* Retire um módulo de controlador falhado apenas quando tiver a substituição e esteja pronto para o instalar. A remoção de um módulo por longos períodos afetará o fluxo de ar e, consequentemente, o arrefecimento do sistema.
* Certifique-se de que as ligações de rede a ambos os módulos do controlador são idênticas e que as interfaces de rede ligadas têm uma configuração de rede idêntica.
* Se um módulo de controlador falhar ou necessitar de substituição, certifique-se de que o outro módulo do controlador está em estado ativo antes de substituir o módulo de controlador falhado. Para verificar se um controlador está ativo, vá identificar [o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova os dois módulos controladores ao mesmo tempo. Se estiver em curso uma falha do controlador, não desligue o módulo do controlador de espera nem o retire do chassis.
* Depois de um comando falhar, aguarde pelo menos cinco minutos antes de remover qualquer um dos módulos do controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

Os módulos de controlador de dispositivoS StorSimple têm cada um quatro 1 Gigabit e duas interfaces de rede Ethernet de 10 Gigabit.

* Certifique-se de que as ligações de rede a ambos os módulos do controlador são idênticas e as interfaces de rede que as interfaces do módulo do controlador estão ligadas para ter uma configuração de rede idêntica.
* Quando possível, desloque as ligações de rede através de diferentes interruptores para garantir a disponibilidade do serviço em caso de falha no dispositivo de rede.
* Ao desligar a única ou última interface iSCSI (com os IPs atribuídos), desative a interface primeiro e, em seguida, desligue os cabos. Se a interface estiver desligada primeiro, fará com que o controlador ativo falhe no controlador passivo. Se o controlador passivo também tiver as interfaces correspondentes desligadas, então ambos os controladores reiniciarão várias vezes antes de se instalarem num controlador.
* Ligue pelo menos duas interfaces DATA à rede a partir de cada módulo de comando.
* Se tiver ativado as duas interfaces de 10 GbE, descoloque-as em diferentes interruptores.
* Sempre que possível, utilize MPIO nos servidores para garantir que os servidores podem tolerar uma falha de ligação, rede ou interface.

Para obter mais informações sobre a rede do seu dispositivo para uma elevada disponibilidade e desempenho, vá instalar [o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Os dispositivos StorSimple incluem discos de estado sólido (SSDs) e discos rígidos (HDDs) que são protegidos usando espaços espelhados. A utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais SSDs ou HDDs.

* Certifique-se de que todos os módulos SSD e HDD estão instalados.
* Se um SSD ou HDD falhar, solicite imediatamente uma substituição.
* Se um SSD ou HDD falhar ou necessitar de substituição, certifique-se de que remove apenas o SSD ou HDD que requer substituição.
* Não remova mais do que um SSD ou HDD do sistema em qualquer momento.
  Uma falha de 2 ou mais discos de determinado tipo (HDD, SSD) ou falha consecutiva num curto espaço de tempo pode resultar em avaria do sistema e perda de dados potenciais. Se isto ocorrer, [contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para obter assistência.
* Durante a substituição, monitorize os **componentes partilhados** na lâmina de saúde do **Hardware** para as unidades nos SSDs e HDDs. Um estado de verificação verde indica que os discos são saudáveis ou OK, enquanto um ponto de exclamação vermelho indica um SSD ou HDD falhado.
* Recomendamos que configure imagens em nuvem para todos os volumes que precisa proteger em caso de falha do sistema.

#### <a name="ebod-enclosure"></a>Recinto eBOD

O modelo 8600 do dispositivo StorSimple inclui um invólucro extended bunch of Disks (EBOD) para além do recinto primário. Um EBOD contém controladores EBOD e discos rígidos (HDDs) que são protegidos usando espaços espelhados. A utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais HDDs. O recinto EBOD está ligado ao recinto primário através de cabos SAS redundantes.

* Certifique-se de que ambos os módulos do controlador de comando eBOD, ambos os cabos SAS, e todas as unidades de disco rígido estão sempre instaladas.
* Se um módulo de controlador de comando ebod falhar, solicite uma substituição imediatamente.
* Se um módulo de controlador de comando eBOD falhar, certifique-se de que o outro módulo do controlador está ativo antes de substituir o módulo falhado. Para verificar se um controlador está ativo, vá identificar [o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante a substituição do módulo do controlador EBOD, monitorize continuamente o estado do componente no serviço StorSimple Device Manager acedendo à**saúde**do **hardware do Monitor** > .
* Se um cabo SAS falhar ou necessitar de substituição (o Suporte microsoft deve estar envolvido para fazer tal determinação), certifique-se de que remove apenas o cabo SAS que necessita de substituição.
* Não retire simultaneamente ambos os cabos SAS do sistema em nenhum momento.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de alta disponibilidade para os seus computadores de acolhimento

Reveja cuidadosamente estas boas práticas para garantir a elevada disponibilidade de anfitriões ligados ao seu dispositivo StorSimple.

* Configure o StorSimple com [configurações de cluster de servidor de ficheiros de dois nós][1]. Ao eliminar pontos únicos de falha e construir na redundância do lado anfitrião, toda a solução fica altamente disponível.
* Utilize as partilhas disponíveis continuamente disponíveis (CA) com o Windows Server 2012 (SMB 3.0) para uma elevada disponibilidade durante a falha dos controladores de armazenamento. Para obter informações adicionais para configurar clusters de servidores de ficheiros e partilhas continuamente disponíveis com o Windows Server 2012, consulte esta [demonstração de vídeo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passos seguintes

* [Conheça os limites do sistema StorSimple](storsimple-8000-limits.md).
* [Aprenda a implementar a sua solução StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
