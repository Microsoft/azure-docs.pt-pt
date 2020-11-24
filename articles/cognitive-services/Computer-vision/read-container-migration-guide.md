---
title: Migrar para os recipientes de OCR de Leitura v3.x
titleSuffix: Azure Cognitive Services
description: Saiba como migrar para os recipientes V3 Read OCR
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: aahi
ms.openlocfilehash: 0ae7bb9acde3cc8552a23db12d208a82ba0fb2f3
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95800866"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrar para os recipientes de OCR de Leitura v3.x

Se estiver a utilizar a versão 2 do recipiente OCR de Leitura de Visão de Computador, utilize este artigo para saber como atualizar a sua aplicação para utilizar a versão 3.x do recipiente. 


## <a name="configuration-changes"></a>Alterações de configuração

* `ReadEngineConfig:ResultExpirationPeriod` já não é apoiado. O recipiente Read tem um trabalho cron construído que remove os resultados e metadados associados a um pedido após 48 horas.
* `Cache:Redis:Configuration` já não é apoiado. O Cache não é utilizado nos recipientes v3.x, pelo que não é necessário defini-lo.

## <a name="api-changes"></a>Alterações na API

O recipiente Read v3.2 utiliza a versão 3 da API de Visão Computacional e tem os seguintes pontos finais:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Consulte o [guia de migração da API da API com Visão De Computador v3](./upgrade-api-versions.md) PARA obter informações detalhadas sobre a atualização das suas aplicações para utilizar a versão 3 da API de leitura baseada na nuvem. Esta informação aplica-se também ao contentor. Note que as operações de sincronização só são suportadas em contentores.

## <a name="memory-requirements"></a>Requisitos de memória

Os requisitos e recomendações baseiam-se em parâmetros de referência com um único pedido por segundo, utilizando uma imagem de 8-MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres. O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente De leitura.

|Contentor  |Mínimo | Recomendado  |
|---------|---------|------|
|Ler 3.2 pré-visualização | 8 núcleos, memória de 16 GB         | 8 núcleos, memória de 24 GB |

Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do comando de execução do estivador.

## <a name="storage-implementations"></a>Implementações de armazenamento

>[!NOTE]
> O MongoDB já não é suportado em versões 3.x do recipiente. Em vez disso, os contentores suportam sistemas de armazenamento Azure e sistemas de ficheiros offline.

| Implementação |    Argumentos de execução necessários |
|---------|---------|
|Nível de ficheiro (padrão)    | Não são necessários argumentos de tempo de execução. `/share` diretório será usado. |
|Blob do Azure    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementações de filas

Em v3.x do recipiente, o RabbitMQ não está atualmente suportado. As implementações apoiadas são:

| Implementação | Argumentos de tempo de execução | Utilização pretendida |
|---------|---------|-------|
| Na Memória (padrão) | Não são necessários argumentos de tempo de execução. | Desenvolvimento e teste |
| Filas do Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produção |
| RabbitMQ    | Indisponível | Produção |

Para uma redundância adicional, o recipiente Read v3.x utiliza um temporizador de visibilidade para garantir que os pedidos podem ser processados com sucesso em caso de colisão, quando funciona numa configuração de vários contentores. 

Ajuste o temporizador com `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` , o que define o tempo para uma mensagem ser invisível quando outro trabalhador está a processá-lo. Para evitar que as páginas sejam processadas de forma redundante, recomendamos que se ajuste o período de tempo limite para 120 segundos. O valor predefinido é de 30 segundos.

| Valor predefinido | Valor recomendado |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Passos seguintes

* [Rever recipientes de configuração](computer-vision-resource-container-config.md) para configurações de configuração
* Reveja [a visão geral da Visão computacional](overview.md) para saber mais sobre o reconhecimento de texto impresso e manuscrito
* Consulte a [API de Visão De Computador](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter mais informações sobre os métodos suportados pelo recipiente.
* Consulte [perguntas frequentes (FAQ)](FAQ.md) para resolver problemas relacionados com a funcionalidade de Visão de Computador.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)