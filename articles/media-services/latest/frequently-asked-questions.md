---
title: Perguntas frequentes dos serviços de mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece respostas para as perguntas frequentes dos serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: c9da29ad288811bbed225fd906f2a7eb1fd9edf7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977731"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Perguntas frequentes sobre os serviços de mídia v3

Este artigo fornece respostas para as perguntas frequentes dos serviços de mídia do Azure (AMS) v3.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos dos serviços de mídia do Azure? 

Consulte [RBAC (controle de acesso baseado em função) para contas de serviços de mídia](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer configurar unidades reservadas de mídia?

Para as tarefas de Análise de áudio e de Análise de vídeo acionadas por Serviços de Multimédia v3 ou Video Indexer, é altamente recomendado que aprovisione a sua conta com 10 MRUs de S3. Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [escala de processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificação ou análise de vídeos. Cada **transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar seus arquivos de vídeo ou áudio. Um [trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real aos serviços de mídia para aplicar a **transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Depois que a transformação tiver sido criada, você poderá enviar trabalhos usando as APIs dos serviços de mídia ou qualquer um dos SDKs publicados. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

### <a name="how-does-pagination-work"></a>Como funciona a paginação?

Ao usar a paginação, você sempre deve usar o link seguinte para enumerar a coleção e não depender de um tamanho de página específico. Para obter detalhes e exemplos, consulte [filtragem, ordenação, paginação](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis nos serviços de mídia do Azure v3?

Para obter detalhes, confira [lacunas de recursos em relação às APIs v2](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de mover uma conta de serviços de mídia entre assinaturas?  

Para obter detalhes, consulte [movendo uma conta dos serviços de mídia entre assinaturas](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão em direto 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e slates de imagem durante a transmissão ao vivo?

A codificação ativa dos serviços de mídia v3 ainda não dá suporte à inserção de slates de vídeo ou imagem durante a transmissão ao vivo. 

Você pode usar um [codificador local ao vivo](recommended-on-premises-live-encoders.md) para alternar o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o switch Studio (no iOS), o OBS Studio (aplicativo gratuito) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar uma criptografia de chave não criptografada AES-128 ou um sistema DRM?

Os clientes freqüentemente se questionam sobre se deve utilizar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente via TLS para que a chave seja criptografada em trânsito, mas sem nenhuma criptografia adicional ("em claro"). Como resultado, a chave usada para descriptografar o conteúdo é acessível ao Player do cliente e pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. Uma criptografia de chave não criptografada AES-128 é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem exibidos por funcionários).

Os sistemas DRM, como PlayReady, Widevine e FairPlay, fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo em comparação com uma chave de limpeza AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução do DRM em um adicional para qualquer criptografia de nível de transporte fornecida pelo TLS. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador mal intencionado a ataques. Para casos de utilização em que o Visualizador pode não ser um confiável e requerem o nível mais elevado de segurança, recomenda-se DRM.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo somente para usuários que têm uma permissão específica, sem usar o Azure AD?

Você não precisa usar nenhum provedor de token específico (como o Azure AD). Você pode criar seu próprio provedor de [JWT](https://jwt.io/) (chamado STS, Secure token Service), usando a criptografia de chave assimétrica. Em seu STS personalizado, você pode adicionar declarações com base em sua lógica de negócios.

Verifique se o emissor, o público e as reivindicações todas correspondem exatamente entre o que está no JWT e o ContentKeyPolicyRestriction usado em ContentKeyPolicy.

Para obter mais informações, consulte [proteger seu conteúdo usando a criptografia dinâmica dos serviços de mídia](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter JWT token antes de o utilizar para a licença de pedido ou a chave?

1. Para produção, você precisa ter um STS (serviços de token de segurança) (serviço Web) que emite um token JWT após uma solicitação HTTPS. Para teste, poderia usar o código mostrado na **GetTokenAsync** método definido na [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Leitor terá de fazer um pedido, após um utilizador é autenticado, para o STS para esse token e atribua-a como o valor do token. Pode utilizar o [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para obter um exemplo de execução do STS, com a chave simétrica e assimétrica, consulte [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Para obter um exemplo de um player com base no leitor de multimédia do Azure com esse token JWT, consulte [ https://aka.ms/amtest ](https://aka.ms/amtest) (expandir a ligação de "player_settings" para ver a entrada de token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizar pedidos para transmitir vídeos em fluxo com encriptação AES?

A abordagem correta é tirar partido do STS (serviço Token seguro):

No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "usuário Premium", "usuário básico", "usuário de avaliação gratuita"). Com afirmações diferentes num JWT, o utilizador pode ver o conteúdo diferente. Obviamente, para conteúdo diferente/recurso, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Use as APIs dos serviços de mídia do Azure para configurar a entrega de licença/chave e criptografar seus ativos (conforme mostrado neste [exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, veja:

- [Descrição geral da proteção de conteúdo](content-protection-overview.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação de leitor de ASP.NET MVC tem de suportar o seguinte:

* Autenticação de utilizador através do Azure AD, o que está sob a HTTPS.
* Troca JWT entre o cliente e o Azure AD, que está abaixo do HTTPS.
* Aquisição de licença DRM pelo cliente, que tem de estar sob HTTPS se a entrega de licenças é fornecida pelos serviços de multimédia. O conjunto de produtos do PlayReady não exige o HTTPS para entrega de licenças. Se o seu servidor de licenças do PlayReady está fora dos serviços de multimédia, pode utilizar HTTP ou HTTPS.

A aplicação de leitor ASP.NET utiliza HTTPS como melhor prática, para que o leitor de multimédia é uma página em HTTPS. No entanto, o HTTP é preferido para a transmissão em fluxo, para que precisa considerar o problema de conteúdo misto.

* O browser não permite que conteúdo misto. Mas o plug-ins como Silverlight e OSMF Plug-in para suavizar e travessão permiti-lo. Conteúdo misto é uma preocupação de segurança por causa da ameaça da capacidade para injetar JavaScript malicioso, que pode fazer com que os dados do cliente estar em risco. Browsers bloquear esta capacidade, por predefinição. É a única maneira de contorná-la no lado do servidor (origem), permitindo que todos os domínios (independentemente do HTTPS ou HTTP). Isto é provavelmente não é uma boa idéia.
* Evite conteúdo misto. A aplicação de leitor e o Media Player, devem utilizar HTTP ou HTTPS. Quando a reprodução de conteúdo misto, é a tecnologia de silverlightSS necessário desmarcar um aviso de conteúdo misto. O tech flashSS processa conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto final de transmissão em fluxo foi criado antes de Agosto de 2014, ele não suporta HTTPS. Neste caso, crie e utilize um novo ponto de final de transmissão em fluxo para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto ao vivo de transmissão em fluxo?

Pode utilizar exatamente o mesmo design e implementação para proteger a transmissão em fluxo em direto nos serviços de multimédia ao tratar o elemento associado um programa como um ativo VOD. Para fornecer uma proteção de vários DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída dinâmica.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos serviços de multimédia?

Muitas vezes, os clientes investiram num farm de servidores de licença em seu próprio Centro de dados ou hospedado por fornecedores de serviços DRM. Com a proteção de conteúdo de serviços de multimédia, pode operar no modo híbrido. Conteúdo pode ser hospedado e dinamicamente protegido nos serviços de multimédia, enquanto as licenças DRM são fornecidas por servidores fora dos serviços de multimédia. Neste caso, considere as seguintes alterações:

* STS deve emitir tokens que são aceitáveis e podem ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pelo Axinom requerem um JWT específico que contém uma mensagem de elegibilidade. Por conseguinte, tem de ter um STS para emitir um JWT desse tipo. 
* Já não terá de configurar o serviço de entrega de licença nos serviços de multimédia. Tem de fornecer a licença de aquisição URLs (para o PlayReady, Widevine e FairPlay) ao configurar ContentKeyPolicies.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Serviços de mídia V2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Posso usar o portal do Azure para gerenciar recursos v3?

Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Há um conceito de Assetfile em v3?

Os AssetFiles foram removidos da API do AMS para separar os serviços de mídia da dependência do SDK de armazenamento. Agora, o armazenamento, não os serviços de mídia, mantém as informações que pertencem ao armazenamento. 

Para obter mais informações, consulte [migrar para os serviços de mídia v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde a criptografia de armazenamento do lado do cliente está em trânsito?

Agora é recomendável usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos serviços de mídia v3](media-services-overview.md)
