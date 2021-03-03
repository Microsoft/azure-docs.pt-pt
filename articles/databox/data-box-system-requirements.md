---
title: Os requisitos do sistema Microsoft Azure Data Box| Microsoft Docs
description: Saiba mais sobre os requisitos importantes do sistema para a sua Caixa de Dados Azure e para os clientes que se ligam à Caixa de Dados.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 85a0ce20f1d8e5a7e943efc088c19a8ad1912fc1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706043"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos do sistema Azure Data Box

Este artigo descreve requisitos importantes do sistema para a sua Caixa de Dados Microsoft Azure e para os clientes que se ligam à Caixa de Dados. Recomendamos que reveja as informações cuidadosamente antes de implementar a sua Caixa de Dados e, em seguida, consulte-as quando for necessário durante a implementação e operação.

Os requisitos do sistema incluem:

* **Requisitos de software:** Para os anfitriões que se ligam à Caixa de Dados, descreve sistemas operativos suportados, protocolos de transferência de ficheiros, contas de armazenamento, tipos de armazenamento e navegadores para a UI web local.
* **Requisitos de rede:** Para a Caixa de Dados, descreve os requisitos para ligações de rede e portas para o melhor funcionamento da Caixa de Dados.


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

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha pelo menos uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, pode utilizar uma ligação de dados de 1 GbE para copiar dados, mas as velocidades de cópia são afetadas.

### <a name="port-requirements"></a>Requisitos portuários

A tabela que se segue lista as portas que precisam de ser abertas na sua firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *In* *(entrada)* refere-se à direção a partir da qual o cliente que chega solicita o acesso ao seu dispositivo. *out* (ou *outbound*) refere-se à direção em que o seu dispositivo Data Box envia dados externamente, para além da implementação. Por exemplo, os dados podem estar de saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
