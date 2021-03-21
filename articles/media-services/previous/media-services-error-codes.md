---
title: Códigos de erro da Azure Media Services | Microsoft Docs
description: Pode receber códigos de erro HTTP do serviço dependendo de questões como tokens de autenticação que expirem a ações que não são suportadas nos Serviços de Comunicação Social. Este artigo apresenta uma visão geral dos códigos de erro da Azure Media Services v2 API.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013419"
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro dos Serviços de Multimédia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ao utilizar o Microsoft Azure Media Services, poderá receber códigos de erro HTTP do serviço dependendo de problemas como tokens de autenticação que expirem a ações que não são suportadas nos Serviços de Media. Segue-se uma lista de códigos de erro HTTP que podem ser **devolvidos** pelos Media Services e as possíveis causas para os mesmos.  

## <a name="400-bad-request"></a>400 Pedido Incorreto
O pedido contém informações inválidas e é rejeitado devido a uma das seguintes razões:

* Uma versão API não suportada é especificada. Para a versão mais atual, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md).
* A versão API dos Serviços de Comunicação social não é especificada. Para obter informações sobre como especificar a versão API, consulte [As Operações de Serviços de Mídia REST Referência API](/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se estiver a utilizar os SDKs .NET ou Java para ligar aos Serviços de Mídia, a versão API é especificada para si sempre que tentar realizar alguma ação contra os Serviços de Media.
  > 
  > 
* Uma propriedade indefinida foi especificada. O nome da propriedade está na mensagem de erro. Apenas as propriedades que são membros de uma determinada entidade podem ser especificadas. Consulte [a Azure Media Services API Referência](/rest/api/media/operations/azure-media-services-rest-api-reference) para uma lista de entidades e seus imóveis.
* Foi especificado um valor de propriedade inválido. O nome da propriedade está na mensagem de erro. Consulte o link anterior para os tipos de propriedade válidos e seus valores.
* Falta um valor patrimonial e é necessário.
* Parte do URL especificado contém um mau valor.
* Foi feita uma tentativa de atualizar uma propriedade da WriteOnce.
* Foi feita uma tentativa de criar um Job que tenha um Ativo de entrada com um AssetFile primário que não foi especificado ou não pôde ser determinado.
* Foi feita uma tentativa de atualizar um localizador SAS. Os localizadores SAS só podem ser criados ou eliminados. Os localizadores de streaming podem ser atualizados. Para mais informações, consulte [localizadores.](/rest/api/media/operations/locator)
* Foi submetida uma operação ou consulta não apoiada.

## <a name="401-unauthorized"></a>401 Não Autorizado
O pedido não pôde ser autenticado (antes de poder ser autorizado) devido a uma das seguintes razões:

* Cabeçalho de autenticação em falta.
* Mau valor do cabeçalho de autenticação.
  * O símbolo expirou. 
  * O símbolo contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
O pedido não é permitido devido a uma das seguintes razões:

* A conta dos Serviços de Comunicação Social não pode ser encontrada ou foi eliminada.
* A conta serviços de comunicação está desativada e o tipo de pedido não é HTTP GET. As operações de serviço também vão devolver uma resposta 403.
* O token de autenticação não contém as informações credenciais do utilizador: Nome de conta e/ou SubscriçãoId. Pode encontrar esta informação na extensão do UI dos Serviços de Comunicação Social para a sua conta de Serviços de Comunicação social no Portal de Gestão Azure.
* O recurso não pode ser acedido.
  
  * Foi feita uma tentativa de utilizar um MediaProcessor que não está disponível para a sua conta de Media Services.
  * Foi feita uma tentativa de atualizar um JobTemplate definido pelos Media Services.
  * Foi feita uma tentativa de substituir o localizador de outra conta dos Media Services.
  * Foi feita uma tentativa de substituir o ContentKey de outra conta dos Media Services.
* O recurso não pôde ser criado devido a uma quota de serviço que foi alcançada para a conta dos Serviços de Comunicação Social. Para obter mais informações sobre as quotas de serviço, consulte [Quotas e Limitações.](media-services-quotas-and-limitations.md)

## <a name="404-not-found"></a>404 Não Encontrado
O pedido não é permitido num recurso devido a uma das seguintes razões:

* Foi feita uma tentativa de atualizar uma entidade que não existe.
* Foi feita uma tentativa de apagar uma entidade que não existe.
* Foi feita uma tentativa de criar uma entidade que se liga a uma entidade que não existe.
* Foi feita uma tentativa de conseguir uma entidade que não existe.
* Foi feita uma tentativa de especificar uma conta de armazenamento que não está associada à conta dos Serviços de Comunicação Social.  

## <a name="409-conflict"></a>409 Conflito
O pedido não é permitido devido a uma das seguintes razões:

* Mais de um AssetFile tem o nome especificado dentro do Ativo.
* Foi feita uma tentativa de criar um segundo ActivoFile primário dentro do Ativo.
* Foi feita uma tentativa de criar um ContentKey com o ID especificado já utilizado.
* Foi feita uma tentativa de criar um Localizador com o ID especificado já utilizado.
* Mais de um IngestManifestFile tem o nome especificado dentro do IngestManifest.
* Foi feita uma tentativa de ligar um segundo contentKey de encriptação de armazenamento ao Ativo encriptado de armazenamento.
* Foi feita uma tentativa de ligar o mesmo ContentKey ao Ativo.
* Foi feita uma tentativa de criar um localizador para um Ativo cujo contentor de armazenamento está em falta ou já não está associado ao Ativo.
* Foi feita uma tentativa de criar um localizador para um Ativo que já tem 5 localizadores em uso. (O Azure Storage impõe o limite de cinco políticas de acesso partilhado num recipiente de armazenamento.)
* Ligar a conta de armazenamento de um Ativo a um IngestManifestAsset não é a mesma que a conta de armazenamento utilizada pelo progenitor IngestManifest.  

## <a name="500-internal-server-error"></a>Erro Interno do Servidor 500
Durante o processamento do pedido, os Serviços de Comunicação encontram algum erro que impede que o processamento continue. Isto pode dever-se a um dos seguintes motivos:

* A criação de um Ativo ou Job falha porque a informação de quota de serviço da conta de Media Services está temporariamente indisponível.
* A criação de um recipiente de armazenamento de bolhas Asset ou IngestManifest falha porque a informação da conta de armazenamento da conta está temporariamente indisponível.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>503 Serviço Indisponível
O servidor não consegue receber pedidos. Este erro pode ser causado por excesso de pedidos ao serviço. O mecanismo de limitação dos Serviços de Multimédia restringe a utilização de recursos para aplicações que fazem pedidos em excesso ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de código do erro para obter informações mais detalhadas sobre o motivo pelo qual obteve o erro 503. Este erro nem sempre significa limitação.
> 
> 

As possíveis descrições do estado são:

* "O servidor está ocupado. As execuções anteriores deste tipo de pedido demoraram mais de {0} segundos."
* "O servidor está ocupado. Mais do que {0} pedidos por segundo pode ser estrangulado.
* "O servidor está ocupado. Mais do que {0} pedidos em {1} segundos podem ser acelerados."

Para lidar com este erro, recomendamos a utilização de uma lógica de retrocesso exponencial. Isto significa que a utilização de esperas progressivamente mais longas entre as retrações para respostas de erro consecutivas.  Para obter mais informações, consulte [o Bloco de Aplicação de Tratamento de Falhas Transitórias](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> Se estiver a utilizar [o Azure Media Services SDK para .Net,](https://github.com/Azure/azure-sdk-for-media-services/tree/master)a lógica de repetição do erro 503 foi implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Consulte também
[Códigos de erro de gestão de serviços de mídia](/rest/api/media/)

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
