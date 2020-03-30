---
title: Microsoft Azure Data Box Requisitos do sistema pesado sinuoso Microsoft Docs
description: Conheça os requisitos de software e networking para a sua Caixa de Dados Azure Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260075"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisitos do sistema de caixa de dados Azure

Este artigo descreve os requisitos importantes do sistema para o seu dispositivo Azure Data Box Heavy e para os clientes que se conectam ao dispositivo. Recomendamos que reveja a informação cuidadosamente antes de implementar a sua Caixa de Dados Pesada e, em seguida, consulte-a de volta, se necessário durante a implementação e posterior operação.

Os requisitos do sistema incluem:

* **Os requisitos de software para os anfitriões que se conectam ao Data Box Heavy** - descreve as plataformas suportadas, navegadores para os ui web locais, clientes SMB e quaisquer requisitos adicionais para os anfitriões que possam ligar-se à Caixa de Dados.
* **Os requisitos** de rede para a Caixa de Dados Heavy - fornece informações sobre os requisitos de rede para o funcionamento ideal do dispositivo Data Box Heavy.

## <a name="software-requirements"></a>Requisitos de software

Os requisitos do software incluem a informação sobre os sistemas operativos suportados, navegadores suportados para os ui web locais e clientes SMB.

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

## <a name="networking-requirements"></a>Requisitos de networking

O seu datacenter tem de ter uma rede de alta velocidade. Para velocidades de cópia mais rápidas, duas ligações de 40-GbE podem ser utilizadas paralelamente (uma por nó). Se não tiver 40-GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó).

### <a name="port-requirements"></a>Requisitos portuários

A tabela seguinte lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *dentro* ou *à entrada* refere-se à direção a partir da qual o cliente que entra solicita acesso ao seu dispositivo. *out* or *out refere-se* à direção em que o seu dispositivo Data Box Heavy envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
