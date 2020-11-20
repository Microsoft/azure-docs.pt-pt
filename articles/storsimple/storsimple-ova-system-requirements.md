---
title: Requisitos do sistema Microsoft Azure StorSimple do sistema Virtual Array
description: Conheça os requisitos de software e networking para o seu StorSimple Virtual Array
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966178"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema da Matriz Virtual StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Descrição geral

Este artigo descreve os requisitos importantes do sistema para o seu Microsoft Azure StorSimple Virtual Array e para os clientes de armazenamento que acedem à matriz. Recomendamos que reveja as informações cuidadosamente antes de implementar o seu sistema StorSimple e, em seguida, volte a fazê-lo conforme necessário durante a implementação e posterior funcionamento.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** - descreve as plataformas de virtualização suportadas, navegadores web, iniciadores iSCSI, clientes SMB, requisitos mínimos de dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operativos.
* **Requisitos de networking para o dispositivo StorSimple** - fornece informações sobre as portas que precisam de estar abertas na sua firewall para permitir iSCSI, cloud ou tráfego de gestão.

O sistema StorSimple exige informações publicadas neste artigo apenas para StorSimple Virtual Arrays.

* Para dispositivos da série 8000, aceda aos [requisitos do Sistema para o seu dispositivo da série StorSimple 8000](./storsimple-8000-system-requirements.md).
* Para dispositivos da série 7000, aceda aos [requisitos do Sistema para o seu dispositivo da série StorSimple 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Os requisitos do software incluem a informação sobre os navegadores web suportados, versões SMB, plataformas de virtualização e os requisitos mínimos do dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualização suportadas
| **Hipervisor** | **Versão** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 e mais tarde |
| VMware ESXi |5.0, 5.5, 6.0 e 6.5. |

> [!IMPORTANT]
> Não instale ferramentas VMware no seu StorSimple Virtual Array; isto resultará numa configuração não apoiada.

### <a name="virtual-device-requirements"></a>Requisitos de dispositivo virtual
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de processadores virtuais (núcleos) |4 |
| Memória mínima (RAM) |8 GB <br> Para um servidor de ficheiros, 8 GB para menos de 2 milhões de ficheiros e 16 GB para 2 - 4 milhões de ficheiros|
| Espaço de<sup>disco 1</sup> |Disco DE SO - 80 GB <br></br>Disco de dados - 500 GB a 8 TB |
| Número mínimo de interfaces de rede |1 |
| Largura de banda da Internet<sup>2</sup> |Largura de banda mínima necessária: 5 Mbps <br> Largura de banda recomendada: 100 Mbps <br> A velocidade das escalas de transferência de dados com a largura de banda da Internet. Por exemplo, 100 GB de dados demoram 2 dias a ser transferidos a 5 Mbps, o que pode levar a falhas de backup porque as cópias de segurança diárias não seriam completadas num dia. Com uma largura de banda de 100 Mbps, 100 GB de dados podem ser transferidos em 2,5 horas.   |

<sup>1</sup> - Fino provisionado

<sup>2</sup> - Os requisitos da rede podem variar dependendo da taxa diária de alteração de dados. Por exemplo, se um dispositivo precisar de fazer backup de 10 GB ou mais alterações durante um dia, então a cópia de segurança diária sobre uma ligação de 5 Mbps pode demorar até 4,25 horas (se os dados não puderem ser comprimidos ou desapliques).

### <a name="supported-web-browsers"></a>Browsers suportados
| **Componente** | **Versão** | **Requisitos/notas adicionais** |
| --- | --- | --- |
| Microsoft Edge |Versão mais recente | |
| Internet Explorer |Versão mais recente |Testado com Internet Explorer 11 |
| Google Chrome |Versão mais recente |Testado com Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de armazenamento suportados
Os seguintes requisitos de software são para os iniciadores iSCSI que acedem ao seu StorSimple Virtual Array (configurado como um servidor iSCSI).

| **Sistemas operativos suportados** | **Versão necessária** | **Requisitos/notas adicionais** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |A StorSimple pode criar volumes pouco abastetados e totalmente abastetados. Não pode criar volumes parcialmente a provisionados. Os volumes StorSimple iSCSI são suportados apenas para: <ul><li>Volumes simples nos discos básicos do Windows.</li><li>Windows NTFS para formatação de um volume.</li> |

Os seguintes requisitos de software são para os clientes SMB que acedem ao seu StorSimple Virtual Array (configurado como um servidor de ficheiros).

| **Versão SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Não copie ou guarde ficheiros protegidos pelo Sistema de Ficheiros encriptadores do Windows (EFS) para o servidor de ficheiros StorSimple Virtual Array; isto resultará numa configuração não apoiada.


### <a name="supported-storage-format"></a>Formato de armazenamento suportado
Apenas o armazenamento do bloco Azure é suportado. As bolhas de página não são suportadas. Mais informações [sobre bolhas de blocos e bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de rede
A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego iSCSI, SMB, nuvem ou gestão. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que entra solicita o acesso ao seu dispositivo. *Para fora* ou *para fora* refere-se à direção em que o seu dispositivo StorSimple envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

| **Porto nº <sup>1</sup>** | **Dentro ou fora** | **Âmbito portuário** | **Obrigatório** | **Notas** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Saída |WAN |No |A porta de saída é utilizada para o acesso à Internet para recuperar atualizações. <br></br>O representante da web de saída é configurável pelo utilizador. |
| TCP 443 (HTTPS) |Saída |WAN |Yes |A porta de saída é utilizada para aceder a dados na nuvem. <br></br>O representante da web de saída é configurável pelo utilizador. |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet. <br></br> Note que se implementar um servidor de ficheiros, recomendamos a utilização do servidor DNS local. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; ver notas. |Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet.<br></br> Note que se implementar um servidor de ficheiros, recomendamos sincronizar o tempo com os seus controladores de domínio ative directory. |
| TCP 80 (HTTP) |Em |LAN |Yes |Este é o porto de entrada para ui local no dispositivo StorSimple para gestão local. <br></br> Note que aceder ao UI local sobre HTTP irá redirecionar automaticamente para HTTPS. |
| TCP 443 (HTTPS) |Em |LAN |Yes |Este é o porto de entrada para ui local no dispositivo StorSimple para gestão local. |
| TCP 3260 (iSCSI) |Em |LAN |No |Esta porta é utilizada para aceder a dados sobre o iSCSI. |

<sup>1</sup> Não é necessário abrir portas de entrada na Internet pública.

> [!IMPORTANT]
> Certifique-se de que a firewall não modifica ou desencripta qualquer tráfego TLS entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall
Os administradores de rede podem frequentemente configurar regras avançadas de firewall com base nos padrões de URL para filtrar a entrada e o tráfego de saída. A sua matriz virtual e o serviço StorSimple Device Manager dependem de outras aplicações da Microsoft, tais como Azure Service Bus, Azure Ative Directory Access Control, contas de armazenamento e servidores Microsoft Update. Os padrões de URL associados a estas aplicações podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a estas aplicações podem mudar. Isto, por sua vez, exigirá que o administrador da rede monitorize e atualize as regras de firewall para o seu StorSimple conforme e quando necessário. 

Recomendamos que estabeleça as suas regras de firewall para tráfego de saída, com base em endereços IP fixos StorSimple, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> 
> * Os IPs do dispositivo (fonte) devem ser sempre definidos para todas as interfaces de rede ativadas pela nuvem. 
> * Os IPs de destino devem ser definidos para [as gamas IP do datacenter Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Padrão url | Componente/Funcionalidade |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acessos<br>Azure Service Bus<br>Serviço de Autenticação|
| `http://*.backup.windowsazure.com` |Registo de dispositivo |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação do certificado |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento Azure e monitorização |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores microsoft update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de apoio |
| `https://*.data.microsoft.com` |Serviço de telemetria no Windows, consulte a [atualização para a experiência do cliente e telemetria de diagnóstico](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Passos seguintes
* [Prepare o portal para implementar o seu StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)