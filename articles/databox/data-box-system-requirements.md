---
title: Requisitos de sistema do Microsoft Azure Data Box | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839755"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importante para o Microsoft Azure Data Box e para os clientes ligar para o Data Box. Recomendamos que reveja as informações cuidadosamente antes de implementar o Data Box e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem:

* **Requisitos de software para os anfitriões a ligar ao Data Box** -descreve as plataformas suportadas, navegadores da web local da interface do Usuário, os clientes do SMB e quaisquer requisitos adicionais para anfitriões que podem ligar-se para o Data Box.
* **Requisitos de rede para o Data Box** -fornece informações sobre os requisitos de rede para a operação ideal da caixa de dados.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os clientes SMB, sistemas operativos suportados e browsers suportados para a web local da interface do Usuário.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados por clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de ficheiros suportados para clientes do Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Browsers suportados

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, uma ligação de dados de 1 GbE pode ser utilizada para copiar dados, mas a cópia velocidades são afetadas.

### <a name="port-requirements"></a>Requisitos de portas

A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *no* ou *entrada* refere-se para a direção do que acesso de pedidos de cliente recebidos para o seu dispositivo. *Out* ou *saída* refere-se para a direção em que o seu dispositivo do Data Box envia dados externamente, além da implementação: por exemplo, a saída à Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passos Seguintes

* [Implementar o Azure Data Box](data-box-deploy-ordered.md)
