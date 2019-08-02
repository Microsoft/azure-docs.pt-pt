---
title: Autorização-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728374"
---
# <a name="security-frame-authorization--mitigations"></a>Quadro de segurança: Autorização | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de confiança do computador** | <ul><li>[Verifique se as ACLs apropriadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo](#acl-restricted-access)</li><li>[Verifique se o conteúdo confidencial do aplicativo específico do usuário está armazenado no diretório do perfil do usuário](#sensitive-directory)</li><li>[Verifique se os aplicativos implantados são executados com privilégios mínimos](#deployed-privileges)</li></ul> |
| **Aplicativo Web** | <ul><li>[Impor ordem de etapa sequencial ao processar fluxos de lógica de negócios](#sequential-logic)</li><li>[Implementar o mecanismo de limitação de taxa para impedir a enumeração](#rate-enumeration)</li><li>[Verifique se a autorização adequada está em vigor e se o princípio de privilégios mínimos é seguido](#principle-least-privilege)</li><li>[As decisões de autorização de acesso a recursos e lógica de negócios não devem ser baseadas em parâmetros de solicitação de entrada](#logic-request-parameters)</li><li>[Garantir que o conteúdo e os recursos não sejam enumeráveis ou acessíveis por meio de navegação forçada](#enumerable-browsing)</li></ul> |
| **Base de Dados** | <ul><li>[Verifique se as contas com privilégios mínimos são usadas para se conectar ao servidor de banco de dados](#privileged-server)</li><li>[Implemente Segurança em Nível de Linha RLS para impedir que os locatários acessem os dados uns dos outros](#rls-tenants)</li><li>[A função sysadmin só deve ter usuários necessários válidos](#sysadmin-users)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Conectar-se ao gateway de nuvem usando tokens com privilégios mínimos](#cloud-least-privileged)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Usar uma chave SAS de permissões somente de envio para gerar tokens de dispositivo](#sendonly-sas)</li><li>[Não use tokens de acesso que forneçam acesso direto ao Hub de eventos](#access-tokens-hub)</li><li>[Conectar-se ao Hub de eventos usando chaves SAS que têm as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **BD de documentos do Azure** | <ul><li>[Usar tokens de recurso para se conectar a Azure Cosmos DB sempre que possível](#resource-docdb)</li></ul> |
| **Limite de confiança do Azure** | <ul><li>[Habilitar o gerenciamento de acesso refinado à assinatura do Azure usando o RBAC](#grained-rbac)</li></ul> |
| **Limite de confiança Service Fabric** | <ul><li>[Restringir o acesso do cliente às operações de cluster usando o RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Executar a modelagem de segurança e usar a segurança em nível de campo quando necessário](#modeling-field)</li></ul> |
| **Portal do Dynamics CRM** | <ul><li>[Executar a modelagem de segurança de contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM](#portal-security)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Conceder permissão refinada em um intervalo de entidades no armazenamento de tabelas do Azure](#permission-entities)</li><li>[Habilitar o RBAC (controle de acesso baseado em função) para a conta de armazenamento do Azure usando Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar a detecção implícita de jailbreak ou raiz](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fraca no WCF](#weak-class-wcf)</li><li>[Controle de autorização de implementação do WCF](#wcf-authz)</li></ul> |
| **API Web** | <ul><li>[Implementar o mecanismo de autorização adequado no ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Executar verificações de autorização no dispositivo se ele der suporte a várias ações que exigem diferentes níveis de permissão](#device-permission)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Executar verificações de autorização no gateway de campo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Verifique se as ACLs apropriadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se as ACLs apropriadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo|

## <a id="sensitive-directory"></a>Verifique se o conteúdo confidencial do aplicativo específico do usuário está armazenado no diretório do perfil do usuário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se o conteúdo confidencial do aplicativo específico do usuário está armazenado no diretório do perfil do usuário. Isso é para impedir que vários usuários do computador acessem os dados uns dos outros.|

## <a id="deployed-privileges"></a>Verifique se os aplicativos implantados são executados com privilégios mínimos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se o aplicativo implantado é executado com privilégios mínimos. |

## <a id="sequential-logic"></a>Impor ordem de etapa sequencial ao processar fluxos de lógica de negócios

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Para verificar se esse estágio foi executado por um usuário original, você deseja impor que o aplicativo processe apenas os fluxos de lógica de negócios na ordem de etapa sequencial, com todas as etapas sendo processadas no horário humano realista e não processar fora de ordem, etapas ignoradas , processadas etapas de outro usuário ou transações enviadas com muita rapidez.|

## <a id="rate-enumeration"></a>Implementar o mecanismo de limitação de taxa para impedir a enumeração

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se os identificadores confidenciais são aleatórios. Implemente o controle CAPTCHA em páginas anônimas. Garantir que o erro e a exceção não revelam dados específicos|

## <a id="principle-least-privilege"></a>Verifique se a autorização adequada está em vigor e se o princípio de privilégios mínimos é seguido

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O princípio significa dar a uma conta de usuário apenas os privilégios que são essenciais para que os usuários funcionem. Por exemplo, um usuário de backup não precisa instalar o software: portanto, o usuário de backup tem direitos apenas para executar aplicativos relacionados a backup e backup. Quaisquer outros privilégios, como a instalação de novos softwares, são bloqueados. O princípio se aplica também a um usuário de computador pessoal que geralmente trabalha em uma conta de usuário normal e abre uma conta privilegiada protegida por senha (ou seja, um superusuário) somente quando a situação absolutamente exige isso. </p><p>Esse princípio também pode ser aplicado aos seus aplicativos Web. Em vez de apenas depender de métodos de autenticação baseados em função usando sessões, queremos atribuir privilégios a usuários por meio de um sistema de autenticação baseado em banco de dados. Ainda usamos sessões para identificar se o usuário estava conectado corretamente, apenas em vez de atribuir a esse usuário uma função específica que atribuímos a ele privilégios para verificar quais ações ele tem o privilégio de executar no sistema. Além disso, um grande profissional desse método é, sempre que um usuário precisa receber menos privilégios, suas alterações serão aplicadas imediatamente, já que a atribuição não depende da sessão que, de outra forma, tinha de expirar primeiro.</p>|

## <a id="logic-request-parameters"></a>As decisões de autorização de acesso a recursos e lógica de negócios não devem ser baseadas em parâmetros de solicitação de entrada

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Sempre que você estiver verificando se um usuário está restrito a examinar determinados dados, as restrições de acesso devem ser processadas no lado do servidor. A userID deve ser armazenada dentro de uma variável de sessão no logon e deve ser usada para recuperar os dados do usuário |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Agora um possível invasor não pode violar e alterar a operação do aplicativo, pois o identificador para recuperar os dados é tratado no lado do servidor.

## <a id="enumerable-browsing"></a>Garantir que o conteúdo e os recursos não sejam enumeráveis ou acessíveis por meio de navegação forçada

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Arquivos de configuração e estáticos confidenciais não devem ser mantidos na raiz da Web. Para que o conteúdo não precise ser público, os controles de acesso apropriados devem ser aplicados ou removidos do próprio conteúdo.</p><p>Além disso, a navegação forçada geralmente é combinada com técnicas de força bruta para coletar informações ao tentar acessar o máximo de URLs possível para enumerar diretórios e arquivos em um servidor. Os invasores podem verificar todas as variações de arquivos comumente existentes. Por exemplo, uma pesquisa de arquivo de senha abrange arquivos, incluindo psswd. txt, password. htm, password. dat e outras variações.</p><p>Para atenuar isso, os recursos para detecção de tentativas de força bruta devem ser incluídos.</p>|

## <a id="privileged-server"></a>Verifique se as contas com privilégios mínimos são usadas para se conectar ao servidor de banco de dados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Hierarquia de permissões do banco de dados SQL](https://msdn.microsoft.com/library/ms191465), [protegíveis do banco de dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Contas com privilégios mínimos devem ser usadas para se conectar ao banco de dados. O logon do aplicativo deve ser restrito no banco de dados e só deve executar procedimentos armazenados selecionados. O logon do aplicativo não deve ter acesso direto à tabela. |

## <a id="rls-tenants"></a>Implemente Segurança em Nível de Linha RLS para impedir que os locatários acessem os dados uns dos outros

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | Versão do SQL-V12, versão do SQL-MsSQL2016 |
| **Referências**              | [SQL Server de segurança em nível de linha (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Passos** | <p>A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução).</p><p>A RLS (segurança em nível de linha) simplifica o design e a codificação de segurança em seu aplicativo. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.</p><p>A lógica de restrição de acesso está localizada na camada de banco de dados, em vez de ficar distante da data em outra camada de aplicativo. O sistema de banco de dados aplica as restrições de acesso toda vez que o acesso a dados é tentado em qualquer camada. Isso torna o sistema de segurança mais confiável e robusto, reduzindo a área de superfície do sistema de segurança.</p><p>|

Observe que a RLS como um recurso de banco de dados pronto para uso é aplicável somente a SQL Server iniciando o 2016 e o banco de dados SQL do Azure. Se o recurso de RLS pronto para uso não for implementado, deverá garantir que o acesso a dados seja restrito usando exibições e procedimentos

## <a id="sysadmin-users"></a>A função sysadmin só deve ter usuários necessários válidos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Hierarquia de permissões do banco de dados SQL](https://msdn.microsoft.com/library/ms191465), [protegíveis do banco de dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Os membros da função de servidor fixa SysAdmin devem ser muito limitados e nunca conter contas usadas por aplicativos.  Examine a lista de usuários na função e remova todas as contas desnecessárias|

## <a id="cloud-least-privileged"></a>Conectar-se ao gateway de nuvem usando tokens com privilégios mínimos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Opção de gateway-Hub IoT do Azure |
| **Referências**              | [Controle de acesso do Hub IOT](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Passos** | Forneça permissões de privilégios mínimos para vários componentes que se conectam ao gateway de nuvem (Hub IoT). O exemplo típico é – o componente de gerenciamento de dispositivos/provisionamento usa registryread/Write, o processador de eventos (ASA) usa o Service Connect. Dispositivos individuais se conectam usando credenciais do dispositivo|

## <a id="sendonly-sas"></a>Usar uma chave SAS de permissões somente de envio para gerar tokens de dispositivo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Visão geral do modelo de segurança e autenticação dos hubs de eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Uma chave SAS é usada para gerar tokens de dispositivo individuais. Usar uma chave SAS de permissões somente de envio ao gerar o token de dispositivo para um determinado Publicador|

## <a id="access-tokens-hub"></a>Não use tokens de acesso que forneçam acesso direto ao Hub de eventos

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Visão geral do modelo de segurança e autenticação dos hubs de eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Um token que concede acesso direto ao Hub de eventos não deve ser fornecido ao dispositivo. Usar um token com privilégios mínimos para o dispositivo que fornece acesso somente a um Publicador ajudaria a identificá-lo e Blacklist-lo se fosse considerado um dispositivo não autorizado ou comprometido.|

## <a id="sas-minimum-permissions"></a>Conectar-se ao Hub de eventos usando chaves SAS que têm as permissões mínimas necessárias

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Visão geral do modelo de segurança e autenticação dos hubs de eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Forneça permissões de privilégios mínimos para vários aplicativos de back-end que se conectam ao Hub de eventos. Gere chaves SAS separadas para cada aplicativo de back-end e forneça apenas as permissões necessárias-enviar, receber ou gerenciar para elas.|

## <a id="resource-docdb"></a>Usar tokens de recurso para se conectar a Cosmos DB sempre que possível

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | BD de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Um token de recurso é associado a um Azure Cosmos DB recurso de permissão e captura a relação entre o usuário de um banco de dados e a permissão que o usuário tem para um recurso de aplicativo Azure Cosmos DB específico (por exemplo, coleção, documento). Sempre use um token de recurso para acessar a Azure Cosmos DB se o cliente não puder ser confiável com chaves mestre de tratamento ou somente leitura, como um aplicativo de usuário final, como um cliente móvel ou de desktop. Use chave mestra ou chaves somente leitura de aplicativos de back-end que podem armazenar essas chaves com segurança.|

## <a id="grained-rbac"></a>Habilitar o gerenciamento de acesso refinado à assinatura do Azure usando o RBAC

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Passos** | O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos.|

## <a id="cluster-rbac"></a>Restringir o acesso do cliente às operações de cluster usando o RBAC

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure |
| **Referências**              | [Controle de acesso baseado em função para clientes Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Passos** | <p>O Azure Service Fabric dá suporte a dois tipos diferentes de controle de acesso para clientes que estão conectados a um Cluster Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.</p><p>Os administradores têm acesso completo aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.</p><p>Você especifica as duas funções de cliente (administrador e cliente) no momento da criação do cluster, fornecendo certificados separados para cada um.</p>|

## <a id="modeling-field"></a>Executar a modelagem de segurança e usar a segurança em nível de campo quando necessário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Executar a modelagem de segurança e usar a segurança em nível de campo quando necessário|

## <a id="portal-security"></a>Executar a modelagem de segurança de contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Portal do Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Executar a modelagem de segurança de contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM|

## <a id="permission-entities"></a>Conceder permissão refinada em um intervalo de entidades no armazenamento de tabelas do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Storagetype-tabela |
| **Referências**              | [Como delegar acesso a objetos em sua conta de armazenamento do Azure usando SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Passos** | Em determinados cenários de negócios, o armazenamento de tabelas do Azure pode ser necessário para armazenar dados confidenciais que atendem a partes diferentes. Por exemplo, dados confidenciais pertencentes a países/regiões diferentes. Nesses casos, as assinaturas SAS podem ser construídas especificando os intervalos de chaves de partição e de linha, de modo que um usuário pode acessar dados específicos para um país/região específico.| 

## <a id="rbac-azure-manager"></a>Habilitar o RBAC (controle de acesso baseado em função) para a conta de armazenamento do Azure usando Azure Resource Manager

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Como proteger sua conta de armazenamento com RBAC (controle de acesso baseado em função)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Passos** | <p>Ao criar uma nova conta de armazenamento, você seleciona um modelo de implantação clássico ou Azure Resource Manager. O modelo clássico de criação de recursos no Azure permite apenas o acesso de tudo ou nada à assinatura e, por sua vez, à conta de armazenamento.</p><p>Com o modelo de Azure Resource Manager, você coloca a conta de armazenamento em um grupo de recursos e controla o acesso ao plano de gerenciamento dessa conta de armazenamento específica usando Azure Active Directory. Por exemplo, você pode conceder a usuários específicos a capacidade de acessar as chaves da conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar as chaves da conta de armazenamento.</p>|

## <a id="rooting-detection"></a>Implementar a detecção implícita de jailbreak ou raiz

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O aplicativo deve proteger sua própria configuração e dados de usuário no caso de o telefone ter raiz ou com Jail Break quebrado. A quebra de raiz/com Jail Break implica o acesso não autorizado, que os usuários normais não farão em seus próprios telefones. Portanto, o aplicativo deve ter lógica de detecção implícita na inicialização do aplicativo, para detectar se o telefone tem raiz.</p><p>A lógica de detecção pode ser simplesmente acessar arquivos que normalmente apenas o usuário raiz pode acessar, por exemplo:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Se o aplicativo puder acessar qualquer um desses arquivos, ele indicará que o aplicativo está sendo executado como usuário raiz.</p>|

## <a id="weak-class-wcf"></a>Referência de classe fraca no WCF

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>O sistema usa uma referência de classe fraca, que pode permitir que um invasor execute código não autorizado. O programa faz referência a uma classe definida pelo usuário que não é identificada exclusivamente. Quando o .NET carrega essa classe fracamente identificada, o carregador do tipo CLR procura a classe nos seguintes locais na ordem especificada:</p><ol><li>Se o assembly do tipo for conhecido, o carregador pesquisará os locais de redirecionamento do arquivo de configuração, o GAC, o assembly atual usando as informações de configuração e o diretório base do aplicativo</li><li>Se o assembly for desconhecido, o carregador pesquisará o assembly atual, mscorlib e o local retornado pelo manipulador de eventos TypeResolve</li><li>Essa ordem de pesquisa do CLR pode ser modificada com ganchos como o mecanismo de encaminhamento de tipo e o evento AppDomain. TypeResolve</li></ol><p>Se um invasor explorar a ordem de pesquisa do CLR criando uma classe alternativa com o mesmo nome e colocando-a em um local alternativo que o CLR carregará primeiro, o CLR executará involuntariamente o código fornecido pelo invasor</p>|

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do arquivo de configuração do WCF abaixo instrui o WCF a adicionar uma classe de comportamento Personalizada a uma extensão específica do WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
O uso de nomes totalmente qualificados (fortes) identifica exclusivamente um tipo e aumenta ainda mais a segurança do sistema. Use nomes de assembly totalmente qualificados ao registrar tipos nos arquivos Machine. config e app. config.

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do arquivo de configuração do WCF abaixo instrui o WCF a adicionar uma classe de comportamento personalizado com referência forte a uma extensão específica do WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>Controle de autorização de implementação do WCF

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>Esse serviço não usa um controle de autorização. Quando um cliente chama um serviço WCF específico, o WCF fornece vários esquemas de autorização que verificam se o chamador tem permissão para executar o método de serviço no servidor. Se os controles de autorização não estiverem habilitados para os serviços WCF, um usuário autenticado poderá obter elevação de privilégio.</p>|

### <a name="example"></a>Exemplo
A configuração a seguir instrui o WCF a não verificar o nível de autorização do cliente ao executar o serviço:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Use um esquema de autorização de serviço para verificar se o chamador do método de serviço está autorizado a fazer isso. O WCF fornece dois modos e permite a definição de um esquema de autorização personalizado. O modo UseWindowsGroups usa funções e usuários do Windows e o modo UseAspNetRoles usa um provedor de função ASP.NET, como SQL Server, para autenticar.

### <a name="example"></a>Exemplo
A configuração a seguir instrui o WCF a garantir que o cliente faça parte do grupo de administradores antes de executar o serviço adicionar:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
O serviço é então declarado como o seguinte:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementar o mecanismo de autorização adequado no ASP.NET Web API

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/A, provedor de identidade-ADFS, provedor de identidade-Azure AD |
| **Referências**              | [Autenticação e autorização no ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Passos** | <p>As informações de função para os usuários do aplicativo podem ser derivadas do Azure AD ou de declarações do ADFS, caso o aplicativo dependa delas como provedor de identidade ou o próprio aplicativo pode fornecer isso. Em qualquer um desses casos, a implementação de autorização personalizada deve validar as informações de função de usuário.</p><p>As informações de função para os usuários do aplicativo podem ser derivadas do Azure AD ou de declarações do ADFS, caso o aplicativo dependa delas como provedor de identidade ou o próprio aplicativo pode fornecer isso. Em qualquer um desses casos, a implementação de autorização personalizada deve validar as informações de função de usuário.</p>

### <a name="example"></a>Exemplo
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Todos os controladores e métodos de ação que precisam ser protegidos devem ser decorados com o atributo acima.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Executar verificações de autorização no dispositivo se ele der suporte a várias ações que exigem diferentes níveis de permissão

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O dispositivo deve autorizar o chamador a verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Por exemplo, digamos que o dispositivo é um bloqueio de porta inteligente que pode ser monitorado na nuvem, além de fornecer funcionalidades como bloquear remotamente a porta.</p><p>O bloqueio de porta inteligente fornece a funcionalidade de desbloqueio somente quando alguém vem fisicamente perto da porta com um cartão. Nesse caso, a implementação do comando remoto e do controle deve ser feita de forma que ele não forneça nenhuma funcionalidade para desbloquear a porta, pois o gateway de nuvem não está autorizado a enviar um comando para desbloquear a porta.</p>|

## <a id="field-permission"></a>Executar verificações de autorização no gateway de campo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | O gateway de campo deve autorizar o chamador a verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Por exemplo, deve haver permissões diferentes para uma interface de usuário/API do administrador usada para configurar um gateway de campo de dispositivos v/s que se conectam a ele.|
