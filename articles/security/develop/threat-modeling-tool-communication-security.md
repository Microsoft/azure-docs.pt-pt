---
title: Segurança de comunicação-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: mitigações para ameaças expostas no Threat Modeling Tool
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
ms.openlocfilehash: 54d34a120c575fd01f746131d909058951d1facf
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839244"
---
# <a name="security-frame-communication-security--mitigations"></a>Quadro de segurança: segurança de comunicação | Mitigações 
| Produto/serviço | Artigo |
| --------------- | ------- |
| **Hub de eventos do Azure** | <ul><li>[Proteger a comunicação com o Hub de eventos usando SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verificar os privilégios da conta de serviço e verificar se os serviços personalizados ou as páginas ASP.NET respeitam a segurança do CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Usar o gateway de gerenciamento de dados ao conectar SQL Server locais a Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Servidor de identidade** | <ul><li>[Verifique se todo o tráfego para o servidor de identidade está por conexão HTTPS](#identity-https)</li></ul> |
| **Aplicação Web** | <ul><li>[Verifique os certificados X. 509 usados para autenticar conexões SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurar o certificado SSL para o domínio personalizado no serviço de Azure App](#ssl-appservice)</li><li>[Forçar todo o tráfego para Azure App serviço pela conexão HTTPS](#appservice-https)</li><li>[Habilitar HSTS (segurança de transporte estrito) HTTP](#http-hsts)</li></ul> |
| **Base de Dados** | <ul><li>[Garantir a criptografia de conexão do SQL Server e a validação de certificado](#sqlserver-validation)</li><li>[Forçar a comunicação criptografada com o SQL Server](#encrypted-sqlserver)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir que a comunicação com o armazenamento do Azure seja por HTTPS](#comm-storage)</li><li>[Validar o hash MD5 depois de baixar o blob se não for possível habilitar o HTTPS](#md5-https)</li><li>[Usar o cliente compatível com SMB 3,0 para garantir a criptografia de dados em trânsito para compartilhamentos de arquivos do Azure](#smb-shares)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar fixação de certificado](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Habilitar HTTPS-proteger canal de transporte](#https-transport)</li><li>[WCF: definir nível de proteção de segurança de mensagem como EncryptAndSign](#message-protection)</li><li>[WCF: Use uma conta com privilégios mínimos para executar o serviço WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forçar todo o tráfego para APIs Web por conexão HTTPS](#webapi-https)</li></ul> |
| **Cache do Azure para Redis** | <ul><li>[Verifique se a comunicação com o cache do Azure para Redis é sobre SSL](#redis-ssl)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Proteger o dispositivo para comunicação de gateway de campo](#device-field)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Proteger o dispositivo para comunicação de gateway de nuvem usando SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Proteger a comunicação com o Hub de eventos usando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de segurança e autenticação dos hubs de eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Proteger conexões AMQP ou HTTP para o Hub de eventos usando SSL/TLS |

## <a id="priv-aspnet"></a>Verificar os privilégios da conta de serviço e verificar se os serviços personalizados ou as páginas ASP.NET respeitam a segurança do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Verificar os privilégios da conta de serviço e verificar se os serviços personalizados ou as páginas ASP.NET respeitam a segurança do CRM |

## <a id="sqlserver-factory"></a>Usar o gateway de gerenciamento de dados ao conectar SQL Server locais a Azure Data Factory

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Tipos de serviço vinculados-Azure e local |
| **Referências**              |[Movendo dados entre o local e o Azure data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), o [Gateway de gerenciamento de dados](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Passos** | <p>A ferramenta de gateway de Gerenciamento de Dados (DMG) é necessária para se conectar a fontes de dados que são protegidas por trás do corpnet ou de um firewall.</p><ol><li>O bloqueio da máquina isola a ferramenta DMG e impede que programas defeituosos danifiquem ou espionagem na máquina de fonte de dados. P. as atualizações mais recentes devem ser instaladas, habilitar as portas mínimas necessárias, provisionamento de contas controladas, auditoria habilitada, criptografia de disco habilitada, etc.)</li><li>A chave do gateway de dados deve ser girada em intervalos frequentes ou sempre que a senha da conta do serviço DMG for renovada</li><li>Os dados em trânsito por meio do serviço de link devem ser criptografados</li></ol> |

## <a id="identity-https"></a>Verifique se todo o tráfego para o servidor de identidade está por conexão HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3-chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3-implantação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | Por padrão, o IdentityServer exige que todas as conexões de entrada sejam recebidas por HTTPS. É absolutamente obrigatório que a comunicação com o IdentityServer seja feita apenas em transportes protegidos. Há alguns cenários de implantação, como o descarregamento de SSL, onde esse requisito pode ser reduzido. Consulte a página de implantação do servidor de identidade nas referências para obter mais informações. |

## <a id="x509-ssltls"></a>Verifique os certificados X. 509 usados para autenticar conexões SSL, TLS e DTLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os aplicativos que usam SSL, TLS ou DTLS devem verificar completamente os certificados X. 509 das entidades às quais eles se conectam. Isso inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e vencimento)</li><li>Status de revogação</li><li>Uso (por exemplo, autenticação de servidor para servidores, autenticação de cliente para clientes)</li><li>Cadeia de confiança. Os certificados devem ser encadeados a uma AC (autoridade de certificação) raiz que seja confiável para a plataforma ou explicitamente configurada pelo administrador</li><li>O comprimento da chave pública do certificado deve ser > 2048 bits</li><li>O algoritmo de hash deve ser SHA256 e superior |

## <a id="ssl-appservice"></a>Configurar o certificado SSL para o domínio personalizado no serviço de Azure App

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambientetype-Azure |
| **Referências**              | [Habilitar HTTPS para um aplicativo no serviço Azure App](../../app-service/configure-ssl-bindings.md) |
| **Passos** | Por padrão, o Azure já habilita o HTTPS para cada aplicativo com um certificado curinga para o domínio *. azurewebsites.net. No entanto, como todos os domínios curinga, ele não é tão seguro quanto usar um domínio personalizado com o [próprio certificado.](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/) É recomendável habilitar o SSL para o domínio personalizado no qual o aplicativo implantado será acessado|

## <a id="appservice-https"></a>Forçar todo o tráfego para Azure App serviço pela conexão HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambientetype-Azure |
| **Referências**              | [Impor HTTPS no serviço de Azure App](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Passos** | <p>Embora o Azure já habilite o HTTPS para os serviços de aplicativo do Azure com um certificado curinga para o domínio *. azurewebsites.net, ele não impõe HTTPS. Os visitantes ainda podem acessar o aplicativo usando HTTP, o que pode comprometer a segurança do aplicativo e, portanto, o HTTPS precisa ser aplicado explicitamente. Os aplicativos MVC ASP.NET devem usar o [filtro RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que força uma solicitação HTTP não segura a ser enviada novamente por HTTPS.</p><p>Como alternativa, o módulo de reescrita de URL, que está incluído com Azure App serviço, pode ser usado para impor HTTPS. O módulo de reescrita de URL permite que os desenvolvedores definam regras que são aplicadas a solicitações de entrada antes que as solicitações sejam entregues ao seu aplicativo. Regras de regravação de URL são definidas em um arquivo Web. config armazenado na raiz do aplicativo</p>|

### <a name="example"></a>Exemplo
O exemplo a seguir contém uma regra básica de reescrita de URL que força todo o tráfego de entrada para usar HTTPS
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
Essa regra funciona retornando um código de status HTTP 301 (redirecionamento permanente) quando o usuário solicita uma página usando HTTP. O 301 redireciona a solicitação para a mesma URL que o visitante solicitou, mas substitui a parte HTTP da solicitação por HTTPS. Por exemplo, `HTTP://contoso.com` seria Redirecionado para `HTTPS://contoso.com`. 

## <a id="http-hsts"></a>Habilitar HSTS (segurança de transporte estrito) HTTP

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Roteiro de segurança de transporte OWASP HTTP estrito](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Passos** | <p>A HSTS (segurança de transporte estrita) HTTP é um aprimoramento de segurança opcional que é especificado por um aplicativo Web por meio do uso de um cabeçalho de resposta especial. Quando um navegador com suporte recebe esse cabeçalho, o navegador impedirá que qualquer comunicação seja enviada por HTTP para o domínio especificado e, em vez disso, enviará todas as comunicações por HTTPS. Ele também impede que o HTTPS clique nos prompts nos navegadores.</p><p>Para implementar o HSTS, o cabeçalho de resposta a seguir deve ser configurado para um site globalmente, seja no código ou na configuração. Strict-Transport-Security: Max-age = 300; includeSubDomains HSTS aborda as seguintes ameaças:</p><ul><li>Os indicadores do usuário ou os tipos manualmente https://example.com e estão sujeitos a um invasor Man-in-the-middle: o HSTS redireciona automaticamente as solicitações HTTP para HTTPS para o domínio de destino</li><li>O aplicativo Web destinado a ser puramente HTTPS, inadvertidamente, contém links HTTP ou fornece conteúdo por HTTP: HSTS redireciona automaticamente as solicitações HTTP para HTTPS para o domínio de destino</li><li>Um invasor Man-in-the-Middle tenta interceptar o tráfego de um usuário vítima usando um certificado inválido e espera que o usuário aceite o certificado incorreto: HSTS não permite que um usuário substitua a mensagem de certificado inválida</li></ul>|

## <a id="sqlserver-validation"></a>Garantir a criptografia de conexão do SQL Server e a validação de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure  |
| **Atributos**              | Versão do SQL-V12 |
| **Referências**              | [Práticas recomendadas sobre a gravação de cadeias de conexão seguras para o banco de dados SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Passos** | <p>Todas as comunicações entre o banco de dados SQL e um aplicativo cliente são criptografadas usando protocolo SSL (SSL) em todos os momentos. O banco de dados SQL não dá suporte a conexões não criptografadas. Para validar certificados com código ou ferramentas do aplicativo, solicite explicitamente uma conexão criptografada e não confie nos certificados do servidor. Se o código do aplicativo ou as ferramentas não solicitarem uma conexão criptografada, eles ainda receberão conexões criptografadas</p><p>No entanto, eles não podem validar os certificados do servidor e, portanto, serão suscetíveis a ataques "Man-in-the-middle". Para validar certificados com o código do aplicativo ADO.NET, defina `Encrypt=True` e `TrustServerCertificate=False` na cadeia de conexão do banco de dados. Para validar certificados por meio do SQL Server Management Studio, abra a caixa de diálogo conectar ao servidor. Clique em criptografar conexão na guia Propriedades da conexão</p>|

## <a id="encrypted-sqlserver"></a>Forçar a comunicação criptografada com o SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL-MsSQL2016, versão do SQL-MsSQL2012, versão do SQL-MsSQL2014 |
| **Referências**              | [Habilitar conexões criptografadas para o Mecanismo de Banco de Dados](https://msdn.microsoft.com/library/ms191192)  |
| **Passos** | Habilitar a criptografia SSL aumenta a segurança dos dados transmitidos entre redes entre instâncias de SQL Server e aplicativos. |

## <a id="comm-storage"></a>Garantir que a comunicação com o armazenamento do Azure seja por HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Criptografia no nível de transporte do armazenamento do Azure – usando HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Passos** | Para garantir a segurança dos dados de armazenamento do Azure em trânsito, use sempre o protocolo HTTPS ao chamar as APIs REST ou acessar objetos no armazenamento. Além disso, as assinaturas de acesso compartilhado, que podem ser usadas para delegar acesso aos objetos de armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser usado ao usar assinaturas de acesso compartilhado, garantindo que qualquer pessoa que enviar links com tokens SAS usará o protocolo adequado.|

## <a id="md5-https"></a>Validar o hash MD5 depois de baixar o blob se não for possível habilitar o HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Storagetype-blob |
| **Referências**              | [Visão geral do Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Passos** | <p>O serviço blob do Windows Azure fornece mecanismos para garantir a integridade dos dados no aplicativo e nas camadas de transporte. Se por algum motivo você precisar usar HTTP em vez de HTTPS e estiver trabalhando com blobs de bloco, poderá usar a verificação MD5 para ajudar a verificar a integridade dos BLOBs que estão sendo transferidos</p><p>Isso ajudará na proteção contra erros de camada de rede/transporte, mas não necessariamente com ataques intermediários. Se você puder usar HTTPS, que fornece segurança em nível de transporte, o uso da verificação MD5 será redundante e desnecessário.</p>|

## <a id="smb-shares"></a>Usar o cliente compatível com SMB 3,0 para garantir a criptografia de dados em trânsito para compartilhamentos de arquivos do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Storagetype-arquivo |
| **Referências**              | [Armazenamento de arquivos do Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [suporte SMB do armazenamento de arquivos do Azure para clientes Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Passos** | O armazenamento de arquivos do Azure dá suporte a HTTPS ao usar a API REST, mas é mais comumente usado como um compartilhamento de arquivos SMB anexado a uma VM. O SMB 2,1 não dá suporte à criptografia, portanto, as conexões só são permitidas na mesma região no Azure. No entanto, o SMB 3,0 dá suporte à criptografia e pode ser usado com o Windows Server 2012 R2, o Windows 8, o Windows 8.1 e o Windows 10, permitindo acesso entre regiões e até mesmo acesso na área de trabalho. |

## <a id="cert-pinning"></a>Implementar fixação de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Windows Phone |
| **Atributos**              | N/D  |
| **Referências**              | [Fixação de certificado e chave pública](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Passos** | <p>A fixação de certificado defende contra ataques MITM (Man-in-the-Middle). A fixação é o processo de associar um host ao certificado X509 ou à chave pública esperada. Depois que um certificado ou uma chave pública é conhecido ou visto para um host, o certificado ou a chave pública é associado ou ' fixado ' ao host. </p><p>Assim, quando um adversário tenta realizar um ataque de MITM SSL, durante o handshake de SSL, a chave do servidor do invasor será diferente da chave do certificado fixado e a solicitação será descartada, impedindo que a fixação do certificado MITM possa ser obtida por Implementando `ServerCertificateValidationCallback` delegado do ServicePointManager.</p>|

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

## <a id="https-transport"></a>Habilitar HTTPS-proteger canal de transporte

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Passos** | A configuração do aplicativo deve garantir que o HTTPS seja usado para todo o acesso a informações confidenciais.<ul><li>**Explicação:** Se um aplicativo lida com informações confidenciais e não usa criptografia no nível de mensagem, ele só deve ter permissão para se comunicar por meio de um canal de transporte criptografado.</li><li>**Recomendações:** Verifique se o transporte HTTP está desabilitado e habilite o transporte HTTPS. Por exemplo, substitua o `<httpTransport/>` por `<httpsTransport/>` marca. Não confie em uma configuração de rede (firewall) para garantir que o aplicativo só possa ser acessado por um canal seguro. De um ponto de vista filosofia, o aplicativo não deve depender da rede para sua segurança.</li></ul><p>Do ponto de vista prático, as pessoas responsáveis pela proteção da rede nem sempre rastreiam os requisitos de segurança do aplicativo à medida que eles evoluem.</p>|

## <a id="message-protection"></a>WCF: definir nível de proteção de segurança de mensagem como EncryptAndSign

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Passos** | <ul><li>**Explicação:** Quando o nível de proteção estiver definido como "nenhum", ele desabilitará a proteção de mensagem. A confidencialidade e a integridade são obtidas com o nível apropriado de configuração.</li><li>**RECOMMENDATIONS**<ul><li>Quando `Mode=None`-desabilita a proteção de mensagem</li><li>Quando `Mode=Sign`, mas não criptografa a mensagem; deve ser usado quando a integridade dos dados é importante</li><li>Quando `Mode=EncryptAndSign` assina e criptografa a mensagem</li></ul></li></ul><p>Considere desativar a criptografia e apenas assinar sua mensagem quando precisar apenas validar a integridade das informações sem preocupações com a confidencialidade. Isso pode ser útil para operações ou contratos de serviço nos quais você precisa validar o remetente original, mas nenhum dado confidencial é transmitido. Ao reduzir o nível de proteção, tenha cuidado para que a mensagem não contenha dados pessoais.</p>|

### <a name="example"></a>Exemplo
A configuração do serviço e a operação para assinar apenas a mensagem é mostrada nos exemplos a seguir. Exemplo de contrato de serviço de `ProtectionLevel.Sign`: Veja a seguir um exemplo de como usar ProtectionLevel. Sign no nível do contrato de serviço: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Exemplo de contrato de operação de `ProtectionLevel.Sign` (para controle granular): Veja a seguir um exemplo de como usar `ProtectionLevel.Sign` no nível de OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Use uma conta com privilégios mínimos para executar o serviço WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Passos** | <ul><li>**Explicação:** Não execute os serviços do WCF em conta de administrador ou de alto privilégio. no caso dos serviços, o comprometimento resultará em alto impacto.</li><li>**Recomendações:** Use uma conta com privilégios mínimos para hospedar seu serviço WCF, pois ela reduzirá a superfície de ataque do aplicativo e reduzirá o dano potencial se você for atacado. Se a conta de serviço exigir direitos de acesso adicionais sobre recursos de infraestrutura, como MSMQ, o log de eventos, contadores de desempenho e o sistema de arquivos, as permissões apropriadas devem ser dadas a esses recursos para que o serviço WCF possa ser executado com êxito.</li></ul><p>Se o serviço precisar acessar recursos específicos em nome do chamador original, use representação e delegação para fluir a identidade do chamador para uma verificação de autorização de downstream. Em um cenário de desenvolvimento, use a conta de serviço de rede local, que é uma conta interna especial que tem privilégios reduzidos. Em um cenário de produção, crie uma conta de serviço de domínio personalizado com privilégios mínimos.</p>|

## <a id="webapi-https"></a>Forçar todo o tráfego para APIs Web por conexão HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impondo SSL em um controlador de API da Web](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passos** | Se um aplicativo tiver uma associação HTTPS e HTTP, os clientes ainda poderão usar HTTP para acessar o site. Para evitar isso, use um filtro de ação para garantir que as solicitações para APIs protegidas sejam sempre por HTTPS.|

### <a name="example"></a>Exemplo 
O código a seguir mostra um filtro de autenticação de API Web que verifica o SSL: 
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
Adicione este filtro a qualquer ação da API Web que exija SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Verifique se a comunicação com o cache do Azure para Redis é sobre SSL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache do Azure para Redis | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte ao SSL do Azure Redis](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Passos** | O servidor Redis não dá suporte ao SSL pronto para uso, mas o cache do Azure para Redis. Se você estiver se conectando ao cache do Azure para Redis e seu cliente oferecer suporte a SSL, como StackExchange. Redis, você deve usar SSL. Por padrão, a porta não SSL está desabilitada para o novo cache do Azure para instâncias de Redis. Certifique-se de que os padrões seguros não sejam alterados, a menos que haja uma dependência no suporte a SSL para clientes Redis. |

Observe que o Redis foi projetado para ser acessado por clientes confiáveis dentro de ambientes confiáveis. Isso significa que geralmente não é uma boa ideia expor a instância Redis diretamente à Internet ou, em geral, a um ambiente em que clientes não confiáveis possam acessar diretamente a porta TCP Redis ou o soquete UNIX. 

## <a id="device-field"></a>Proteger o dispositivo para comunicação de gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para dispositivos baseados em IP, o protocolo de comunicação normalmente poderia ser encapsulado em um canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não oferecem suporte a SSL/TLS, investigue se há versões seguras do protocolo que fornecem segurança no transporte ou na camada de mensagem. |

## <a id="device-cloud"></a>Proteger o dispositivo para comunicação de gateway de nuvem usando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Escolha seu protocolo de comunicação](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Passos** | Proteger os protocolos HTTP/AMQP ou MQTT usando SSL/TLS. |
