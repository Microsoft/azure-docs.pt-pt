---
title: Microsoft Azure StorSimple os requisitos de sistema da matriz virtual
description: Saiba mais sobre os requisitos de software e rede para sua matriz virtual StorSimple
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 38f9c432191ac613c1c0f8c02458e8bc4bf8232a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273772"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema da Matriz Virtual StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Visão geral

Este artigo descreve os requisitos de sistema importantes para sua Microsoft Azure StorSimple matriz virtual e para os clientes de armazenamento que acessam a matriz. Recomendamos que você revise as informações cuidadosamente antes de implantar o sistema StorSimple e, em seguida, reveja-as conforme necessário durante a implantação e a operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** – descreve as plataformas de virtualização com suporte, navegadores da Web, iniciadores iSCSI, clientes SMB, requisitos mínimos de dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operacionais.
* **Requisitos de rede para o dispositivo StorSimple** – fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego de iSCSI, nuvem ou gerenciamento.

As informações de requisitos do sistema do StorSimple publicadas neste artigo aplicam-se somente a matrizes virtuais StorSimple.

* Para dispositivos da série 8000, vá para [requisitos do sistema para seu dispositivo StorSimple da série 8000](storsimple-system-requirements.md).
* Para dispositivos da série 7000, vá para [requisitos do sistema para seu dispositivo StorSimple da série 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Os requisitos de software incluem as informações sobre os navegadores da Web com suporte, as versões SMB, as plataformas de virtualização e os requisitos mínimos de dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualização com suporte
| **Visor** | **Versão** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi |5,0, 5,5, 6,0 e 6,5. |

> [!IMPORTANT]
> Não instale as ferramentas do VMware em sua matriz virtual StorSimple; Isso resultará em uma configuração sem suporte.

### <a name="virtual-device-requirements"></a>Requisitos do dispositivo virtual
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de processadores virtuais (núcleos) |4 |
| Memória mínima (RAM) |8 GB <br> Para um servidor de arquivos, 8 GB para menos de 2 milhões arquivos e 16 GB para 2-4 milhões de arquivos|
| Espaço em disco<sup>1</sup> |Disco do so-80 GB <br></br>Disco de dados-500 GB a 8 TB |
| Número mínimo de interface (s) de rede |1 |
| Largura de banda de Internet<sup>2</sup> |Largura de banda mínima necessária: 5 Mbps <br> Largura de banda recomendada: 100 Mbps <br> A velocidade da transferência de dados é dimensionada com a largura de banda da Internet. Por exemplo, 100 GB de dados leva 2 dias para serem transferidos a 5 Mbps, o que pode levar a falhas de backup porque os backups diários não seriam concluídos em um dia. Com uma largura de banda de 100 Mbps, 100 GB de dados podem ser transferidos em 2,5 horas.   |

<sup>1</sup> -provisionamento dinâmico

<sup>2</sup> -os requisitos de rede podem variar dependendo da taxa diária de alteração de dados. Por exemplo, se um dispositivo precisar fazer backup de 10 GB ou mais alterações durante um dia, o backup diário em uma conexão de 5 Mbps poderá levar até 4,25 horas (se os dados não puderem ser compactados ou com eliminação de duplicação).

### <a name="supported-web-browsers"></a>Browsers suportados
| **Componente** | **Versão** | **Requisitos/observações adicionais** |
| --- | --- | --- |
| Microsoft Edge |Versão mais recente | |
| Internet Explorer |Versão mais recente |Testado com o Internet Explorer 11 |
| Google Chrome |Versão mais recente |Testado com o Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de armazenamento com suporte
Os seguintes requisitos de software são para os iniciadores iSCSI que acessam o StorSimple virtual array (configurado como um servidor iSCSI).

| **Sistemas operativos suportados** | **Versão necessária** | **Requisitos/observações adicionais** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |O StorSimple pode criar volumes com provisionamento dinâmico e provisionamento total. Ele não pode criar volumes parcialmente provisionados. Os volumes iSCSI do StorSimple têm suporte apenas para: <ul><li>Volumes simples em discos básicos do Windows.</li><li>Windows NTFS para formatar um volume.</li> |

Os seguintes requisitos de software são para os clientes SMB que acessam o StorSimple virtual array (configurado como um servidor de arquivos).

| **Versão do SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3, 2 |

> [!IMPORTANT]
> Não copie nem armazene arquivos protegidos pelo Windows Encrypting File System (EFS) no servidor de arquivos do StorSimple virtual array; Isso resultará em uma configuração sem suporte.


### <a name="supported-storage-format"></a>Formato de armazenamento com suporte
Somente o armazenamento de blobs de blocos do Azure tem suporte. Não há suporte para BLOBs de páginas. Mais informações [sobre blobs de blocos e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de rede
A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego de iSCSI, SMB, de nuvem ou de gerenciamento. *Nesta tabela, entrada* ou *entrada* refere-se à direção da qual as solicitações de entrada do cliente acessam seu dispositivo. *Out* ou *Outbound* refere-se à direção na qual o dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, de saída para a Internet.

| **N º da porta<sup>1</sup>** | **Entrada ou saída** | **Escopo da porta** | **Necessário** | **Notas** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Saída |WAN |Não |A porta de saída é usada para acesso à Internet para recuperar atualizações. <br></br>O proxy da Web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS) |Saída |WAN |Sim |A porta de saída é usada para acessar dados na nuvem. <br></br>O proxy da Web de saída é configurável pelo usuário. |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte observações. |Essa porta será necessária somente se você estiver usando um servidor DNS baseado na Internet. <br></br> Observe que, se estiver implantando um servidor de arquivos, recomendamos o uso do servidor DNS local. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte observações. |Essa porta será necessária somente se você estiver usando um servidor NTP baseado na Internet.<br></br> Observe que, se estiver implantando um servidor de arquivos, é recomendável sincronizar o tempo com seus Active Directory controladores de domínio. |
| TCP 80 (HTTP) |Em |LAN |Sim |Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local. <br></br> Observe que o acesso à interface do usuário local por HTTP será redirecionado automaticamente para HTTPS. |
| TCP 443 (HTTPS) |Em |LAN |Sim |Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local. |
| TCP 3260 (iSCSI) |Em |LAN |Não |Essa porta é usada para acessar dados por iSCSI. |

<sup>1</sup> nenhuma porta de entrada precisa ser aberta na Internet pública.

> [!IMPORTANT]
> Certifique-se de que o firewall não modifique nem descriptografe nenhum tráfego SSL entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall
Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Sua matriz virtual e o serviço de Device Manager do StorSimple dependem de outros aplicativos da Microsoft, como o barramento de serviço do Azure, o controle de acesso Azure Active Directory, as contas de armazenamento e os servidores Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall para seu StorSimple como e quando necessário. 

Recomendamos que você defina suas regras de firewall para o tráfego de saída, com base em endereços IP fixos do StorSimple, livremente na maioria dos casos. No entanto, você pode usar as informações abaixo para definir regras de firewall avançadas que são necessárias para criar ambientes seguros.

> [!NOTE]
> 
> * Os IPs do dispositivo (origem) devem ser sempre definidos para todas as interfaces de rede habilitadas para nuvem. 
> * Os IPs de destino devem ser definidos para [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Padrão de URL | Componente/funcionalidade |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Serviço de Gestor de Dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Azure Service Bus<br>Serviço de autenticação|
| `http://*.backup.windowsazure.com` |Registo de dispositivo |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitoramento e contas de armazenamento do Azure |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |
| `https://*.data.microsoft.com` |Serviço de telemetria no Windows, consulte a [atualização para a telemetria de experiência e diagnóstico do cliente](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Passos seguintes
* [Preparar o portal para implantar o StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md)
