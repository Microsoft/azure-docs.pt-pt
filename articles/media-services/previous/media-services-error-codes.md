---
title: Códigos de erro da Azure Media Services Microsoft Docs
description: Pode receber códigos de erro HTTP do serviço dependendo de questões como fichas de autenticação que expirem em ações que não sejam suportadas nos Serviços de Media. Este artigo dá uma visão geral dos códigos de erro da Azure Media Services v2 API.
author: Juliako
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74887073"
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro dos Serviços De Mídia Azure
Ao utilizar o Microsoft Azure Media Services, poderá receber códigos de erro HTTP do serviço, dependendo de questões como fichas de autenticação que expirem em ações que não sejam suportadas nos Serviços de Media. Segue-se uma lista de **códigos** de erro HTTP que podem ser devolvidos pelos Serviços de Media e as possíveis causas para os mesmos.  

## <a name="400-bad-request"></a>400 Mau Pedido
O pedido contém informações inválidas e é rejeitado devido a uma das seguintes razões:

* É especificada uma versão API não suportada. Para a versão mais atual, consulte [Configuração para Media Services REST API Development](media-services-rest-how-to-use.md).
* A versão API dos Serviços de Media não está especificada. Para obter informações sobre como especificar a versão API, consulte [Media Services Operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se estiver a utilizar os SDKs .NET ou Java para se ligar aos Media Services, a versão API é especificada para si sempre que tenta realizar alguma ação contra os Serviços de Media.
  > 
  > 
* Uma propriedade indefinida foi especificada. O nome da propriedade está na mensagem de erro. Só podem ser especificados os imóveis que são membros de uma determinada entidade. Consulte [o Azure Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) para uma lista de entidades e seus imóveis.
* Foi especificado um valor de propriedade inválido. O nome da propriedade está na mensagem de erro. Consulte o link anterior para tipos de propriedade válidos e seus valores.
* Falta um valor de propriedade e é necessário.
* Parte do URL especificado contém um mau valor.
* Foi feita uma tentativa de atualizar uma propriedade writeOnce.
* Foi feita uma tentativa de criar um Job que tenha um Ativo de entrada com um AssetFile primário que não foi especificado ou não poderia ser determinado.
* Foi feita uma tentativa de atualizar um Localizador SAS. Os localizadores SAS só podem ser criados ou eliminados. Os localizadores de streaming podem ser atualizados. Para mais informações, consulte [localizadores.](https://docs.microsoft.com/rest/api/media/operations/locator)
* Foi submetida uma operação ou consulta não apoiada.

## <a name="401-unauthorized"></a>401 Não autorizado
O pedido não pôde ser autenticado (antes de poder ser autorizado) devido a uma das seguintes razões:

* Cabeçalho de autenticação em falta.
* Valor do cabeçalho de autenticação má.
  * O símbolo expirou. 
  * O símbolo contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
O pedido não é permitido devido a uma das seguintes razões:

* A conta de Serviços de Media não pode ser encontrada ou foi eliminada.
* A conta media services está desativada e o tipo de pedido não é HTTP GET. As operações de serviço também devolverão uma resposta 403.
* O símbolo de autenticação não contém as informações credenciais do utilizador: Nome de conta e/ou SubscriçãoId. Pode encontrar esta informação na extensão da UI dos Serviços de Media para a sua conta de Media Services no Portal de Gestão do Azure.
* O recurso não pode ser acedido.
  
  * Foi feita uma tentativa de utilização de um MediaProcessor que não está disponível para a sua conta de Media Services.
  * Foi feita uma tentativa de atualizar um JobTemplate definido pelos Media Services.
  * Foi feita uma tentativa de substituir o Localizador de outra conta de media.
  * Foi feita uma tentativa de substituir o ContentKey de outras contas de Media Services.
* O recurso não pôde ser criado devido a uma quota de serviço que foi atingida para a conta de Serviços de Media. Para obter mais informações sobre as quotas de serviço, consulte [Quotas e Limitações.](media-services-quotas-and-limitations.md)

## <a name="404-not-found"></a>404 Não Encontrado
O pedido não é permitido num recurso devido a uma das seguintes razões:

* Foi feita uma tentativa de atualizar uma entidade que não existe.
* Foi feita uma tentativa de apagar uma entidade que não existe.
* Foi feita uma tentativa de criar uma entidade que se liga a uma entidade que não existe.
* Foi feita uma tentativa de obter uma entidade que não existe.
* Foi feita uma tentativa de especificar uma conta de armazenamento que não está associada à conta dos Serviços de Media.  

## <a name="409-conflict"></a>409 Conflito
O pedido não é permitido devido a uma das seguintes razões:

* Mais de um AssetFile tem o nome especificado dentro do Ativo.
* Foi feita uma tentativa de criar um segundo AssetFile primário dentro do Ativo.
* Foi feita uma tentativa de criar um ContentKey com o id especificado já utilizado.
* Foi feita uma tentativa de criar um Localizador com o id especificado já utilizado.
* Mais de um IngestManifestFile tem o nome especificado dentro do IngestManifest.
* Foi feita uma tentativa de ligar uma segunda encriptação de armazenamento ContentKey ao Ativo encriptado de armazenamento.
* Foi feita uma tentativa de ligar o mesmo ContentKey ao Ativo.
* Foi feita uma tentativa de criar um localizador para um Ativo cujo contentor de armazenamento está desaparecido ou já não está associado ao Ativo.
* Foi feita uma tentativa de criar um localizador para um Ativo que já tem 5 localizadores em uso. (O Armazenamento Azure impõe o limite de cinco políticas de acesso partilhado num contentor de armazenamento.)
* Ligar a conta de armazenamento de um Ativo a um IngestManifestAsset não é o mesmo que a conta de armazenamento utilizada pelo progenitor IngestManifest.  

## <a name="500-internal-server-error"></a>500 Erro de Servidor Interno
Durante o processamento do pedido, a Media Services encontra algum erro que impede que o processamento continue. Isto pode dever-se a um dos seguintes motivos:

* A criação de um Ativo ou Trabalho falha porque a informação de quota de serviço da conta Media Services está temporariamente indisponível.
* A criação de um contentor de armazenamento de bolhas Asset ou IngestManifest falha porque as informações da conta de armazenamento da conta estão temporariamente indisponíveis.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>503 Serviço Indisponível
O servidor não está atualmente em modo de receber pedidos. Este erro pode ser causado por pedidos excessivos ao serviço. O mecanismo de estrangulamento dos Serviços de Media restringe o uso de recursos para aplicações que fazem um pedido excessivo ao serviço.

> [!NOTE]
> Verifique a linha de erro e código de erro para obter informações mais detalhadas sobre a razão pela qual obteve o erro 503. Este erro nem sempre significa estrangulamento.
> 
> 

As possíveis descrições do estado são:

* "O servidor está ocupado. As anteriores corridas deste {0} tipo de pedido demoraram mais do que segundos."
* "O servidor está ocupado. Mais {0} do que pedidos por segundo pode ser estrangulado.
* "O servidor está ocupado. Mais {0} do que {1} pedidos em segundos podem ser estrangulados.

Para lidar com este erro, recomendamos a utilização de uma lógica exponencial de retry back-off. Isto significa utilizar esperas progressivamente mais longas entre as tentativas de respostas de erro consecutivas.  Para mais informações, consulte o Bloco transitório de aplicação de [manuseamento](https://msdn.microsoft.com/library/hh680905.aspx)de falhas .

> [!NOTE]
> Se estiver a utilizar o [Azure Media Services SDK para .Net,](https://github.com/Azure/azure-sdk-for-media-services/tree/master)a lógica de retry para o erro 503 foi implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Veja também
[Códigos de erro de gestão de serviços de mídia](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

