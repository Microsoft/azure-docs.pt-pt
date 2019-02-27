---
title: Comunicação segurança - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: atenuações para ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: d451b53868dcd3253aba2a1c3118ddcc140445c3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882999"
---
# <a name="security-frame-communication-security--mitigations"></a>Quadro de segurança: Segurança da comunicação | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Hub de eventos do Azure** | <ul><li>[Proteger a comunicação para um Hub de eventos com SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verifique os privilégios de conta de serviço e verificar que os serviços ou páginas ASP.NET personalizadas respeitam a segurança do CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Utilizar o gateway de gestão de dados ao ligar no local do SQL Server à fábrica de dados do Azure](#sqlserver-factory)</li></ul> |
| **Servidor de identidades** | <ul><li>[Certifique-se de que todo o tráfego para o servidor de identidades é através de ligação HTTPS](#identity-https)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de X.509 certificados utilizados para autenticar as ligações SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurar certificado SSL para o domínio personalizado no App Service do Azure](#ssl-appservice)</li><li>[Forçar todo o tráfego para o serviço de aplicações do Azure através de ligação HTTPS](#appservice-https)</li><li>[Ativar a segurança de transporte Strict do HTTP (HSTS)](#http-hsts)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se o SQL server certificado de encriptação e validação da ligação](#sqlserver-validation)</li><li>[Forçar a comunicação encriptado para o SQL server](#encrypted-sqlserver)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que a comunicação para armazenamento do Azure é através de HTTPS](#comm-storage)</li><li>[Validar o MD5 hash depois de transferir o blob caso não é possível ativar o HTTPS](#md5-https)</li><li>[Utilizar o cliente compatível SMB 3.0 para garantir que a encriptação de dados em trânsito para partilhas de ficheiros do Azure](#smb-shares)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar a afixação de certificado](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Ativar o HTTPS - proteger o canal de transporte](#https-transport)</li><li>[WCF: Definir o nível de proteção de segurança de mensagem para EncryptAndSign](#message-protection)</li><li>[WCF: Utilizar uma conta com privilégios mínimos para executar seu serviço do WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forçar todo o tráfego para as APIs da Web através de ligação HTTPS](#webapi-https)</li></ul> |
| **Cache do Azure para Redis** | <ul><li>[Certifique-se de que a comunicação para a Cache do Azure para o Redis é através de SSL](#redis-ssl)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Proteger o dispositivo para a comunicação de Gateway de campo](#device-field)</li></ul> |
| **Gateway de Cloud da IoT** | <ul><li>[Proteger o dispositivo para a comunicação de Gateway de nuvem utilizando o SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Proteger a comunicação para um Hub de eventos com SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Event Hubs autenticação e segurança visão geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Proteja as ligações AMQP ou HTTP para um Hub de eventos com SSL/TLS |

## <a id="priv-aspnet"></a>Verifique os privilégios de conta de serviço e verificar que os serviços ou páginas ASP.NET personalizadas respeitam a segurança do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique os privilégios de conta de serviço e verificar que os serviços ou páginas ASP.NET personalizadas respeitam a segurança do CRM |

## <a id="sqlserver-factory"></a>Utilizar o gateway de gestão de dados ao ligar no local do SQL Server à fábrica de dados do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipos de serviço ligado - do Azure e no local |
| **Referências**              |[Mover dados entre no local e o Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [o Data management gateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Passos** | <p>A ferramenta de Gateway de gestão de dados (DMG) é necessária para ligar a origens de dados que estão protegidas por trás de um firewall ou de rede empresarial.</p><ol><li>Bloquear a máquina isola a ferramenta DMG e impede que os programas com funcionamento incorreto de prejudiciais ou monitorização na máquina de origem de dados. (Por exemplo as atualizações mais recentes tem de estar instaladas, portas de enable mínima necessária, provisionamento de contas de controlado auditoria ativada, encriptação de disco ativada etc.)</li><li>Chave de Gateway de dados deve ser girado em intervalos frequentes ou sempre que a palavra-passe da conta do serviço DMG renova</li><li>Transits de dados através do serviço de ligação tem de estar encriptados</li></ol> |

## <a id="identity-https"></a>Certifique-se de que todo o tráfego para o servidor de identidades é através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [IdentityServer3 - chaves, criptografia e assinaturas](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - implementação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | Por predefinição, IdentityServer requer que todas as ligações recebidas por vir através de HTTPS. É absolutamente obrigatório que a comunicação com IdentityServer é efetuada através de apenas transportes seguros. Existem determinados cenários de implementação, como a descarga de SSL em que este requisito pode ser relaxado. Consulte a página de implementação do servidor de identidades em referências para obter mais informações. |

## <a id="x509-ssltls"></a>Certifique-se de X.509 certificados utilizados para autenticar as ligações SSL, TLS e DTLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Aplicativos que usam o SSL, TLS e DTLS totalmente tem de verificar os certificados X.509 das entidades que se ligam ao. Isto inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e de expiração)</li><li>Estado de revogação</li><li>Utilização (por exemplo, autenticação de servidor para servidores, autenticação de cliente para clientes)</li><li>Cadeia de confiança. Certificados devem estar encadeados a uma autoridade de certificação de raiz (AC) que é considerado fidedigno pela plataforma ou explicitamente configurada pelo administrador</li><li>Comprimento de chave de chave pública do certificado tem de ser > 2048 bits</li><li>Algoritmo de hash tem de ser SHA256 e acima |

## <a id="ssl-appservice"></a>Configurar certificado SSL para o domínio personalizado no App Service do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Ativar HTTPS para uma aplicação no serviço de aplicações do Azure](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Passos** | Por predefinição, o Azure permite já HTTPS para todas as aplicações com um certificado de caráter universal para o *. azurewebsites.net domínio. No entanto, como todos os domínios de caráter universal, não é tão seguro quanto a utilizar um domínio personalizado com o próprio certificado [consulte](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). É recomendado para ativar o SSL para o domínio personalizado que a aplicação implementada será acedida através de|

## <a id="appservice-https"></a>Forçar todo o tráfego para o serviço de aplicações do Azure através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Impor HTTPS no serviço de aplicações do Azure](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Passos** | <p>Embora o Azure permite já HTTPS para os serviços de aplicações do Azure com um certificado de caráter universal para o domínio *. azurewebsites.net, ele não impor HTTPS. Visitantes podem continuar a aceder a aplicação através de HTTP, que pode comprometer a segurança da aplicação e, portanto, HTTPS tem de ser impostas explicitamente. Aplicativos ASP.NET MVC devem utilizar o [RequireHttps filtro](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que força uma solicitação HTTP não segura para ser enviado através de HTTPS.</p><p>Em alternativa, pode servir-se o URL Rewrite module, que está incluído no serviço de aplicações do Azure para impor HTTPS. URL Rewrite module permite aos programadores definir as regras que são aplicadas aos pedidos de entrada antes dos pedidos são entregues à sua aplicação. Regras de reescrita de URLs são definidas num arquivo Web. config armazenado na raiz do aplicativo</p>|

### <a name="example"></a>Exemplo
O exemplo a seguir contém uma regra básica de reescrita de URLs que força todo o tráfego de entrada para utilizar HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Esta regra funciona, retornando um código de estado HTTP de 301 (redirecionamento permanente) quando o usuário solicita uma página através de HTTP. O 301 redireciona o pedido para o mesmo URL que o visitante solicitado, mas substitui a porção HTTP da solicitação com HTTPS. Por exemplo, HTTP://contoso.com poderia ser redirecionado para HTTPS://contoso.com. 

## <a id="http-hsts"></a>Ativar a segurança de transporte Strict do HTTP (HSTS)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Roteiro para segurança de transporte Strict do OWASP HTTP](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Passos** | <p>Segurança de transporte Strict da HTTP (HSTS) é um aprimoramento de segurança participação ativa especificado por um aplicativo web com o uso de um cabeçalho de resposta especial. Depois de um browser suportado recebe esse cabeçalho esse navegador impedirá que todas as comunicações sejam enviados através de HTTP para o domínio especificado e em vez disso, enviará todas as comunicações através de HTTPS. Ele também impede clique HTTPS através de pedidos em navegadores.</p><p>Para implementar HSTS, o seguinte cabeçalho de resposta deve ser configurado para um Web site globalmente, no código ou na configuração. Strict--segurança de transporte: idade máxima = 300; includeSubDomains HSTS lida com as seguintes ameaças:</p><ul><li>Indicadores de utilizador, ou manualmente tipos http://example.com e está sujeita a um invasor man-in-the-middle: HSTS redireciona automaticamente os pedidos de HTTP para HTTPS para o domínio de destino</li><li>Aplicação Web que se destina a ser puramente HTTPS inadvertidamente contém ligações HTTP ou serve conteúdo através de HTTP: HSTS redireciona automaticamente os pedidos de HTTP para HTTPS para o domínio de destino</li><li>Um invasor man-in-the-middle tenta interceptar o tráfego a partir de um utilizador da vítima a utilizar um certificado inválido e espera que o utilizador irá aceitar o certificado incorreto: HSTS não permite que um utilizador a ignorar a mensagem de certificado inválido</li></ul>|

## <a id="sqlserver-validation"></a>Certifique-se o SQL server certificado de encriptação e validação da ligação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure  |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Melhores práticas em Writing Secure cadeias de ligação para base de dados SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Passos** | <p>Todas as comunicações entre a base de dados SQL e um aplicativo cliente são encriptadas utilizando Secure Sockets Layer (SSL) em todos os momentos. Base de dados SQL não suporta ligações não encriptadas. Para validar certificados com o código da aplicação ou de ferramentas, explicitamente solicitar uma ligação encriptada e não confiar nos certificados de servidor. Se o seu código da aplicação ou ferramentas não pedem uma ligação encriptada, eles ainda receberá ligações encriptadas</p><p>No entanto, não pode validar os certificados de servidor e, portanto, estará suscetíveis a ataques de "homem no meio". Para validar certificados com o código de aplicativo do ADO.NET, defina `Encrypt=True` e `TrustServerCertificate=False` na cadeia de ligação de base de dados. Para validar certificados através do SQL Server Management Studio, abra a caixa ligar ao servidor caixa de diálogo. Clique em criptografar conexão no separador Propriedades de ligação</p>|

## <a id="encrypted-sqlserver"></a>Forçar a comunicação encriptado para o SQL server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | MsSQL2014 de versão - MsSQL2012, versão do SQL - SQL versão - MsSQL2016, SQL |
| **Referências**              | [Ativar ligações encriptadas para o motor de base de dados](https://msdn.microsoft.com/library/ms191192)  |
| **Passos** | Habilitação do SSL a criptografia aumenta a segurança dos dados transmitidos através de redes entre instâncias do SQL Server e os aplicativos. |

## <a id="comm-storage"></a>Certifique-se de que a comunicação para armazenamento do Azure é através de HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Encriptação de nível de transporte de armazenamento do Azure – através de HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Passos** | Para garantir a segurança do armazenamento do Azure dados em do trânsito, utilize sempre o protocolo HTTPS ao chamar as APIs REST ou aceder a objetos no armazenamento. Além disso, assinaturas de acesso partilhado, que pode ser utilizado para delegar acesso a objetos de armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa enviar ligações com SAS tokens irá utilizar o protocolo apropriado.|

## <a id="md5-https"></a>Validar o MD5 hash depois de transferir o blob caso não é possível ativar o HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Descrição geral de MD5 de Blobs do Azure do Windows](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Passos** | <p>Serviço Blob do Windows Azure oferece mecanismos para garantir a integridade dos dados, tanto no aplicativo e camadas de transporte. Se por algum motivo, que terá de utilizar HTTP em vez de HTTPS e estiver a trabalhar com blobs de blocos, pode utilizar a verificação de MD5 para ajudar a verificar a integridade dos blobs a ser transferidos</p><p>Isso ajudará com proteção contra erros de camada de rede/transporte, mas não necessariamente com ataques de intermediários. Se puder usar HTTPS, que fornece segurança de nível de transporte, em seguida, usando a verificação de MD5 é redundante e desnecessários.</p>|

## <a id="smb-shares"></a>Utilizar o cliente compatível SMB 3.0 para garantir que a encriptação de dados em trânsito para partilhas de ficheiros do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - ficheiros |
| **Referências**              | [Armazenamento de ficheiros do Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [suporte SMB de armazenamento de ficheiros do Azure para clientes do Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Passos** | Armazenamento de ficheiros do Azure suporta HTTPS ao usar a API REST, mas é mais frequentemente utilizado como uma partilha de ficheiros anexados a uma VM. SMB 2.1 suporta a encriptação, para que as ligações só são permitidas na mesma região no Azure. No entanto, suporta a encriptação SMB 3.0 e pode ser utilizado com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo que entre regiões de acesso e até mesmo acesso no ambiente de trabalho. |

## <a id="cert-pinning"></a>Implementar a afixação de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Generic, Windows Phone |
| **Atributos**              | N/A  |
| **Referências**              | [Certificado e da fixação de chave pública](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Passos** | <p>A afixação de certificado faz a defesa contra ataques Man-In-The-Middle (MITM). Afixar é o processo de associação de um anfitrião com seus esperado X509 certificado ou chave pública. Assim que um certificado ou chave pública é conhecida ou vista para um anfitrião, o certificado ou chave pública é associada ou "fixada" para o anfitrião. </p><p>Assim, quando um adversário tenta fazer de ataque MITM de SSL, durante o handshake SSL a chave do servidor do invasor será diferente da chave do certificado afixado e o pedido será eliminado, evitando assim o certificado de MITM fixação pode ser alcançado ao implementação do ServicePointManager `ServerCertificateValidationCallback` delegar.</p>|

### <a name="example"></a>Exemplo
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Ativar o HTTPS - proteger o canal de transporte

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Passos** | A configuração da aplicação deve certificar-se de que o HTTPS é utilizado para todo o acesso a informações confidenciais.<ul><li>**EXPLICAÇÃO:** Se um aplicativo lida com informações confidenciais e não utiliza encriptação de nível de mensagem, em seguida, deve apenas ter permissão para comunicar através de um canal de transporte encriptados.</li><li>**RECOMENDAÇÕES:** Certifique-se de que o transporte HTTP está desativado e ativar o transporte HTTPS em vez disso. Por exemplo, substitua a `<httpTransport/>` com `<httpsTransport/>` marca. Não confie numa configuração de rede (firewall) para garantir que a aplicação só pode ser acedida através de um canal seguro. Do ponto de vista filosófica, o aplicativo não deve depender da rede para sua segurança.</li></ul><p>Do ponto de vista prático, as pessoas responsáveis por proteger a rede não sempre controlar os requisitos de segurança do aplicativo à medida que evoluem.</p>|

## <a id="message-protection"></a>WCF: Definir o nível de proteção de segurança de mensagem para EncryptAndSign

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Quando proteção nível é definido como "none" ele irá desativar a proteção de mensagem. Confidencialidade e integridade é obtida com o nível apropriado de definição.</li><li>**RECOMENDAÇÕES:**<ul><li>Quando `Mode=None` -desativa a proteção de mensagem</li><li>Quando `Mode=Sign` -sinais, mas não encripta a mensagem; deve ser utilizadas quando a integridade dos dados é importante</li><li>Quando `Mode=EncryptAndSign` -sinais e encripta a mensagem</li></ul></li></ul><p>Considere desativar a encriptação e assinatura só sua mensagem quando só precisa de validar a integridade das informações sem preocupações de confidencialidade. Isso poderá ser útil para operações ou contratos de serviço no qual tem de validar o remetente original, mas não os dados confidenciais são transmitidos. Ao reduzir o nível de proteção, cuidado-se de que a mensagem não contém qualquer informação identificativa (PII).</p>|

### <a name="example"></a>Exemplo
Configurar o serviço e a operação para apenas assinar a mensagem é mostrado nos exemplos a seguir. Exemplo de contrato de serviço `ProtectionLevel.Sign`: Segue-se um exemplo de uso ProtectionLevel.Sign ao nível do contrato de serviço: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Exemplo de contrato de operação de `ProtectionLevel.Sign` (para um controle Granular): Segue-se um exemplo de uso `ProtectionLevel.Sign` no nível de OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Utilizar uma conta com privilégios mínimos para executar seu serviço do WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Não execute os serviços WCF administrador ou conta de alto privilégio. em caso de comprometimento de serviços irá resultar num alto impacto.</li><li>**RECOMENDAÇÕES:** Utilize uma conta com privilégios mínimos para hospedar seu serviço WCF, porque irá reduzir a superfície de ataque do seu aplicativo e reduzir o estrago em potencial se estão a ser atacado. Se a conta de serviço requer direitos de acesso adicionais nos recursos de infraestrutura, como o MSMQ, o registo de eventos, contadores de desempenho e o sistema de ficheiros, as permissões adequadas devem receber a estes recursos para que o serviço WCF pode ser executado com êxito.</li></ul><p>Se o seu serviço tem de aceder a recursos específicos em nome do chamador original, utilize a representação e delegação fluir a identidade do chamador para uma verificação de autorização de downstream. Num cenário de desenvolvimento, utilize a conta de serviço de rede local, o que é uma conta incorporada especial que tem privilégios limitados. Num cenário de produção, crie uma conta de serviço de domínio personalizado de com privilégios mínimos.</p>|

## <a id="webapi-https"></a>Forçar todo o tráfego para as APIs da Web através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Imposição de SSL num controlador da API Web](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passos** | Se um aplicativo tiver HTTPS e um enlace HTTP, os clientes podem ainda utilizar HTTP para aceder ao site. Para evitar esta situação, utilize um filtro de ação para garantir que os pedidos para APIs protegidos estão sempre através de HTTPS.|

### <a name="example"></a>Exemplo 
O código seguinte mostra um filtro de autenticação de Web API que verifica a existência de SSL: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Adicione este filtro para quaisquer ações de Web API que exigem SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Certifique-se de que a comunicação para a Cache do Azure para o Redis é através de SSL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache do Azure para Redis | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Suporte de SSL de Redis do Azure](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Passos** | Redis server não suporta SSL de imediato, mas não da Cache de Redis do Azure. Se estiver a ligar à Cache do Azure para Redis e o cliente suportar o SSL, como stackexchange. redis, em seguida, deve usar SSL. Por predefinição a porta não SSL está desativada para o novo Cache do Azure para instâncias de Redis. Certifique-se de que as predefinições seguras não são alteradas, a menos que existe uma dependência no suporte SSL para clientes de redis. |

Tenha em atenção que o Redis é projetado para ser acessado por clientes fidedignos dentro de ambientes confiáveis. Isso significa que, normalmente, não é uma boa idéia para expor a instância de Redis diretamente à internet ou, em geral, para um ambiente em que os clientes não fidedignos podem acessar diretamente a porta Redis TCP ou o UNIX socket. 

## <a id="device-field"></a>Proteger o dispositivo para a comunicação de Gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Para dispositivos com base no IP, o protocolo de comunicação, normalmente, poderia ser encapsulado num canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não suportam SSL/TLS investigar se há versões seguras do protocolo que fornecem segurança na camada de transporte ou de mensagens. |

## <a id="device-cloud"></a>Proteger o dispositivo para a comunicação de Gateway de nuvem utilizando o SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Escolha o seu protocolo de comunicação](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Passos** | Secure HTTP/AMQP ou protocolos MQTT usando SSL/TLS. |
