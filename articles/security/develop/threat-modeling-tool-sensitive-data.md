---
title: Dados Sensíveis - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
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
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502224"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Quadro de Segurança: Dados Sensíveis [ Dados Sensíveis] Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança de máquina** | <ul><li>[Certifique-se de que os binários são obfuscados se contiverem informações sensíveis](#binaries-info)</li><li>[Considere utilizar o Sistema de Ficheiros Encriptados (EFS) para proteger dados confidenciais específicos do utilizador](#efs-user)</li><li>[Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros estão encriptados](#filesystem)</li></ul> | 
| **Aplicação Web** | <ul><li>[Certifique-se de que o conteúdo sensível não está em cache no navegador](#cache-browser)</li><li>[Criptografe secções dos ficheiros de configuração da Web App que contenham dados sensíveis](#encrypt-data)</li><li>[Desativar explicitamente o atributo HTML autocompleto em formas e inputs sensíveis](#autocomplete-input)</li><li>[Certifique-se de que os dados sensíveis apresentados no ecrã do utilizador são mascarados](#data-mask)</li></ul> | 
| **Base de Dados** | <ul><li>[Implementar máscaras dinâmicas de dados para limitar a exposição a utilizadores não privilegiados de dados sensíveis](#dynamic-users)</li><li>[Certifique-se de que as palavras-passe são armazenadas em formato hash salgado](#salted-hash)</li><li>[Certifique-se de que os dados sensíveis nas colunas de base de dados estão encriptados](#db-encrypted)</li><li>[Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada](#tde-enabled)</li><li>[Certifique-se de que as cópias de segurança da base de dados estão encriptadas](#backup)</li></ul> | 
| **API Web** | <ul><li>[Certifique-se de que os dados sensíveis relevantes para a Web API não são armazenados no armazenamento do navegador](#api-browser)</li></ul> | 
| Documento Azure DB | <ul><li>[Criptografe dados confidenciais armazenados em Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Fronteira de confiança Azure IaaS VM** | <ul><li>[Utilize encriptação de disco azure para encriptar discos usados por máquinas virtuais](#disk-vm)</li></ul> | 
| **Limite de confiança de tecido de serviço** | <ul><li>[Criptografe segredos em aplicações de Tecido de Serviço](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Execute modelação de segurança e utilize Unidades/Equipas de Negócio supérbio, sempre que necessário](#modeling-teams)</li><li>[Minimizar o acesso à funcionalidade de partilha em entidades críticas](#entities)</li><li>[Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança](#good-practices)</li><li>[Incluir uma regra de padrões de desenvolvimento proscribing mostrando detalhes da config na gestão de exceção](#exception-mgmt)</li></ul> | 
| **Storage do Azure** | <ul><li>[Utilize encriptação do serviço de armazenamento Azure (SSE) para dados em repouso (pré-visualização)](#sse-preview)</li><li>[Use encriptação do lado do cliente para armazenar dados confidenciais no Armazenamento Azure](#client-storage)</li></ul> | 
| **Cliente Móvel** | <ul><li>[Criptografe dados sensíveis ou PII escritos para telefones armazenamento local](#pii-phones)</li><li>[Obfuscate gerou binários antes de distribuir para utilizadores finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir clienteCredentialType para Certificado ou Windows](#cert)</li><li>[Modo WCF-Segurança não está ativado](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Certifique-se de que os binários são obfuscados se contiverem informações sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários são obfuscados se contiverem informações sensíveis, tais como segredos comerciais, lógica sucutória de negócios que não deve inverter. Isto é para parar a engenharia inversa de conjuntos. Ferramentas `CryptoObfuscator` como podem ser usadas para este fim. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Considere utilizar o Sistema de Ficheiros Encriptados (EFS) para proteger dados confidenciais específicos do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Considere a utilização do Sistema de Ficheiros Encriptados (EFS) para proteger dados confidenciais específicos do utilizador de adversários com acesso físico ao computador. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros estão encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os dados sensíveis armazenados pela aplicação no sistema de ficheiros são encriptados (por exemplo, utilizando dPAPI), se o EFS não puder ser aplicado |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Certifique-se de que o conteúdo sensível não está em cache no navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, Formulários Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os navegadores podem armazenar informações para fins de cache e histórico. Estes ficheiros em cache são armazenados numa pasta, como a pasta Ficheiros de Internet Temporários no caso do Internet Explorer. Quando estas páginas são novamente remetidas, o navegador exibe-as a partir da sua cache. Se forem apresentadas informações sensíveis ao utilizador (como o seu endereço, dados do cartão de crédito, Número de Segurança Social ou nome de utilizador), então esta informação poderá ser armazenada na cache do navegador e, portanto, recuperável através do exame da cache do navegador ou por basta premir o botão "Back" do navegador. Dete teto de cabeçalho de resposta de controlo de cache para "não-loja" para todas as páginas. |

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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Encriptar secções de configuração em ASP.NET 2.0 Utilizando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), especificar um fornecedor de [configuração protegido,](https://msdn.microsoft.com/library/68ze1hb2.aspx)utilizando o cofre de [chaves Azure para proteger os segredos](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) da aplicação |
| **Passos** | Ficheiros de configuração como o Web.config, appsettings.json são frequentemente usados para conter informações sensíveis, incluindo nomes de utilizadores, palavras-passe, cadeias de ligação de base de dados e chaves de encriptação. Caso não proteja esta informação, a sua aplicação é vulnerável a atacantes ou utilizadores maliciosos que obtenham informações confidenciais, tais como nomes de utilizadores de contas e palavras-passe, nomes de bases de dados e nomes de servidores. Com base no tipo de implementação (azure/on-prem), criptografe as secções sensíveis dos ficheiros config utilizando DPAPI ou serviços como o Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Desativar explicitamente o atributo HTML autocompleto em formas e inputs sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN: atributo autocompleto](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Utilização automática Em HTML](https://msdn.microsoft.com/library/ms533032.aspx), VULNERABILIDADE de [Sanitização HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete.,again?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Passos** | O atributo autocompleto especifica se um formulário deve ter auto-completado dentro ou fora. Quando a conclusão automática está, o navegador completa automaticamente os valores com base nos valores que o utilizador já introduziu antes. Por exemplo, quando um novo nome e senha é introduzido num formulário e o formulário é submetido, o navegador pergunta se a palavra-passe deve ser guardada. A partir daí, quando o formulário é apresentado, o nome e a palavra-passe são preenchidos automaticamente ou são preenchidos à medida que o nome é introduzido. Um intruso com acesso local poderia obter a senha de texto clara a partir da cache do navegador. Por defeito, está ativada automaticamente e deve ser explicitamente desativada. |

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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os dados sensíveis, tais como palavras-passe, números de cartão de crédito, SSN, etc. devem ser mascarados quando apresentados no ecrã. Isto é para evitar que o pessoal não autorizado aceda aos dados (por exemplo, palavras-passe de navegação por ombros, pessoal de apoio que veja números de SSN de utilizadores) . Certifique-se de que estes elementos de dados não são visíveis em texto simples e que estão devidamente mascarados. Isto tem de ser tomado cuidado ao aceitá-los como entrada (por exemplo,. tipo de entrada="password") bem como exibir de volta no ecrã (por exemplo, exibir apenas os últimos 4 dígitos do número do cartão de crédito). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementar máscaras dinâmicas de dados para limitar a exposição a utilizadores não privilegiados de dados sensíveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Sql Azure |
| **Atributos**              | Versão SQL - V12, Versão SQL - MsSQL2016 |
| **Referências**              | [Mascaramento de dados dinâmicos](https://msdn.microsoft.com/library/mt130841) |
| **Passos** | O objetivo da máscara dinâmica de dados é limitar a exposição de dados sensíveis, impedindo que os utilizadores que não devam ter acesso aos dados os possam ver. A máscara de dados dinâmicos não tem como objetivo impedir que os utilizadores de bases de dados se conectem diretamente à base de dados e executem consultas exaustivas que exponham peças dos dados sensíveis. A máscara de dados dinâmicos é complementar a outras funcionalidades de segurança do SQL Server (auditoria, encriptação, segurança do nível da linha...) e é altamente recomendado utilizar esta funcionalidade em conjunto com eles, além de proteger melhor os dados sensíveis no base de dados. Por favor, note que esta funcionalidade é suportada apenas pelo SQL Server a partir de 2016 e pela Base de Dados Azure SQL. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Certifique-se de que as palavras-passe são armazenadas em formato hash salgado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Hashing de palavra-passe usando .NET Crypto APIs](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Passos** | As palavras-passe não devem ser armazenadas nas bases de dados das lojas de utilizadores personalizadas. As hashes de senha devem ser armazenadas com valores de sal. Certifique-se de que o sal para o utilizador é sempre único e aplica b-cripta, s-cripta ou PBKDF2 antes de armazenar a palavra-passe, com uma contagem mínima de iteração de fator de trabalho de 150.000 loops para eliminar a possibilidade de forçar brutos.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Certifique-se de que os dados sensíveis nas colunas de base de dados estão encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Versão SQL - Tudo |
| **Referências**              | [Encriptar dados sensíveis no servidor SQL](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Como encriptar uma Coluna de Dados no Servidor SQL](https://msdn.microsoft.com/library/ms179331), [Encriptar por Certificado](https://msdn.microsoft.com/library/ms188061) |
| **Passos** | Dados sensíveis, como os números de cartões de crédito, têm de ser encriptados na base de dados. Os dados podem ser encriptados utilizando encriptação ao nível da coluna ou por uma função de aplicação utilizando as funções de encriptação. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Compreender a encriptação transparente de dados transparentes do Servidor SQL (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Passos** | A funcionalidade transparente de encriptação de dados (TDE) no servidor SQL ajuda a encriptar dados sensíveis numa base de dados e a proteger as chaves que são usadas para encriptar os dados com um certificado. Isto impede que qualquer pessoa sem as chaves utilize os dados. O TDE protege os dados "em repouso", ou seja, os dados e os ficheiros de registo. Proporciona a capacidade de cumprir muitas leis, regulamentos e orientações estabelecidas em várias indústrias. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Certifique-se de que as cópias de segurança da base de dados estão encriptadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, Versão SQL - MsSQL2014 |
| **Referências**              | [Encriptação de backup de base de dados SQL](https://msdn.microsoft.com/library/dn449489) |
| **Passos** | O SQL Server tem a capacidade de encriptar os dados enquanto cria uma cópia de segurança. Especificando o algoritmo de encriptação e o encriptador (um Certificado ou Chave Assimétrica) ao criar uma cópia de segurança, pode-se criar um ficheiro de cópia de segurança encriptado. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Certifique-se de que os dados sensíveis relevantes para a Web API não são armazenados no armazenamento do navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | Fornecedor de Identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | N/D  |
| **Passos** | <p>Em determinadas implementações, artefactos sensíveis relevantes para a autenticação da Web API são armazenados no armazenamento local do navegador. Por exemplo, artefactos de autenticação Azure AD como adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Todos estes artefactos estão disponíveis mesmo depois de assinado ou o navegador está fechado. Se um adversário tiver acesso a estes artefactos, pode reutilizá-los para aceder aos recursos protegidos (APIs). Certifique-se de que todos os artefactos sensíveis relacionados com a Web API não estão armazenados no armazenamento do navegador. Nos casos em que o armazenamento do lado do cliente é inevitável (por exemplo, Aplicações de página única (SPA) que alavancam os fluxos implícitos do OpenIdConnect/OAuth precisam de armazenar fichas de acesso localmente), use escolhas de armazenamento com não persistência. por exemplo, prefira o SessionStorage ao LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O snippet JavaScript abaixo é de uma biblioteca de autenticação personalizada que armazena artefactos de autenticação no armazenamento local. Tais implementações devem ser evitadas. 
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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Criptografe dados sensíveis ao nível da aplicação antes de armazenar em documento DB ou armazenar quaisquer dados sensíveis em outras soluções de armazenamento, como o Azure Storage ou o Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Utilize encriptação de disco azure para encriptar discos usados por máquinas virtuais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira de confiança Azure IaaS VM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Utilizando encriptação de disco azure para encriptar discos usados pelas suas máquinas virtuais](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Passos** | <p>A Encriptação do Disco Azure é uma nova funcionalidade que está atualmente em pré-visualização. Esta funcionalidade permite-lhe encriptar os discos OS e os discos de Dados utilizados por uma Máquina Virtual IaaS. Para o Windows, as unidades são encriptadas utilizando a tecnologia de encriptação BitLocker padrão da indústria. Para o Linux, os discos são encriptados utilizando a tecnologia DM-Crypt. Isto está integrado com o Azure Key Vault para permitir controlar e gerir as chaves de encriptação do disco. A solução de encriptação do disco Azure suporta os seguintes três cenários de encriptação do cliente:</p><ul><li>Ative a encriptação em novos VMs IaaS criados a partir de ficheiros VHD encriptados pelo cliente e chaves de encriptação fornecidas pelo cliente, que são armazenadas no Cofre de Chaves Azure.</li><li>Ativar a encriptação em novos VMs IaaS criados a partir do Mercado Azure.</li><li>Ative a encriptação dos VMs IaaS existentes já em funcionamento em Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Criptografe segredos em aplicações de Tecido de Serviço

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Gerir segredos em aplicações de Tecido de Serviço](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Passos** | Os segredos podem ser qualquer informação sensível, como cordas de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples. Utilize o Cofre chave Azure para gerir chaves e segredos em aplicações de tecido de serviço. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Execute modelação de segurança e utilize Unidades/Equipas de Negócio supérbio, sempre que necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Execute modelação de segurança e utilize Unidades/Equipas de Negócio supérbio, sempre que necessário |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimizar o acesso à funcionalidade de partilha em entidades críticas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Minimizar o acesso à funcionalidade de partilha em entidades críticas |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Treine os utilizadores sobre os riscos associados à funcionalidade Dynamics CRM Share e boas práticas de segurança |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Incluir uma regra de padrões de desenvolvimento proscribing mostrando detalhes da config na gestão de exceção

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Inclua uma regra de padrões de desenvolvimento proscribing mostrando detalhes da config na gestão excecionada fora do desenvolvimento. Teste para tal como parte de revisões de códigoou inspeção periódica.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Utilize encriptação do serviço de armazenamento Azure (SSE) para dados em repouso (pré-visualização)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Encriptação do serviço de armazenamento Azure para dados em repouso (pré-visualização)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Passos** | <p>A Encriptação do Serviço de Armazenamento Azure (SSE) para Dados em Repouso ajuda-o a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Com esta funcionalidade, o Armazenamento do Azure encripta automaticamente os dados antes de continuar a armazenar e desencriptar antes da obtenção. A encriptação, desencriptação e gestão chave é totalmente transparente para os utilizadores. A SSE aplica-se apenas a bolhas de bloco, bolhas de página e bolhas de apêndice. Os outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.</p><p>Fluxo de trabalho de encriptação e desencriptação:</p><ul><li>O cliente permite encriptação na conta de armazenamento</li><li>Quando o cliente escrever novos dados (PUT Blob, PUT Block, PUT Page, etc.) para o armazenamento blob; cada escrita é encriptada usando encriptação AES de 256 bits, uma das cifras de bloco mais fortes disponíveis</li><li>Quando o cliente precisa de aceder a dados (GET Blob, etc.), os dados são automaticamente desencriptados antes de regressar em funções ao utilizador</li><li>Se a encriptação for desativada, as novas escritas deixaram de ser encriptadas e os dados encriptados existentes permanecem encriptados até serem reescritos pelo utilizador. Enquanto a encriptação estiver ativada, os escritos para o armazenamento Blob serão encriptados. O estado dos dados não muda com o utilizador a alternar entre permitir/desativar a encriptação para a conta de armazenamento</li><li>Todas as chaves de encriptação são armazenadas, encriptadas e geridas pela Microsoft</li></ul><p>Por favor, note que, neste momento, as chaves utilizadas para a encriptação são geridas pela Microsoft. A Microsoft gera as chaves originalmente e gere o armazenamento seguro das teclas, bem como a rotação regular, tal como definida pela política interna da Microsoft. No futuro, os clientes terão a capacidade de gerir as suas próprias chaves de encriptação >e fornecer um caminho de migração desde chaves geridas pela Microsoft até chaves geridas pelo cliente.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Use encriptação do lado do cliente para armazenar dados confidenciais no Armazenamento Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação do lado do cliente e cofre de chaves Azure para armazenamento Microsoft Azure,](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/) [Tutorial: Criptografar e desencriptar blobs no Armazenamento Microsoft Azure usando cofre de chaves Azure](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [armazenar dados de forma segura no armazenamento de Blob Azure com extensões de encriptação Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Passos** | <p>A Biblioteca de Clientes de Armazenamento Azure para o pacote .NET Nuget suporta encriptar dados dentro das aplicações dos clientes antes de enviar para o Armazenamento Azure e desencriptar dados enquanto descarrega para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Aqui está uma breve descrição de como a encriptação do lado do cliente funciona:</p><ul><li>O SDK, cliente de armazenamento azure, gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único</li><li>Os dados do cliente são encriptados usando este CEK</li><li>O CEK é então embrulhado (encriptado) utilizando a chave de encriptação da chave (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado no Cofre chave Azure. O próprio cliente de Armazenamento nunca tem acesso ao KEK. Apenas invoca o algoritmo de embrulho chave que é fornecido pela Key Vault. Os clientes podem optar por utilizar fornecedores personalizados para embrulhar/desembrulhar chaves se quiserem</li><li>Os dados encriptados são depois enviados para o serviço de armazenamento Azure. Verifique os links na secção de referências para obter detalhes de implementação de baixo nível.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Criptografe dados sensíveis ou PII escritos para telefones armazenamento local

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico  |
| **Atributos**              | N/D  |
| **Referências**              | [Gerencie as definições e funcionalidades dos seus dispositivos com as políticas Microsoft Intune](https://docs.microsoft.com/mem/intune/configuration/), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **Passos** | <p>Se a aplicação escrever informações sensíveis como o PII do utilizador (e-mail, número de telefone, primeiro nome, apelido, preferências, etc.) - no sistema de ficheiros do telemóvel, então deve ser encriptado antes de escrever para o sistema de ficheiros local. Se a aplicação for uma aplicação empresarial, então explore a possibilidade de publicar aplicação usando o Windows Intune.</p>|

### <a name="example"></a>Exemplo
Intune pode ser configurado com as seguintes políticas de segurança para salvaguardar dados sensíveis: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se a aplicação não for uma aplicação empresarial, utilize a plataforma fornecida por keystore, porta-chaves para armazenar chaves de encriptação, utilizando a operação criptográfica que pode ser realizada no sistema de ficheiros. O seguinte código mostra como aceder à chave a partir do chaveiro utilizando xamarin: 
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

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Obfuscate gerou binários antes de distribuir para utilizadores finais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Obfuscção criptopara .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Passos** | Binários gerados (conjuntos dentro da APK) devem ser obfuscados para parar a engenharia inversa de conjuntos. Ferramentas `CryptoObfuscator` como podem ser usadas para este fim. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Definir clienteCredentialType para Certificado ou Windows

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | N/D  |
| **Referências**              | [Fortificar](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Passos** | A utilização de um UsernameToken com uma palavra-passe de texto simples sobre um canal não encriptado expõe a palavra-passe a intrusos que podem cheirar as mensagens SOAP. Os Fornecedores de Serviços que utilizam o UsernameToken podem aceitar senhas enviadas em texto simples. Enviar palavras-passe simples por um canal não encriptado pode expor a credencial a atacantes que possam cheirar a mensagem SOAP. | 

### <a name="example"></a>Exemplo
A seguinte configuração do fornecedor de serviços WCF utiliza o UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Detete o clienteCredentialType para Certificado ou Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>Modo WCF-Segurança não está ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, .NET Quadro 3 |
| **Atributos**              | Modo de Segurança - Transporte, Modo de Segurança - Mensagem |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Fundamentos da WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Passos** | Não foi definida qualquer segurança de transporte ou mensagem. As aplicações que transmitem mensagens sem transporte ou segurança de mensagens não podem garantir a integridade ou confidencialidade das mensagens. Quando uma ligação de segurança wCF é definida para nenhuma, tanto a segurança de transporte como a segurança da mensagem são desativadas. |

### <a name="example"></a>Exemplo
A configuração seguinte define o modo de segurança para Nenhum. 
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
Modo de Segurança Em todas as ligações de serviço existem cinco modos de segurança possíveis: 
* Nenhum. Desliga a segurança. 
* O transporte. Utiliza a segurança do transporte para autenticação mútua e proteção de mensagens. 
* Mensagem. Utiliza a segurança da mensagem para autenticação mútua e proteção de mensagens. 
* Os dois. Permite-lhe fornecer configurações para transporte e segurança ao nível da mensagem (apenas mSMQ suporta isto). 
* TransportWithMessageCredential. As credenciais são passadas com a proteção de mensagens e mensagens e a autenticação do servidor são fornecidas pela camada de transporte. 
* TransportCredentialOnly. As credenciais do cliente são passadas com a camada de transporte e não é aplicada proteção contra mensagens. Utilize a segurança do transporte e da mensagem para proteger a integridade e a confidencialidade das mensagens. A configuração abaixo indica ao serviço para usar a segurança do transporte com credenciais de mensagem.
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
