---
title: Requisitos do sistema Microsoft Azure Data Box Microsoft Docs
description: Saiba mais sobre o software e os requisitos de rede para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380588"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos do sistema Azure Data Box

Este artigo descreve os requisitos importantes do sistema para a sua Caixa de Dados Microsoft Azure e para os clientes que se conectam à Caixa de Dados. Recomendamos que reveja a informação cuidadosamente antes de implementar a sua Caixa de Dados e, em seguida, remeta-a de volta para ela conforme necessário durante a implementação e posterior operação.

Os requisitos do sistema incluem:

* **Requisitos de software para anfitriões que se conectam à Data Box** - descreve as plataformas suportadas, navegadores para os ui web locais, clientes SMB, e quaisquer requisitos adicionais para os anfitriões que possam ligar-se à Caixa de Dados.
* **Requisitos de rede para a Caixa** de Dados - fornece informações sobre os requisitos de networking para o melhor funcionamento da Caixa de Dados.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos do software incluem a informação sobre os sistemas operativos suportados, navegadores suportados para os ui web locais e clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de ficheiros suportados para clientes Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Browsers suportados

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de networking

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, uma ligação de dados de 1-GbE pode ser utilizada para copiar dados, mas as velocidades de cópia são impactadas.

### <a name="port-requirements"></a>Requisitos portuários

A tabela seguinte lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *dentro* ou *à entrada* refere-se à direção a partir da qual o cliente que entra solicita acesso ao seu dispositivo. *out* or *out refere-se* à direção em que o seu dispositivo Data Box envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passos Seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
