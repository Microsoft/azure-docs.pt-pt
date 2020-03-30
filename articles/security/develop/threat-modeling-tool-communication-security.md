---
title: Segurança de comunicação para a Ferramenta de Modelação de Ameaças da Microsoft
titleSuffix: Azure
description: atenuações para ameaças expostas na Ferramenta de Modelação de Ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 39ee0fa2dc973cd6c20756cae2024af79d1375dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294142"
---
# <a name="security-frame-communication-security--mitigations"></a>Quadro de Segurança: Segurança da Comunicação [ Segurança da Comunicação] Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Hub de Eventos do Azure** | <ul><li>[Comunicação segura para o Centro de Eventos utilizando SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou páginas ASP.NET respeitam a segurança da CRM](#priv-aspnet)</li></ul> |
| **Fábrica de Dados Azure** | <ul><li>[Utilize o gateway de gestão de dados ao ligar o SQL Server às instalações da Fábrica de Dados Azure](#sqlserver-factory)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Certifique-se de que todo o tráfego para O Servidor de Identidade está sobre a ligação HTTPS](#identity-https)</li></ul> |
| **Aplicação Web** | <ul><li>[Verifique os certificados X.509 utilizados para autenticar ligações SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configure o certificado SSL para domínio personalizado no Serviço de Aplicações Azure](#ssl-appservice)</li><li>[Forçar todo o tráfego para o Serviço de Aplicações Azure sobre a ligação HTTPS](#appservice-https)</li><li>[Ativar http segurança rigorosa de transporte (HSTS)](#http-hsts)</li></ul> |
| **Base de Dados** | <ul><li>[Garantir encriptação de ligação ao servidor SQL e validação de certificado](#sqlserver-validation)</li><li>[Forçar comunicação encriptada ao servidor SQL](#encrypted-sqlserver)</li></ul> |
| **Storage do Azure** | <ul><li>[Certifique-se de que a comunicação ao Armazenamento Azure está acima de HTTPS](#comm-storage)</li><li>[Valide o hash MD5 após o download do blob se HTTPS não puder ser ativado](#md5-https)</li><li>[Utilize um cliente compatível com SMB 3.0 para garantir encriptação de dados em trânsito a Ações de Ficheiros Azure](#smb-shares)</li></ul> |
| **Cliente Móvel** | <ul><li>[Aplicação de Certificado de Fixação](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Ativar HTTPS - Canal de Transporte Seguro](#https-transport)</li><li>[WCF: Definir o nível de proteção de segurança de mensagens para encriptar AndSign](#message-protection)</li><li>[WCF: Use uma conta menos privilegiada para executar o seu serviço WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forçar todo o tráfego para APIs web sobre conexão HTTPS](#webapi-https)</li></ul> |
| **Cache do Azure para Redis** | <ul><li>[Certifique-se de que a comunicação com Azure Cache for Redis está sobre a SSL](#redis-ssl)</li></ul> |
| **Gateway de campo iot** | <ul><li>[Dispositivo seguro para comunicação gateway de campo](#device-field)</li></ul> |
| **Gateway da nuvem iot** | <ul><li>[Dispositivo seguro para comunicação Cloud Gateway usando SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Comunicação segura para o Centro de Eventos utilizando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e modelo de segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Ligações AMQP ou HTTP seguras ao Hub de Eventos utilizando SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou páginas ASP.NET respeitam a segurança da CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou páginas ASP.NET respeitam a segurança da CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Utilize o gateway de gestão de dados ao ligar o SQL Server às instalações da Fábrica de Dados Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Tipos de serviço ligados - Azure e No-local |
| **Referências**              |[Dados de mudança entre as instalações e a Fábrica de Dados Azure,](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway) [gateway de gestão de dados](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Passos** | <p>A ferramenta Data Management Gateway (DMG) é necessária para se ligar a fontes de dados protegidas por trás da rede de dados ou de uma firewall.</p><ol><li>O bloqueio da máquina isola a ferramenta DMG e evita que os programas avariados danifiquem ou bisem na máquina de origem de dados. (Por exemplo. as atualizações mais recentes devem ser instaladas, permitir as portas mínimas exigidas, o provisionamento de contas controladas, a auditoria ativada, a encriptação do disco ativada, etc.)</li><li>A tecla Data Gateway deve ser rodada em intervalos frequentes ou sempre que a palavra-passe da conta de serviço DMG se renova</li><li>Os trânsitos de dados através do Link Service devem ser encriptados</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Certifique-se de que todo o tráfego para O Servidor de Identidade está sobre a ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - Chaves, Assinaturas e Criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - Implementação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | Por defeito, o IdentityServer requer que todas as ligações de entrada venham a surgir HTTPS. É absolutamente obrigatório que a comunicação com o IdentityServer seja feita apenas sobre transportes seguros. Existem certos cenários de implantação, como o descarregamento de SSL, onde esta exigência pode ser relaxada. Consulte a página de implementação do Servidor de Identidade nas referências para obter mais informações. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verifique os certificados X.509 utilizados para autenticar ligações SSL, TLS e DTLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>As aplicações que utilizem SSL, TLS ou DTLS devem verificar plenamente os certificados X.509 das entidades a que se ligam. Isto inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e expiração)</li><li>Estatuto de revogação</li><li>Utilização (por exemplo, Autenticação do Servidor para servidores, Autenticação do Cliente para clientes)</li><li>Cadeia de confiança. Os certificados devem ser acorrentados a uma autoridade de certificação de raiz (CA) que seja confiada pela plataforma ou explicitamente configurada pelo administrador</li><li>O comprimento-chave da chave pública do certificado deve ser >2048 bits</li><li>Algoritmo de hashing deve ser SHA256 ou acima |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configure o certificado SSL para domínio personalizado no Serviço de Aplicações Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | AmbienteType - Azure |
| **Referências**              | [Ativar HTTPS para uma aplicação no Serviço de Aplicações Azure](../../app-service/configure-ssl-bindings.md) |
| **Passos** | Por padrão, o Azure já permite HTTPS para cada aplicação com um certificado wildcard para o domínio *.azurewebsites.net. No entanto, como todos os domínios wildcard, não é tão seguro como usar um domínio personalizado com certificado próprio [Refer](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Recomenda-se ativar o SSL para o domínio personalizado a que a aplicação implementada será acedida através de|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Forçar todo o tráfego para o Serviço de Aplicações Azure sobre a ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | AmbienteType - Azure |
| **Referências**              | [Impor HTTPS no Serviço de Aplicações Azure](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Passos** | <p>Embora o Azure já permita serviços de aplicações HTTPS para azure com um certificado wildcard para o domínio *.azurewebsites.net, não aplica HTTPS. Os visitantes podem ainda aceder à aplicação utilizando http, o que pode comprometer a segurança da aplicação e, portanto, https tem de ser aplicado explicitamente. ASP.NET aplicações mVC devem utilizar o [filtro RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que obriga a que um pedido HTTP não garantido seja reenviado em HTTPS.</p><p>Em alternativa, o módulo URL Rewrite, que está incluído no Serviço de Aplicações Azure, pode ser utilizado para impor HTTPS. O módulo de reescrita url permite que os desenvolvedores definam regras que são aplicadas aos pedidos de entrada antes de os pedidos serem entregues à sua aplicação. As regras de reescrita de URL são definidas num ficheiro web.config armazenado na raiz da aplicação</p>|

### <a name="example"></a>Exemplo
O exemplo seguinte contém uma regra básica de reescrita url que força todo o tráfego a entrar a usar HTTPS
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
Esta regra funciona devolvendo um código de estado HTTP de 301 (redirecionamento permanente) quando o utilizador solicitar uma página usando HTTP. O 301 redireciona o pedido para o mesmo URL que o visitante solicitou, mas substitui a parte HTTP do pedido por HTTPS. Por exemplo, `HTTP://contoso.com` seria redirecionado `HTTPS://contoso.com`para . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Ativar http segurança rigorosa de transporte (HSTS)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [OWASP HTTP Estrita Folha de Batota de Segurança de Transporte](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Passos** | <p>HTTP Strict Transport Security (HSTS) é uma melhoria de segurança de opt-in que é especificada por uma aplicação web através da utilização de um cabeçalho de resposta especial. Assim que um navegador suportado receber este cabeçalho, o navegador impedirá que quaisquer comunicações sejam enviadas em HTTP para o domínio especificado e, em vez disso, enviará todas as comunicações para HTTPS. Também impede https clicar através de solicitações nos navegadores.</p><p>Para implementar o HSTS, o seguinte cabeçalho de resposta tem de ser configurado para um website globalmente, seja em código ou em config. Rigoroso-Transporte-Segurança: idade máxima=300; incluir SubDomínios HSTS aborda as seguintes ameaças:</p><ul><li>Marcadores de utilizador `https://example.com` ou tipos manualmente e está sujeito a um atacante man-in-the-middle: HSTS redireciona automaticamente pedidos de HTTP para HTTPS para o domínio alvo</li><li>Aplicação web que se destina a ser puramente HTTPS inadvertidamente contém links HTTP ou serve conteúdo sobre HTTP: HSTS redireciona automaticamente pedidos de HTTP para HTTPS para o domínio alvo</li><li>Um agressor man-in-the-middle tenta intercetar o tráfego de um utilizador da vítima usando um certificado inválido e espera que o utilizador aceite o certificado mau: O HSTS não permite que um utilizador sobrepor-se à mensagem de certificado inválida</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Garantir encriptação de ligação ao servidor SQL e validação de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure  |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Boas práticas na escrita de cordas de ligação seguras para base de dados SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Passos** | <p>Todas as comunicações entre a Base de Dados SQL e uma aplicação de cliente são encriptadas utilizando a Camada de Tomadas Seguras (SSL) em todos os momentos. A Base de Dados SQL não suporta ligações não encriptadas. Para validar certificados com código de aplicação ou ferramentas, solicite explicitamente uma ligação encriptada e não confie nos certificados do servidor. Se o seu código de aplicação ou ferramentas não solicitarem uma ligação encriptada, continuarão a receber ligações encriptadas</p><p>No entanto, podem não validar os certificados do servidor e, portanto, serão suscetíveis a ataques "homem no meio". Para validar certificados com `Encrypt=True` ADO.NET `TrustServerCertificate=False` código de aplicação, detetete e na cadeia de ligação à base de dados. Para validar os certificados através do Estúdio de Gestão do Servidor SQL, abra a caixa de diálogo Connect to Server. Clique em encriptar a ligação no separador Propriedades de Ligação</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Forçar comunicação encriptada ao servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | OnPrem |
| **Atributos**              | Versão SQL - MsSQL2016, Versão SQL - MsSQL2012, Versão SQL - MsSQL2014 |
| **Referências**              | [Ativar ligações encriptadas ao motor de base de dados](https://msdn.microsoft.com/library/ms191192)  |
| **Passos** | Ativar a encriptação SSL aumenta a segurança dos dados transmitidos através de redes entre instâncias do Servidor SQL e aplicações. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Certifique-se de que a comunicação ao Armazenamento Azure está acima de HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação de nível de transporte de armazenamento Azure - Utilização DE HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Passos** | Para garantir a segurança dos dados do Armazenamento Azure em trânsito, utilize sempre o protocolo HTTPS quando ligar para as APIs rest ou aceder a objetos no armazenamento. Além disso, as Assinaturas de Acesso Partilhado, que podem ser usadas para delegar acesso a objetos de armazenamento Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser usado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa que envie links com tokens SAS utilizará o protocolo adequado.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Valide o hash MD5 após o download do blob se HTTPS não puder ser ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Visão geral do Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Passos** | <p>O serviço Windows Azure Blob fornece mecanismos para garantir a integridade dos dados tanto na aplicação como nas camadas de transporte. Se, por alguma razão, precisar de utilizar http em vez de HTTPS e estiver a trabalhar com bolhas de bloco, pode utilizar a verificação MD5 para ajudar a verificar a integridade das bolhas que estão a ser transferidas</p><p>Isto ajudará na proteção contra erros de rede/camada de transporte, mas não necessariamente com ataques intermediários. Se puder utilizar https, que proporciona segurança ao nível de transporte, então a utilização da verificação MD5 é redundante e desnecessária.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Utilize um cliente compatível com SMB 3.0 para garantir encriptação de dados em trânsito às ações do Ficheiro Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Tipo de armazenamento - Arquivo |
| **Referências**              | [Armazenamento de Ficheiros Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), Suporte SMB de Armazenamento de [Ficheiros Azure para clientes windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Passos** | O Armazenamento de Ficheiros Azure suporta HTTPS ao utilizar a API REST, mas é mais comumente usado como uma partilha de ficheiroS SMB anexada a um VM. O SMB 2.1 não suporta encriptação, pelo que as ligações só são permitidas na mesma região do Azure. No entanto, o SMB 3.0 suporta encriptação e pode ser utilizado com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo o acesso transversal e até mesmo o acesso no ambiente de trabalho. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Aplicação de Certificado de Fixação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, Windows Phone |
| **Atributos**              | N/D  |
| **Referências**              | [Certificado e Fixação de Chaves Públicas](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Passos** | <p>O predileto de certificado defende-se contra os ataques man-in-the-middle (MITM). A fixação é o processo de associar um hospedeiro ao seu certificado X509 esperado ou chave pública. Uma vez que um certificado ou chave pública é conhecido ou visto para um hospedeiro, o certificado ou chave pública é associado ou 'fixado' ao hospedeiro. </p><p>Assim, quando um adversário tenta fazer o ataque Do SSL MITM, durante o aperto de mão sSL a chave do servidor do intruso será diferente da chave do certificado fixado, e `ServerCertificateValidationCallback` o pedido será descartado, evitando assim que o certificado MITM possa ser alcançado implementando o delegado do ServicePointManager.</p>|

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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Ativar HTTPS - Canal de Transporte Seguro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro Líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Passos** | A configuração da aplicação deve garantir que HTTPS é utilizado para todo o acesso a informações sensíveis.<ul><li>**EXPLICAÇÃO:** Se uma aplicação lida com informações sensíveis e não utiliza encriptação ao nível da mensagem, então só deve ser permitida a comunicação através de um canal de transporte encriptado.</li><li>**RECOMENDAÇÕES:** Certifique-se de que o transporte HTTP está desativado e que permita o transporte HTTPS. Por exemplo, `<httpTransport/>` substitua a etiqueta por `<httpsTransport/>` etiqueta. Não confie numa configuração de rede (firewall) para garantir que a aplicação só pode ser acedida por um canal seguro. Do ponto de vista filosófico, a aplicação não deve depender da rede para a sua segurança.</li></ul><p>Do ponto de vista prático, os responsáveis pela segurança da rede nem sempre seguem os requisitos de segurança da aplicação à medida que evoluem.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Definir o nível de proteção de segurança de mensagens para encriptar AndSign

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Quando o nível de proteção estiver definido para "nenhum" irá desativar a proteção da mensagem. A confidencialidade e integridade são alcançadas com um nível de definição adequado.</li><li>**RECOMENDAÇÕES:**<ul><li>quando `Mode=None` - Desativa a proteção contra mensagens</li><li>quando `Mode=Sign` - Sinais mas não encriptam a mensagem; deve ser usado quando a integridade dos dados é importante</li><li>quando `Mode=EncryptAndSign` - Assina e encripta a mensagem</li></ul></li></ul><p>Considere desligar a encriptação e apenas assinar a sua mensagem quando apenas precisa validar a integridade da informação sem preocupações de confidencialidade. Isto pode ser útil para operações ou contratos de serviço em que você precisa validar o remetente original, mas nenhum dados sensíveis é transmitido. Ao reduzir o nível de proteção, tenha cuidado para que a mensagem não contenha quaisquer dados pessoais.</p>|

### <a name="example"></a>Exemplo
Configurar o serviço e a operação apenas para assinar a mensagem é mostrado nos seguintes exemplos. Contrato de `ProtectionLevel.Sign`Serviço Exemplo de: O seguinte é um exemplo de utilização do ProtectionLevel.Sign at the Service Contract level: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Início do `ProtectionLevel.Sign` contrato Exemplo de (para controlo granular): O seguinte é um exemplo de utilização `ProtectionLevel.Sign` ao nível do Contrato de Funcionamento:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Use uma conta menos privilegiada para executar o seu serviço WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Não executar serviços WCF sob conta de administrador ou de alto privilégio. em caso de compromisso de serviços, isso resultará num impacto elevado.</li><li>**RECOMENDAÇÕES:** Utilize uma conta menos privilegiada para hospedar o seu serviço WCF porque reduzirá a superfície de ataque da sua aplicação e reduzirá os danos potenciais se for atacado. Se a conta de serviço necessitar de direitos de acesso adicionais sobre recursos de infraestrutura, tais como MSMQ, o registo de eventos, contadores de desempenho e o sistema de ficheiros, devem ser dadas permissões adequadas a esses recursos para que o serviço WCF possa funcionar com sucesso.</li></ul><p>Se o seu serviço precisar de aceder a recursos específicos em nome do chamador original, use a personificação e a delegação para fluir a identidade do chamador para uma verificação de autorização a jusante. Num cenário de desenvolvimento, utilize a conta de serviço de rede local, que é uma conta especial incorporada que reduziu os privilégios. Num cenário de produção, crie uma conta de serviço de domínio personalizado menos privilegiada.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Forçar todo o tráfego para APIs web sobre conexão HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impor o SSL num controlador Web API](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passos** | Se uma aplicação tiver um CONTRATO HTTPS e um http vinculativo, os clientes podem ainda utilizar http para aceder ao site. Para evitar isto, utilize um filtro de ação para garantir que os pedidos de APIs protegidos estão sempre acima de HTTPS.|

### <a name="example"></a>Exemplo 
O seguinte código mostra um filtro de autenticação Web API que verifica o SSL: 
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
Adicione este filtro a quaisquer ações da Web API que exijam SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Certifique-se de que a comunicação com Azure Cache for Redis está sobre a SSL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache do Azure para Redis | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte Azure Redis SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Passos** | O servidor Redis não suporta o SSL fora da caixa, mas o Azure Cache para o Redis tem. Se está a ligar-se ao Azure Cache para redis e o seu cliente suporta a SSL, tal como stackExchange.Redis, então deve utilizar o SSL. Por defeito, a porta não-SSL é desativada para o novo Azure Cache para os casos Redis. Certifique-se de que os incumprimentos seguros não são alterados a menos que exista uma dependência do suporte SSL para os clientes redis. |

Por favor, note que redis é projetado para ser acedido por clientes de confiança dentro de ambientes confiáveis. Isto significa que normalmente não é boa ideia expor a instância Redis diretamente à internet ou, em geral, a um ambiente onde clientes não fidedignos podem aceder diretamente à porta Redis TCP ou à tomada UNIX. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Dispositivo seguro para comunicação gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para dispositivos baseados em IP, o protocolo de comunicação poderia normalmente ser encapsuado num canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não suportam o SSL/TLS, investigue se existem versões seguras do protocolo que fornecem segurança no transporte ou camada de mensagem. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Dispositivo seguro para comunicação Cloud Gateway usando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Escolha o seu Protocolo de Comunicação](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Passos** | Proteja os protocolos HTTP/AMQP ou MQTT utilizando sSL/TLS. |
