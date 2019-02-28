---
title: Proteger o seu conteúdo com os serviços de suporte de dados - Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da proteção de conteúdo com os Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6fc7da067e5680af0d4e3f07c5e87be2e9592f3f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986044"
---
# <a name="content-protection-overview"></a>Descrição geral da proteção de conteúdo

Pode utilizar os serviços de multimédia do Azure para proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda dinamicamente encriptado com o Advanced Encryption Standard (AES-128) ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. 

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger conteúdo](./media/content-protection/content-protection.png)

&#42;*encriptação dinâmica suporta AES-128 "chave não encriptada", CBCS e CENC. Para obter detalhes, consulte a matriz de suporte [aqui](#streaming-protocols-and-encryption-types).*

Este artigo explica os conceitos e terminologia relevante para compreender a proteção de conteúdo com os Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Componentes principais de um sistema de proteção de conteúdo

Para concluir com êxito o design do sistema/aplicativo "proteção de conteúdo", precisa compreender plenamente o âmbito do esforço. A lista seguinte fornece uma visão geral de três partes, que terá de implementar. 

1. Código de serviços de multimédia do Azure
  
  O [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) exemplo mostra-lhe como implementar o sistema de multi-DRM com serviços de multimédia v3 e também utilizar o serviço de entrega de licença/chave de serviços de multimédia. Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar.
  
  O exemplo mostra como:

  1. Criar e configurar [diretivas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

    * Defina a autorização de entrega de licença, especificando a lógica de verificação de autorização com base em declarações em JWT.
    * Configure a encriptação de DRM ao especificar a chave de conteúdo.
    * Configurar [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), e/ou [FairPlay](fairplay-license-overview.md) licenças. Os modelos permitem-lhe configurar direitos e permissões para cada um os DRMs utilizados.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
  2. Criar uma [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) que está configurado para transmitir o elemento encriptado. 
  
    O **localizador de transmissão em fluxo** tem de ser associado a uma [política de transmissão em fluxo] (https://docs.microsoft.com/rest/api/media/streamingpolicies). No exemplo, definimos StreamingLocator.StreamingPolicyName como a política de "Predefined_MultiDrmCencStreaming". Esta política indica que queremos para duas chaves de conteúdo (envelope e CENC) para obter gerado e definir sobre o localizador. Por conseguinte, são aplicadas as encriptação de envelope, do PlayReady e do Widevine (a chave é entregue ao cliente para reprodução, com base nas licenças DRM configuradas). Se também quiser encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize "Predefined_MultiDrmStreaming".
    
    Uma vez que queremos encriptar o vídeo, o **política de chave de conteúdo** que configurámos anteriormente também tem de ser associados a **localizador de transmissão em fluxo**. 
    
  3. Crie um token de teste.

    O **GetTokenAsync** método mostra como criar um teste de token.
  4. Crie o URL de transmissão em fluxo.

    O **GetDASHStreamingUrlAsync** método mostra como criar o URL de transmissão em fluxo. Neste caso, os fluxos de URL a **DASH** conteúdo.

2. Jogador com AES ou DRM cliente. Uma aplicação de leitor de vídeo com base num player SDK (nativo ou baseada no browser) tem de cumprir os seguintes requisitos:
  * O SDK player suporta os clientes DRM necessários
  * O SDK player suporta os protocolos de transmissão em fluxo necessários: Uniforme, DASH e/ou HLS
  * O SDK player tem de ser capaz de lidar com a passagem de um token JWT no pedido de aquisição de licença
  
    Pode criar um leitor, utilizando o [API de leitor de multimédia do Azure](http://amp.azure.net/libs/amp/latest/docs/). Utilizar o [API do Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) para especificar a tecnologia DRM a ser utilizada em diferentes plataformas DRM.

    Para teste AES ou CENC (Widevine e/ou PlayReady) encriptados conteúdo, pode utilizar [leitor de multimédia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html). Certifique-se de que clicar em "Opções avançadas" e verifique as opções de encriptação.

    Se pretender testar conteúdo do FairPlay encriptado, utilize [player este teste](https://aka.ms/amtest). O jogador suporta Widevine, PlayReady, e encriptação de chave de não FairPlay DRMs, bem como AES-128. 
    
    Tem de escolher o navegador certo para testar DRMs diferentes: Chrome/Opera/Firefox para Widevine, Microsoft Edge/IE11 para PlayReady, Safari no macOS para FairPlay.

3. Proteger o serviço de Token (STS), que emite o JSON Web Token (JWT) como token de acesso para acesso a recursos back-end. Pode utilizar os serviços de entrega de licença do AMS como o recurso de back-end. Tem um STS para definir o seguinte:

  * Emissor e público-alvo (ou escopo)
  * Afirmações, que são dependentes nos requisitos de negócios na proteção de conteúdo
  * Verificação de simétrica ou assimétrica para verificação da assinatura
  * Suporte de rollover de chave (se necessário)

    Pode usar [essa ferramenta de STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para teste STS, que oferece suporte a todos os 3 tipos de chave de verificação: simétrica, assimétrica ou do Azure AD com o rollover da chave. 

> [!NOTE]
> É altamente recomendado para concentrar-se e testar totalmente a cada parte (descrito anteriormente) antes de passar para a parte seguinte. Para testar o seu sistema de "proteção de conteúdo", utilize as ferramentas especificadas na lista acima.  

## <a name="streaming-protocols-and-encryption-types"></a>Protocolos de transmissão em fluxo e tipos de encriptação

Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a chave não encriptada AES ou encriptação de DRM com o PlayReady, Widevine e FairPlay. Atualmente, pode criptografar os formatos de HTTP Live Streaming (HLS), MPEG DASH e Smooth Streaming. Cada protocolo suporta os seguintes métodos de encriptação:

|Protocolo|Formato de contêiner|Esquema de encriptação|
|---|---|---|---|
|MPEG-DASH|Todos|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Todos|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Transmissão em Fluxo Uniforme|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="aes-128-clear-key-vs-drm"></a>Vs de chaves não encriptada AES-128. DRM

Os clientes freqüentemente se questionam sobre se deve utilizar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que com encriptação AES a chave de conteúdo é transmitida ao cliente via TLS para que a chave for encriptada em trânsito, mas sem qualquer adicional encriptação ("Limpar"). Como resultado, a chave utilizada para desencriptar o conteúdo está acessível para o leitor de cliente e pode ser visualizada num rastreio de rede no cliente em texto simples. Encriptação de chave não encriptada AES-128 é adequada para casos de utilização em que o Visualizador é um confiável (por exemplo, encriptar empresariais vídeos distribuídos dentro de uma empresa sejam visualizados por funcionários).

Sistemas DRM, como o PlayReady, Widevine e FairPlay que todos fornecerem um nível adicional de encriptação na chave utilizada para desencriptar o conteúdo em comparação comparado a chave não encriptada do AES-128. A chave de conteúdo é encriptada para uma chave protegida pelo runtime DRM na adicionais para qualquer encriptação de nível de transporte fornecida por TLS. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador mal intencionado a ataques. Para casos de utilização em que o Visualizador pode não ser um confiável e requerem o nível mais elevado de segurança, recomenda-se DRM.

## <a name="dynamic-encryption-and-key-delivery-service"></a>A encriptação dinâmica e o serviço de entrega de chave

Em serviços de multimédia v3, uma chave de conteúdo é associada com o localizador de transmissão em fluxo (consulte [neste exemplo](protect-with-aes128.md)). Se utilizar o serviço de entrega de chave de serviços de multimédia, pode permitir que os serviços de multimédia do Azure gere a chave de conteúdo para. Deve gerar a chave de conteúdo por conta própria se estiver usando o serviço de entrega de chave próprio, ou se tiver de lidar com um cenário de elevada disponibilidade em que tem de ter a mesma chave de conteúdo em dois datacenters.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através da utilização de chave não encriptada AES ou encriptação de DRM. Para descriptografar o fluxo, o jogador solicita a chave do serviço de entrega de chave de serviços de multimédia ou o serviço de entrega de chave que especificou. Para decidir se é ou não o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Os Media Services fornecem um serviço de entrega de chave para entregar licenças DRM (PlayReady, Widevine, FairPlay) e as chaves AES para os clientes autorizados. Pode utilizar a API REST ou uma biblioteca de cliente dos serviços de multimédia para configurar políticas de autorização e autenticação para as suas licenças e chaves.

### <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de licença e de chave personalizado

Utilize os seguintes modelos se pretender especificar um diferente licença e a chave de serviço de entrega (não dos serviços de multimédia). Os dois campos substituíveis nos modelos existem para que pode partilhar a política de transmissão em fluxo em muitos ativos em vez de criar uma política de transmissão em fluxo de mensagens em fila por recurso. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - modelo para o URL do serviço personalizado a entrega de chaves para jogadores do utilizador final. Não é necessário quando utilizar serviços de multimédia do Azure para a emissão de chaves. O modelo oferece suporte a tokens substituíveis, que o serviço será atualizada em tempo de execução com o valor específico para o pedido.  Os valores de token atualmente suportados são {AlternativeMediaId}, que é substituído pelo valor do StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave a ser solicitada.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - modelo para o URL do serviço personalizado a entrega de licenças para jogadores do utilizador final. Não é necessário quando utilizar os serviços de multimédia do Azure para emitir licenças. O modelo oferece suporte a tokens substituíveis, que o serviço será atualizada em tempo de execução com o valor específico para o pedido. Os valores de token atualmente suportados são {AlternativeMediaId}, que é substituído pelo valor do StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave a ser solicitada. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - mesmo que acima, apenas para o Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - mesmo que acima, apenas para o FairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

O `ContentKeyId` tem um valor da chave a ser pedido e o `AlternativeMediaId` pode ser utilizado para mapear a solicitação para uma entidade no seu lado. Por exemplo, o `AlternativeMediaId` pode ser utilizado para o ajudar a procurar permissões.

Para obter exemplos REST que usar custom de chaves e URLs de aquisição de licença, consulte [criar políticas de transmissão em fluxo de mensagens em fila -](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Controlar o acesso do conteúdo

Pode controlar quem tem acesso ao seu conteúdo ao configurar a política de chave de conteúdo. Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. Tem de configurar a política de chave de conteúdo. O cliente (leitor) tem de cumprir a política antes da chave pode ser entregue ao cliente. A política de chave de conteúdo pode ter **abra** ou **token** restrição. 

Com uma conteúdo chave política de token restrito, a chave de conteúdo é enviada apenas para um cliente que apresenta um válido JSON Web Token (JWT) ou simple web tokens (SWT) no pedido de chaves/licenças. Este token deve ser emitido por um serviço de token de segurança (STS). Pode utilizar o Azure Active Directory como um STS ou implementar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de chave de serviços de multimédia devolve a chave/licença pedida para o cliente se o token é válido e as afirmações no token corresponderem às configuradas para a chave/licença.

Ao configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público-alvo, às vezes chamado de âmbito, descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Os clientes utilizam frequentemente um STS personalizado para incluir declarações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com diferentes parâmetros de licença DRM (uma licença de subscrição em comparação com uma licença de aluguel) ou para incluir uma declaração representando a chave de conteúdo Identificador da chave que o token concede acesso para.
 
## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus ativos inativos, os recursos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona em serviços de multimédia v3:

|Opção de encriptação|Descrição|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia| Encriptação AES-256, chave gerida pelos serviços de multimédia|Não suportado<sup>(1)</sup>|
|[Encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chave de encriptação do lado do servidor oferecidas pelo armazenamento do Azure, gerida pelo Azure ou pelo cliente|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferecidas pelo armazenamento do Azure, chave gerida pelo cliente no Cofre de chaves de encriptação do lado do cliente|Não suportado|

<sup>1</sup> em serviços de multimédia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para em versões anteriores compatibilidade quando os recursos foram criados com os serviços de multimédia v2. O que significa v3 funciona com o armazenamento existente encriptado ativos, mas não permitirá que a criação de novos itens.

## <a name="next-steps"></a>Passos Seguintes

* [Proteger com encriptação AES](protect-with-aes128.md)
* [Proteger com o DRM](protect-with-drm.md)
* [Crie o sistema de proteção de conteúdo de múltipla drm com controlo de acesso](design-multi-drm-system-with-access-control.md)
* [Perguntas mais frequentes](frequently-asked-questions.md)

