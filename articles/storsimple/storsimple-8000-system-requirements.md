---
title: Requisitos de sistema de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve o software, sistema de rede e requisitos de alta disponibilidade e as práticas recomendadas para uma solução do Microsoft Azure StorSimple.
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
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013632"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software de série StorSimple 8000, elevada disponibilidade e os requisitos de rede

## <a name="overview"></a>Descrição geral

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos de sistema importante e melhores práticas para o dispositivo StorSimple e para os clientes de armazenamento aceder ao dispositivo. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu sistema do StorSimple e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem:

* **Requisitos de software para os clientes de armazenamento** -descreve os sistemas operativos suportados e os requisitos adicionais para nesses sistemas operativos.
* **Requisitos de rede para o dispositivo StorSimple** -fornece informações sobre as portas que precisam de estar abertas na firewall para permitir o tráfego de iSCSI, nuvem ou gestão.
* **Requisitos de alta disponibilidade para o StorSimple** – descreve os requisitos de alta disponibilidade e as práticas recomendadas para o seu computador de anfitrião e de dispositivos do StorSimple.

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para os clientes de armazenamento

São os seguintes requisitos de software para os clientes de armazenamento que aceda ao seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Notas/requisitos adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Volumes do StorSimple iSCSI são suportadas para utilização em apenas os tipos de disco de Windows seguintes:<ul><li>Volume simples num disco básico</li><li>Simples e espelhado volume num disco dinâmico</li></ul>Apenas os software iniciadores iSCSI presentes nativamente no sistema operativo são suportados. Iniciadores iSCSI de hardware não são suportadas.<br></br>Windows Server 2012 e o aprovisionamento dinâmico de 2016 e a funcionalidades ODX são suportadas se estiver a utilizar um volume de iSCSI do StorSimple.<br><br>StorSimple pode criar com aprovisionamento dinâmico e totalmente aprovisionado volumes. Ele não é possível criar volumes parcialmente aprovisionados.<br><br>Reformatação um volume com aprovisionamento dinâmico pode demorar muito tempo. Recomendamos a eliminação do volume e, em seguida, criar um novo, em vez de reformatação. No entanto, se ainda preferem reformatar um volume:<ul><li>Execute o seguinte comando antes do reformat para evitar atrasos na recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Depois da formatação estiver concluída, utilize o seguinte comando para voltar a ativar a recuperação de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplicar a correção do Windows Server 2012, conforme descrito em [KB 2878635](https://support.microsoft.com/kb/2870270) para o seu computador Windows Server.</li></ul></li></ul></ul> Se estiver a configurar o Snapshot Manager do StorSimple ou StorSimple Adapter para SharePoint, aceda a [requisitos de Software para componentes opcionais](#software-requirements-for-optional-components). |
| VMware ESX |5.5 e 6.0 |Suportado com o VMware vSphere como cliente de iSCSI. Funcionalidade de bloco VAAI é suportada com o VMware vSphere nos dispositivos do StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para clientes de iSCSI do Linux com as versões de iniciador de iSCSI aberto 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX não é atualmente suportada com o StorSimple.


## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

São os seguintes requisitos de software para os componentes opcionais do StorSimple (Snapshot Manager do StorSimple e o StorSimple Adapter para SharePoint).

| Componente | Plataforma de anfitrião | Notas/requisitos adicionais |
| --- | --- | --- |
| Snapshot Manager do StorSimple |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Utilização do Snapshot Manager do StorSimple no Windows Server é necessária para a cópia de segurança/restauro de discos dinâmicos espelhados e para quaisquer cópias de segurança consistentes com aplicações.<br> Snapshot Manager do StorSimple é suportado apenas no Windows Server 2008 R2 SP1 (64-bit), Windows Server 2012 R2 e Windows Server 2012.<ul><li>Se estiver a utilizar o Windows Server 2012, tem de instalar o .NET 3.5 – 4.5 antes de instalar o Snapshot Manager do StorSimple.</li><li>Se estiver a utilizar o Windows Server 2008 R2 SP1, tem de instalar Windows Management Framework 3.0 antes de instalar o Snapshot Manager do StorSimple.</li></ul> |
| Adaptador do StorSimple para o SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple Adapter para SharePoint só é suportada no SharePoint 2010 e SharePoint 2013.</li><li>RBS requer o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para o dispositivo StorSimple

O dispositivo StorSimple é um dispositivo bloqueado. No entanto, as portas tem de ser aberta na firewall para permitir o iSCSI, a cloud e o tráfego de gestão. A tabela seguinte lista as portas que têm de ser aberta na firewall. Nesta tabela, *no* ou *entrada* refere-se para a direção a partir do qual os pedidos de cliente recebidos, aceda ao seu dispositivo. *Out* ou *saída* refere-se para a direção em que o dispositivo StorSimple envia dados externamente, além da implementação: por exemplo, a saída à Internet.

| N º de porta<sup>1,2</sup> | Dentro ou para fora | Âmbito de porta | Necessário | Notas |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |Não |<ul><li>Porta de saída é utilizada para acesso à Internet para obter atualizações.</li><li>O proxy da web de saída é configurável de utilizador.</li><li>Para permitir atualizações do sistema, esta porta tem também de estar aberta para o IPs fixos do controlador.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Sim |<ul><li>Porta de saída é utilizada para aceder aos dados na cloud.</li><li>O proxy da web de saída é configurável de utilizador.</li><li>Para permitir atualizações do sistema, esta porta tem também de estar aberta para o IPs fixos do controlador.</li><li>Esta porta também é usada em ambos os controladores para coleta de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Sim |A porta de saída é utilizada pelo dispositivo StorSimple para comunicar com o serviço StorSimple Device Manager. |
| 3260 (iSCSI) |Em |LAN |Não |Esta porta é utilizada para aceder aos dados através de iSCSI. |
| 5985 |Em |LAN |Não |Porta de entrada é utilizada pelo StorSimple Snapshot Manager para comunicar com o dispositivo StorSimple.<br>Esta porta também é usada quando ligar remotamente ao Windows PowerShell para StorSimple através de HTTP. |
| 5986 |Em |LAN |Não |Esta porta é utilizada quando ligar remotamente ao Windows PowerShell para StorSimple através de HTTPS. |

<sup>1</sup> nenhuma porta de entrada tem de ser aberta na Internet pública.

<sup>2</sup> se várias portas de transportar uma configuração de gateway, a ordem de tráfego de saída roteados será determinada com base na ordem de encaminhamento de porta descrito na [encaminhamento de porta](#routing-metric), abaixo.

<sup>3</sup> o controlador de IPs fixo no dispositivo StorSimple tem de ser encaminháveis e conseguir estabelecer ligação à Internet diretamente ou através do proxy web configurado. Os endereços IP fixos são utilizados para a manutenção de atualizações para o dispositivo e para a coleta de lixo. Se os controladores de dispositivo não é possível ligar à Internet através dos IPs fixos, não será capaz de atualizar o dispositivo StorSimple e coleta de lixo não funcionará corretamente.

> [!IMPORTANT]
> Certifique-se de que a firewall não modificar ou descriptografe qualquer tráfego SSL entre o dispositivo StorSimple e o Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede, muitas vezes, podem configurar regras de firewall avançado com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O dispositivo StorSimple e o serviço StorSimple Device Manager dependem de outros aplicativos da Microsoft, como o Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que os padrões de URL associados a estas aplicações podem alterar. Isso por sua vez, irá exigir o administrador de rede monitorizar e atualizar as regras de firewall para o seu StorSimple como e quando necessário.

Recomendamos que defina as regras de firewall para tráfego de saída, com base no StorSimple fixo livremente os endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir as regras de firewall avançado que são necessários para criar ambientes seguros.

> [!NOTE]
> O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede ativada. O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para o portal do Azure

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço do Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede com capacidade de nuvem |
| `https://*.backup.windowsazure.com` |Registo de dispositivo |Apenas dados 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento do Azure e monitorização |Interfaces de rede com capacidade de nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixos do controlador apenas |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |IPs fixos do controlador apenas |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede com capacidade de nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal do Azure Government

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Serviço do Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de Autenticação |Interfaces de rede com capacidade de nuvem |
| `https://*.backup.windowsazure.us` |Registo de dispositivo |Apenas dados 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento do Azure e monitorização |Interfaces de rede com capacidade de nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixos do controlador apenas |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |IPs fixos do controlador apenas |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede com capacidade de nuvem |

### <a name="routing-metric"></a>Métrica de encaminhamento

Uma métrica de encaminhamento está associada com as interfaces e o gateway que encaminhar os dados para redes especificadas. Métrica de encaminhamento é utilizada pelo protocolo de encaminhamento para calcular o melhor caminho para um determinado destino, se ele aprende a que vários caminhos existem para o mesmo destino. Menor a métrica de encaminhamento, maior será a preferência.

No contexto do StorSimple, se várias interfaces de rede e gateways estiverem configuradas para tráfego de canal, as métricas de encaminhamento serão entram para determinar a ordem relativa na qual irão se acostumar as interfaces. As métricas de encaminhamento não podem ser alteradas pelo utilizador. No entanto pode utilizar o `Get-HcsRoutingTable` cmdlet para imprimir a tabela de encaminhamento (e as métricas) no dispositivo StorSimple. Obter mais informações sobre o cmdlet Get-HcsRoutingTable [StorSimple de resolução de problemas de implementação](storsimple-troubleshoot-deployment.md).

O algoritmo de métrica encaminhamento utilizado para a atualização 2 e versões posteriores pode ser explicado da seguinte forma.

* Um conjunto de valores predeterminados tenham sido atribuídos a interfaces de rede.
* Considere uma tabela de exemplo mostrada abaixo, com valores atribuídos a várias interfaces de rede quando este for cloud-ativado ou desativado na cloud, mas com um gateway configurado. Tenha em atenção de que os valores atribuídos aqui são apenas os valores de exemplo.

    | Interface de rede | Capacidade de cloud | Desativado na cloud com o gateway |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem na qual o tráfego de nuvem será encaminhado através das interfaces de rede é:
  
    *Dados 0 > dados 1 > data 2 > dados 3 > 4 de dados > dados 5*
  
    Isso pode ser explicado no exemplo seguinte.
  
    Considere um dispositivo do StorSimple com duas interfaces de rede com capacidade de nuvem, Data 0 e 5 de dados. Dados de 1 a 4 de dados estão na cloud-desativado mas tem um gateway configurado. A ordem na qual o tráfego será encaminhado para este dispositivo será:
  
    *Dados 0 (1) > 5 (6) de dados > dados 1 (20) > dados 2 (30) > dados 3 (40) > 4 (50) de dados*
  
    *Os números em parênteses indicam as respectivas métricas de encaminhamento.*
  
    Se Data 0 falhar, o tráfego de nuvem é roteado a 5 de dados. Uma vez que um gateway está configurado em todos os outro rede, se Data 0 e 5 de dados falhar, o tráfego de nuvem terão de 1 de dados.
* Se falhar uma interface de rede com capacidade de nuvem, em seguida, são 3 repetições com 30 segundo de atraso para ligar à interface. Se as novas tentativas falharem, o tráfego é encaminhado para a próxima disponível habilitados para cloud interface conforme determinado pela tabela de roteamento. Se todos os a rede com capacidade de nuvem interfaces falhar, em seguida, o dispositivo irá efetuar a ativação pós-falha para o controlador (não reiniciado neste caso).
* Se houver uma falha de VIP para uma interface de rede com capacidade de iSCSI, haverá 3 repetições com um atraso de 2 segundos. Esse comportamento tem acordado durante o mesmo do versões anteriores. Se todas as interfaces de rede iSCSI falharem, irá ocorrer uma ativação pós-falha de controlador (accompanied por um reinício).
* Um alerta também é gerado no dispositivo StorSimple quando houver uma falha de VIP. Para obter mais informações, aceda a [referência rápida de alertas](storsimple-8000-manage-alerts.md).
* Em termos de repetições, o iSCSI terá prioridade sobre a cloud.
  
    Considere o exemplo a seguir: Um dispositivo tem duas interfaces de rede ativados o StorSimple, dados 0 e 1 de dados. Dados 0 são a capacidade de cloud, ao passo que os dados 1 é tanto na cloud e habilitados para iSCSI. Não existem outras interfaces de rede neste dispositivo estão ativados para iSCSI ou na cloud.
  
    Se falhar de 1 de dados, Considerando que é a última interface de rede iSCSI, isso resultará numa ativação pós-falha de controlador para 1 de dados no outro controlador.

### <a name="networking-best-practices"></a>Melhores práticas de rede

Para além dos requisitos de sistema de rede acima, para otimizar o desempenho da sua solução StorSimple, cumprir as melhores práticas seguintes:

* Certifique-se de que o dispositivo StorSimple tem uma largura de banda Mbps dedicado 40 (ou mais) disponível, o tempo todo. Esta largura de banda não deve ser partilhada (ou alocação deve ser garantida através da utilização de políticas de QoS) com quaisquer outras aplicações.
* Certifique-se de que a conectividade de rede à Internet está disponível em todos os momentos. Ligações de Internet esporádicos ou pouco fiáveis para os dispositivos, incluindo sem conectividade Internet alguma, irão resultar numa configuração não suportada.
* Isole o tráfego de iSCSI e na cloud por ter dedicado a interfaces de rede no seu dispositivo para acesso de iSCSI e na cloud. Para obter mais informações, consulte como [modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces) no dispositivo StorSimple.
* Não utilize uma configuração de protocolo de controlo de agregação de ligações (LACP) para as interfaces de rede. Esta é uma configuração não suportada.

## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidade para o StorSimple

A plataforma de hardware que está incluída com a solução StorSimple tem funcionalidades de disponibilidade e fiabilidade que fornecem uma Fundação para uma infraestrutura de armazenamento de elevada disponibilidade, tolerante a falhas no seu datacenter. No entanto, existem requisitos e melhores práticas que deve estar em conformidade com para ajudar a garantir a disponibilidade da solução StorSimple. Antes de implementar o StorSimple, reveja com atenção os seguintes requisitos e melhores práticas para o dispositivo StorSimple e computadores anfitriões ligados.

Para obter mais informações sobre a monitorização e manter os componentes de hardware do dispositivo StorSimple, aceda a [utilizar o serviço StorSimple Device Manager para monitorizar componentes de hardware e o estado](storsimple-8000-monitor-hardware-status.md) e [StorSimple substituição de componente de hardware](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de elevada disponibilidade e procedimentos para o dispositivo StorSimple

Reveja as seguintes informações com cuidado para garantir a elevada disponibilidade do seu dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Dispositivos do StorSimple incluem redundante e fontes de energia e resfriamento de módulos (PCMs). Cada PCM tem capacidade suficiente para fornecer um serviço para os chassis de todos. Para garantir a elevada disponibilidade, ambos os PCMs tem de ser instalados.

* Ligar o seu PCMs a diferentes origens de dados para fornecer disponibilidade se uma fonte de alimentação falhar.
* Se falhar um PCM, solicite uma substituição imediatamente.
* Remova um PCM com falha apenas quando tiver a substituição e estiver pronto para instalá-lo.
* Não remova ambas as PCMs em simultâneo. O módulo do PCM inclui o módulo de bateria de segurança. Remover ambas as PCMs irá resultar num encerramento sem proteção por bateria e o estado do dispositivo não será guardado. Para obter mais informações sobre a bateria, aceda a [manter o módulo de bateria de segurança](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos de controlador

Dispositivos do StorSimple incluem módulos de controlador redundantes e fontes. Os módulos de controlador operam de maneira ativo/passivo. Em qualquer momento, um módulo de controlador está ativo e fornece o serviço, estiver ligada o outro módulo do controlador passivo. O módulo de controlador passivo seja ativado e fica operacional se o módulo do controlador ativo falha ou for removido. Cada módulo controlador tem capacidade suficiente para fornecer um serviço para os chassis de todos. Ambos os módulos de controlador tem de estar instalados para garantir a elevada disponibilidade.

* Certifique-se de que ambos os módulos de controlador estão instalados em todos os momentos.
* Se um módulo de controlador falhar, solicite uma substituição imediatamente.
* Remova um módulo de controlador com falha apenas quando tiver a substituição e estiver pronto para instalá-lo. Remover um módulo para períodos prolongados afetará a ventilação e, por conseguinte, ao arrefecimento do sistema.
* Certifique-se de que as ligações de rede para ambos os módulos do controlador são idênticas, e as interfaces de rede ligada tem uma configuração de rede idênticos.
* Se um módulo de controlador falha ou tiver substituição, certifique-se de que o módulo de controlador está no Estado ativo antes de substituir o módulo de controlador com falha. Para verificar se um controlador está ativo, aceda ao [identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova ambos os módulos de controlador ao mesmo tempo. Se uma ativação pós-falha de controlador está em curso, não encerre o módulo de controlador em modo de espera ou removê-lo do chassi.
* Após uma ativação pós-falha de controlador, aguarde, pelo menos, cinco minutos antes de remover o módulo de controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

StorSimple controlador módulos dispositivo cada tenham quatro de 1 Gigabit e duas 10 interfaces de rede Gigabit Ethernet.

* Certifique-se de que as ligações de rede para ambos os módulos do controlador são idênticas e interfaces de rede que as interfaces de módulo de controlador estão ligadas a ter uma configuração de rede idênticas.
* Quando possível, implante conexões de rede em comutadores diferentes para garantir a disponibilidade do serviço em caso de falha de dispositivo de rede.
* Quando desligar o único ou última interface habilitados para iSCSI restante (com IPs atribuídos), desative a interface primeiro e, em seguida, desligue os cabos. Se a interface está desligada pela primeira vez, em seguida, ele fará com que o controlador ativo para a ativação pós-falha para o controlador passivo. Se o controlador passivo também tem suas interfaces correspondentes não-conectados, ambos os controladores irão reiniciar várias vezes antes de optar por um controlador.
* Ligar-se pelo menos duas interfaces de dados para a rede de cada módulo de controlador.
* Se tiver ativado as duas interfaces de 10 GbE, implementá-los em diferentes comutadores.
* Sempre que possível, utilize o MPIO nos servidores para se certificar de que os servidores podem tolerar uma ligação, a rede ou a falha de interface.

Para obter mais informações sobre o seu dispositivo para a elevada disponibilidade e desempenho de rede, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Dispositivos do StorSimple incluem discos de estado sólido (SSDs) e unidades de disco rígido (HDDs) que são protegidas através do espelhamento espaços. Utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais SSDs ou HDDs.

* Certifique-se de que todos os módulos SSD e HDD estão instalados.
* Se falhar um SSD ou HDD, solicite uma substituição imediatamente.
* Se um SSD ou HDD falha ou requer substituição, certifique-se de que remove apenas o SSD ou HDD que necessita de substituição.
* Não remova mais de um SSD ou HDD do sistema em qualquer ponto no tempo.
  Uma falha de 2 ou mais discos de determinado tipo (HDD, SSD) ou falha consecutiva dentro de um curto período de tempo pode resultar em perda de dados de mau funcionamento e o potencial do sistema. Se isto ocorrer, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter assistência.
* Durante a substituição, monitorizar o **componentes partilhados** no **estado de funcionamento do Hardware** painel para as unidades no SSDs e HDDs. Um Estado de marca de verificação verde indica que os discos estão em bom estado ou OK, ao passo que o ponto de exclamação uma vermelho indica um SSD ou HDD com falha.
* Recomendamos que configure instantâneos de cloud para todos os volumes que precisa proteger em caso de falha de sistema.

#### <a name="ebod-enclosure"></a>Bastidor EBOD

Modelo do dispositivo StorSimple 8600 inclui um bastidor estendido monte de discos (EBOD), além do bastidor principal. Um EBOD contém controladores de EBOD e unidades de disco rígido (HDDs) que são protegidas através do espelhamento espaços. Utilização de espaços espelhados garante que o dispositivo é capaz de tolerar a falha de um ou mais HDDs. A inclusão EBOD está ligada ao bastidor principal através de cabos SAS redundantes.

* Certifique-se de que ambos os módulos de controlador de bastidor EBOD, cabos SAS tanto as unidades de disco rígido estão todas instaladas em todos os momentos.
* Se um módulo de controlador de bastidor EBOD falhar, solicite uma substituição imediatamente.
* Se um módulo de controlador de bastidor EBOD falhar, certifique-se de que o módulo de controlador está ativo antes de substituir o módulo com falha. Para verificar se um controlador está ativo, aceda ao [identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante uma substituição de módulo de controlador EBOD, monitorizar continuamente o estado do componente no serviço StorSimple Device Manager ao aceder a **Monitor** > **estado de funcionamento do Hardware**.
* Se um cabo de SAS falha ou requer substituição (Support da Microsoft devem estar envolvido para tal a determinação), certifique-se de que remove apenas o cabo SAS que necessita de substituição.
* Não simultaneamente remova os dois cabos SAS do sistema em qualquer ponto no tempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de elevada disponibilidade para os seus computadores anfitrião

Reveja com atenção estas melhores práticas para garantir a elevada disponibilidade dos anfitriões ligados ao dispositivo StorSimple.

* Configurar o StorSimple com [configurações de cluster de servidor de ficheiros de dois nós][1]. Ao remover pontos únicos de falha e a criar no redundância no lado do host, a solução inteira torna-se elevada disponibilidade.
* Utilizam partilhas de continuamente disponíveis (AC) disponíveis com o Windows Server 2012 (SMB 3.0) para elevada disponibilidade durante a ativação pós-falha dos controladores de armazenamento. Para obter informações adicionais para a configuração de clusters de servidor de ficheiros e partilhas continuamente disponíveis com o Windows Server 2012, consulte este [vídeo de demonstração](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os limites de sistema do StorSimple](storsimple-8000-limits.md).
* [Saiba como implementar a solução StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
