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
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011743"
---
Todos os dados armazenados em Ficheiros Azure são encriptados em repouso utilizando encriptação do serviço de armazenamento Azure (SSE). A encriptação do serviço de armazenamento funciona de forma semelhante ao BitLocker no Windows: os dados são encriptados abaixo do nível do sistema de ficheiros. Como os dados são encriptados no sistema de ficheiros da Azure, uma vez que está codificado para o disco, não é necessário ter acesso à chave subjacente no cliente para ler ou escrever para a partilha de ficheiros Azure. A encriptação em repouso aplica-se tanto aos protocolos SMB como NFS.

Por predefinição, os dados armazenados em Ficheiros Azure são encriptados com teclas geridas pela Microsoft. Com as teclas geridas pela Microsoft, a Microsoft detém as chaves para encriptar/desencriptar os dados, e é responsável por rodá-los regularmente. Também pode optar por gerir as suas próprias chaves, o que lhe dá controlo sobre o processo de rotação. Se optar por encriptar as suas partilhas de ficheiros com chaves geridas pelo cliente, a Azure Files está autorizada a aceder às suas chaves para cumprir pedidos de leitura e de escrita dos seus clientes. Com as chaves geridas pelo cliente, pode revogar esta autorização a qualquer momento, mas isso significa que a sua partilha de ficheiros Azure deixará de estar acessível através da SMB ou da API filerest.

O Azure Files utiliza o mesmo esquema de encriptação que os outros serviços de armazenamento Azure, como o armazenamento Azure Blob. Para saber mais sobre a encriptação do serviço de armazenamento Azure (SSE), consulte [a encriptação de armazenamento Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).