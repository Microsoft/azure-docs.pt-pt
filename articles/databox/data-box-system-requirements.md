---
title: Requisitos do sistema Microsoft Azure Data Box/ Microsoft Docs
description: Saiba mais sobre o software e os requisitos de rede para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79259958"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos do sistema Azure Data Box

Este artigo descreve os requisitos importantes do sistema para a sua Caixa de Dados Do Microsoft Azure e para os clientes que se ligam à Caixa de Dados. Recomendamos que reveja as informações cuidadosamente antes de implementar a sua Caixa de Dados e, em seguida, volte a fazê-la conforme necessário durante a implementação e posterior funcionamento.

Os requisitos do sistema incluem:

* **Requisitos de software para anfitriões que se conectam à Data Box** - descreve as plataformas suportadas, navegadores para a web uI local, clientes SMB, e quaisquer requisitos adicionais para anfitriões que possam ligar-se à Caixa de Dados.
* **Requisitos de rede para a Caixa de Dados** - fornece informações sobre os requisitos de rede para o funcionamento ideal da Caixa de Dados.


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

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, pode ser utilizada uma ligação de dados de 1 GbE para copiar dados, mas as velocidades de cópia são impactadas.

### <a name="port-requirements"></a>Requisitos portuários

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *dentro* ou *entrada* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *Para fora* ou *para fora* refere-se à direção em que o seu dispositivo Data Box envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Próximos passos

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
