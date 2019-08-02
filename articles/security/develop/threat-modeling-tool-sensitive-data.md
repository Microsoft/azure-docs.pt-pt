---
title: Dados confidenciais-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: d482ae375fca3824213b54c2045d114fa2f0bfbe
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727873"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Quadro de segurança: Dados confidenciais | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de confiança do computador** | <ul><li>[Verifique se os binários estão ofuscados se contiverem informações confidenciais](#binaries-info)</li><li>[Considere usar o EFS (sistema de arquivos criptografados) é usado para proteger dados confidenciais específicos do usuário](#efs-user)</li><li>[Verifique se os dados confidenciais armazenados pelo aplicativo no sistema de arquivos estão criptografados](#filesystem)</li></ul> | 
| **Aplicativo Web** | <ul><li>[Garantir que o conteúdo confidencial não seja armazenado em cache no navegador](#cache-browser)</li><li>[Criptografar seções de arquivos de configuração do aplicativo Web que contêm dados confidenciais](#encrypt-data)</li><li>[Desabilitar explicitamente o atributo HTML de preenchimento automático em formulários e entradas confidenciais](#autocomplete-input)</li><li>[Verifique se os dados confidenciais exibidos na tela do usuário estão mascarados](#data-mask)</li></ul> | 
| **Base de Dados** | <ul><li>[Implementar a máscara de dados dinâmicos para limitar a exposição de dados confidenciais usuários sem privilégios](#dynamic-users)</li><li>[Verifique se as senhas estão armazenadas no formato de hash salted](#salted-hash)</li><li>[Certifique-se de que os dados confidenciais nas colunas de dados estejam criptografados](#db-encrypted)</li><li>[Verifique se a criptografia no nível do banco de dados (TDE) está habilitada](#tde-enabled)</li><li>[Garantir que os backups de banco de dados sejam criptografados](#backup)</li></ul> | 
| **API Web** | <ul><li>[Garantir que os dados confidenciais relevantes para a API da Web não sejam armazenados no armazenamento do navegador](#api-browser)</li></ul> | 
| BD de documentos do Azure | <ul><li>[Criptografar dados confidenciais armazenados no Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Limite de confiança de VM IaaS do Azure** | <ul><li>[Usar Azure Disk Encryption para criptografar discos usados por máquinas virtuais](#disk-vm)</li></ul> | 
| **Limite de confiança Service Fabric** | <ul><li>[Criptografar segredos em aplicativos Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Executar a modelagem de segurança e usar unidades/equipes de negócios onde for necessário](#modeling-teams)</li><li>[Minimizar o acesso para compartilhar recursos em entidades críticas](#entities)</li><li>[Treine os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e boas práticas de segurança](#good-practices)</li><li>[Incluir uma regra de padrões de desenvolvimento proscreva-se mostrando detalhes da configuração no gerenciamento de exceções](#exception-mgmt)</li></ul> | 
| **Armazenamento do Azure** | <ul><li>[Usar o Azure Criptografia do Serviço de Armazenamento (SSE) para dados em repouso (visualização)](#sse-preview)</li><li>[Usar a criptografia do lado do cliente para armazenar dados confidenciais no armazenamento do Azure](#client-storage)</li></ul> | 
| **Cliente móvel** | <ul><li>[Criptografar dados confidenciais ou PII gravados no armazenamento local de telefones](#pii-phones)</li><li>[Ofuscar binários gerados antes de distribuir aos usuários finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir clientCredentialtype como certificado ou Windows](#cert)</li><li>[O modo de segurança do WCF não está habilitado](#security)</li></ul> | 

## <a id="binaries-info"></a>Verifique se os binários estão ofuscados se contiverem informações confidenciais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os binários sejam ofuscados se contiverem informações confidenciais, como segredos comerciais, lógica comercial confidencial que não deva ser revertida. Isso é para parar a engenharia reversa de assemblies. Ferramentas como `CryptoObfuscator` o podem ser usadas para essa finalidade. |

## <a id="efs-user"></a>Considere usar o EFS (sistema de arquivos criptografados) é usado para proteger dados confidenciais específicos do usuário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Considere usar o EFS (sistema de arquivos criptografados) é usado para proteger dados confidenciais específicos do usuário de adversários com acesso físico ao computador. |

## <a id="filesystem"></a>Verifique se os dados confidenciais armazenados pelo aplicativo no sistema de arquivos estão criptografados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se os dados confidenciais armazenados pelo aplicativo no sistema de arquivos estão criptografados (por exemplo, usando DPAPI), se o EFS não puder ser imposto |

## <a id="cache-browser"></a>Garantir que o conteúdo confidencial não seja armazenado em cache no navegador

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Web Forms, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Os navegadores podem armazenar informações para fins de cache e histórico. Esses arquivos em cache são armazenados em uma pasta, como a pasta Temporary Internet Files no caso do Internet Explorer. Quando essas páginas são referenciadas novamente, o navegador as exibe de seu cache. Se informações confidenciais forem exibidas para o usuário (como endereço, detalhes do cartão de crédito, número do seguro social ou nome de usuário), essas informações poderão ser armazenadas no cache do navegador e, portanto, podem ser recuperadas por meio da verificação do cache do navegador ou do simplesmente pressionando o botão "voltar" do navegador. Defina o valor do cabeçalho de resposta de controle de cache como "no-Store" para todas as páginas. |

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
Isso pode ser implementado por meio de um filtro. O exemplo a seguir pode ser usado: 
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

## <a id="encrypt-data"></a>Criptografar seções de arquivos de configuração do aplicativo Web que contêm dados confidenciais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [How To: Criptografar seções de configuração no ASP.NET 2,0](https://msdn.microsoft.com/library/ff647398.aspx)usando DPAPI, [especificando um provedor de configuração protegida](https://msdn.microsoft.com/library/68ze1hb2.aspx), [usando Azure Key Vault para proteger os segredos do aplicativo](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Arquivos de configuração, como o Web. config, appSettings. JSON, geralmente são usados para manter informações confidenciais, incluindo nomes de usuário, senhas, cadeias de conexão de banco de dados e chaves de criptografia. Se você não proteger essas informações, seu aplicativo ficará vulnerável a invasores ou usuários mal-intencionados que obtêm informações confidenciais, como nomes de usuário e senhas de conta, nomes de banco de dados e nomes de servidor. Com base no tipo de implantação (Azure/local), criptografe as seções confidenciais dos arquivos de configuração usando DPAPI ou serviços como Azure Key Vault. |

## <a id="autocomplete-input"></a>Desabilitar explicitamente o atributo HTML de preenchimento automático em formulários e entradas confidenciais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn: atributo de preenchimento automático](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [usando preenchimento automático em HTML](https://msdn.microsoft.com/library/ms533032.aspx), [vulnerabilidade de limpeza de HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [preenchimento automático., novamente?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Passos** | O atributo preenchimento automático especifica se um formulário deve ter preenchimento automático ativado ou desativado. Quando preenchimento automático está ativado, o navegador automaticamente preenche valores com base nos valores que o usuário inseriu antes. Por exemplo, quando um novo nome e senha são inseridos em um formulário e o formulário é enviado, o navegador pergunta se a senha deve ser salva. Depois disso, quando o formulário for exibido, o nome e a senha serão preenchidos automaticamente ou serão concluídos conforme o nome for inserido. Um invasor com acesso local pode obter a senha de texto não criptografado do cache do navegador. Por padrão, o preenchimento automático é habilitado e deve ser desabilitado explicitamente. |

### <a name="example"></a>Exemplo
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Verifique se os dados confidenciais exibidos na tela do usuário estão mascarados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Dados confidenciais, como senhas, números de cartão de crédito, SSN etc., devem ser mascarados quando exibidos na tela. Isso é para impedir que pessoas não autorizadas acessem os dados (por exemplo, senhas de navegação tiracolo, pessoal de suporte que exiba números de usuários de SSN). Certifique-se de que esses elementos de dados não sejam visíveis em texto sem formatação e sejam mascarados adequadamente. Isso deve ser levado em questão ao aceitá-los como entrada (por exemplo,. tipo de entrada = "senha"), bem como a exibição de volta na tela (por exemplo, exibe apenas os últimos 4 dígitos do número do cartão de crédito). |

## <a id="dynamic-users"></a>Implementar a máscara de dados dinâmicos para limitar a exposição de dados confidenciais usuários sem privilégios

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | Versão do SQL-V12, versão do SQL-MsSQL2016 |
| **Referências**              | [Máscara de Dados Dinâmicos](https://msdn.microsoft.com/library/mt130841) |
| **Passos** | A finalidade do mascaramento de dados dinâmicos é limitar a exposição de dados confidenciais, impedindo que os usuários que não devem ter acesso aos dados os exibam. O mascaramento de dados dinâmicos não tem como objetivo impedir que os usuários de banco de dado se conectem diretamente ao banco e executem consultas exaustivas que exponham partes dos dados confidenciais. O mascaramento de dados dinâmicos é complementar a outros SQL Server recursos de segurança (auditoria, criptografia, segurança em nível de linha...) e é altamente recomendável usar esse recurso em conjunto com eles, além de para proteger melhor os dados confidenciais no banco. Observe que esse recurso tem suporte apenas SQL Server a partir do 2016 e do banco de dados SQL do Azure. |

## <a id="salted-hash"></a>Verifique se as senhas estão armazenadas no formato de hash salted

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Hash de senha usando APIs de criptografia do .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Passos** | As senhas não devem ser armazenadas em bancos de dados de repositório de usuários personalizados. Os hashes de senha devem ser armazenados com valores Salt em vez disso. Verifique se o Salt do usuário é sempre exclusivo e se você aplica b-cript, s-cript ou PBKDF2 antes de armazenar a senha, com uma contagem de iteração de fator de trabalho mínima de loops 150.000 para eliminar a possibilidade de força bruta.| 

## <a id="db-encrypted"></a>Certifique-se de que os dados confidenciais nas colunas de dados estejam criptografados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Versão do SQL-todos |
| **Referências**              | [Criptografando dados confidenciais no SQL Server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [como: Criptografar uma coluna de dados em](https://msdn.microsoft.com/library/ms179331)SQL Server, [criptografar por certificado](https://msdn.microsoft.com/library/ms188061) |
| **Passos** | Dados confidenciais, como números de cartão de crédito, têm que ser criptografados no banco de dado. Os dados podem ser criptografados usando a criptografia em nível de coluna ou por uma função de aplicativo usando as funções de criptografia. |

## <a id="tde-enabled"></a>Verifique se a criptografia no nível do banco de dados (TDE) está habilitada

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Noções básicas sobre Transparent Data Encryption de SQL Server (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Passos** | O recurso de Transparent Data Encryption (TDE) no SQL Server ajuda a criptografar dados confidenciais em um banco de dado e a proteger as chaves que são usadas para criptografar os dados com um certificado. Isso impede que qualquer pessoa sem as chaves use os dados. O TDE protege os dados "em repouso", o que significa os arquivos de dados e de log. Ele fornece a capacidade de obedecer a muitas leis, regulamentos e diretrizes estabelecidas em vários setores. |

## <a id="backup"></a>Garantir que os backups de banco de dados sejam criptografados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL-V12, versão do SQL-MsSQL2014 |
| **Referências**              | [Criptografia de backup do banco de dados SQL](https://msdn.microsoft.com/library/dn449489) |
| **Passos** | SQL Server tem a capacidade de criptografar os dados durante a criação de um backup. Ao especificar o algoritmo de criptografia e o criptografador (um certificado ou uma chave assimétrica) ao criar um backup, é possível criar um arquivo de backup criptografado. |

## <a id="api-browser"></a>Garantir que os dados confidenciais relevantes para a API da Web não sejam armazenados no armazenamento do navegador

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | Provedor de identidade-ADFS, provedor de identidade-Azure AD |
| **Referências**              | N/A  |
| **Passos** | <p>Em determinadas implementações, os artefatos confidenciais relevantes para a autenticação da API Web são armazenados no armazenamento local do navegador. Por exemplo, os artefatos de autenticação do Azure AD, como Adal. token, Adal. nonce. token, Adal. Access. token. Key, Adal. token. Keys, Adal. State. login, Adal. Session. State, Adal. Expiration. Key etc.</p><p>Todos esses artefatos estão disponíveis mesmo após a saída ou o navegador ser fechado. Se um adversário obtiver acesso a esses artefatos, ele poderá reutilizá-los para acessar os recursos protegidos (APIs). Verifique se todos os artefatos confidenciais relacionados à API Web não estão armazenados no armazenamento do navegador. Nos casos em que o armazenamento do lado do cliente é inevitável (por exemplo, aplicativos de página única (SPA) que aproveitam os fluxos OpenIdConnect/OAuth implícitos precisam armazenar tokens de acesso localmente), use opções de armazenamento com não ter persistência. por exemplo, prefira SessionStorage a LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O trecho de código JavaScript abaixo é de uma biblioteca de autenticação personalizada que armazena artefatos de autenticação no armazenamento local. Essas implementações devem ser evitadas. 
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

## <a id="encrypt-docdb"></a>Criptografar dados confidenciais armazenados no Cosmos DB

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | BD de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Criptografe dados confidenciais no nível do aplicativo antes de armazenar no BD de documentos ou armazene dados confidenciais em outras soluções de armazenamento, como o armazenamento do Azure ou SQL do Azure| 

## <a id="disk-vm"></a>Usar Azure Disk Encryption para criptografar discos usados por máquinas virtuais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de VM IaaS do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Usando Azure Disk Encryption para criptografar discos usados por suas máquinas virtuais](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Passos** | <p>Azure Disk Encryption é um novo recurso que está atualmente em versão prévia. Esse recurso permite que você criptografe os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS. Para o Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. Para o Linux, os discos são criptografados usando a tecnologia DM-cript. Isso é integrado com o Azure Key Vault para permitir que você controle e gerencie as chaves de criptografia de disco. A solução de Azure Disk Encryption dá suporte aos três cenários de criptografia do cliente a seguir:</p><ul><li>Habilite a criptografia em novas VMs IaaS criadas de arquivos VHD criptografados pelo cliente e chaves de criptografia fornecidas pelo cliente, que são armazenadas em Azure Key Vault.</li><li>Habilite a criptografia em novas VMs IaaS criadas no Azure Marketplace.</li><li>Habilite a criptografia em VMs IaaS existentes já em execução no Azure.</li></ul>| 

## <a id="fabric-apps"></a>Criptografar segredos em aplicativos Service Fabric

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure |
| **Referências**              | [Gerenciando segredos em aplicativos Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Passos** | Os segredos podem ser qualquer informação confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser manipulados em texto sem formatação. Use Azure Key Vault para gerenciar chaves e segredos em aplicativos do Service Fabric. |

## <a id="modeling-teams"></a>Executar a modelagem de segurança e usar unidades/equipes de negócios onde for necessário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Executar a modelagem de segurança e usar unidades/equipes de negócios onde for necessário |

## <a id="entities"></a>Minimizar o acesso para compartilhar recursos em entidades críticas

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Minimizar o acesso para compartilhar recursos em entidades críticas |

## <a id="good-practices"></a>Treine os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e boas práticas de segurança

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Treine os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e boas práticas de segurança |

## <a id="exception-mgmt"></a>Incluir uma regra de padrões de desenvolvimento proscreva-se mostrando detalhes da configuração no gerenciamento de exceções

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Inclua uma regra de padrões de desenvolvimento de assinatura mostrando detalhes da configuração no gerenciamento de exceções fora do desenvolvimento. Teste para isso como parte das revisões de código ou inspeção periódica.|

## <a id="sse-preview"></a>Usar o Azure Criptografia do Serviço de Armazenamento (SSE) para dados em repouso (visualização)

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Storagetype-blob |
| **Referências**              | [Criptografia do Serviço de Armazenamento do Azure para dados em repouso (visualização)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Passos** | <p>O Azure Criptografia do Serviço de Armazenamento (SSE) para dados em repouso ajuda você a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Com esta funcionalidade, o Armazenamento do Azure encripta automaticamente os dados antes de continuar a armazenar e desencriptar antes da obtenção. A criptografia, a descriptografia e o gerenciamento de chaves são totalmente transparentes para os usuários. O SSE aplica-se somente a blobs de blocos, blobs de páginas e blobs de acréscimo. Os outros tipos de dados, incluindo tabelas, filas e arquivos, não serão criptografados.</p><p>Fluxo de trabalho de criptografia e descriptografia:</p><ul><li>O cliente habilita a criptografia na conta de armazenamento</li><li>Quando o cliente grava novos dados (colocar BLOB, colocar bloco, colocar página, etc.) no armazenamento de BLOBs; todas as gravações são criptografadas usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis</li><li>Quando o cliente precisa acessar dados (GET BLOB, etc.), os dados são descriptografados automaticamente antes de retornar ao usuário</li><li>Se a criptografia estiver desabilitada, as novas gravações não serão mais criptografadas e os dados criptografados existentes permanecerão criptografados até serem regravados pelo usuário. Enquanto a criptografia estiver habilitada, as gravações no armazenamento de BLOBs serão criptografadas. O estado dos dados não é alterado com o usuário alternando entre habilitar/desabilitar a criptografia para a conta de armazenamento</li><li>Todas as chaves de criptografia são armazenadas, criptografadas e gerenciadas pela Microsoft</li></ul><p>Observe que, neste momento, as chaves usadas para a criptografia são gerenciadas pela Microsoft. A Microsoft gera as chaves originalmente e gerencia o armazenamento seguro das chaves, bem como a rotação regular, conforme definido pela política interna da Microsoft. No futuro, os clientes terão a capacidade de gerenciar suas próprias chaves de criptografia > e fornecer um caminho de migração de chaves gerenciadas pela Microsoft para chaves gerenciadas pelo cliente.</p>| 

## <a id="client-storage"></a>Usar a criptografia do lado do cliente para armazenar dados confidenciais no armazenamento do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [tutorial: Criptografar e descriptografar BLOBs no](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/)armazenamento do Microsoft Azure usando Azure Key Vault, [armazenando dados com segurança no armazenamento de BLOBs do Azure com extensões de criptografia do Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Passos** | <p>O pacote NuGet da biblioteca de cliente do armazenamento do Azure para .NET dá suporte à criptografia de dados em aplicativos cliente antes do carregamento no armazenamento do Azure e à descriptografia de dados durante o download para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Aqui está uma breve descrição de como funciona a criptografia do lado do cliente:</p><ul><li>O SDK do cliente de armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo), que é uma chave simétrica de uso único</li><li>Os dados do cliente são criptografados usando este CEK</li><li>O CEK é então encapsulado (criptografado) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciado localmente ou armazenado em Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso ao KEK. Ele simplesmente invoca o algoritmo de encapsulamento de chaves que é fornecido pelo Key Vault. Os clientes podem optar por usar provedores personalizados para encapsulamento/desencapsulamento de chave se desejarem</li><li>Os dados criptografados são então carregados no serviço de armazenamento do Azure. Verifique os links na seção de referências para obter detalhes de implementação de nível baixo.</li></ul>|

## <a id="pii-phones"></a>Criptografar dados confidenciais ou PII gravados no armazenamento local de telefones

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Xamarin  |
| **Atributos**              | N/A  |
| **Referências**              | [Gerenciar configurações e recursos em seus dispositivos com políticas de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), valet de conjunto de [chaves](https://components.xamarin.com/view/square.valet) |
| **Passos** | <p>Se o aplicativo gravar informações confidenciais como PII do usuário (email, número de telefone, nome, sobrenome, preferências, etc.) -no sistema de arquivos do Mobile, ele deve ser criptografado antes de gravar no sistema de arquivos local. Se o aplicativo for um aplicativo empresarial, explore a possibilidade de publicar o aplicativo usando o Windows Intune.</p>|

### <a name="example"></a>Exemplo
O Intune pode ser configurado com as seguintes políticas de segurança para proteger dados confidenciais: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se o aplicativo não for um aplicativo empresarial, use o keystore fornecido pela plataforma, conjuntos de chaves para armazenamento de chave de criptografia, usando qual operação de criptografia pode ser executada no sistema de arquivos. O trecho de código a seguir mostra como acessar a chave do conjunto de chaves usando o xamarin: 
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

## <a id="binaries-end"></a>Ofuscar binários gerados antes de distribuir aos usuários finais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Ofuscação de criptografia para .net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Passos** | Os binários gerados (assemblies em apk) devem ser ofuscados para interromper a engenharia reversa de assemblies. Ferramentas como `CryptoObfuscator` o podem ser usadas para essa finalidade. |

## <a id="cert"></a>Definir clientCredentialtype como certificado ou Windows

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Fortalece](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Passos** | Usar um UsernameToken com uma senha de texto sem formatação em um canal não criptografado expõe a senha a invasores que podem detectar mensagens SOAP. Provedores de serviço que usam o UsernameToken podem aceitar senhas enviadas em texto sem formatação. O envio de senhas de texto sem formatação por um canal não criptografado pode expor a credencial a invasores que podem detectar a mensagem SOAP. | 

### <a name="example"></a>Exemplo
A seguinte configuração do provedor de serviços do WCF usa o UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Defina clientCredentialtype como certificado ou Windows. 

## <a id="security"></a>O modo de segurança do WCF não está habilitado

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Modo de segurança – transporte, modo de segurança-mensagem |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [conceitos básicos da revista de código de segurança do WCF](https://www.codemag.com/article/0611051) |
| **Passos** | Nenhuma segurança de transporte ou mensagem foi definida. Os aplicativos que transmitem mensagens sem segurança de transporte ou mensagem não podem garantir a integridade ou a confidencialidade das mensagens. Quando uma associação de segurança do WCF é definida como nenhuma, tanto a segurança de transporte quanto a de mensagem são desabilitadas. |

### <a name="example"></a>Exemplo
A configuração a seguir define o modo de segurança como nenhum. 
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
O modo de segurança em todas as associações de serviço há cinco modos de segurança possíveis: 
* Nenhum. Desativa a segurança. 
* Porta. Usa segurança de transporte para autenticação mútua e proteção de mensagem. 
* mensagem. Usa segurança de mensagem para autenticação mútua e proteção de mensagem. 
* Mesmo. Permite que você forneça configurações de transporte e segurança em nível de mensagem (somente o MSMQ dá suporte a isso). 
* TransportWithMessageCredential. As credenciais são passadas com a mensagem e a proteção de mensagem e a autenticação de servidor são fornecidas pela camada de transporte. 
* TransportCredentialOnly. As credenciais do cliente são passadas com a camada de transporte e nenhuma proteção de mensagem é aplicada. Use o transporte e a segurança de mensagens para proteger a integridade e a confidencialidade das mensagens. A configuração a seguir informa o serviço para usar a segurança de transporte com credenciais de mensagem.
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
