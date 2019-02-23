---
title: Exemplos da CLI do Azure - serviços de multimédia do Azure | Documentos da Microsoft
description: Exemplos da CLI do Azure para o serviço de serviços de multimédia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: bbd57933993e22dd32b84f1d44175bb3b3d749c9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672431"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemplos da CLI do Azure para serviços de multimédia do Azure

A tabela seguinte inclui ligações para os exemplos da CLI do Azure para serviços de multimédia do Azure.

## <a name="examples"></a>Exemplos

|  |  |
|---|---|
|**Dimensionamento**||
| [Unidades reservadas de multimédia de dimensionamento](media-reserved-units-cli-how-to.md)|Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 MRUs de S3. <br/>O script mostra como utilizar a CLI para dimensionar unidades reservadas de multimédia (MRUs).|
|**Conta**||
| [Criar uma conta de serviços de multimédia](create-account-cli-how-to.md) | Cria uma conta de Media Services do Azure. Além disso, cria um serviço principal que podem ser utilizados para aceder a APIs para gerir a conta de forma programática. |
| [Repor as credenciais da conta](./scripts/cli-reset-account-credentials.md)|Repõe as credenciais da conta e recebe de volta as definições de App. config.|
|**Ativos**||
| [Criar recursos](./scripts/cli-create-asset.md)|Cria um recurso de serviços de multimédia para carregar conteúdo para.|
| [Carregar um ficheiro](./scripts/cli-upload-file-asset.md)|Carrega um ficheiro local para um contentor de armazenamento.|
| **Transforma** e **tarefas**||
| [Criar transformações](./scripts/cli-create-transform.md)|Mostra como criar transformações. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita").<br/> Deve sempre verificar se já existe uma Transformação com o nome e a "receita" pretendidos. Se assim for, reutilizá-lo. |
| [Criar tarefas](./scripts/cli-create-jobs.md)|Submete uma tarefa para uma transformação de codificação simples usando a HTTPs URL.|
| [Criar EventGrid](./scripts/cli-create-event-grid.md)|Cria uma subscrição de Event Grid de nível de conta para alterações de estado de tarefa.|
| **Deliver**||
| [Publicar um elemento](./scripts/cli-publish-asset.md)| Cria um localizador de transmissão em fluxo e recebe de volta URLs de transmissão em fluxo. |
| [Filtro](filters-dynamic-manifest-cli-howto.md)| Configura um filtro para um recurso do vídeo a pedido e mostra como utilizar a CLI para criar [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Asset filtros](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Consulte também

- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Quickstart: Ficheiros de vídeo do Stream - CLI](stream-files-cli-quickstart.md)
