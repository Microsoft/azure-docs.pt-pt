---
title: Utilização da Axinom para entregar licenças da Widevine à Azure Media Services [ Axinom] Microsoft Docs
description: Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um stream que é encriptado dinamicamente pela AMS com drMs PlayReady e Widevine. A licença PlayReady provém do servidor de licença PlayReady dos Media Services e a licença Widevine é entregue pelo servidor de licença axinom.
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
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78197169"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do Axinom para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral
A Azure Media Services (AMS) adicionou a proteção dinâmica do Google Widevine (ver blog [de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para mais detalhes). Além disso, o Azure Media Player (AMP) também acrescentou suporte widevine (ver [documento AMP](https://amp.azure.net/libs/amp/latest/docs/) para mais detalhes). Esta é uma grande realização no streaming de conteúdo DASH protegido pelo CENC com multi-nativo-DRM (PlayReady e Widevine) em navegadores modernos equipados com MSE e EME.

Começando pelo Media Services .NET SDK versão 3.5.2, os Media Services permitem configurar o modelo de licença Widevine e obter licenças Widevine. Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) e [castLabs](https://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licença widevine gerido pela Axinom. Especificamente, abrange:  

* Configurar encriptação comum dinâmica com multi-DRM (PlayReady e Widevine) com URLs de aquisição de licença correspondentes;
* Gerar um símbolo JWT para satisfazer os requisitos do servidor de licença;
* Desenvolver a app Azure Media Player, que trata da aquisição de licença com autenticação simbólica JWT;

O sistema completo e o fluxo da chave de conteúdo, id chave, semente chave, símbolo JTW, e suas alegações podem ser melhor descritos pelo seguinte diagrama:

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção de Conteúdo
Para configurar a política de proteção dinâmica e chave, consulte o blog da Mingfei: [Como configurar a embalagem widevine com a Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Pode configurar uma proteção dinâmica do CENC com multi-DRM para o streaming DASH com ambos os seguintes:

1. Proteção PlayReady para Microsoft Edge e IE11, que pode ter uma restrição de autorização simbólica. A política restrita simbólica deve ser acompanhada por um símbolo emitido por um Serviço de Token Seguro (STS), como o Azure Ative Directory;
2. A proteção widevine para o Chrome, pode exigir autenticação simbólica com token emitido por outro STS. 

Consulte a secção [JWT Token Generation](media-services-axinom-integration.md#jwt-token-generation) para saber por que razão o Azure Ative Directory não pode ser usado como sTS para o servidor de licença Widevine da Axinom.

### <a name="considerations"></a>Considerações
1. Deve utilizar a semente-chave especificada a Axinom (8888000000000000000000000000000000000000000000) e o seu ID chave gerado ou selecionado para gerar a chave de conteúdo para configurar o serviço de entrega de chaves. O servidor de licença axinom emite todas as licenças que contenham chaves de conteúdo com base na mesma semente chave, que é válida tanto para testes como para produção.
2. O URL de aquisição da [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)licença Widevine para testes: . Tanto o HTTP como o HTTS são permitidos.

## <a name="azure-media-player-preparation"></a>Preparação de jogador estomados azure media
AMP v1.4.0 suporta a reprodução de conteúdo AMS que é embalado dinamicamente com o PlayReady e o Widevine DRM.
Se o servidor de licença Widevine não necessitar de autenticação simbólica, não há nada adicional que precise de fazer para testar um conteúdo DASH protegido pela Widevine. Por exemplo, a equipa AMP fornece uma [amostra](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)simples, onde pode vê-la a trabalhar no Microsoft Edge e iE11 com playReady e Chrome com Widevine.
O servidor de licença Widevine fornecido pela Axinom requer autenticação simbólica JWT. O símbolo JWT precisa de ser submetido com pedido de licença através de um cabeçalho HTTP "X-AxDRM-Message". Para o efeito, é necessário adicionar o seguinte javascript na página web que acolhe a AMP antes de definir a fonte:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O resto do código AMP é a Amp API padrão como no documento AMP [aqui](https://amp.azure.net/libs/amp/latest/docs/).

O javascript acima para definir o cabeçalho de autorização personalizada ainda é uma abordagem a curto prazo antes da abordagem oficial a longo prazo na AMP ser lançada.

## <a name="jwt-token-generation"></a>JWT Token Generation
O servidor de licença Axinom Widevine para testes requer autenticação simbólica JWT. Além disso, uma das alegações no símbolo JWT é de um tipo de objeto complexo em vez de tipo de dados primitivos.

Infelizmente, a AD Azure só pode emitir fichas JWT com tipos primitivos. Da mesma forma, a .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) apenas permite inserir o tipo de objeto complexo como alegações. No entanto, as alegações ainda são serializadas como cordas. Por conseguinte, não podemos utilizar nenhum dos dois para gerar o símbolo JWT para o pedido de licença widevine.

O [pacote JWT NuGet](https://www.nuget.org/packages/JWT) de John Sheehan satisfaz as necessidades, por isso vamos usar este pacote NuGet.

Abaixo está o código para gerar token JWT com as reivindicações necessárias, conforme exigido pelo servidor de licença Axinom Widevine para testes:

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

Servidor de licença Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerações
1. Mesmo que o serviço de entrega de licenças AMS PlayReady exija "Bearer=" antes de um símbolo de autenticação, o servidor de licença Axinom Widevine não o utiliza.
2. A chave de comunicação Axinom é usada como chave de assinatura. A chave é uma corda hexéeis, no entanto deve ser tratada como uma série de bytes e não uma corda ao codificar. Isto é conseguido pelo método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Recuperação do ID da chave
Pode ter reparado que no código para gerar um símbolo JWT, é necessário identificar a chave. Uma vez que o símbolo JWT precisa de estar pronto antes de carregar o leitor AMP, o ID chave precisa de ser recuperado para gerar ficha JWT.

Claro, há várias maneiras de obter a identificação chave. Por exemplo, pode-se armazenar o ID da chave juntamente com os metadados de conteúdo numa base de dados. Ou pode obter o ID da chave do ficheiro DASH MPD (Descrição da Apresentação dos Media). O código abaixo é para este último.

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

## <a name="summary"></a>Resumo

Com a mais recente adição de suporte widevine tanto na Proteção de Conteúdos dos Serviços de Mídia Azure como no Azure Media Player, somos capazes de implementar o streaming de DASH + Multi-native-DRM (PlayReady + Widevine) com o serviço de licença PlayReady em AMS e o servidor de licença Widevine da Axinom para os seguintes navegadores modernos:

* Chrome
* Microsoft Edge no Windows 10
* IE 11 no Windows 8.1 e Windows 10
* Tanto o Firefox (Desktop) como o Safari no Mac (não iOS) também são suportados via Silverlight e o mesmo URL com O Leitor de Mídia Azure

São necessários os seguintes parâmetros na mini-solução alavancando o servidor de licença Axinom Widevine. Com exceção da identificação da chave, o resto dos parâmetros são fornecidos pela Axinom com base na configuração do servidor Widevine.

| Parâmetro | Como é usado |
| --- | --- |
| ID chave de comunicação |Deve ser incluído como valor da reclamação "com_key_id" em símbolo JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Chave de comunicação |Deve ser utilizado como chave de assinatura do símbolo JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Semente-chave |Deve ser utilizado para gerar a chave de conteúdo com qualquer id da chave de conteúdo (ver [esta](media-services-axinom-integration.md#content-protection) secção). |
| URL de aquisição da Licença Widevine |Deve ser utilizado na configuração da política de entrega de ativos para o streaming DASH (ver [esta](media-services-axinom-integration.md#content-protection) secção). |
| ID da chave de conteúdo |Deve ser incluído como parte do valor da reclamação da mensagem de direito do símbolo JWT (ver [esta](media-services-axinom-integration.md#jwt-token-generation) secção). |

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para a criação deste documento: Kristjan Jõgi, da Axinom, Mingfei Yan, e Amit Rajput.

