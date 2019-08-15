---
title: Opções de transferência de dados para o Azure usando um dispositivo | Microsoft Docs
description: Saiba como escolher o dispositivo certo para transferir dados para o Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965348"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Comparar o StorSimple com as opções de transferência de dados Sincronização de Arquivos do Azure e Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Este documento fornece uma visão geral das opções de transferência de dados locais para o Azure, comparando: Data Box Edge vs. Sincronização de Arquivos do Azure vs. Série StorSimple 8000.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – data Box Edge é um dispositivo de rede local que move dados para dentro e para fora do Azure e tem a computação de borda habilitada para ia para pré-processar dados durante o carregamento. Gateway do Data Box é uma versão virtual do dispositivo com os mesmos recursos de transferência de dados.
- **[Sincronização de arquivos do Azure](/azure/storage/files/storage-sync-files-deployment-guide)** – sincronização de arquivos do Azure pode ser usado para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. Sincronização de Arquivos do Azure transforma o Windows Server em um cache rápido de seu compartilhamento de arquivos do Azure. A disponibilidade geral do Sincronização de Arquivos do Azure foi anunciada anteriormente em 2018.
- **[Storsimple](/azure/storsimple/storsimple-overview)** – o storsimple é um dispositivo híbrido que ajuda as empresas a consolidarem sua infraestrutura de armazenamento para armazenamento primário, proteção de dados, arquivamento e recuperação de desastre em uma única solução, integrando totalmente com o armazenamento do Azure. O ciclo de vida do produto para StorSimple pode ser encontrado [aqui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Resumo de comparação

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Descrição geral         |Armazenamento híbrido em camadas e arquivamento|Armazenamento geral do servidor de arquivos com camadas de nuvem e sincronização de vários sites.  |Solução de armazenamento para pré-processar dados e enviá-los pela rede para o Azure.        |
|Cenários        |Servidor de arquivos, arquivamento, destino de backup |Servidor de arquivos, arquivamento (multissite)   |Transferência de dados, pré-processamento de dados, incluindo ML inferência, IoT, arquivamento    |
|Computação Edge     |Indisponível |Indisponível |Dá suporte a contêineres em execução usando Azure IoT Edge    |
|Fator forma      |Dispositivo físico   |Agente instalado no Windows Server |Dispositivo físico   |
|Hardware         |Dispositivo físico fornecido da Microsoft como parte do serviço | Fornecido pelo cliente |Dispositivo físico fornecido da Microsoft como parte do serviço  |
|Formato de dados      |Formato personalizado   |Ficheiros         |BLOBs ou arquivos    |
|Suporte de protocolo |iSCSI          |SMB, NFS    | SMB ou NFS      |
|Preços          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Sincronização de Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Azure data Box Edge](/azure/databox-online/data-box-edge-overview) e [gateway do Azure data Box](/azure/databox-online/data-box-gateway-overview)
- Saiba mais sobre o [sincronização de arquivos do Azure](/azure/storage/files/storage-sync-files-deployment-guide)
