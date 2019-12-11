---
title: Usando o Axinom para fornecer licenças Widevine para os serviços de mídia do Azure | Microsoft Docs
description: Este artigo descreve como você pode usar o AMS (serviços de mídia do Azure) para fornecer um fluxo que é criptografado dinamicamente pelo AMS com o PlayReady e o Widevine DRMs. A licença do PlayReady vem do servidor de licença do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licença do Axinom.
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
ms.openlocfilehash: 275fa173c5005c4d1609a858c8edb39b5c307c5e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974619"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do Axinom para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Visão geral
O AMS (serviços de mídia do Azure) adicionou a proteção dinâmica do Google Widevine (consulte o [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter detalhes). Além disso, Player de Mídia do Azure (AMP) também adicionou suporte Widevine (consulte [amp Document](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes). Essa é uma grande realização no conteúdo de streaming DASH protegido por CENC com vários DRM nativos (PlayReady e Widevine) em navegadores modernos equipados com o MSE e o EME.

A partir do SDK do .NET dos serviços de mídia versão 3.5.2, os serviços de mídia permitem que você configure o modelo de licença do Widevine e obtenha licenças do Widevine. Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/) e [castLabs](https://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licença Widevine gerenciado pelo Axinom. Especificamente, ele abrange:  

* Configurando o Criptografia Comum dinâmico com vários DRM (PlayReady e Widevine) com as URLs de aquisição de licença correspondentes;
* Gerando um token JWT para atender aos requisitos do servidor de licença;
* Desenvolvendo Player de Mídia do Azure aplicativo, que lida com a aquisição de licença com autenticação de token JWT;

O sistema completo e o fluxo de chave de conteúdo, ID de chave, semente de chave, token JTW e suas declarações podem ser mais bem descritos pelo seguinte diagrama:

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção de Conteúdo
Para configurar a política de proteção dinâmica e de distribuição de chaves, consulte o blog de Mingfei: [como configurar o empacotamento de Widevine com os serviços de mídia do Azure](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Você pode configurar a proteção de CENC dinâmico com vários DRM para streaming de DASH tendo os dois procedimentos a seguir:

1. Proteção PlayReady para Microsoft Edge e IE11, que pode ter uma restrição de autorização de token. A política restrita de token deve ser acompanhada por um token emitido por um serviço de token seguro (STS), como Azure Active Directory;
2. Proteção Widevine para Chrome, ela pode exigir autenticação de token com token emitido por outro STS. 

Consulte a seção [geração de tokens JWT](media-services-axinom-integration.md#jwt-token-generation) para saber por que Azure Active Directory não pode ser usada como um STS para o servidor de licença do Widevine da Axinom.

### <a name="considerations"></a>Considerações
1. Você deve usar a semente de chave Axinom especificada (8888000000000000000000000000000000000000) e a ID de chave gerada ou selecionada para gerar a chave de conteúdo para configurar o serviço de distribuição de chaves. O servidor de licença Axinom emite todas as licenças que contêm chaves de conteúdo com base na mesma semente de chave, que é válida para teste e produção.
2. A URL de aquisição de licença do Widevine para teste: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidos.

## <a name="azure-media-player-preparation"></a>Preparação de Player de Mídia do Azure
O 1.4.0 de AMP v dá suporte à reprodução de conteúdo do AMS que é empacotado dinamicamente com o PlayReady e o Widevine DRM.
Se o servidor de licença do Widevine não exigir autenticação de token, não há nada adicional que você precise fazer para testar um conteúdo de traço protegido pelo Widevine. Por exemplo, a equipe de AMP fornece um [exemplo](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)simples, onde você pode vê-lo funcionando no Microsoft Edge e no IE11 com PlayReady e Chrome com Widevine.
O servidor de licença Widevine fornecido pelo Axinom requer autenticação de token JWT. O token JWT precisa ser enviado com a solicitação de licença por meio de um cabeçalho HTTP "X-AxDRM-Message". Para essa finalidade, você precisa adicionar o seguinte JavaScript na página da Web que está hospedando AMP antes de definir a origem:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O restante do código AMP é a API de AMP padrão como no documento AMP [aqui](https://amp.azure.net/libs/amp/latest/docs/).

O JavaScript acima para definir o cabeçalho de autorização personalizado ainda é uma abordagem de curto prazo antes que a abordagem oficial de longo prazo no AMP seja liberada.

## <a name="jwt-token-generation"></a>Geração de token JWT
O servidor de licença Axinom Widevine para teste requer autenticação de token JWT. Além disso, uma das declarações no token JWT é de um tipo de objeto complexo em vez de um tipo de dados primitivo.

Infelizmente, o Azure AD só pode emitir tokens JWT com tipos primitivos. Da mesma forma, .NET Framework API (System. IdentityModel. Tokens. SecurityTokenHandler e JwtPayload) só permite que você insira o tipo de objeto complexo como declarações. No entanto, as declarações ainda são serializadas como cadeia de caracteres. Portanto, não podemos usar nenhum dos dois para gerar o token JWT para a solicitação de licença Widevine.

O [pacote NuGet do JWT](https://www.nuget.org/packages/JWT) de John Sheehan atende às necessidades para que vamos usar esse pacote NuGet.

Abaixo está o código para gerar o token JWT com as declarações necessárias, conforme exigido pelo servidor de licença Axinom Widevine para teste:

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

Servidor de licença do Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerações
1. Embora o serviço de entrega de licença do PlayReady do AMS exija "portador =" antes de um token de autenticação, o servidor de licença Axinom Widevine não o utiliza.
2. A chave de comunicação Axinom é usada como chave de assinatura. A chave é uma cadeia de caracteres hexadecimal, no entanto, ela deve ser tratada como uma série de bytes e não uma cadeia de caracteres durante a codificação. Isso é conseguido pelo método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Recuperando a ID da chave
Você pode ter notado que, no código para gerar um token JWT, a ID da chave é necessária. Como o token JWT precisa estar pronto antes de carregar o AMP Player, a ID de chave precisa ser recuperada para gerar o token JWT.

É claro que há várias maneiras de obter a ID da chave. Por exemplo, uma pode armazenar a ID de chave junto com metadados de conteúdo em um banco de dados. Ou você pode recuperar a ID de chave do arquivo de traço MPD (descrição de apresentação de mídia). O código a seguir é para o último.

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

Com a adição mais recente do suporte a Widevine nos serviços de mídia do Azure Proteção de Conteúdo e Player de Mídia do Azure, podemos implementar streaming de DASH + multinativo-DRM (PlayReady + Widevine) com o serviço de licença do PlayReady no AMS e licença do Widevine servidor da Axinom para os seguintes navegadores modernos:

* Chrome
* Microsoft Edge no Windows 10
* IE 11 em Windows 8.1 e Windows 10
* O Firefox (Desktop) e o Safari no Mac (não iOS) também têm suporte por meio do Silverlight e da mesma URL com Player de Mídia do Azure

Os parâmetros a seguir são necessários na mini-solução utilizando o servidor de licença Axinom Widevine. Exceto para ID de chave, o restante dos parâmetros é fornecido pelo Axinom com base na configuração do servidor Widevine.

| Parâmetro | Como ele é usado |
| --- | --- |
| ID da chave de comunicação |Deve ser incluído como o valor da declaração "com_key_id" no token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |
| Chave de comunicação |Deve ser usado como a chave de assinatura do token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |
| Semente de chave |Deve ser usado para gerar a chave de conteúdo com qualquer ID de chave de conteúdo específica (consulte [esta](media-services-axinom-integration.md#content-protection) seção). |
| URL de aquisição de licença do Widevine |Deve ser usado na configuração da política de entrega de ativos para streaming de DASH (consulte [esta](media-services-axinom-integration.md#content-protection) seção). |
| ID da chave de conteúdo |Deve ser incluído como parte do valor da declaração de mensagem de autorização do token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para criar este documento: Kristjan jõgi of Axinom, Mingfei Yan e Amit Rajput.

