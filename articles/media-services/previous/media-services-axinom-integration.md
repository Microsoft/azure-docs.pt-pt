---
title: Utilizar o Axinom para entregar licenças de Widevine para serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um fluxo dinamicamente criptografada pela AMS com PlayReady e Widevine DRMs. A licença de PlayReady vem do servidor de licenças do PlayReady dos serviços de multimédia e licença do Widevine é entregue pelo servidor de licenças do Axinom.
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
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 6714beae690e23c686fc08b88e93044ae3901c89
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839496"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do Axinom para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral
Serviços de multimédia do Azure (AMS) adicionou proteção dinâmica do Google Widevine (consulte [blogue do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter detalhes). Além disso, leitor de multimédia do Azure (AMP) também adicionou suporte a Widevine (consulte [documento AMP](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes). Esta é uma grande conquista em transmissão em fluxo conteúdo de TRAÇO protegido pelo CENC com várias-native multi-DRM (PlayReady e Widevine) em browsers modernos equipados com MSE e EME.

Começando com o SDK de .NET de Media Services versão 3.5.2, serviços de multimédia permite-lhe configurar o modelo de licença do Widevine e obtenha licenças do Widevine. Também pode utilizar os seguintes parceiros de AMS para ajudar a fornecer licenças do Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licenças de Widevine gerido pelo Axinom. Especificamente, abrange:  

* Configurar a encriptação comum dinâmica com múltipla DRM (PlayReady e Widevine) com URLs de aquisição de licença correspondente;
* Gerar um token JWT para cumprir os requisitos de licença de servidor;
* Desenvolvimento de aplicação de leitor de multimédia do Azure, que lida com a aquisição de licença com a autenticação de token JWT;

O sistema completo e o fluxo de chave de conteúdo, chave de ID, seed chave, JTW token e às declarações podem ser melhor descritas pelo diagrama seguinte:

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção de Conteúdo
Para configurar a proteção dinâmica e a política de entrega de chave, consulte o blog do Mingfei: [Como configurar empacotamento Widevine com os serviços de multimédia do Azure](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Pode configurar a proteção do dinâmica CENC com múltipla DRM para DASH, transmissão em fluxo de ter os seguintes elementos:

1. Proteção de PlayReady para Microsoft Edge e IE11, o que poderia ter uma restrição de token de autorização. A política de token restrito tem de ser acompanhada de um token emitido por um Secure Token STS (serviço), como o Azure Active Directory;
2. Proteção de Widevine para o Chrome, ele pode exigir autenticação de token com o token emitido por outra STS. 

Ver [geração de tokens JWT](media-services-axinom-integration.md#jwt-token-generation) por que as do Azure Active Directory não pode ser usado como um STS para o servidor de licenças do Widevine do Axinom na secção.

### <a name="considerations"></a>Considerações
1. Tem de utilizar o Axinom especificado ID para gerar a chave de conteúdo para a configuração do serviço de entrega de chave da chave de seed chave (8888000000000000000000000000000000000000) e sua gerado ou selecionado. Servidor de licenças do Axinom problemas de todas as licenças que contêm chaves de conteúdo com base no mesmo seed de chave, que é válido para fins de teste e produção.
2. O URL de aquisição de licença do Widevine para fins de teste: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidas.

## <a name="azure-media-player-preparation"></a>Preparação do leitor de multimédia do Azure
AMP v1.4.0 oferece suporte a reprodução de conteúdo de AMS empacotado de forma dinâmica com PlayReady e Widevine DRM.
Se o servidor de licenças do Widevine não requer autenticação de token, não há nada adicional que precisa fazer para testar um conteúdo de TRAÇO protegido pelo Widevine. Por exemplo, a equipe e fornece um simples [exemplo](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), onde pode vê-lo a trabalhar na Microsoft Edge e IE11 com PlayReady e o Chrome com Widevine.
O servidor de licenças de Widevine fornecido pelo Axinom requer autenticação de token JWT. O token JWT tem de ser submetido com pedido de licença através de um cabeçalho HTTP "X-AxDRM-Message". Para essa finalidade, terá de adicionar o seguinte javascript na página da web de hospedagem AMP antes de definir a origem:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O resto do código AMP é padrão e API, como no documento de AMP [aqui](https://amp.azure.net/libs/amp/latest/docs/).

O javascript acima para o cabeçalho de autorização personalizado de definição ainda é uma abordagem de curto prazo, antes que a abordagem de longo prazo oficial em AMP é lançada.

## <a name="jwt-token-generation"></a>Geração de tokens JWT
Servidor de licenças de Axinom Widevine para fins de teste requer autenticação de token JWT. Além disso, uma das afirmações no JWT token é de um tipo de objeto complexo em vez do tipo de dados primitivos.

Infelizmente, o Azure AD, pode emitir JWT tokens apenas com tipos primitivos. Da mesma forma, API do .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) apenas permite-lhe introduzir o tipo de objeto complexo como afirmações. No entanto, as declarações ainda são serializadas como cadeia de caracteres. Portanto, não é possível utilizar qualquer uma das duas para gerar o token JWT para solicitação de licença do Widevine.

John Sheehan [pacote NuGet do JWT](https://www.nuget.org/packages/JWT) atende às necessidades de, pelo que vamos usar esse pacote de NuGet.

Segue-se o código para gerar token JWT com as declarações necessárias, conforme exigido pelo servidor de licenças do Axinom Widevine para fins de teste:

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

Servidor de licenças do Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerações
1. Mesmo que o serviço de entrega de licença de PlayReady de AMS requer "portador =" anteriores um token de autenticação, servidor de licenças do Axinom Widevine não usá-lo.
2. A chave de comunicação do Axinom é utilizada como chave de assinatura. A chave é uma cadeia de caracteres hexadecimal, no entanto, deve ser tratado como uma série de bytes não uma cadeia de caracteres ao codificar. Isso é feito pelo método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>A obter o ID da chave
Talvez tenha notado que, no código para gerar um JWT, ID de token, a chave é necessária. Uma vez que as necessidades de token JWT para estar pronto antes de leitor de carregamento e da chave ID precisarem ser recuperados para gerar o JWT token.

Obviamente, existem várias formas de obter espera da chave de ID. Por exemplo, pode armazenar um ID de chave, juntamente com os metadados dos conteúdos numa base de dados. Ou pode recuperar a ID do ficheiro de TRAÇO MPD (descrição de apresentação do suporte de dados) da chave. O código a seguir é ao segundo.

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
Com mais recente adição de suporte do Widevine na proteção de conteúdo do serviços de multimédia do Azure e o leitor de multimédia do Azure, somos capazes de implementar a transmissão em fluxo DASH + várias-native multi-DRM (PlayReady + Widevine), com ambos os serviços de licença PlayReady na licença do AMS e o Widevine servidor do Axinom para os seguintes browsers modernos:

* Chrome
* Microsoft Edge on Windows 10
* IE 11 no Windows 8.1 e Windows 10
* (Ambiente de trabalho) do Firefox e Safari no Mac (não iOS) também são suportados através do Silverlight e o mesmo URL com o leitor de multimédia do Azure

Os seguintes parâmetros sejam necessários no servidor de licenças de Axinom Widevine tirar partido da mini solução de. Exceto para a chave de ID, o restante dos parâmetros fornecidos pelo Axinom com base na respetiva configuração de servidor do Widevine.

| Parâmetro | Como são utilizadas |
| --- | --- |
| ID da chave de comunicação |Tem de ser incluído como valor da afirmação "com_key_id" no JWT token (consulte [isso](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Chave de comunicação |Tem de ser utilizada como a chave de assinatura de JWT token (consulte [isso](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Propagação de chave |Tem de ser utilizado para gerar a chave de conteúdo com qualquer conteúdo determinado ID da chave (consulte [isso](media-services-axinom-integration.md#content-protection) secção). |
| URL de aquisição de licença do Widevine |Tem de ser utilizado na configuração de política de entrega de elementos para DASH de transmissão em fluxo (consulte [isso](media-services-axinom-integration.md#content-protection) secção). |
| ID de chave de conteúdo |Tem de ser incluído como parte do valor de afirmação de mensagem de elegibilidade de JWT token (consulte [isso](media-services-axinom-integration.md#jwt-token-generation) secção). |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de agradecer a seguintes pessoas que contribuíram para a criação deste documento: Kristjan Jõgi do Axinom Mingfei Yan e Amit Rajput.

