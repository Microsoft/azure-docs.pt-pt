---
title: Requisitos do sistema do Microsoft Azure dados caixa pesada | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para seu pesadas de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839786"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisitos de sistema pesadas de caixa de dados do Azure

Este artigo descreve os requisitos de sistema importante para o seu dispositivo pesadas de caixa de dados do Azure e para os clientes conectados ao dispositivo. Recomendamos que reveja as informações cuidadosamente antes de implementar seu pesada dados de caixa e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem:

* **Requisitos de software para anfitriões ligar a dados de caixa pesada** -descreve as plataformas suportadas, navegadores da web local da interface do Usuário, os clientes do SMB e quaisquer requisitos adicionais para anfitriões que podem ligar-se para o Data Box.
* **Requisitos de rede para os dados de caixa pesada** -fornece informações sobre os requisitos de rede para a operação ideal do dispositivo pesadas de caixa de dados.

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

O seu datacenter tem de ter uma rede de alta velocidade. Para mais rápidas velocidades de cópia, duas ligações de 40 GbE podem ser utilizadas em paralelo (uma por nó). Se não tiver 40 GbE disponível, recomendamos que tenha, pelo menos, duas ligações de 10 GbE (um por nó).

### <a name="port-requirements"></a>Requisitos de portas

A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *no* ou *entrada* refere-se para a direção do que acesso de pedidos de cliente recebidos para o seu dispositivo. *Out* ou *saída* refere-se para a direção em que o seu dispositivo dados caixa pesada envia a dados externamente, além da implementação: por exemplo, a saída à Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o Azure Data Box](data-box-deploy-ordered.md)
