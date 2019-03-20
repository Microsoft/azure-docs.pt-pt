---
title: LogDownloader - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Transferir ficheiros de registo que são produzidos pelo serviço de decisão personalizada do Azure.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109414"
---
# <a name="logdownloader"></a>LogDownloader

Transferir ficheiros de registo que são produzidos pelo serviço de decisão personalizada do Azure e gerarem a *.gz* ficheiros que são utilizados por experimentação.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3: Instalado e no seu caminho. Recomendamos que a versão de 64 bits para lidar com ficheiros grandes.
- O *mwt/Microsoft-ds* repositório: [Clone o repositório](https://github.com/Microsoft/mwt-ds).
- O *azure-storage-blob* pacote: Para obter detalhes de instalação, aceda a [biblioteca de armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Introduza a cadeia de ligação de armazenamento do Azure no *mwt-ds/DataScience/ds.config*: Siga os *my_app_id: my_connectionString* modelo. Pode especificar várias `app_id`. Quando executa `LogDownloader.py`, se a entrada `app_id` não se encontra na `ds.config`, `LogDownloader.py` utiliza o `$Default` cadeia de ligação.

## <a name="usage"></a>Utilização

Aceda a `mwt-ds/DataScience` e execute `LogDownloader.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parâmetros

| Input | Descrição | Predefinição |
| --- | --- | --- |
| `-h`, `--help` | Mostre a mensagem de ajuda e sair. | |
| `-a APP_ID`, `--app_id APP_ID` | O ID da aplicação (ou seja, o armazenamento do Azure blob nome do contentor). | Necessário |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | O diretório base para a transferência de dados (uma subpasta é criada).  | Necessário |
| `-s START_DATE`, `--start_date START_DATE` | O download data (incluída), além de início *DD-MM-AAAA* formato. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | A transferir data de fim (incluída), no *DD-MM-AAAA* formato. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | O modo de substituição a utilizar. | |
| | `0`: Nunca substituir; Peça ao utilizador se os blobs são utilizadas atualmente. | Predefinição |
| | `1`: Peça ao utilizador como proceder quando os ficheiros têm tamanhos diferentes ou os blobs estão atualmente a ser utilizados. | |
| | `2`: Substitui sempre; Transferir blobs usados no momento. | |
| | `3`: Nunca substituir e, se o tamanho é maior, sem pedir; de acréscimo Transferir blobs usados no momento. | |
| | `4`: Nunca substituir e, se o tamanho é maior, sem pedir; de acréscimo blobs de ignorar atualmente a ser utilizado. | |
| `--dry_run` | Imprimir os blobs seriam tiverem sido descarregadas, sem a transferir. | `False` |
| `--create_gzip` | Criar uma *gzip* ficheiro para o Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | A janela de tempo, em segundos, para detectar se um ficheiro está atualmente em utilização. | `3600` s (`1` hora) |
| `--verbose` | Imprima a obter mais detalhes. | `False` |
| `-v VERSION`, `--version VERSION` | A versão do downloader de registo para utilizar. | |
| | `1`: Para obter registos uncooked (apenas para compatibilidade com versões anteriores). | Preterido |
| | `2`: Para os registos de cooked. | Predefinição |

### <a name="examples"></a>Exemplos

Para uma execução completa de baixar todos os dados no contentor de blob de armazenamento do Azure, utilize o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Para transferir apenas os registos criados desde 1 de Janeiro de 2018 com `overwrite_mode=4`, utilize o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Para criar uma *gzip* mesclagem de todos os ficheiros transferidos de ficheiros, utilize o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
