---
title: LogDownloader-Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Baixe os arquivos de log produzidos pelo Serviço de Decisão Personalizada do Azure.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707233"
---
# <a name="logdownloader"></a>LogDownloader

Baixe os arquivos de log produzidos pelo Serviço de Decisão Personalizada do Azure e gere os arquivos *. gz* que são usados pela experimentação.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3: Instalado e em seu caminho. Recomendamos a versão de 64 bits para lidar com arquivos grandes.
- O repositório *Microsoft/MWT-DS* : [Clone o repositório](https://github.com/Microsoft/mwt-ds).
- O pacote *Azure-Storage-blob* : Para obter detalhes da instalação, vá para [armazenamento do Microsoft Azure biblioteca para Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Insira sua cadeia de conexão de armazenamento do Azure em *MWT-DS/dataciência/DS. config*: Siga o modelo *my_app_id: my_connectionString* . Você pode especificar vários `app_id`. Quando você executar `LogDownloader.py`, se a entrada `app_id` não for encontrada em `ds.config`, `LogDownloader.py` o usará a cadeia de `$Default` conexão.

## <a name="usage"></a>Utilização

Vá para `mwt-ds/DataScience` e execute `LogDownloader.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parâmetros

| Input | Descrição | Predefinição |
| --- | --- | --- |
| `-h`, `--help` | Mostrar a mensagem de ajuda e sair. | |
| `-a APP_ID`, `--app_id APP_ID` | A ID do aplicativo (ou seja, o nome do contêiner de blob do armazenamento do Azure). | Requerido |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | O diretório base para baixar dados (uma subpasta é criada).  | Requerido |
| `-s START_DATE`, `--start_date START_DATE` | A data de início do download (incluída), no formato *aaaa-mm-dd* . | `None` |
| `-e END_DATE`, `--end_date END_DATE` | A data de término do download (incluída), no formato *aaaa-mm-dd* . | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | O modo de substituição a ser usado. | |
| | `0`: Nunca substituir; Pergunte ao usuário se os BLOBs estão sendo usados no momento. | Predefinição |
| | `1`: Pergunte ao usuário como proceder quando os arquivos têm tamanhos diferentes ou quando os BLOBs estão sendo usados no momento. | |
| | `2`: Sempre substituir; baixar BLOBs usados no momento. | |
| | `3`: Nunca substituir e acrescentar se o tamanho for maior, sem perguntar; baixar BLOBs usados no momento. | |
| | `4`: Nunca substituir e acrescentar se o tamanho for maior, sem perguntar; ignorar BLOBs usados no momento. | |
| `--dry_run` | Imprima Quais blobs teriam sido baixados, sem baixar. | `False` |
| `--create_gzip` | Crie um arquivo *gzip* para Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | A janela de tempo, em segundos, para detectar se um arquivo está em uso no momento. | `3600`s (`1` hora) |
| `--verbose` | Imprima mais detalhes. | `False` |
| `-v VERSION`, `--version VERSION` | A versão do downloader de log a ser usada. | |
| | `1`: Para logs não-encookie (somente para compatibilidade com versões anteriores). | Preterido |
| | `2`: Para logs do cooked. | Predefinição |

### <a name="examples"></a>Exemplos

Para uma simulação de download de todos os dados em seu contêiner de blob do armazenamento do Azure, use o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Para baixar somente os logs criados desde 1º de janeiro de `overwrite_mode=4`2018 com o, use o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Para criar um arquivo *gzip* mesclando todos os arquivos baixados, use o seguinte código:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
