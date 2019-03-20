---
title: Códigos de erro de serviços de multimédia do Azure | Documentos da Microsoft
description: O tópico apresenta uma visão geral dos códigos de erro de serviços de multimédia do Azure.
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
ms.openlocfilehash: f3c362730e7908e88b363659b7fa580b6f2cddf1
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189466"
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro de serviços de multimédia do Azure
Ao utilizar os serviços de multimédia do Microsoft Azure, poderá receber códigos de erro HTTP do serviço consoante problemas como tokens de autenticação expirar a ações que não são suportadas nos serviços de multimédia. Segue-se uma lista de **códigos de erro HTTP** que podem ser devolvidos por serviços de multimédia e as possíveis causas para eles.  

## <a name="400-bad-request"></a>400 pedido inválido
O pedido contém informação inválida e foi rejeitado devido a um dos seguintes motivos:

* Foi especificada uma versão de API não suportada. Para a versão mais recente, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).
* A versão de API dos serviços de multimédia não está especificada. Para obter informações sobre como especificar a versão de API, consulte [referência da API do REST do Media Services operações](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se estiver a utilizar os SDKs de Java ou .NET para se ligar aos Media Services, a versão de API é especificada por si sempre que tente e executa alguma ação em relação a serviços de multimédia.
  > 
  > 
* Foi especificada uma propriedade não definida. O nome da propriedade é na mensagem de erro. Somente as propriedades que são membros de uma determinada entidade podem ser especificadas. Ver [referência da API do REST do Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) para obter uma lista de entidades e suas propriedades.
* Foi especificado um valor de propriedade inválido. O nome da propriedade é na mensagem de erro. Consulte a hiperligação anterior para tipos de propriedade válido e os respetivos valores.
* Um valor de propriedade está em falta e é necessário.
* Parte da URL especificada contém um valor incorreto.
* Foi efetuada uma tentativa para atualizar uma propriedade de WriteOnce.
* Foi efetuada uma tentativa para criar uma tarefa que tenha um elemento de entrada com um AssetFile primário que não foi especificado ou não foi possível determinar.
* Foi efetuada uma tentativa para atualizar um localizador SAS. Localizadores SAS só podem ser criados ou eliminados. Os localizadores de transmissão em fluxo pode ser atualizado. Para obter mais informações, consulte [localizadores](https://docs.microsoft.com/rest/api/media/operations/locator).
* Uma consulta ou operação não suportada foi submetida.

## <a name="401-unauthorized"></a>401 não autorizado
O pedido não foi possível autenticar (antes de pode ser autorizado) devido a um dos seguintes motivos:

* Cabeçalho de autenticação em falta.
* Valor de cabeçalho de autenticação incorreta.
  * O token expirou. 
  * O token contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
O pedido não é permitido devido a um dos seguintes motivos:

* Conta de Media Services não é possível localizar ou foi eliminada.
* A conta de serviços de multimédia está desativada e o tipo de pedido não é HTTP GET. Operações de serviço irão devolver uma resposta 403 também.
* O token de autenticação não contém informações de credenciais do utilizador: AccountName e/ou SubscriptionId. Pode encontrar estas informações na extensão da interface do Usuário de serviços de suporte de dados para a sua conta de serviços de multimédia no Portal de gestão do Azure.
* Não é possível aceder ao recurso.
  
  * Foi efetuada uma tentativa para utilizar um MediaProcessor que não está disponível para a sua conta de Media Services.
  * Foi efetuada uma tentativa para atualizar um JobTemplate definido pelos serviços de multimédia.
  * Foi efetuada uma tentativa de substituir o localizador de alguns outros serviços de multimédia da conta.
  * Foi efetuada uma tentativa de substituir ContentKey alguns outros serviços de multimédia da conta.
* Não foi possível criar o recurso devido a uma quota de serviço que foi atingida para a conta de Media Services. Para obter mais informações sobre as quotas de serviço, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Não Encontrado
O pedido não é permitido num recurso devido a um dos seguintes motivos:

* Foi efetuada uma tentativa de atualizar uma entidade que não existe.
* Foi efetuada uma tentativa de eliminar uma entidade que não existe.
* Foi efetuada uma tentativa de criar uma entidade que ligue a uma entidade que não existe.
* Foi efetuada uma tentativa para obter uma entidade que não existe.
* Foi efetuada uma tentativa para especificar uma conta de armazenamento que não está associada a conta de Media Services.  

## <a name="409-conflict"></a>409 conflito
O pedido não é permitido devido a um dos seguintes motivos:

* AssetFile mais do que uma tem o nome especificado no elemento.
* Foi efetuada uma tentativa de criar uma segunda AssetFile principal dentro do elemento.
* Foi efetuada uma tentativa de criar um ContentKey com o Id especificado já utilizado.
* Foi efetuada uma tentativa para criar um localizador com o Id especificado já utilizado.
* IngestManifestFile mais do que uma tem o nome especificado no IngestManifest.
* Foi efetuada uma tentativa para ligar uma segunda encriptação de armazenamento ContentKey ao elemento de encriptação de armazenamento.
* Foi efetuada uma tentativa para ligar o mesmo ContentKey ao elemento.
* Foi efetuada uma tentativa para criar um localizador para um elemento cujo contentor de armazenamento está em falta ou não se encontra associado o elemento.
* Foi efetuada uma tentativa para criar um localizador para um elemento que já tem 5 localizadores em utilização. (O armazenamento do azure impõe o limite de cinco políticas de acesso partilhado num contentor de armazenamento).
* Conta de armazenamento de um ativo de ligação para um IngestManifestAsset não é o mesmo que a conta de armazenamento utilizada pelo IngestManifest pai.  

## <a name="500-internal-server-error"></a>500 Erro de Servidor Interno
Durante o processamento do pedido, dos serviços de multimédia encontra um erro que impede o processamento de continuar. Isto pode dever-se a um dos seguintes motivos:

* A criação de uma tarefa ou recurso falha porque as informações sobre a quota da conta de Media Services serviço está temporariamente indisponível.
* A criação de um contentor de armazenamento de BLOBs de ativo ou IngestManifest falha porque as informações de conta de armazenamento da conta estão temporariamente indisponíveis.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>503 Serviço Indisponível
O servidor está atualmente não é possível receber pedidos. Este erro pode ser causado por excesso pedidos para o serviço. Mecanismo de limitação de serviços de multimédia restringe a utilização de recursos para aplicativos que tornam o pedido excessivos para o serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres de código de erro para obter informações mais detalhadas sobre o motivo pelo qual obteve o erro 503. Este erro não significa sempre a limitação.
> 
> 

Descrições de estado possíveis são:

* "O servidor está ocupado. Execuções anteriores deste tipo de pedido demorou mais do que {0} segundos. "
* "O servidor está ocupado. Mais do que {0} pedidos por segundo, podem ser otimizados. "
* "O servidor está ocupado. Mais do que {0} solicita dentro {1} segundos podem ser otimizados. "

Para lidar com este erro, recomendamos que utilize a lógica de repetição de término exponencial. Isso significa que através de cada vez mais aguarda entre as repetições para respostas de erro consecutivos.  Para obter mais informações, consulte [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Se estiver a utilizar [SDK de serviços de multimédia do Azure para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 foi implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Consultar Também
[Códigos de erro de gestão dos serviços de multimédia](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

