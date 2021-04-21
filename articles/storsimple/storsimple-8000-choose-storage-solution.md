---
title: Opções de transferência de dados para a Azure utilizando um aparelho | Microsoft Docs
description: Saiba como escolher o aparelho certo para transferência de dados no local para Azure entre data box edge, Azure File Sync e série StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791183"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Comparar o StorSimple com as opções de transferência de dados do Azure File Sync e do Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Este documento fornece uma visão geral das opções para transferência de dados no local para a Azure, comparando: Data Box Edge vs. Azure File Sync vs. StorSimple série 8000.

- **[Data Box Edge](../databox-online/azure-stack-edge-overview.md)** – Data Box Edge é um dispositivo de rede no local que move dados para dentro e para fora do Azure e tem um computamento edge ativado pela IA para pré-processar dados durante o upload. O Data Box Gateway é uma versão virtual do dispositivo com as mesmas capacidades de transferência de dados.
- **[Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)** – Azure File Sync pode ser usado para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. A disponibilidade geral do Azure File Sync foi anunciada no início de 2018.
- **[A StorSimple](./storsimple-overview.md)** – StorSimple é um dispositivo híbrido que ajuda as empresas a consolidarem a sua infraestrutura de armazenamento para armazenamento primário, proteção de dados, arquivamento e recuperação de desastres numa única solução, integrando-se firmemente com o armazenamento Azure. O ciclo de vida do produto para storSimple pode ser consultado [aqui.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)

## <a name="comparison-summary"></a>Resumo da comparação

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Descrição geral**     |Armazenamento híbrido hierárquico e arquivo|Armazenamento geral de servidor de ficheiros com tiering de nuvem e sincronização de vários locais.  |Solução de armazenamento para pré-processar dados e enviá-lo em rede para Azure.        |
|**Cenários**    |Servidor de ficheiros, arquivo, alvo de backup |Servidor de ficheiros, arquivo (multi-site)   |Transferência de dados, pré-processamento de dados, incluindo inferição de ML, IoT, arquivo    |
|**Cálculo de borda** |Não disponível |Não disponível |Suporta recipientes de funcionamento usando Azure IoT Edge    |
|**Fator de forma**  |Dispositivo físico   |Agente instalado no Windows Server |Dispositivo físico   |
|**Hardware**     |Dispositivo físico fornecido pela Microsoft como parte do serviço | Cliente fornecido |Dispositivo físico fornecido pela Microsoft como parte do serviço  |
|**Formato de dados**  |Formato personalizado   |Ficheiros         |Blobs ou Ficheiros    |
|**Suporte de protocolo** |iSCSI          |SMB, NFS    | SMB ou NFS      |
|**Preços**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) e [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Saiba mais sobre [o Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)