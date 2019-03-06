---
title: Diferenças de armazenamento do Azure stack e considerações | Documentos da Microsoft
description: Compreenda as diferenças entre o armazenamento do Azure stack e o armazenamento do Azure, juntamente com considerações de implementação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: ff07d2197f1a0bd67a3c152941e7c9f466e5e414
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441530"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure da pilha: Diferenças e considerações

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O armazenamento do Azure Stack é o conjunto de serviços de cloud de armazenamento no Microsoft Azure Stack. Armazenamento de pilha do Azure fornece BLOBs, tabela, fila e funcionalidade de gestão de conta com uma semântica consistente para o Azure.

Este artigo resume as diferenças de armazenamento do Azure Stack conhecidas dos serviços de armazenamento do Azure. Ele também apresenta uma lista de aspetos a considerar ao implementar o Azure Stack. Para saber mais sobre das principais diferenças entre global do Azure e o Azure Stack, veja a [considerações da chave](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-storage-differences"></a>Referência rápida: Diferenças de armazenamento

| Funcionalidade | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Armazenamento de ficheiros|Com base na cloud partilhas de ficheiros SMB suportadas|Ainda não é suportado
|Encriptação do serviço de armazenamento do Azure para dados Inativos|encriptação AES de 256 bits. Suporta a encriptação a utilizar chaves geridas pelo cliente no Cofre de chaves.|Encriptação de AES de 128 bits do BitLocker. Utilizar chaves geridas pelo cliente de encriptação não é suportada.
|Tipo de conta de armazenamento|Contas de armazenamento para fins gerais V1, V2 e BLOBs|Para fins gerais V1.
|Opções de replicação|Armazenamento localmente redundante, armazenamento georredundante, armazenamento georredundante com acesso de leitura e armazenamento com redundância de zona|Armazenamento localmente redundante.
|Armazenamento Premium|Totalmente suportado|Pode ser aprovisionado, mas sem limite de desempenho ou garantia.
|Managed disks|Premium e standard suportados|Suportada quando utiliza a versão 1808 ou posterior.
|Nome do blob|os 1024 carateres (2.048 bytes)|880 carateres (1,760 bytes)
|Tamanho máximo de blob de bloco|4.75 TB (100 MB X 50 000 blocos)|4.75 TB (100 MB x 50 000 blocos) para a atualização 1802 ou a versão mais recente. 50 000 x 4 MB (aproximadamente, 195 GB) para versões anteriores.
|Cópia do instantâneo de blob de página|Cópia de segurança do Azure discos não geridos VM ligados a uma VM em execução suportada|Ainda não é suportado.
|Cópia do instantâneo incremental de blob de página|Premium e blobs de página do Azure standard suportados|Ainda não é suportado.
|A faturação do blob de página|São cobradas taxas de páginas exclusivas, quer estejam no blob ou no instantâneo. Não faria cobrado custos adicionais para instantâneos associados um blob até que o blob de base que está a ser atualizado.|São cobradas taxas de instantâneos de blob e assiociated bases. Serão cobrados encargos adicionais para cada instantâneo individual.
|Camadas de armazenamento para armazenamento de BLOBs|Camadas frequente, esporádica e de camadas de armazenamento de arquivo.|Ainda não é suportado.
|Eliminação de forma recuperável para armazenamento de BLOBs|Geralmente disponível|Ainda não é suportado.
|Tamanho máximo de blob de página|8 TB|1 TB
|Tamanho de página do blob de página|512 bytes|4 KB
|Tamanho da chave de linha e de chave de partição de tabela|os 1024 carateres (2.048 bytes)|400 caracteres (800 bytes)
|Instantâneo de blob|O número máximo de instantâneos de um blob não é limitado.|O número máximo de instantâneos de um blob é 1000.
|Autenticação do Azure AD para o armazenamento|Em pré-visualização|Ainda não é suportado.
|Blobs imutáveis|Geralmente disponível|Ainda não é suportado.
|Firewall e regras de rede virtual para o armazenamento|Geralmente disponível|Ainda não é suportado.|

Também existem diferenças com métricas de armazenamento:

* Os dados de transação nas métricas de armazenamento não distinguem largura de banda de rede internos ou externos.
* Os dados de transação nas métricas de armazenamento não incluem o acesso à máquina virtual para os discos montados.

## <a name="api-version"></a>Versão de API

As seguintes versões são suportadas com o armazenamento do Azure Stack:

APIs de serviços de armazenamento do Azure:

1811 ou versões mais recentes de atualização:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versões anteriores:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

APIs de gestão dos serviços de armazenamento do Azure:

1811 ou versões mais recentes de atualização:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Versões anteriores:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Para obter mais informações sobre bibliotecas de cliente de armazenamento do Azure Stack suportados, consulte: [Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Passos Seguintes

* [Introdução às ferramentas de desenvolvimento do armazenamento do Azure Stack](azure-stack-storage-dev.md)
* [Utilizar ferramentas de transferência de dados para o armazenamento do Azure Stack](azure-stack-storage-transfer.md)
* [Introdução ao armazenamento do Azure Stack](azure-stack-storage-overview.md)
