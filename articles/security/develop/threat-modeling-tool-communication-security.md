---
title: Segurança de comunicação para a ferramenta de modelação de ameaças da Microsoft
titleSuffix: Azure
description: Saiba mais sobre a mitigação das ameaças à segurança da comunicação expostas na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517534"
---
# <a name="security-frame-communication-security--mitigations"></a>Quadro de Segurança: Segurança de Comunicação / Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Hub de Eventos do Azure** | <ul><li>[Comunicação segura para o Centro de Eventos utilizando SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou ASP.NET Páginas respeitam a segurança da CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Utilize o gateway de gestão de dados ao ligar o Servidor SQL às Fábrica de Dados do Azure](#sqlserver-factory)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Certifique-se de que todo o tráfego para o Servidor de Identidade está sobre a ligação HTTPS](#identity-https)</li></ul> |
| **Aplicação Web** | <ul><li>[Verifique os certificados X.509 utilizados para autenticar ligações SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configure o certificado TLS/SSL para domínio personalizado no Serviço de Aplicações Azure](#ssl-appservice)</li><li>[Forçar todo o tráfego para o Serviço de Aplicações Azure sobre a ligação HTTPS](#appservice-https)</li><li>[Ativar a segurança estrita do transporte http (HSTS)](#http-hsts)</li></ul> |
| **Base de dados** | <ul><li>[Garantir encriptação de ligação ao servidor SQL e validação de certificados](#sqlserver-validation)</li><li>[Force a comunicação encriptada para o servidor SQL](#encrypted-sqlserver)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que a comunicação ao Azure Storage acabou https](#comm-storage)</li><li>[Valide o hash MD5 após o download do blob se HTTPS não puder ser ativado](#md5-https)</li><li>[Utilize cliente compatível com SMB 3.0 para garantir encriptação de dados em trânsito para Azure File Shares](#smb-shares)</li></ul> |
| **Cliente Móvel** | <ul><li>[Implementar pinning de certificado](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Ativar HTTPS - Canal de Transporte Seguro](#https-transport)</li><li>[WCF: Definir o nível de proteção de segurança da mensagem para encriptar esferindo](#message-protection)</li><li>[WCF: Utilize uma conta menos privilegiada para executar o seu serviço WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forçar todo o tráfego para APIs web sobre conexão HTTPS](#webapi-https)</li></ul> |
| **Cache do Azure para Redis** | <ul><li>[Certifique-se de que a comunicação com Azure Cache para Redis é sobre TLS](#redis-ssl)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Dispositivo seguro para a comunicação do Gateway de Campo](#device-field)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Dispositivo seguro para cloud gateway comunicação usando SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Comunicação segura para o Centro de Eventos utilizando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança do Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Passos** | Ligações AMQP ou HTTP seguras ao Centro de Eventos utilizando SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou ASP.NET Páginas respeitam a segurança da CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Verifique os privilégios da conta de serviço e verifique se os Serviços personalizados ou ASP.NET Páginas respeitam a segurança da CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Utilize o gateway de gestão de dados ao ligar o Servidor SQL às Fábrica de Dados do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Tipos de Serviços Ligados - Azul e No-Local |
| **Referências**              |[Dados móveis entre as instalações e a Azure Data Factory,](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway) [gateway de gestão de dados](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Passos** | <p>A ferramenta Data Management Gateway (DMG) é necessária para se conectar a fontes de dados protegidas por trás de corpnet ou firewall.</p><ol><li>Bloquear a máquina isola a ferramenta DMG e evita que os programas de mau funcionamento danifiquem ou bisbilhotem a máquina de origem de dados. (Por exemplo, as atualizações mais recentes devem ser instaladas, permitir portas mínimas exigidas, provisão de contas controladas, auditoria ativada, encriptação do disco ativada, etc.)</li><li>A tecla Data Gateway deve ser rodada em intervalos frequentes ou sempre que a palavra-passe da conta de serviço DMG renovar</li><li>Os trânsitos de dados através do Link Service devem ser encriptados</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Certifique-se de que todo o tráfego para o Servidor de Identidade está sobre a ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IdentidadeServer3 - Chaves, Assinaturas e Criptografia,](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) [IdentityServer3 - Implantação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | Por predefinição, o IdentityServer requer que todas as ligações recebidas venham ao HTTPS. É absolutamente obrigatório que a comunicação com o IdentityServer seja feita apenas através de transportes seguros. Existem certos cenários de implantação, como o descarregamento de TLS, onde este requisito pode ser relaxado. Consulte a página de implementação do Servidor de Identidade nas referências para obter mais informações. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verifique os certificados X.509 utilizados para autenticar ligações SSL, TLS e DTLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>As aplicações que utilizem SSL, TLS ou DTLS devem verificar plenamente os certificados X.509 das entidades a que se ligam. Isto inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e de validade)</li><li>Estatuto de revogação</li><li>Utilização (por exemplo, Autenticação do Servidor para servidores, Autenticação do Cliente para clientes)</li><li>Cadeia de confiança. Os certificados devem ser acorrentados a uma autoridade de certificação de raiz (CA) que seja fidedigna pela plataforma ou explicitamente configurada pelo administrador</li><li>O comprimento chave da chave pública do certificado deve ser >bits de 2048</li><li>Algoritmo de hashing deve ser SHA256 e acima |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configure o certificado TLS/SSL para domínio personalizado no Serviço de Aplicações Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Ativar HTTPS para uma aplicação no Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Passos** | Por predefinição, o Azure já permite HTTPS para cada aplicação com um certificado wildcard para o domínio *.azurewebsites.net. No entanto, como todos os domínios wildcard, não é tão seguro como usar um domínio personalizado com certificado próprio [Refer](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Recomenda-se que o TLS seja o domínio personalizado a que a aplicação implementada será acedida através|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Forçar todo o tráfego para o Serviço de Aplicações Azure sobre a ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Impor HTTPS no Serviço de Aplicações Azure](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Passos** | <p>Embora o Azure já permita serviços de aplicações HTTPS para Azure com um certificado wildcard para o domínio *.azurewebsites.net, não aplica HTTPS. Os visitantes podem ainda aceder à aplicação através do HTTP, o que pode comprometer a segurança da aplicação e, por isso, o HTTPS tem de ser aplicado explicitamente. ASP.NET aplicações MVC devem utilizar o [filtro RequireHttps](/dotnet/api/system.web.mvc.requirehttpsattribute) que força um pedido HTTP não seguro a ser reencamprovado https.</p><p>Em alternativa, o módulo de reescrita de URL, que está incluído no Serviço de Aplicações Azure, pode ser utilizado para impor HTTPS. O módulo URL Rewrite permite que os desenvolvedores definam regras que são aplicadas aos pedidos de entrada antes de os pedidos serem entregues à sua aplicação. As regras de reescrita de URL são definidas num ficheiro web.config armazenado na raiz da aplicação</p>|

### <a name="example"></a>Exemplo
O exemplo a seguir contém uma regra básica de reescrita de URL que força todo o tráfego de entrada a usar HTTPS
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
Esta regra funciona devolvendo um código de estado HTTP de 301 (redirecionamento permanente) quando o utilizador solicita uma página utilizando HTTP. O 301 redireciona o pedido para o mesmo URL que o visitante solicitou, mas substitui a parte HTTP do pedido por HTTPS. Por exemplo, `HTTP://contoso.com` seria redirecionado para `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Ativar a segurança estrita do transporte http (HSTS)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [OWASP HTTP Folha de batota de segurança de transporte rigorosa](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Passos** | <p>HTTP Strict Transport Security (HSTS) é um reforço de segurança de opt-in que é especificado por uma aplicação web através da utilização de um cabeçalho de resposta especial. Assim que um navegador suportado receber este cabeçalho, o navegador impedirá que quaisquer comunicações sejam enviadas em HTTP para o domínio especificado e, em vez disso, enviará todas as comunicações em HTTPS. Também impede que HTTPS clique através de solicitações nos navegadores.</p><p>Para implementar o HSTS, o seguinte cabeçalho de resposta tem de ser configurado para um website globalmente, seja em código ou em config. Estrita-Transporte-Segurança: max-age=300; inclui AUbDomains HSTS aborda as seguintes ameaças:</p><ul><li>Os marcadores do utilizador ou os tipos manuais `https://example.com` e estão sujeitos a um intruso homem-no-meio: O HSTS redireciona automaticamente os pedidos HTTP para HTTPS para o domínio alvo</li><li>A aplicação web que se destina a ser exclusivamente HTTPS contém inadvertidamente links HTTP ou serve conteúdo em HTTP: HSTS redireciona automaticamente pedidos HTTP para HTTPS para o domínio alvo</li><li>Um agressor homem no meio tenta intercetar o tráfego de um utilizador vítima usando um certificado inválido e espera que o utilizador aceite o certificado mau: O HSTS não permite que um utilizador substitua a mensagem de certificado inválido</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Garantir encriptação de ligação ao servidor SQL e validação de certificados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | SQL Azure  |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Boas práticas na escrita de cadeias de conexão seguras para base de dados SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Passos** | <p>Todas as comunicações entre a SQL Database e uma aplicação do cliente são encriptadas utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL), em todos os momentos. A BASE de Dados SQL não suporta ligações não encriptadas. Para validar certificados com código de aplicação ou ferramentas, solicite explicitamente uma ligação encriptada e não confie nos certificados do servidor. Se o código ou ferramentas da sua aplicação não solicitarem uma ligação encriptada, continuarão a receber ligações encriptadas</p><p>No entanto, não podem validar os certificados do servidor e, portanto, serão suscetíveis a ataques de "homem no meio". Para validar certificados com ADO.NET código de aplicação, desatado `Encrypt=True` e na cadeia de `TrustServerCertificate=False` ligação da base de dados. Para validar certificados através do SQL Server Management Studio, abra a caixa de diálogo Connect to Server. Clique em Encriptar a ligação no separador Propriedades de Ligação</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Force a comunicação encriptada para o servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | VERSÃO SQL - MsSQL2016, VERSÃO SQL - MsSQL2012, VERSÃO SQL - MsSQL2014 |
| **Referências**              | [Enable Encrypted Connections to the Database Engine](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) (Ativar as Ligações Encriptadas para o Motor de Base de Dados)  |
| **Passos** | Permitir a encriptação TLS aumenta a segurança dos dados transmitidos através de redes entre instâncias do SQL Server e aplicações. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Certifique-se de que a comunicação ao Azure Storage acabou https

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação Transport-Level de armazenamento Azure – Usando HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Passos** | Para garantir a segurança dos dados de armazenamento Azure em trânsito, utilize sempre o protocolo HTTPS quando ligar para as APIs REST ou aceder a objetos armazenados. Além disso, as Assinaturas de Acesso Partilhado, que podem ser usadas para delegar o acesso a objetos de armazenamento Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa que envie links com fichas SAS utilizará o protocolo adequado.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Valide o hash MD5 após o download do blob se HTTPS não puder ser ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Dispositivo de Armazenamento - Blob |
| **Referências**              | [Visão geral do Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Passos** | <p>O serviço Windows Azure Blob fornece mecanismos para garantir a integridade dos dados tanto nas camadas de aplicação como de transporte. Se, por qualquer motivo, precisar de utilizar HTTP EM vez de HTTPS e estiver a trabalhar com bolhas de blocos, pode utilizar a verificação MD5 para ajudar a verificar a integridade das bolhas que estão a ser transferidas</p><p>Isto ajudará na proteção contra erros de rede/camada de transporte, mas não necessariamente com ataques intermediários. Se puder utilizar o HTTPS, que proporciona segurança ao nível do transporte, então a utilização de verificação MD5 é redundante e desnecessária.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Utilize cliente compatível com SMB 3.0 para garantir encriptação de dados em trânsito para ações do Ficheiro Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | StorageType - Arquivo |
| **Referências**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB Support for Windows Clients](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Passos** | O Azure File Storage suporta HTTPS quando utiliza a API REST, mas é mais comumente usado como uma partilha de ficheiro SMB anexada a um VM. O SMB 2.1 não suporta encriptação, pelo que as ligações só são permitidas dentro da mesma região em Azure. No entanto, o SMB 3.0 suporta a encriptação e pode ser utilizado com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo o acesso a regiões cruzadas e até mesmo o acesso ao ambiente de trabalho. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementar pinning de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, Windows Phone |
| **Atributos**              | N/D  |
| **Referências**              | [Certificado e Chave Pública](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Passos** | <p>A fixação de certificados defende-se contra os ataques man-in-the-middle (MITM). Pinning é o processo de associar um hospedeiro com o seu certificado X509 esperado ou chave pública. Uma vez conhecido ou visto um certificado ou chave pública para um hospedeiro, o certificado ou chave pública está associado ou 'fixado' ao anfitrião. </p><p>Assim, quando um adversário tenta fazer o ataque do MITM TLS, durante o aperto de mão TLS a chave do servidor do atacante será diferente da chave do certificado fixado, e o pedido será descartado, impedindo assim a fixação do Certificado MITM pode ser alcançado implementando o delegado do ServicePointManager. `ServerCertificateValidationCallback`</p>|

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
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Passos** | A configuração da aplicação deve garantir que o HTTPS seja utilizado para todo o acesso a informações sensíveis.<ul><li>**EXPLICAÇÃO:** Se uma aplicação tratar informações sensíveis e não utilizar encriptação ao nível da mensagem, então só deve ser permitida a comunicação através de um canal de transporte encriptado.</li><li>**RECOMENDAÇÕES:** Certifique-se de que o transporte HTTPS está desativado e em vez disso, ative o transporte HTTPS. Por exemplo, substitua o `<httpTransport/>` por `<httpsTransport/>` etiqueta. Não confie numa configuração de rede (firewall) para garantir que a aplicação só pode ser acedida por um canal seguro. Do ponto de vista filosófico, a aplicação não deve depender da rede para a sua segurança.</li></ul><p>Do ponto de vista prático, os responsáveis pela segurança da rede nem sempre acompanham os requisitos de segurança da aplicação à medida que evoluem.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Definir o nível de proteção de segurança da mensagem para encriptar esferindo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Quando o nível de proteção estiver definido como "nenhum", irá desativar a proteção da mensagem. A confidencialidade e a integridade são alcançadas com o nível de regulação adequado.</li><li>**RECOMENDAÇÕES:**<ul><li>quando `Mode=None` - Desativa a proteção de mensagens</li><li>quando `Mode=Sign` - Sinais mas não encriptam a mensagem; deve ser usado quando a integridade dos dados é importante</li><li>quando `Mode=EncryptAndSign` - Assina e encripta a mensagem</li></ul></li></ul><p>Considere desligar a encriptação e apenas assinar a sua mensagem quando apenas precisa de validar a integridade da informação sem preocupações de confidencialidade. Isto pode ser útil para operações ou contratos de serviço em que você precisa validar o remetente original, mas nenhum dado sensível é transmitido. Ao reduzir o nível de proteção, tenha cuidado para que a mensagem não contenha quaisquer dados pessoais.</p>|

### <a name="example"></a>Exemplo
Configurar o serviço e a operação para assinar apenas a mensagem é mostrado nos seguintes exemplos. Exemplo do contrato de serviço: `ProtectionLevel.Sign` O seguinte é um exemplo de utilização do ProtectionLevel.Sign ao nível do Contrato de Serviço: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Operação Exemplo do Contrato de `ProtectionLevel.Sign` (para Controlo Granular): O seguinte é um exemplo de utilização `ProtectionLevel.Sign` ao nível da OperaçãoContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Utilize uma conta menos privilegiada para executar o seu serviço WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** Não gere serviços WCF sob conta de administração ou alta privilégio. em caso de compromisso de serviços, resultará em elevado impacto.</li><li>**RECOMENDAÇÕES:** Utilize uma conta menos privilegiada para hospedar o seu serviço WCF, pois reduzirá a superfície de ataque da sua aplicação e reduzirá os danos potenciais se for atacado. Se a conta de serviço necessitar de direitos de acesso adicionais sobre recursos de infraestrutura como o MSMQ, o registo de eventos, os contadores de desempenho e o sistema de ficheiros, devem ser dadas permissões adequadas a esses recursos para que o serviço WCF possa funcionar com sucesso.</li></ul><p>Se o seu serviço precisar de aceder a recursos específicos em nome do chamador original, use a personificação e a delegação para fluir a identidade do chamador para uma verificação de autorização a jusante. Num cenário de desenvolvimento, utilize a conta de serviço de rede local, que é uma conta especial incorporada que tem privilégios reduzidos. Num cenário de produção, crie uma conta de serviço de domínio personalizada menos privilegiada.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Forçar todo o tráfego para APIs web sobre conexão HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Aplicação do SSL num Controlador web de API](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passos** | Se uma aplicação tiver um HTTPS e uma ligação HTTP, os clientes ainda podem utilizar HTTP para aceder ao site. Para evitar isto, utilize um filtro de ação para garantir que os pedidos de APIs protegidos são sempre sobre HTTPS.|

### <a name="example"></a>Exemplo 
O seguinte código mostra um filtro de autenticação web API que verifica o TLS: 
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
Adicione este filtro a quaisquer ações da Web API que requeiram TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Certifique-se de que a comunicação com Azure Cache para Redis é sobre TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache do Azure para Redis | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte Azure Redis TLS](../../azure-cache-for-redis/cache-faq.md) |
| **Passos** | O servidor Redis não suporta TLS fora da caixa, mas Azure Cache para Redis tem. Se estiver a ligar-se ao Azure Cache para Redis e o seu cliente apoiar o TLS, como o StackExchange.Redis, então deve utilizar o TLS. Por predefinição, a porta não-TLS é desativada para a nova Cache Azure para instâncias Redis. Certifique-se de que os incumprimentos de segurança não são alterados a menos que exista uma dependência do suporte TLS para os clientes redis. |

Por favor, note que o Redis foi concebido para ser acedido por clientes de confiança dentro de ambientes fidedignos. Isto significa que normalmente não é boa ideia expor a instância Redis diretamente à internet ou, em geral, a um ambiente onde clientes não fidedtensivos possam aceder diretamente à porta Redis TCP ou à tomada UNIX. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Dispositivo seguro para a comunicação do Gateway de Campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para dispositivos baseados em IP, o protocolo de comunicação poderia normalmente ser encapsulado num canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não suportam SSL/TLS investigue se existem versões seguras do protocolo que fornecem segurança na camada de transporte ou de mensagem. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Dispositivo seguro para cloud gateway comunicação usando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Escolha o seu Protocolo de Comunicação](../../iot-hub/iot-hub-devguide.md) |
| **Passos** | Protocolos SECURE HTTP/AMQP ou MQTT utilizando SSL/TLS. |