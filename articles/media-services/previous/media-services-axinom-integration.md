---
title: Usar a Axinom para entregar licenças widevine à Azure Media Services Microsoft Docs
description: Este artigo descreve como pode utilizar a Azure Media Services (AMS) para entregar um stream que é dinamicamente encriptado pela AMS com DRMs PlayReady e Widevine. A licença PlayReady provém do servidor de licenças PlayReady dos Media Services e a licença Widevine é entregue pelo servidor de licença da Axinom.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1f486bfe7cb96e81cef2da64552be5ddfd87990b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264696"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do Axinom para entregar licenças de Widevine para Serviços de Multimédia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral
A Azure Media Services (AMS) adicionou a proteção dinâmica do Google Widevine (ver [blog da Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para mais detalhes). Além disso, o Azure Media Player (AMP) também adicionou suporte widevine (ver [documento AMP](https://amp.azure.net/libs/amp/latest/docs/) para mais detalhes). Esta é uma grande realização no streaming de conteúdo do DASH protegido pelo CENC com multi-nativo-DRM (PlayReady e Widevine) em navegadores modernos equipados com MSE e EME.

Começando pela versão 3.5.2 dos Media Services .NET SDK, os Serviços de Mídia permitem-lhe configurar o modelo de licença widevine e obter licenças Widevine. Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) e [castLabs](https://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licença widevine gerido pela Axinom. Especificamente, abrange:  

* Configurar a encriptação comum dinâmica com multi-DRM (PlayReady e Widevine) com URLs de aquisição de licença correspondentes;
* Gerar um token JWT para satisfazer os requisitos do servidor de licença;
* Desenvolvimento da app Azure Media Player, que trata da aquisição de licença com autenticação simbólica JWT;

O sistema completo e o fluxo da chave de conteúdo, ID chave, semente chave, ficha JTW, e suas reivindicações podem ser melhor descritos pelo seguinte diagrama:

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção de Conteúdo
Para configurar a política de proteção dinâmica e de entrega de chaves, consulte o blog da Mingfei: [Como configurar a embalagem Widevine com a Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Pode configurar uma proteção dinâmica do CENC com multi-DRM para streaming DASH com ambos os seguintes:

1. A proteção PlayReady para o Microsoft Edge e o IE11, que pode ter uma restrição de autorização simbólica. A política restrita simbólica deve ser acompanhada de um símbolo emitido por um Serviço DeKen Seguro (STS), como o Azure Ative Directory;
2. A proteção de toda a largura para o Chrome, pode requerer a autenticação simbólica com ficha emitida por outra STS. 

Consulte a secção [JWT Token Generation](media-services-axinom-integration.md#jwt-token-generation) para saber por que razão o Azure Ative Directory não pode ser usado como STS para o servidor de licença widevine da Axinom.

### <a name="considerations"></a>Considerações
1. Deve utilizar a semente de chave especificada Axinom (8888000000000000000000000000000000000000000000000000000) e o seu ID de chave gerado ou selecionado para gerar a chave de conteúdo para configurar o serviço de entrega de chaves. O servidor de licenças Axinom emite todas as licenças que contêm chaves de conteúdo com base na mesma semente de chave, que é válida tanto para testes como para produção.
2. O URL de aquisição de licença widevine para testes: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . Tanto HTTP como HTTS são permitidos.

## <a name="azure-media-player-preparation"></a>Preparação do jogador de media Azure
O AMP v1.4.0 suporta a reprodução de conteúdos AMS que são dinamicamente embalados tanto com o PlayReady como com o Widevine DRM.
Se o servidor de licença Widevine não necessitar de autenticação simbólica, não há nada adicional que precise fazer para testar um conteúdo DASH protegido pela Widevine. Por exemplo, a equipa amp fornece uma [amostra](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)simples, onde pode vê-la a trabalhar no Microsoft Edge e no IE11 com o PlayReady e o Chrome com Widevine.
O servidor de licença Widevine fornecido pela Axinom requer autenticação simbólica JWT. O token JWT precisa de ser submetido com pedido de licença através de um cabeçalho HTTP "X-AxDRM-Message". Para o efeito, tem de adicionar o seguinte javascript na página web que hospeda AMP antes de definir a fonte:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

O resto do código AMP é a API AMP padrão como no documento AMP [aqui](https://amp.azure.net/libs/amp/latest/docs/).

O javascript acima para definir o cabeçalho de autorização personalizado ainda é uma abordagem de curto prazo antes da abordagem oficial a longo prazo na AMP ser lançada.

## <a name="jwt-token-generation"></a>Geração JWT Token
O servidor de licença de widevine Axinom para teste requer autenticação simbólica JWT. Além disso, uma das alegações no token JWT é de um tipo de objeto complexo em vez de tipo de dados primitivos.

Infelizmente, a Azure AD só pode emitir fichas JWT com tipos primitivos. Da mesma forma, a API.Net Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) só permite inserir um tipo de objeto complexo como reclamações. No entanto, as alegações ainda são serializadas como cordas. Portanto, não podemos usar nenhum dos dois para gerar o símbolo JWT para pedido de licença widevine.

O [pacote JWT NuGet](https://www.nuget.org/packages/JWT) de John Sheehan satisfaz as necessidades, por isso vamos usar este pacote NuGet.

Abaixo está o código para gerar ficha JWT com as alegações necessárias, conforme exigido pelo servidor de licença widevine da Axinom widevine para testes:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.IdentityModel.Tokens;
using System.IdentityModel.Protocols.WSTrust;
using System.Security.Claims;

namespace OpenIdConnectWeb.Utils
{
    public class JwtUtils
    {
        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
        {
            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

            var payload = new Dictionary<string, object>()
            {
                { "version", 1 },
                { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
            };

            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

            return token;
        }

        //convert hex string to byte[]
        public static byte[] ConvertHexStringToByteArray(string hexString)
        {
            if (hexString.Length % 2 != 0)
            {
                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
            }

            byte[] HexAsBytes = new byte[hexString.Length / 2];
            for (int index = 0; index < HexAsBytes.Length; index++)
            {
                string byteValue = hexString.Substring(index * 2, 2);
                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
            }

            return HexAsBytes;
        }

    }  

}  
```

Servidor de licença de Axinom Widevine

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Considerações
1. Mesmo que o serviço de entrega de licenças AMS PlayReady exija "Bearer=" antes de um token de autenticação, o servidor de licença da Axinom Widevine não o utiliza.
2. A chave de comunicação Axinom é usada como chave de assinatura. A chave é uma corda hexaduto, no entanto deve ser tratada como uma série de bytes e não uma corda ao codificar. Isto é conseguido pelo método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>ID da chave de recuperação
Pode ter reparado que no código para gerar um token JWT, é necessário um ID de chave. Uma vez que o token JWT precisa de estar pronto antes de carregar o leitor DE AMP, o ID chave precisa de ser recuperado para gerar o token JWT.

Claro que há várias formas de obter a identificação da chave. Por exemplo, pode-se armazenar iD chave juntamente com metadados de conteúdo numa base de dados. Ou pode obter o ID da chave do ficheiro MPD do DASH (Media Presentation Description). O código abaixo é para este último.

```csharp
//get key_id from DASH MPD
public static string GetKeyID(string dashUrl)
{
    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
    {
        dashUrl += "(format=mpd-time-csf)";
    }

    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
    objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
    objXmlNamespaceManager.PushScope();

    XPathNodeIterator objXPathNodeIterator;
    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

    string key_id = string.Empty;
    if (objXPathNodeIterator.MoveNext())
    {
        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
    }

    return key_id;
}
```

## <a name="summary"></a>Resumo

Com a mais recente adição de suporte widevine tanto na Azure Media Services Content Protection como no Azure Media Player, somos capazes de implementar o streaming do DASH + Multi-nativo-DRM (PlayReady + Widevine) com o serviço de licença PlayReady em AMS e Widevine servidor de licenças da Axinom para os seguintes navegadores modernos:

* Chrome
* Microsoft Edge no Windows 10
* IE 11 no Windows 8.1 e Windows 10
* Tanto o Firefox (Desktop) como o Safari no Mac (não o iOS) também são suportados via Silverlight e o mesmo URL com o Azure Media Player

São necessários os seguintes parâmetros na mini-solução que utiliza o servidor de licença Axinom Widevine. Com exceção da identificação das chaves, o resto dos parâmetros são fornecidos pela Axinom com base na configuração do servidor Widevine.

| Parâmetro | Como é usado |
| --- | --- |
| ID chave de comunicação |Deve ser incluído como valor da alegação "com_key_id" em ficha JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Chave de comunicação |Deve ser utilizado como a chave de assinatura do símbolo JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Semente chave |Deve ser utilizado para gerar chave de conteúdo com qualquer identificação da chave de conteúdo (ver  [esta](media-services-axinom-integration.md#content-protection) secção). |
| URL de aquisição de licenças de widevine |Deve ser utilizado na configuração da política de entrega de ativos para o streaming DOR (ver  [esta](media-services-axinom-integration.md#content-protection) secção). |
| ID da chave de conteúdo |Deve ser incluído como parte do valor da reclamação da mensagem de direito do símbolo JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para a criação deste documento: Kristjan Jõgi, de Axinom, Mingfei Yan e Amit Rajput.

