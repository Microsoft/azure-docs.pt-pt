---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597866"
---
Todos os dados armazenados em Ficheiros Azure são encriptados em repouso utilizando encriptação do serviço de armazenamento Azure (SSE). A encriptação do serviço de armazenamento funciona de forma semelhante ao BitLocker no Windows: os dados são encriptados abaixo do nível do sistema de ficheiros. Como os dados estão encriptados por baixo do sistema de ficheiros da partilha de ficheiros Azure, uma vez que está codificado para o disco, não é necessário ter acesso à chave subjacente ao cliente para ler ou escrever para a partilha de ficheiros Azure.

Por padrão, os dados armazenados nos Ficheiros Azure são encriptados com chaves geridas pela Microsoft. Com as chaves geridas pela Microsoft, a Microsoft detém as chaves para encriptar/desencriptar os dados, e é responsável por rodá-los regularmente. Também pode optar por gerir as suas próprias chaves, o que lhe dá controlo sobre o processo de rotação. Se optar por encriptar as suas partilhas de ficheiros com chaves geridas pelo cliente, o Azure Files está autorizado a aceder às suas chaves para cumprir os pedidos de leitura e escrita dos seus clientes. Com as chaves geridas pelo cliente, pode revogar esta autorização a qualquer momento, mas isto significa que a sua quota de ficheiros Azure deixará de estar acessível através da SMB ou da API FileREST.

O Azure Files utiliza o mesmo esquema de encriptação que os outros serviços de armazenamento do Azure, como o armazenamento da Azure Blob. Para saber mais sobre a encriptação do serviço de armazenamento Azure (SSE), consulte a encriptação de [armazenamento azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).