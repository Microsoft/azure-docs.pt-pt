---
title: Requisitos de sistema do Microsoft Azure StorSimple Virtual Array | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para a sua matriz Virtual StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: becf48320b346feea82944ed2f7e752125795d40
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999515"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema da Matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
Este artigo descreve os requisitos de sistema importante para o Microsoft Azure StorSimple Virtual Array e para os clientes de armazenamento aceder à matriz. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu sistema do StorSimple e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem:

* **Requisitos de software para os clientes de armazenamento** -descreve as plataformas de Virtualização suportado, navegadores da web, iniciadores iSCSI, SMB clientes, os requisitos de mínima do dispositivo virtual e quaisquer requisitos adicionais para nesses sistemas operativos.
* **Requisitos de rede para o dispositivo StorSimple** -fornece informações sobre as portas que precisam de estar abertas na firewall para permitir o tráfego de iSCSI, nuvem ou gestão.

As informações de requisitos de sistema do StorSimple publicadas neste artigo aplica-se apenas para as matrizes virtuais do StorSimple.

* Para dispositivos de 8000 série, aceda a [requisitos de sistema do seu dispositivo da série StorSimple 8000](storsimple-system-requirements.md).
* Para dispositivos das 7000 séries, aceda a [requisitos de sistema para o seu dispositivo de séries StorSimple 5000 a 7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Os requisitos de software incluem informações sobre os browsers suportados, as versões da SMB, plataformas de Virtualização e os requisitos de mínima do dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de Virtualização suportados
| **Hypervisor** | **Versão** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi |5.0, 5.5, 6.0 e 6.5. |

> [!IMPORTANT]
> Não instale as ferramentas do VMware em sua matriz Virtual StorSimple. Isto irá resultar numa configuração não suportada.

### <a name="virtual-device-requirements"></a>Requisitos do dispositivo virtual
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de processadores virtuais (núcleos) |4 |
| Mínimo de memória (RAM) |8 GB <br> Para um servidor de ficheiros, 8 GB para menos de 2 milhões de ficheiros e 16 GB para 2-4 milhões de ficheiros|
| Espaço em disco<sup>1</sup> |Disco do SO - 80 GB <br></br>Disco de dados - 500 GB para 8 TB |
| Número mínimo de interfaces de rede |1 |
| Largura de banda de Internet<sup>2</sup> |Largura de banda mínima necessária: 5 Mbps <br> Largura de banda recomendada: 100 Mbps <br> A velocidade de escalas de transferência de dados com a largura de banda de Internet. Por exemplo, 100 GB de dados leva dois dias para transferir a 5 Mbps, que pode levar a falhas de cópia de segurança porque as cópias de segurança diárias não seriam concluída num dia. Com uma largura de banda de 100 Mbps, 100 GB de dados podem ser transferido em 2,5 horas.   |

<sup>1</sup> - fina aprovisionado

<sup>2</sup> -requisitos de rede podem variar consoante a taxa de alteração de dados diária. Por exemplo, se um dispositivo tiver de fazer cópias de segurança de 10 GB ou mais alterações durante um dia, em seguida, a cópia de segurança diária através de uma ligação de Mbps 5 pode levar até 4.25 horas (se os dados não foi possível ser comprimidos ou eliminação de duplicados).

### <a name="supported-web-browsers"></a>Browsers suportados
| **Componente** | **Versão** | **Notas/requisitos adicionais** |
| --- | --- | --- |
| Microsoft Edge |Versão mais recente | |
| Internet Explorer |Versão mais recente |Testado com o Internet Explorer 11 |
| Google Chrome |Versão mais recente |Testado com o Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de armazenamento suportadas
São os seguintes requisitos de software para os iniciadores iSCSI que acedem a sua matriz Virtual do StorSimple (que é configurado como um servidor de iSCSI).

| **Sistemas operativos suportados** | **Versão necessária** | **Notas/requisitos adicionais** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple pode criar com aprovisionamento dinâmico e totalmente aprovisionado volumes. Ele não é possível criar volumes parcialmente aprovisionados. Volumes do StorSimple iSCSI apenas são suportados para: <ul><li>Simples volumes em discos básicos do Windows.</li><li>NTFS do Windows para a formatação de um volume.</li> |

São os seguintes requisitos de software para os clientes SMB que acedem a sua matriz Virtual do StorSimple (que é configurado como servidor de ficheiros).

| **Versão do SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Não copie ou armazenar ficheiros protegidos pelo Windows Encrypting File System (EFS) para o servidor de ficheiros do StorSimple Virtual Array; Isto irá resultar numa configuração não suportada.


### <a name="supported-storage-format"></a>Formato de armazenamento suportado
Apenas o armazenamento de BLOBs de bloco do Azure é suportado. Blobs de páginas não são suportados. Obter mais informações [sobre blobs de blocos e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de rede
A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir o iSCSI, SMB, na cloud ou tráfego de gestão. Nesta tabela, *no* ou *entrada* refere-se para a direção a partir do qual os pedidos de cliente recebidos, aceda ao seu dispositivo. *Out* ou *saída* refere-se para a direção em que o dispositivo StorSimple envia dados externamente, além da implementação: por exemplo, a saída à Internet.

| **N º de porta<sup>1</sup>** | **Dentro ou para fora** | **Âmbito de porta** | **Necessário** | **Notas** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Saída |WAN |Não |Porta de saída é utilizada para acesso à Internet para obter atualizações. <br></br>O proxy da web de saída é configurável de utilizador. |
| TCP 443 (HTTPS) |Saída |WAN |Sim |Porta de saída é utilizada para aceder aos dados na cloud. <br></br>O proxy da web de saída é configurável de utilizador. |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor DNS baseado na Internet. <br></br> Tenha em atenção que se implementar um servidor de ficheiros, recomendamos que utilize o servidor DNS local. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseado na Internet.<br></br> Tenha em atenção que se implementar um servidor de ficheiros, recomendamos que a sincronização de hora com os controladores de domínio do Active Directory. |
| TCP 80 (HTTP) |Em |LAN |Sim |Esta é a porta de entrada para a interface do Usuário local no dispositivo StorSimple para gestão local. <br></br> Tenha em atenção que a interface do Usuário local a aceder através de HTTP será automaticamente redirecionada para HTTPS. |
| TCP 443 (HTTPS) |Em |LAN |Sim |Esta é a porta de entrada para a interface do Usuário local no dispositivo StorSimple para gestão local. |
| TCP 3260 (iSCSI) |Em |LAN |Não |Esta porta é utilizada para aceder aos dados através de iSCSI. |

<sup>1</sup> nenhuma porta de entrada tem de ser aberta na Internet pública.

> [!IMPORTANT]
> Certifique-se de que a firewall não modificar ou descriptografe qualquer tráfego SSL entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall
Os administradores de rede, muitas vezes, podem configurar regras de firewall avançado com base nos padrões de URL para filtrar a entrada e o tráfego de saída. A sua matriz virtual e o serviço StorSimple Device Manager dependem de outros aplicativos da Microsoft, como o Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que os padrões de URL associados a estas aplicações podem alterar. Isso por sua vez, irá exigir o administrador de rede monitorizar e atualizar as regras de firewall para o seu StorSimple como e quando necessário. 

Recomendamos que defina as regras de firewall para tráfego de saída, com base no StorSimple fixo livremente os endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir as regras de firewall avançado que são necessários para criar ambientes seguros.

> [!NOTE]
> 
> * O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede com capacidade de nuvem. 
> * O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Padrão de URL | Componente/funcionalidade |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Serviço do Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de Autenticação|
| `http://*.backup.windowsazure.com` |Registo de dispositivo |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificados |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento do Azure e monitorização |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |
| `https://*.data.microsoft.com ` |O serviço de telemetria no Windows, consulte o [atualização para a experiência do cliente e a telemetria de diagnóstico](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Passos Seguintes
* [Preparar o portal para implementar a sua matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
