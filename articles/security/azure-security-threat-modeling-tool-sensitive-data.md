---
title: Dados confidenciais - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
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
ms.openlocfilehash: 93beef5702df9b4cf0a51a01fb286a3f023f9839
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876624"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Quadro de segurança: Dados confidenciais | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os binários são oculto se contiverem informações confidenciais](#binaries-info)</li><li>[Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger dados confidenciais de utilizadores específicos](#efs-user)</li><li>[Certifique-se de que os dados confidenciais armazenados pelo aplicativo no sistema de ficheiros estão encriptados](#filesystem)</li></ul> | 
| **Aplicação Web** | <ul><li>[Certifique-se de que o conteúdo confidencial não está em cache no navegador](#cache-browser)</li><li>[Encriptar secções dos arquivos de configuração da aplicação Web que contêm dados confidenciais](#encrypt-data)</li><li>[Desativar explicitamente o atributo de autocompletar HTML em formulários confidenciais e de entradas](#autocomplete-input)</li><li>[Certifique-se de que os dados confidenciais apresentados no ecrã de utilizador está oculto](#data-mask)</li></ul> | 
| **Base de Dados** | <ul><li>[Máscara de dados dinâmica de implementar para limitar os utilizadores de exposição não privilegiado de dados confidenciais](#dynamic-users)</li><li>[Certifique-se de que as senhas são armazenadas no formato de hash de Salt](#salted-hash)</li><li>[Certifique-se de que os dados confidenciais nas colunas de base de dados são encriptados](#db-encrypted)</li><li>[Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada](#tde-enabled)</li><li>[Certifique-se de que as cópias de segurança da base de dados são encriptadas](#backup)</li></ul> | 
| **API Web** | <ul><li>[Certifique-se de que os dados confidenciais relevantes para a Web API não são armazenados no armazenamento do navegador](#api-browser)</li></ul> | 
| DB de documentos do Azure | <ul><li>[Encriptar dados confidenciais armazenados no Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Limite de fidedignidade da VM IaaS do Azure** | <ul><li>[Utilizar o Azure Disk Encryption para encriptar discos utilizados por máquinas virtuais](#disk-vm)</li></ul> | 
| **Limite de fidedignidade do Service Fabric** | <ul><li>[Encriptar os segredos em aplicações do Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Execute a Modelagem de segurança e utilizar unidades de negócios ou várias onde for necessário](#modeling-teams)</li><li>[Minimizar o acesso para partilhar recursos em entidades críticas](#entities)</li><li>[Treinar os usuários sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e boas práticas de segurança](#good-practices)</li><li>[Incluir uma regra de padrões de desenvolvimento proscribing que mostra os detalhes de configuração na gestão de exceções](#exception-mgmt)</li></ul> | 
| **Armazenamento do Azure** | <ul><li>[Utilizar a encriptação do serviço de armazenamento do Azure (SSE) para dados Inativos (pré-visualização)](#sse-preview)</li><li>[Utilize a encriptação do lado do cliente para armazenar dados confidenciais no armazenamento do Azure](#client-storage)</li></ul> | 
| **Cliente móvel** | <ul><li>[Encriptar sensível ou dados PII escritos para o armazenamento local de telemóveis](#pii-phones)</li><li>[Oculte binários gerados antes de distribuir aos utilizadores finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir clientCredentialType para o certificado ou do Windows](#cert)</li><li>[Modo de segurança do WCF não está ativado](#security)</li></ul> | 

## <a id="binaries-info"></a>Certifique-se de que os binários são oculto se contiverem informações confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os binários são oculto se contiverem informações confidenciais, tais como segredos comerciais, lógica de negócio sensíveis que deve não invertida. Trata-se parar a engenharia reversa de assemblies. Ferramentas como o `CryptoObfuscator` podem ser utilizados para esta finalidade. |

## <a id="efs-user"></a>Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger dados confidenciais de utilizadores específicos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger dados confidenciais de utilizadores específicos de adversários com acesso físico ao computador. |

## <a id="filesystem"></a>Certifique-se de que os dados confidenciais armazenados pelo aplicativo no sistema de ficheiros estão encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os dados confidenciais armazenados pelo aplicativo no sistema de ficheiros são encriptados (por exemplo, utilizando a DPAPI), se o EFS não pode ser imposto |

## <a id="cache-browser"></a>Certifique-se de que o conteúdo confidencial não está em cache no navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genéricas, Web Forms, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Browsers podem armazenar informações para fins de colocação em cache e o histórico. Esses arquivos em cache são armazenados numa pasta, como a pasta Temporary Internet Files no caso do Internet Explorer. Quando essas páginas são referidas novamente, o navegador apresenta-os partir da respetiva cache. Se são apresentadas informações confidenciais para o utilizador (como o respetivo endereço, detalhes do cartão de crédito, número da Previdência Social ou nome de utilizador), em seguida, estas informações podem ser armazenado em cache do navegador e, portanto, recuperável por meio de examinar o cache do navegador ou de simplesmente pressionando o botão "Voltar" do navegador. Defina o valor de cabeçalho de resposta de cache-control para "no-store" para todas as páginas. |

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
Isso pode ser implementado por meio de um filtro. Pode ser utilizado o exemplo seguinte: 
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

## <a id="encrypt-data"></a>Encriptar secções dos arquivos de configuração da aplicação Web que contêm dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [How To: Encriptar secções de configuração no ASP.NET 2.0 usando o DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [especificação de um fornecedor de configuração protegida](https://msdn.microsoft.com/library/68ze1hb2.aspx), [utilizando o Azure Key Vault para proteger segredos da aplicação](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Arquivos de configuração, tais como o Web. config appSettings costumam ser usados para armazenar informações confidenciais, incluindo os nomes de utilizador, palavras-passe, cadeias de ligação de base de dados e chaves de encriptação. Se não proteger essas informações, a aplicação está vulnerável a atacantes ou obtenção de informações confidenciais, como nomes de conta de utilizador e palavras-passe, nomes de base de dados e os nomes dos servidores que usuários mal-intencionados. Com base no tipo de implementação (azure/no local), criptografe as seções confidenciais dos ficheiros de configuração com DPAPI ou serviços, como o Azure Key Vault. |

## <a id="autocomplete-input"></a>Desativar explicitamente o atributo de autocompletar HTML em formulários confidenciais e de entradas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN: atributo de autocompletar](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [usando o preenchimento automático em HTML](https://msdn.microsoft.com/library/ms533032.aspx), [vulnerabilidade de limpeza de HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [preenchimento automático., novamente?!](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Passos** | O atributo de autocompletar Especifica se um formulário deve ter a conclusão automática ativada ou desativada. Quando o preenchimento automático está ativado, o navegador concluir automaticamente valores com base nos valores que o utilizador introduziu antes. Por exemplo, quando um novo nome e a palavra-passe é introduzida num formulário e o formulário é submetido, o browser pede-se a palavra-passe deve ser salvos. Posteriormente, quando o formulário é exibido, o nome e a palavra-passe são preenchidos automaticamente ou são concluídas, como o nome é introduzido. Um invasor com acesso local foi possível obter a palavra-passe de texto não encriptado da cache do navegador. Por predefinição o preenchimento automático está ativado e explicitamente devem ser desativada. |

### <a name="example"></a>Exemplo
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Certifique-se de que os dados confidenciais apresentados no ecrã de utilizador está oculto

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Dados confidenciais como palavras-passe, números de cartão de crédito, etc. de SSN devem ser mascarados quando apresentado no ecrã. Este procedimento impede que a pessoa não-autorizada de aceder os dados (por exemplo, surfar the shoulder palavras-passe, visualizar os números de SSN de utilizadores do suporte técnico). Certifique-se de que esses elementos de dados não estão visíveis em texto simples e adequadamente são mascarados. Isso deve ser resolvido ao aceitá-los como entrada (por exemplo. tipo de entrada = "password"), bem como a exibição de volta na tela (por exemplo, apresentar apenas os 4 últimos dígitos do número de cartão de crédito). |

## <a id="dynamic-users"></a>Máscara de dados dinâmica de implementar para limitar os utilizadores de exposição não privilegiado de dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | MsSQL2016 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Máscara de dados dinâmicos](https://msdn.microsoft.com/library/mt130841) |
| **Passos** | É a finalidade de máscara de dados dinâmicos limitar a exposição de dados confidenciais, impedindo os utilizadores que não devem ter acesso aos dados de visualizá-lo. Máscara de dados dinâmicos não têm como objetivo para impedir que os utilizadores da base de dados ligar diretamente à base de dados e executar consultas exaustivas que expõem os dados confidenciais. Máscara de dados dinâmica é complementar aos outros recursos de segurança do SQL Server (auditoria, encriptação, segurança ao nível da linha...) e é altamente recomendável para utilizar esta funcionalidade em conjunto com os mesmos além do mais para melhor proteger os dados confidenciais no base de dados. Tenha em atenção que esta funcionalidade é suportada apenas pelo SQL Server a partir de 2016 e a base de dados do Azure SQL. |

## <a id="salted-hash"></a>Certifique-se de que as senhas são armazenadas no formato de hash de Salt

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Hashing de palavra-passe com APIs de criptografia do .NET](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Passos** | As palavras-passe não devem ser armazenadas em bases de dados de arquivo de utilizador personalizada. Os hashes de palavra-passe devem ser armazenados em vez disso, com valores de salt. Certificar-se de que o salt para o utilizador é sempre único e aplicar b-crypt, s-crypt ou PBKDF2 antes de armazenar a palavra-passe, com uma contagem de iteração do fator de trabalho mínimo de 150 000 loops para eliminar a possibilidade de força bruta às.| 

## <a id="db-encrypted"></a>Certifique-se de que os dados confidenciais nas colunas de base de dados são encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Versão do SQL - todos os |
| **Referências**              | [Encriptar dados confidenciais no SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [como: Encriptar uma coluna de dados no SQL Server](https://msdn.microsoft.com/library/ms179331), [certificado para encriptar](https://msdn.microsoft.com/library/ms188061) |
| **Passos** | Dados confidenciais, como números de cartão de crédito tem de ser encriptados na base de dados. Dados podem ser encriptados utilizando a encriptação de nível de coluna ou por uma função de aplicativo usando as funções de encriptação. |

## <a id="tde-enabled"></a>Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Noções básicas sobre criptografia de dados transparente (TDE) do SQL Server](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Passos** | Recursos de encriptação de dados (TDE) transparente no SQL server ajuda em criptografar dados confidenciais num banco de dados e Proteja as chaves que são utilizadas para encriptar os dados com um certificado. Isso impede que qualquer pessoa sem as chaves a utilizar os dados. TDE protege os dados "em repouso", que significa que os ficheiros de dados e de registo. Fornece a capacidade para estar em conformidade com várias leis, regulamentações e diretrizes estabelecidas em vários setores. |

## <a id="backup"></a>Certifique-se de que as cópias de segurança da base de dados são encriptadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | MsSQL2014 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Encriptação de cópia de segurança de base de dados do SQL](https://msdn.microsoft.com/library/dn449489) |
| **Passos** | SQL Server tem a capacidade para encriptar os dados ao criar uma cópia de segurança. Ao especificar o algoritmo de encriptação e o encriptador (um certificado ou chave assimétrica) ao criar uma cópia de segurança, é possível criar um ficheiro de cópia de segurança encriptado. |

## <a id="api-browser"></a>Certifique-se de que os dados confidenciais relevantes para a Web API não são armazenados no armazenamento do navegador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | Fornecedor de identidade de fornecedor - ADFS, de identidade - Azure AD |
| **Referências**              | N/A  |
| **Passos** | <p>Em determinadas implementações, artefatos confidenciais relevantes para a autenticação da API Web são armazenados no armazenamento local do navegador. Por exemplo, os artefactos de autenticação do Azure AD como adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Todos esses artefatos estão disponíveis mesmo depois de terminar sessão ou browser é fechado. Se um adversário dispõe do acesso a esses artefactos, ele pode reutilizar-lhes aceder os recursos protegidos (APIs). Certifique-se de que todos os artefactos confidenciais relacionadas com a Web API não são armazenados no armazenamento do navegador. Em casos em que o armazenamento do lado do cliente é inevitável (por exemplo, única página de aplicações (SPA) que tiram partido dos fluxos do OAuth/OpenIdConnect implícita precisa armazenar localmente os tokens de acesso), opções de armazenamento de utilização com não têm de persistência. Por exemplo, prefiro SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O abaixo JavaScript trecho é de uma biblioteca de autenticação personalizado que armazena artefactos de autenticação no armazenamento local. Esse tipo de implementação deve ser evitado. 
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

## <a id="encrypt-docdb"></a>Encriptar dados confidenciais armazenados no Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | DB de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Encriptar dados confidenciais em nível de aplicativo antes de armazenar no document DB ou armazenar dados confidenciais em outras soluções de armazenamento, como o armazenamento do Azure ou SQL do Azure| 

## <a id="disk-vm"></a>Utilizar o Azure Disk Encryption para encriptar discos utilizados por máquinas virtuais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade da VM IaaS do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Com o Azure Disk Encryption para encriptar discos utilizados pelas suas máquinas virtuais](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Passos** | <p>O Azure Disk Encryption é um novo recurso que está atualmente em pré-visualização. Esta funcionalidade permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina Virtual IaaS. Para Windows, as unidades são encriptadas com a tecnologia de encriptação do BitLocker de norma da indústria. Para o Linux, os discos são encriptados com a tecnologia de DM-Crypt. Isso está integrado com o Azure Key Vault, para que possa controlar e gerir as chaves de encriptação de disco. A solução Azure Disk Encryption suporta os seguintes cenários de encriptação de clientes de três:</p><ul><li>Ative a encriptação em novas VMs de IaaS, criado a partir de ficheiros do VHD encriptados de cliente e chaves de encriptação fornecida pelo cliente, que são armazenadas no Azure Key Vault.</li><li>Ative a encriptação em novas VMs de IaaS, criado no Azure Marketplace.</li><li>Ative a encriptação em VMs de IaaS existentes já em execução no Azure.</li></ul>| 

## <a id="fabric-apps"></a>Encriptar os segredos em aplicações do Service Fabric

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Gerir segredos em aplicações do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Passos** | Segredos podem ser qualquer informações confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples. Utilize o Azure Key Vault para gerir as chaves e segredos em aplicações do service fabric. |

## <a id="modeling-teams"></a>Execute a Modelagem de segurança e utilizar unidades de negócios ou várias onde for necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Execute a Modelagem de segurança e utilizar unidades de negócios ou várias onde for necessário |

## <a id="entities"></a>Minimizar o acesso para partilhar recursos em entidades críticas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Minimizar o acesso para partilhar recursos em entidades críticas |

## <a id="good-practices"></a>Treinar os usuários sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e boas práticas de segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Treinar os usuários sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e boas práticas de segurança |

## <a id="exception-mgmt"></a>Incluir uma regra de padrões de desenvolvimento proscribing que mostra os detalhes de configuração na gestão de exceções

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Inclua uma regra de padrões de desenvolvimento proscribing que mostra detalhes de configuração na gestão de exceções fora do desenvolvimento. Testar este como parte de revisões de código ou inspeção periódica.|

## <a id="sse-preview"></a>Utilizar a encriptação do serviço de armazenamento do Azure (SSE) para dados Inativos (pré-visualização)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Encriptação do serviço de armazenamento do Azure para dados Inativos (pré-visualização)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Passos** | <p>Azure Storage Service Encryption (SSE) para dados Inativos ajuda a proteger e salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacionais. Com esta funcionalidade, o Armazenamento do Azure encripta automaticamente os dados antes de continuar a armazenar e desencriptar antes da obtenção. A encriptação, desencriptação e gestão de chaves é totalmente transparente para os utilizadores. O SSE aplica-se apenas para blobs de blocos, blobs de páginas e blobs de acréscimo. Os outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.</p><p>Encriptação e desencriptação fluxo de trabalho:</p><ul><li>O cliente ativa a encriptação na conta de armazenamento</li><li>Quando o cliente grava dados novos (colocar o Blob, Colocação de blocos, página colocar, etc.) para armazenamento de BLOBs cada gravação é encriptada com a encriptação AES de 256 bits, uma das codificações de bloco mais fortes disponíveis</li><li>Quando o cliente precisa de aceder a dados (obter BLOBs, etc.), os dados são automaticamente desencriptados antes de o devolver ao utilizador</li><li>Se a encriptação estiver desativada, escritas novo já não são encriptadas e os dados encriptados existentes permanecem encriptados até reescrito pelo utilizador. Enquanto estiver ativada a encriptação, serão encriptadas escritas para armazenamento de Blobs. Não altera o estado dos dados com o usuário alternar entre a ativação/desativação de encriptação para a conta de armazenamento</li><li>Todas as chaves de encriptação são armazenadas encriptadas e geridas pela Microsoft</li></ul><p>Tenha em atenção que, neste momento, as chaves utilizadas para a encriptação são geridas pela Microsoft. A Microsoft gera as chaves originalmente e gerir o armazenamento seguro das chaves, bem como a rotação regular, conforme definido pela diretiva interna da Microsoft. No futuro, os clientes beneficiam a capacidade de gerenciar suas próprias > chaves de encriptação e forneça um caminho de migração a partir das chaves geridas pela Microsoft para chaves geridas pelo cliente.</p>| 

## <a id="client-storage"></a>Utilize a encriptação do lado do cliente para armazenar dados confidenciais no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Encriptação do lado do cliente e o Azure Cofre de chaves para o armazenamento do Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Tutorial: Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [armazenar dados em forma segura no armazenamento de Blobs do Azure com extensões de encriptação do Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Passos** | <p>A biblioteca de cliente de armazenamento do Azure para o pacote .NET Nuget suporta a encriptação de dados em aplicativos de cliente antes de carregar para o armazenamento do Azure e a desencriptação de dados durante a transferência para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Eis uma breve descrição de como funciona a encriptação do lado do cliente:</p><ul><li>O SDK do cliente de armazenamento do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uma único uso</li><li>Dados de cliente são encriptados utilizando este CEK</li><li>O CEK, em seguida, é encapsulado (encriptada) com a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada no Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso para a KEK. Ele simplesmente invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os clientes podem optar por utilizar fornecedores personalizados para a chave de encapsulamento de aplicações/abertura se desejarem</li><li>Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. Verifique os links na seção referências para obter detalhes de implementação de nível baixo.</li></ul>|

## <a id="pii-phones"></a>Encriptar sensível ou dados PII escritos para o armazenamento local de telemóveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Xamarin  |
| **Atributos**              | N/A  |
| **Referências**              | [Gerir definições e funcionalidades nos seus dispositivos com políticas do Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [porta-chaves Valet](https://components.xamarin.com/view/square.valet) |
| **Passos** | <p>Se a aplicação escreve informações confidenciais, como PII do usuário (e-mail, número de telefone, nome próprio, apelido, preferências etc.) -no sistema de arquivos do mobile, em seguida, ele deve ser encriptado antes de gravar o sistema de arquivos local. Se o aplicativo é uma aplicação empresarial, em seguida, explore a possibilidade de aplicativo de publicação através do Windows Intune.</p>|

### <a name="example"></a>Exemplo
Intune pode ser configurado com políticas de segurança seguintes para salvaguardar os dados confidenciais: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se a aplicação não é uma aplicação empresarial, em seguida, utilizar keystore de plataforma fornecida, keychains para armazenar chaves de encriptação, com o qual operação criptográfica pode ser executada no sistema de ficheiros. Fragmento de código seguinte mostra como chave de acesso de keychain a utilizar o xamarin: 
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

## <a id="binaries-end"></a>Oculte binários gerados antes de distribuir aos utilizadores finais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Criptografia Ofuscação para .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Passos** | Binários gerados (assemblies da apk) devem estar ocultados para parar a engenharia reversa de assemblies. Ferramentas como o `CryptoObfuscator` podem ser utilizados para esta finalidade. |

## <a id="cert"></a>Definir clientCredentialType para o certificado ou do Windows

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Fortaleça](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Passos** | Utilizar um token de nome de usuário com uma palavra-passe de texto simples através de um canal não encriptado expõe a palavra-passe para os invasores que podem detectar as mensagens SOAP. Fornecedores de serviços que utilizam o token de nome de usuário pode aceitar as palavras-passe enviadas em texto não encriptado. Enviar palavras-passe de texto simples através de um canal não encriptado, pode expor as credenciais para os invasores que possa farejá-lo a mensagem SOAP. | 

### <a name="example"></a>Exemplo
A seguinte configuração do fornecedor de serviço do WCF usa o token de nome de usuário: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Definir clientCredentialType para o certificado ou Windows. 

## <a id="security"></a>Modo de segurança do WCF não está ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | Mensagem de modo - transporte, modo de segurança - segurança |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Noções básicas de segurança do WCF CoDe Magazine](http://www.codemag.com/article/0611051) |
| **Passos** | Não foi definida nenhuma segurança de transporte ou de mensagens. Aplicativos que transmitir mensagens sem transporte ou mensagem de segurança não pode garantir a integridade ou confidencialidade das mensagens. Quando uma associação de segurança do WCF é definida como None, a segurança de transporte e de mensagem estão desativadas. |

### <a name="example"></a>Exemplo
A seguinte configuração define o modo de segurança como None. 
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
Modo de segurança em todos os enlaces de serviço lá são cinco modos de segurança possíveis: 
* Nenhum. Desativa a segurança. 
* Transporte. Segurança para proteção mútua de autenticação e a mensagem de transporte de utilizações. 
* mensagem. Usa a segurança de mensagem para a proteção de autenticação e a mensagem mútua. 
* Ambos. Permite-lhe fornecer definições para transporte e de segurança de nível de mensagem (MSMQ só oferece suporte a isso). 
* TransportWithMessageCredential. As credenciais são passadas com a mensagem e a proteção de mensagem e autenticação de servidor são fornecidos pela camada de transporte. 
* TransportCredentialOnly. Credenciais de cliente são passadas com a camada de transporte e sem proteção de mensagem é aplicada. Utilize segurança de transporte e de mensagem para proteger a integridade e confidencialidade das mensagens. A configuração abaixo indica ao serviço para utilizar a segurança de transporte com credenciais de mensagem.
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
