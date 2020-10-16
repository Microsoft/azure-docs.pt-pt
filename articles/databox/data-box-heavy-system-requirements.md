---
title: Requisitos do sistema Da Caixa de Dados do Microsoft Azure Microsoft Docs
description: Saiba mais sobre os requisitos de software e networking para a sua Caixa de Dados Azure Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707757"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisitos do sistema Azure Data Box

Este artigo descreve os requisitos importantes do sistema para o seu dispositivo Azure Data Box Heavy e para os clientes que se ligam ao dispositivo. Recomendamos que reveja as informações cuidadosamente antes de implementar a sua Caixa de Dados Pesada e, em seguida, volte a fazê-las conforme necessário durante a implementação e posterior funcionamento.

Os requisitos do sistema incluem:

* **Requisitos de software para anfitriões que se conectam a Data Box Heavy** - descreve as plataformas suportadas, navegadores para a web uI local, clientes SMB, e quaisquer requisitos adicionais para anfitriões que possam ligar-se à Caixa de Dados.
* **Requisitos de rede para a Caixa de Dados Pesada** - fornece informações sobre os requisitos de rede para o funcionamento ideal do dispositivo Data Box Heavy.

## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem a informação sobre os sistemas operativos suportados, navegadores suportados para a UI web local e clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de ficheiros suportados para clientes Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Browsers suportados

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

O seu datacenter tem de ter uma rede de alta velocidade. Para velocidades de cópia mais rápidas, duas ligações de 40 GbE podem ser utilizadas em paralelo (uma por nó). Se não tiver 40 GbE disponíveis, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó).

### <a name="port-requirements"></a>Requisitos portuários

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *Para fora* ou *para fora* refere-se à direção em que o seu dispositivo Data Box Heavy envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
