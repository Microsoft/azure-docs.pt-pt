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
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840635"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemplos da CLI do Azure para serviços de multimédia do Azure

A tabela seguinte inclui ligações para os exemplos da CLI do Azure para serviços de multimédia do Azure.

## <a name="examples"></a>Exemplos

|  |  |
|---|---|
|**Dimensionamento**||
| [Unidades reservadas de multimédia de dimensionamento](media-reserved-units-cli-how-to.md)|Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 MRUs de S3. <br/>O script mostra como utilizar a CLI para dimensionar unidades reservadas de multimédia (MRUs).|
|**Conta**||
| [Criar uma conta de serviços de multimédia](create-account-cli-how-to.md) | O script cria uma conta de Media Services do Azure. |
| [Repor as credenciais da conta](./scripts/cli-reset-account-credentials.md)|Repõe as credenciais da conta e recebe de volta as definições de App. config.|
|**Ativos**||
| [Criar recursos](./scripts/cli-create-asset.md)|Cria um recurso de serviços de multimédia para carregar conteúdo para.|
| [Carregar um ficheiro](./scripts/cli-upload-file-asset.md)|Carrega um ficheiro local para um contentor de armazenamento.|
| **Transforma** e **tarefas**||
| [Criar transformações](./scripts/cli-create-transform.md)|Mostra como criar transformações. As transformações descrevem um fluxo de trabalho de tarefas simples para processar os ficheiros de vídeo ou áudio (normalmente designados como "receita").<br/> Sempre deve verificar se uma transformação com um nome desejado e "receita" já existe. Se assim for, reutilizá-lo. |
| [Codificar com uma transformação personalizados](custom-preset-cli-howto.md) | Mostra como criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.|
| [Criar tarefas](./scripts/cli-create-jobs.md)|Submete uma tarefa para uma transformação de codificação simples usando a HTTPs URL.|
| [Criar EventGrid](./scripts/cli-create-event-grid.md)|Cria uma subscrição de Event Grid de nível de conta para alterações de estado de tarefa.|
| **Deliver**||
| [Publicar um elemento](./scripts/cli-publish-asset.md)| Cria um localizador de transmissão em fluxo e recebe de volta URLs de transmissão em fluxo. |
| [Filtro](filters-dynamic-manifest-cli-howto.md)| Configura um filtro para um recurso do vídeo a pedido e mostra como utilizar a CLI para criar [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Asset filtros](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Consulte também

- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Quickstart: Ficheiros de vídeo do Stream - CLI](stream-files-cli-quickstart.md)
