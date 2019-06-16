---
title: Requisitos do sistema do Microsoft Azure dados caixa pesada | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para seu pesadas de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427737"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Requisitos de sistema pesadas de caixa de dados do Azure (pré-visualização)

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

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o Azure Data Box](data-box-deploy-ordered.md)
