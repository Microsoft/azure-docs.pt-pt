---
title: Requisitos do sistema Microsoft Azure Data Box/ Microsoft Docs
description: Saiba mais sobre os requisitos importantes do sistema para a sua Caixa de Dados Azure e para os clientes que se ligam à Caixa de Dados.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767870"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos do sistema Azure Data Box

Este artigo descreve requisitos importantes do sistema para a sua Caixa de Dados Microsoft Azure e para os clientes que se ligam à Caixa de Dados. Recomendamos que reveja as informações cuidadosamente antes de implementar a sua Caixa de Dados e, em seguida, consulte-as conforme necessário durante a implementação e operação.

Os requisitos do sistema incluem:

* **Requisitos de software:** Para os anfitriões que se ligam à Caixa de Dados, descreve sistemas operativos suportados, protocolos de transferência de ficheiros, contas de armazenamento, tipos de armazenamento e navegadores para a UI web local.
* **Requisitos de rede:** Para a Caixa de Dados, descreve a ligação da rede e os requisitos da porta para o melhor funcionamento da Caixa de Dados.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos do software incluem sistemas operativos suportados, protocolos de transferência de ficheiros, contas de armazenamento, tipos de armazenamento e navegadores para a UI web local.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocolos de transferência de ficheiros suportados para clientes

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> A ligação às ações da Data Box não é suportada através de REST para encomendas de exportação. 

### <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento suportados

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Browsers suportados

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, pode ser utilizada uma ligação de dados de 1 GbE para copiar dados, mas as velocidades de cópia são afetadas.

### <a name="port-requirements"></a>Requisitos portuários

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *In* *(entrada)* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *out* (ou *outbound*) refere-se à direção em que o seu dispositivo Data Box envia dados externamente, para além da implementação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
