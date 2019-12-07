---
title: Códigos de erro dos serviços de mídia do Azure | Microsoft Docs
description: Você pode receber códigos de erro HTTP do serviço dependendo de problemas como tokens de autenticação expirando para ações que não têm suporte nos serviços de mídia. Este artigo fornece uma visão geral dos códigos de erro da API do Azure Media Services v2.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887073"
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro dos serviços de mídia do Azure
Ao usar Serviços de Mídia do Microsoft Azure, você pode receber códigos de erro HTTP do serviço, dependendo de problemas como tokens de autenticação expirando para ações que não têm suporte nos serviços de mídia. A seguir está uma lista de **códigos de erro http** que podem ser retornados pelos serviços de mídia e as possíveis causas para eles.  

## <a name="400-bad-request"></a>400 solicitação inadequada
A solicitação contém informações inválidas e foi rejeitada por um dos seguintes motivos:

* Uma versão de API sem suporte foi especificada. Para obter a versão mais atual, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).
* A versão de API dos serviços de mídia não foi especificada. Para obter informações sobre como especificar a versão da API, consulte [referência da API REST de operações dos serviços de mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se você estiver usando os SDKs .NET ou Java para se conectar aos serviços de mídia, a versão da API será especificada sempre que você tentar e executar alguma ação nos serviços de mídia.
  > 
  > 
* Uma propriedade indefinida foi especificada. O nome da propriedade está na mensagem de erro. Somente as propriedades que são membros de uma determinada entidade podem ser especificadas. Consulte [API REST de serviços de mídia do Azure referência](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) para obter uma lista de entidades e suas propriedades.
* Um valor de propriedade inválido foi especificado. O nome da propriedade está na mensagem de erro. Consulte o link anterior para obter os tipos de propriedade válidos e seus valores.
* Um valor de propriedade está ausente e é necessário.
* Parte da URL especificada contém um valor inadequado.
* Foi feita uma tentativa de atualizar uma propriedade WriteOnce.
* Foi feita uma tentativa de criar um trabalho que tem um ativo de entrada com um Assetfile primário que não foi especificado ou não pôde ser determinado.
* Foi feita uma tentativa de atualizar um localizador SAS. Os localizadores SAS só podem ser criados ou excluídos. Os localizadores de streaming podem ser atualizados. Para obter mais informações, consulte [localizadores](https://docs.microsoft.com/rest/api/media/operations/locator).
* Uma operação ou consulta sem suporte foi enviada.

## <a name="401-unauthorized"></a>401 não autorizado
Não foi possível autenticar a solicitação (antes que ela possa ser autorizada) devido a um dos seguintes motivos:

* Cabeçalho de autenticação ausente.
* Valor de cabeçalho de autenticação inadequado.
  * O token expirou. 
  * O token contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
A solicitação não é permitida devido a um dos seguintes motivos:

* A conta dos serviços de mídia não pode ser encontrada ou foi excluída.
* A conta dos serviços de mídia está desabilitada e o tipo de solicitação não é HTTP GET. As operações de serviço também retornarão uma resposta 403.
* O token de autenticação não contém as informações de credencial do usuário: AccountName e/ou SubscriptionId. Você pode encontrar essas informações na extensão da interface do usuário dos serviços de mídia para sua conta dos serviços de mídia na Portal de Gerenciamento do Azure.
* O recurso não pode ser acessado.
  
  * Foi feita uma tentativa de usar um MediaProcessor que não está disponível para sua conta de serviços de mídia.
  * Foi feita uma tentativa de atualizar um JobTemplate definido pelos serviços de mídia.
  * Foi feita uma tentativa de substituir algum outro localizador da conta de serviços de mídia.
  * Foi feita uma tentativa de substituir alguns outros ContentKey da conta dos serviços de mídia.
* Não foi possível criar o recurso devido a uma cota de serviço que foi atingida para a conta dos serviços de mídia. Para obter mais informações sobre as cotas de serviço, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Não Encontrado
A solicitação não é permitida em um recurso devido a um dos seguintes motivos:

* Foi feita uma tentativa de atualizar uma entidade que não existe.
* Foi feita uma tentativa de excluir uma entidade que não existe.
* Foi feita uma tentativa de criar uma entidade que se vincula a uma entidade que não existe.
* Foi feita uma tentativa de obter uma entidade que não existe.
* Foi feita uma tentativa de especificar uma conta de armazenamento que não está associada à conta dos serviços de mídia.  

## <a name="409-conflict"></a>409 conflito
A solicitação não é permitida devido a um dos seguintes motivos:

* Mais de um Assetfile tem o nome especificado no ativo.
* Foi feita uma tentativa de criar um segundo Assetfile primário no ativo.
* Foi feita uma tentativa de criar um ContentKey com a ID especificada já usada.
* Foi feita uma tentativa de criar um localizador com a ID especificada já usada.
* Mais de um IngestManifestFile tem o nome especificado dentro do IngestManifest.
* Foi feita uma tentativa de vincular um segundo ContentKey de criptografia de armazenamento ao ativo criptografado pelo armazenamento.
* Foi feita uma tentativa de vincular o mesmo ContentKey ao ativo.
* Foi feita uma tentativa de criar um localizador para um ativo cujo contêiner de armazenamento está ausente ou não está mais associado ao ativo.
* Foi feita uma tentativa de criar um localizador para um ativo que já tem 5 localizadores em uso. (O armazenamento do Azure impõe o limite de cinco políticas de acesso compartilhado em um contêiner de armazenamento.)
* Vincular a conta de armazenamento de um ativo a um IngestManifestAsset não é o mesmo que a conta de armazenamento usada pelo IngestManifest pai.  

## <a name="500-internal-server-error"></a>500 Erro de Servidor Interno
Durante o processamento da solicitação, os serviços de mídia encontram algum erro que impede a continuação do processamento. Isto pode dever-se a um dos seguintes motivos:

* A criação de um ativo ou trabalho falha porque as informações de cota de serviço da conta dos serviços de mídia estão temporariamente indisponíveis.
* A criação de um contêiner de armazenamento de BLOBs IngestManifest ou ativo falha porque as informações da conta de armazenamento da conta estão temporariamente indisponíveis.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>503 Serviço Indisponível
No momento, o servidor não pode receber solicitações. Esse erro pode ser causado por solicitações excessivas ao serviço. O mecanismo de limitação dos serviços de mídia restringe o uso de recursos para aplicativos que fazem uma solicitação excessiva ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres do código de erro para obter informações mais detalhadas sobre o motivo pelo qual você recebeu o erro 503. Esse erro nem sempre significa limitação.
> 
> 

As descrições de status possíveis são:

* "O servidor está ocupado. As execuções anteriores desse tipo de solicitação levaram mais de {0} segundos. "
* "O servidor está ocupado. Mais de {0} solicitações por segundo podem ser limitadas. "
* "O servidor está ocupado. Mais de {0} solicitações dentro de {1} segundos podem ser limitadas. "

Para lidar com esse erro, é recomendável usar a lógica de repetição de retirada exponencial. Isso significa usar esperas progressivamente mais longas entre repetições para respostas de erro consecutivas.  Para obter mais informações, consulte o [bloco de aplicativo de tratamento de falhas transitórias](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Se você estiver usando o [SDK dos serviços de mídia do Azure para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 foi implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Consulte também
[Códigos de erro de gerenciamento dos serviços de mídia](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

