---
title: Dados Sensíveis - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a mitigação de dados sensíveis na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3ee0a376b28676e3f74ac2e53010c2c953ad9d7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317825"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Quadro de segurança: Dados sensíveis / Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança da máquina** | <ul><li>[Certifique-se de que os binários são obfuscados se contiverem informações sensíveis](#binaries-info)</li><li>[Considere usar o Sistema de Ficheiros Encriptados (EFS) usado para proteger dados confidenciais específicos do utilizador](#efs-user)</li><li>[Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros são encriptados](#filesystem)</li></ul> | 
| **Aplicação Web** | <ul><li>[Certifique-se de que o conteúdo sensível não está em cache no navegador](#cache-browser)</li><li>[Criptografe secções dos ficheiros de configuração da Web App que contenham dados sensíveis](#encrypt-data)</li><li>[Desativar explicitamente o atributo HTML auto-completa em formas e entradas sensíveis](#autocomplete-input)</li><li>[Certifique-se de que os dados sensíveis apresentados no ecrã do utilizador são mascarados](#data-mask)</li></ul> | 
| **Base de dados** | <ul><li>[Implementar máscaras dinâmicas de dados para limitar utilizadores sensíveis de exposição a dados não privilegiados](#dynamic-users)</li><li>[Certifique-se de que as palavras-passe são armazenadas em formato de haxixe salgado](#salted-hash)</li><li>[Certifique-se de que os dados sensíveis nas colunas de base de dados são encriptados](#db-encrypted)</li><li>[Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada](#tde-enabled)</li><li>[Certifique-se de que as cópias de segurança da base de dados são encriptadas](#backup)</li></ul> | 
| **API Web** | <ul><li>[Certifique-se de que os dados sensíveis relevantes para a API Web não são armazenados no armazenamento do navegador](#api-browser)</li></ul> | 
| Documento Azure DB | <ul><li>[Criptografe dados sensíveis armazenados em Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Fronteira da Confiança Azure IaaS VM Boundary** | <ul><li>[Use encriptação do disco Azure para encriptar discos utilizados por Máquinas Virtuais](#disk-vm)</li></ul> | 
| **Limite de confiança do tecido de serviço** | <ul><li>[Criptografe segredos em aplicações de Tecido de Serviço](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Realizar modelação de segurança e utilizar Unidades/Equipas de Negócio sempre que necessário](#modeling-teams)</li><li>[Minimizar o acesso à funcionalidade de partilha em entidades críticas](#entities)</li><li>[Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança](#good-practices)</li><li>[Inclua uma regra de regra de desenvolvimento proscrevindo detalhes config na gestão de exceções](#exception-mgmt)</li></ul> | 
| **Armazenamento do Azure** | <ul><li>[Utilize encriptação do serviço de armazenamento Azure (SSE) para dados em repouso (pré-visualização)](#sse-preview)</li><li>[Utilize Client-Side Encriptação para armazenar dados sensíveis no Azure Storage](#client-storage)</li></ul> | 
| **Cliente Móvel** | <ul><li>[Encriptar dados sensíveis ou PII escritos para telefones armazenamento local](#pii-phones)</li><li>[Obfuscate gerou binários antes de distribuir aos utilizadores finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir clienteTipo de Identificação Para Certificado ou Janelas](#cert)</li><li>[O modo de segurança WCF não está ativado](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Certifique-se de que os binários são obfuscados se contiverem informações sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários são obfuscados se contiverem informações sensíveis, tais como segredos comerciais, lógicas comerciais sensíveis que não devem ser invertidas. Isto é para parar a engenharia inversa das assembleias. Ferramentas como `CryptoObfuscator` esta podem ser utilizadas para este fim. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Considere usar o Sistema de Ficheiros Encriptados (EFS) usado para proteger dados confidenciais específicos do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Considere a utilização do Sistema de Ficheiros Encriptados (EFS) para proteger dados confidenciais específicos do utilizador de adversários com acesso físico ao computador. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros são encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros são encriptados (por exemplo, utilizando o DPAPI), se o EFS não puder ser aplicado |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Certifique-se de que o conteúdo sensível não está em cache no navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, Formulários Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os navegadores podem armazenar informações para fins de caching e histórico. Estes ficheiros em cache são armazenados numa pasta, como a pasta Ficheiros de Internet Temporária no caso do Internet Explorer. Quando estas páginas são remetidas novamente, o navegador exibe-as a partir da sua cache. Se forem apresentadas informações sensíveis ao utilizador (como morada, dados do cartão de crédito, Número de Segurança Social ou nome de utilizador), então esta informação poderá ser armazenada na cache do navegador e, portanto, recuperável através da análise da cache do navegador ou simplesmente premindo o botão "Back" do navegador. Desa cos para todas as páginas, desate o valor do cabeçalho de resposta do controlo de cache para "não armazenar". |

### <a name="example"></a>Exemplo
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Exemplo
Isto pode ser implementado através de um filtro. Podem ser utilizados os seguintes exemplos: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Criptografe secções dos ficheiros de configuração da Web App que contenham dados sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Criptografar secções de configuração em ASP.NET 2.0 Utilizando o DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Especificando um Fornecedor de Configuração Protegido](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Usando o cofre de chave Azure para proteger os segredos da aplicação](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Ficheiros de configuração como o Web.config, appsettings.jsligados são frequentemente utilizados para conter informações confidenciais, incluindo nomes de utilizador, palavras-passe, cadeias de ligação de bases de dados e chaves de encriptação. Se não proteger estas informações, a sua aplicação é vulnerável a intrusos ou utilizadores maliciosos que obtenham informações sensíveis, tais como nomes de utilizadores de conta e palavras-passe, nomes de bases de dados e nomes de servidores. Com base no tipo de implementação (azure/on-prem), criptografe as secções sensíveis de ficheiros config usando DPAPI ou serviços como o Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Desativar explicitamente o atributo HTML auto-completa em formas e entradas sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN: atributo autocompleto](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Utilização Automática em HTML](https://msdn.microsoft.com/library/ms533032.aspx), [Vulnerabilidade de Sanitização HTML,](https://technet.microsoft.com/security/bulletin/MS10-071) [Autocomplete.,,again?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Passos** | O atributo auto-completa especifica se um formulário deve ter um preenrígio automático ligado ou desligado. Quando o preentro está ligado automaticamente, o navegador completa automaticamente valores com base nos valores que o utilizador já introduziu anteriormente. Por exemplo, quando um novo nome e senha é introduzido num formulário e o formulário é submetido, o navegador pergunta se a palavra-passe deve ser guardada. Posteriormente, quando o formulário é apresentado, o nome e a palavra-passe são preenchidos automaticamente ou são preenchidos à medida que o nome é introduzido. Um intruso com acesso local poderia obter a senha de texto clara a partir da cache do navegador. Por predefinição, o preconto automático está ativado e deve ser explicitamente desativado. |

### <a name="example"></a>Exemplo
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Certifique-se de que os dados sensíveis apresentados no ecrã do utilizador são mascarados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Dados sensíveis como palavras-passe, números de cartão de crédito, SSN, etc. devem ser mascarados quando apresentados no ecrã. Isto é para evitar que o pessoal não autorizado aceda aos dados (por exemplo, palavras-passe para navegar no ombro, apoiar o pessoal que vê os números de utilizadores da SSN) . Certifique-se de que estes elementos de dados não são visíveis em texto simples e estão devidamente mascarados. Isto tem de ser tomado em consideração, aceitando-os como entrada (por exemplo, .. inserir tipo="password") bem como exibir de volta no ecrã (por exemplo, mostrar apenas os últimos 4 dígitos do número do cartão de crédito). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementar máscaras dinâmicas de dados para limitar utilizadores sensíveis de exposição a dados não privilegiados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, VERSÃO SQL - MsSQL2016 |
| **Referências**              | [Mascaramento dinâmico de dados](https://msdn.microsoft.com/library/mt130841) |
| **Passos** | O objetivo da mascaração dinâmica de dados é limitar a exposição de dados sensíveis, impedindo os utilizadores que não devem ter acesso aos dados de visualização dos mesmos. A mascaração dinâmica de dados não visa impedir que os utilizadores de bases de dados se conectem diretamente à base de dados e executem consultas exaustivas que exponham peças dos dados sensíveis. A máscara dinâmica de dados é complementar a outras funcionalidades de segurança do SQL Server (auditoria, encriptação, segurança ao nível da linha...) e é altamente recomendado utilizar esta funcionalidade em conjunto com eles, além de melhor proteger os dados sensíveis na base de dados. Por favor, note que esta funcionalidade é suportada apenas pelo SQL Server a partir de 2016 e Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Certifique-se de que as palavras-passe são armazenadas em formato de haxixe salgado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Hashing de palavra-passe usando .NET Crypto APIs](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Passos** | As palavras-passe não devem ser armazenadas nas bases de dados de lojas de utilizadores personalizadas. Os hashes de palavra-passe devem ser armazenados com valores de sal. Certifique-se de que o sal para o utilizador é sempre único e aplica b-cripta, s-crypt ou PBKDF2 antes de armazenar a palavra-passe, com uma contagem mínima de iteração de fator de trabalho de 150.000 loops para eliminar a possibilidade de força bruta.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Certifique-se de que os dados sensíveis nas colunas de base de dados são encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Versão SQL - Todos |
| **Referências**              | [Encriptar dados sensíveis no servidor SQL,](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx) [Como: Encriptar uma Coluna de Dados no SqL Server](https://msdn.microsoft.com/library/ms179331), [Criptografar por Certificado](https://msdn.microsoft.com/library/ms188061) |
| **Passos** | Dados sensíveis, como números de cartões de crédito, devem ser encriptados na base de dados. Os dados podem ser encriptados utilizando encriptação ao nível da coluna ou por uma função de aplicação utilizando as funções de encriptação. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Compreender a encriptação de dados transparentes do sql server (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Passos** | A funcionalidade de Encriptação de Dados Transparente (TDE) no servidor SQL ajuda a encriptar dados confidenciais numa base de dados e a proteger as chaves que são usadas para encriptar os dados com um certificado. Isto evita que qualquer pessoa sem as chaves utilize os dados. O TDE protege os dados "em repouso", ou seja, os ficheiros de dados e registos. Fornece a capacidade de cumprir muitas leis, regulamentos e orientações estabelecidas em várias indústrias. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Certifique-se de que as cópias de segurança da base de dados são encriptadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, VERSÃO SQL - MsSQL2014 |
| **Referências**              | [Encriptação de backup de base de dados SQL](https://msdn.microsoft.com/library/dn449489) |
| **Passos** | O SQL Server tem a capacidade de encriptar os dados enquanto cria uma cópia de segurança. Ao especificar o algoritmo de encriptação e o encriptador (um Certificado ou Chave Assimétrica) ao criar uma cópia de segurança, pode-se criar um ficheiro de backup encriptado. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Certifique-se de que os dados sensíveis relevantes para a API Web não são armazenados no armazenamento do navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | Fornecedor de identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | N/D  |
| **Passos** | <p>Em certas implementações, os artefactos sensíveis relevantes para a autenticação da Web API são armazenados no armazenamento local do navegador. Por exemplo, artefactos de autenticação AD AD como adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Todos estes artefactos estão disponíveis mesmo depois de a assinatura ou o navegador estar fechado. Se um adversário tiver acesso a estes artefactos, pode reutilizá-los para aceder aos recursos protegidos (APIs). Certifique-se de que todos os artefactos sensíveis relacionados com a API Web não são armazenados no armazenamento do navegador. Nos casos em que o armazenamento do lado do cliente é inevitável (por exemplo, aplicações de página única (SPA) que alavancam os fluxos Implicit OpenIdConnect/OAuth precisam de armazenar tokens de acesso localmente), use escolhas de armazenamento sem ter persistência. por exemplo, prefere sessionStorage a LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O snippet javaScript abaixo é de uma biblioteca de autenticação personalizada que armazena artefactos de autenticação no armazenamento local. Estas implementações devem ser evitadas. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Criptografe dados sensíveis armazenados em Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Criptografe dados sensíveis ao nível da aplicação antes de armazenar no documento DB ou armazenar quaisquer dados sensíveis em outras soluções de armazenamento como o Azure Storage ou o Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Use encriptação do disco Azure para encriptar discos utilizados por Máquinas Virtuais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira da Confiança Azure IaaS VM Boundary | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Utilização da Encriptação do Disco Azure para encriptar discos utilizados pelas suas máquinas virtuais](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Passos** | <p>A Azure Disk Encryption é uma nova funcionalidade que está atualmente em pré-visualização. Esta funcionalidade permite encriptar os discos de OS e os discos de dados utilizados por uma Máquina Virtual IaaS. Para o Windows, as unidades são encriptadas utilizando a tecnologia de encriptação BitLocker padrão da indústria. Para o Linux, os discos são encriptados utilizando a tecnologia DM-Crypt. Isto está integrado com o Azure Key Vault para permitir controlar e gerir as chaves de encriptação do disco. A solução de encriptação do disco Azure suporta os seguintes três cenários de encriptação do cliente:</p><ul><li>Ativar a encriptação em novos VMs IaaS criados a partir de ficheiros VHD encriptados pelo cliente e chaves de encriptação fornecidas pelo cliente, que são armazenadas no Cofre da Chave Azure.</li><li>Ativar a encriptação em novos IaaS VMs criados a partir do Mercado Azure.</li><li>Ativar a encriptação dos VMs existentes já em execução em Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Criptografe segredos em aplicações de Tecido de Serviço

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Gestão de segredos em aplicações de Tecido de Serviço](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Passos** | Os segredos podem ser qualquer informação sensível, como cadeias de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples. Utilize o Cofre de Chaves Azure para gerir chaves e segredos em aplicações de tecido de serviço. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Realizar modelação de segurança e utilizar Unidades/Equipas de Negócio sempre que necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Realizar modelação de segurança e utilizar Unidades/Equipas de Negócio sempre que necessário |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimizar o acesso à funcionalidade de partilha em entidades críticas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Minimizar o acesso à funcionalidade de partilha em entidades críticas |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Inclua uma regra de regra de desenvolvimento proscrevindo detalhes config na gestão de exceções

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Inclua uma regra de regra de desenvolvimento proscrevindo detalhes config na gestão de exceções fora do desenvolvimento. Teste para tal como parte de revisões de código ou inspeção periódica.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Utilize encriptação do serviço de armazenamento Azure (SSE) para dados em repouso (pré-visualização)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Dispositivo de Armazenamento - Blob |
| **Referências**              | [Encriptação do serviço de armazenamento Azure para dados em repouso (pré-visualização)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Passos** | <p>A Azure Storage Service Encryption (SSE) for Data at Rest ajuda-o a proteger e a salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Com esta funcionalidade, o Armazenamento do Azure encripta automaticamente os dados antes de continuar a armazenar e desencriptar antes da obtenção. A encriptação, desencriptação e gestão de chaves é totalmente transparente para os utilizadores. A SSE aplica-se apenas a bolhas de blocos, bolhas de página e bolhas de apêndice. Os outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.</p><p>Fluxo de trabalho de encriptação e desencriptação:</p><ul><li>O cliente permite encriptação na conta de armazenamento</li><li>Quando o cliente escreve novos dados (PUT Blob, PUT Block, PUT Page, etc.) para o armazenamento blob; cada escrita é encriptada usando encriptação AES de 256 bits, uma das cifras de bloco mais fortes disponíveis</li><li>Quando o cliente precisa de aceder aos dados (GET Blob, etc.), os dados são automaticamente desencriptados antes de regressarem ao utilizador</li><li>Se a encriptação for desativada, as novas escritas deixaram de ser encriptadas e os dados encriptados existentes permanecem encriptados até serem reescritos pelo utilizador. Enquanto a encriptação estiver ativada, as escritas para o armazenamento blob serão encriptadas. O estado dos dados não muda com o utilizador a mexer entre permitir/desativar a encriptação para a conta de armazenamento</li><li>Todas as chaves de encriptação são armazenadas, encriptadas e geridas pela Microsoft</li></ul><p>Por favor, note que neste momento, as chaves utilizadas para a encriptação são geridas pela Microsoft. A Microsoft gera as teclas originalmente e gere o armazenamento seguro das teclas, bem como a rotação regular, tal como definida pela política interna da Microsoft. No futuro, os clientes terão a capacidade de gerir as suas próprias chaves de encriptação >e fornecer um caminho de migração das chaves geridas pela Microsoft para as chaves geridas pelo cliente.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Utilize Client-Side Encriptação para armazenar dados sensíveis no Azure Storage

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação do lado do cliente e cofre de chave Azure para armazenamento de Azure do Microsoft](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Tutorial: Criptografe e desencripta as bolhas no Armazenamento Azure da Microsoft usando o Cofre da Chave Azure](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [armazenando dados de forma segura no armazenamento de blob Azure com extensões de encriptação Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Passos** | <p>A Biblioteca do Cliente de Armazenamento Azure para o pacote .NET Nuget suporta encriptar dados dentro das aplicações do cliente antes de fazer o upload para o Azure Storage e desencriptar dados enquanto faz o download para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Aqui está uma breve descrição de como a encriptação do lado do cliente funciona:</p><ul><li>O cliente Azure Storage SDK gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único</li><li>Os dados do cliente são encriptados usando este CEK</li><li>O CEK é então embrulhado (encriptado) utilizando a chave de encriptação (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso ao KEK. Apenas invoca o algoritmo de embrulho chave que é fornecido pela Key Vault. Os clientes podem optar por utilizar fornecedores personalizados para o embrulho/desembrulho das chaves, se quiserem</li><li>Os dados encriptados são então enviados para o serviço de Armazenamento Azure. Consulte os links na secção de referências para obter detalhes de implementação de baixo nível.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Encriptar dados sensíveis ou PII escritos para telefones armazenamento local

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico  |
| **Atributos**              | N/D  |
| **Referências**              | [Gerir definições e funcionalidades nos seus dispositivos com as políticas Microsoft Intune](https://docs.microsoft.com/mem/intune/configuration/), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **Passos** | <p>Se a aplicação escrever informações sensíveis como PII do utilizador (e-mail, número de telefone, nome próprio, apelido, preferências, etc.) - no sistema de ficheiros do telemóvel, deve ser encriptado antes de escrever para o sistema de ficheiros local. Se a aplicação for uma aplicação da empresa, explore a possibilidade de publicar a aplicação usando o Windows Intune.</p>|

### <a name="example"></a>Exemplo
O Intune pode ser configurado com as seguintes políticas de segurança para salvaguardar dados sensíveis: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se a aplicação não for uma aplicação da empresa, utilize a plataforma fornecida como keystore, porta-chaves para armazenar chaves de encriptação, utilizando o funcionamento criptográfico que pode ser realizado no sistema de ficheiros. O corte de código que segue mostra como aceder à chave a partir do chaveiro utilizando xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Obfuscate gerou binários antes de distribuir aos utilizadores finais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Cripto Obfuscação para .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Passos** | Os binários gerados (conjuntos dentro da APK) devem ser obfuscados para parar a engenharia inversa dos conjuntos. Ferramentas como `CryptoObfuscator` esta podem ser utilizadas para este fim. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Definir clienteTipo de Identificação Para Certificado ou Janelas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [Fortificar](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Passos** | A utilização de um UsernameToken com uma palavra-passe de texto simples sobre um canal não encriptado expõe a palavra-passe a atacantes que podem cheirar as mensagens SOAP. Os Fornecedores de Serviços que utilizem o Nome de UtilizadorToken podem aceitar palavras-passe enviadas em texto simples. O envio de palavras-passe de texto simples sobre um canal não encriptado pode expor a credencial a atacantes que podem cheirar a mensagem SOAP. | 

### <a name="example"></a>Exemplo
A seguinte configuração do prestador de serviços WCF utiliza o Nome de UtilizadorToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Desconfiem do Cliente Tipôm de Identificação para Certificado ou Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>O modo de segurança WCF não está ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Modo de Segurança - Transporte, Modo de Segurança - Mensagem |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reino fortificado,](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) [Fundamentos da WcF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Passos** | Não foi definida qualquer segurança de transporte ou mensagem. As aplicações que transmitem mensagens sem segurança de transporte ou mensagem não podem garantir a integridade ou confidencialidade das mensagens. Quando uma ligação de segurança do WCF é definida para Nenhuma, tanto a segurança do transporte como da mensagem são desativadas. |

### <a name="example"></a>Exemplo
A seguinte configuração define o modo de segurança para Nenhum. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Exemplo
Modo de Segurança Em todas as ligações de serviço existem cinco possíveis modos de segurança: 
* Nenhum. Desliga a segurança. 
* O transporte. Utiliza a segurança do transporte para a autenticação mútua e a proteção de mensagens. 
* Mensagem. Utiliza a segurança da mensagem para autenticação mútua e proteção de mensagens. 
* Os dois. Permite-lhe fornecer definições para segurança ao nível de transporte e de mensagens (apenas o MSMQ o suporta). 
* TransportWithMessageCredential. As credenciais são passadas com a proteção da mensagem e da mensagem e a autenticação do servidor são fornecidas pela camada de transporte. 
* TransportesCredentia Apenas. As credenciais do cliente são passadas com a camada de transporte e não é aplicada nenhuma proteção de mensagens. Utilize a segurança de transporte e mensagem para proteger a integridade e confidencialidade das mensagens. A configuração abaixo diz ao serviço para usar a segurança do transporte com credenciais de mensagem.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
