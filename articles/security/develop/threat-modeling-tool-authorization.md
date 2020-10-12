---
title: Autorização - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a mitigação da autorização na Ferramenta de Modelação de Ameaças. Consulte uma lista de potenciais ameaças e instruções de mitigação.
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
ms.openlocfilehash: 51d8b740ba1275b23bc17a58284141dce0d48fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89300005"
---
# <a name="security-frame-authorization--mitigations"></a>Quadro de Segurança: Autorização / Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança da máquina** | <ul><li>[Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo](#acl-restricted-access)</li><li>[Certifique-se de que o conteúdo de aplicação sensível específico do utilizador é armazenado no diretório de perfil do utilizador](#sensitive-directory)</li><li>[Certifique-se de que as aplicações implementadas são executadas com menos privilégios](#deployed-privileges)</li></ul> |
| **Aplicação Web** | <ul><li>[Impor a ordem de passo sequencial ao processar fluxos de lógica de negócio](#sequential-logic)</li><li>[Implementar mecanismo de limitação de taxas para evitar a enumeração](#rate-enumeration)</li><li>[Certifique-se de que a devida autorização está em vigor e o princípio dos privilégios menos adequados é seguido](#principle-least-privilege)</li><li>[As decisões de autorização de acesso à lógica do negócio e ao acesso aos recursos não devem basear-se em parâmetros de pedido de entrada](#logic-request-parameters)</li><li>[Certifique-se de que o conteúdo e os recursos não são enumeradores ou acessíveis através de uma navegação forçada](#enumerable-browsing)</li></ul> |
| **Base de dados** | <ul><li>[Certifique-se de que as contas menos privilegiadas são usadas para se ligar ao servidor Base de Dados](#privileged-server)</li><li>[Implementar RLS de Segurança de Nível de Linha para impedir que os inquilinos acedam aos dados uns dos outros](#rls-tenants)</li><li>[Função Sysadmin só deve ter utilizadores válidos necessários](#sysadmin-users)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Conecte-se ao Cloud Gateway usando fichas menos privilegiadas](#cloud-least-privileged)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Utilize uma chave SAS apenas de envio para gerar fichas de dispositivo](#sendonly-sas)</li><li>[Não utilize fichas de acesso que ofereçam acesso direto ao Centro de Eventos](#access-tokens-hub)</li><li>[Ligue ao Centro de Eventos usando chaves SAS que tenham as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **Documento Azure DB** | <ul><li>[Use fichas de recursos para ligar à Azure Cosmos DB sempre que possível](#resource-docdb)</li></ul> |
| **Fronteira da Confiança Azure** | <ul><li>[Permitir a gestão de acesso de grãos finos à Assinatura Azure utilizando o RBAC](#grained-rbac)</li></ul> |
| **Limite de confiança do tecido de serviço** | <ul><li>[Restringir o acesso do cliente a operações de cluster usando o RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Realizar modelação de segurança e utilizar a Segurança do Nível de Campo sempre que necessário](#modeling-field)</li></ul> |
| **Portal crm dinâmico** | <ul><li>[Realizar modelação de segurança das contas do portal tendo em conta que o modelo de segurança do portal difere do resto do CRM](#portal-security)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Conceder permissão de grãos finos a uma série de entidades no Armazenamento de MesaS Azure](#permission-entities)</li><li>[Permitir Role-Based Controlo de Acesso (RBAC) para a conta de armazenamento Azure usando O Gestor de Recursos Azure](#rbac-azure-manager)</li></ul> |
| **Cliente Móvel** | <ul><li>[Implementar jailbreak implícito ou deteção de enraizamento](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fraca no WCF](#weak-class-wcf)</li><li>[Controlo de autorização de implementação do WCF](#wcf-authz)</li></ul> |
| **API Web** | <ul><li>[Implementar mecanismo de autorização adequado na ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Efetue verificações de autorização no dispositivo se suportar várias ações que requerem diferentes níveis de permissão](#device-permission)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Efetue verificações de autorização no Field Gateway se apoiar várias ações que requerem diferentes níveis de permissão](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Certifique-se de que o conteúdo de aplicação sensível específico do utilizador é armazenado no diretório de perfil do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que o conteúdo de aplicação sensível específico do utilizador é armazenado no diretório de perfil do utilizador. Isto é para evitar que vários utilizadores da máquina acedam aos dados uns dos outros.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Certifique-se de que as aplicações implementadas são executadas com menos privilégios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que a aplicação implantada é executada com menos privilégios. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Impor a ordem de passo sequencial ao processar fluxos de lógica de negócio

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para verificar se esta fase foi executada por um utilizador genuíno, pretende-se impor a aplicação apenas para processar fluxos de lógica de negócio em ordem de passo sequencial, com todas as etapas a serem processadas em tempo humano realista, e não processar fora de ordem, passos ignorados, passos processados de outro utilizador, ou transações demasiado rápidas.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementar mecanismo de limitação de taxas para evitar a enumeração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os identificadores sensíveis são aleatórios. Implementar o controlo do CAPTCHA em páginas anónimas. Certifique-se de que erro e exceção não devem revelar dados específicos|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Certifique-se de que a devida autorização está em vigor e o princípio dos privilégios menos adequados é seguido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O princípio significa dar apenas a uma conta de utilizador os privilégios essenciais para que os utilizadores trabalhem. Por exemplo, um utilizador de backup não precisa de instalar software: por isso, o utilizador de backup tem direitos apenas para executar aplicações de backup e de backup. Quaisquer outros privilégios, como a instalação de novos softwares, estão bloqueados. O princípio aplica-se também a um utilizador de computador pessoal que normalmente trabalha numa conta normal de utilizador, e abre uma conta privilegiada e protegida por palavra-passe (isto é, um superuser) apenas quando a situação a exige. </p><p>Este princípio também pode ser aplicado às suas aplicações web. Em vez de depender exclusivamente de métodos de autenticação baseados em funções utilizando sessões, queremos atribuir privilégios aos utilizadores através de um sistema de autenticação Database-Based. Ainda utilizamos sessões para identificar se o utilizador foi iniciado corretamente, só agora em vez de atribuir esse utilizador com uma função específica que lhe atribuímos com privilégios para verificar quais as ações que tem o privilégio de realizar no sistema. Também um grande pro deste método é que, sempre que um utilizador tem de ser atribuído menos privilégios, as suas alterações serão aplicadas no voo, uma vez que a atribuição não depende da sessão que de outra forma teve de expirar primeiro.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>As decisões de autorização de acesso à lógica do negócio e ao acesso aos recursos não devem basear-se em parâmetros de pedido de entrada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Sempre que estiver a verificar se um utilizador está restrito a rever determinados dados, as restrições de acesso devem ser processadas do lado do servidor. O userID deve ser armazenado dentro de uma variável de sessão no início de sessão e deve ser usado para recuperar dados do utilizador a partir da base de dados |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Agora, um possível intruso não pode adulterar e alterar a operação da aplicação, uma vez que o identificador para recuperar os dados é tratado do lado do servidor.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Certifique-se de que o conteúdo e os recursos não são enumeradores ou acessíveis através de uma navegação forçada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os ficheiros estáticos e de configuração sensíveis não devem ser mantidos na raiz da web. Para que os conteúdos não sejam necessários para serem públicos, devem ser aplicados controlos de acesso adequados ou a remoção do conteúdo em si.</p><p>Além disso, a navegação forçada é geralmente combinada com técnicas brute Force para recolher informações, tentando aceder ao maior número possível de URLs para enumerar diretórios e ficheiros em um servidor. Os atacantes podem verificar todas as variações dos ficheiros comumente existentes. Por exemplo, uma pesquisa de ficheiros de palavra-passe abrangeria ficheiros incluindo psswd.txt, password.htm, password.dat e outras variações.</p><p>Para mitigar isto, devem ser incluídas capacidades de deteção de tentativas de força bruta.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Certifique-se de que as contas menos privilegiadas são usadas para se ligar ao servidor Base de Dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [SqL permissões hierarquia,](https://docs.microsoft.com/sql/relational-databases/security/permissions-hierarchy-database-engine) [sql securables](https://docs.microsoft.com/sql/relational-databases/security/securables) |
| **Passos** | As contas menos privilegiadas devem ser utilizadas para se ligarem à base de dados. O login de aplicação deve ser restringido na base de dados e deve apenas executar procedimentos armazenados selecionados. O login da aplicação não deve ter acesso direto à mesa. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementar RLS de Segurança de Nível de Linha para impedir que os inquilinos acedam aos dados uns dos outros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, VERSÃO SQL - MsSQL2016 |
| **Referências**              | [Sql Server Row-Level Security (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Passos** | <p>A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução).</p><p>Row-Level Security (RLS) simplifica a conceção e codificação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.</p><p>A lógica de restrição de acesso está localizada no nível da base de dados e não longe dos dados de outro nível de aplicação. O sistema de base de dados aplica as restrições de acesso sempre que o acesso aos dados é tentado a partir de qualquer nível. Isto torna o sistema de segurança mais fiável e robusto, reduzindo a área de superfície do sistema de segurança.</p><p>|

Por favor, note que o RLS como uma funcionalidade de base de dados fora da caixa é aplicável apenas ao SQL Server a partir de 2016, Azure SQL Database e SQL Managed Instance. Se a função RLS fora da caixa não for implementada, deve ser assegurado que o acesso aos dados é restrito usando visualizações e procedimentos

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Função Sysadmin só deve ter utilizadores válidos necessários

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [SqL permissões hierarquia,](https://docs.microsoft.com/sql/relational-databases/security/permissions-hierarchy-database-engine) [sql securables](https://docs.microsoft.com/sql/relational-databases/security/securables) |
| **Passos** | Os membros da função do servidor fixo SysAdmin devem ser muito limitados e nunca conter contas utilizadas por aplicações.  Por favor, reveja a lista de utilizadores na função e remova quaisquer contas desnecessárias|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Conecte-se ao Cloud Gateway usando fichas menos privilegiadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Controlo de acesso do Hub Iot](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Passos** | Fornecer permissões de menor privilégio a vários componentes que se ligam ao Cloud Gateway (IoT Hub). Exemplo típico é – A gestão/provisão de dispositivos utiliza a leitura/escrita de registo, o processador de eventos (ASA) utiliza o Service Connect. Dispositivos individuais ligam-se usando credenciais do Dispositivo|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Utilize uma chave SAS apenas de envio para gerar fichas de dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Uma chave SAS é usada para gerar fichas individuais do dispositivo. Utilize uma chave SAS apenas de envio enquanto gera o token do dispositivo para um determinado editor|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Não utilize fichas de acesso que ofereçam acesso direto ao Centro de Eventos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Um sinal que concede acesso direto ao centro de eventos não deve ser dado ao dispositivo. A utilização de um símbolo menos privilegiado para o dispositivo que dê acesso apenas a uma editora ajudaria a identificá-lo e a despromudê-lo se se encontrasse um dispositivo fraudulento ou comprometido.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Ligue ao Centro de Eventos usando chaves SAS que tenham as permissões mínimas necessárias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Fornecer permissões de menor privilégio para várias aplicações de back-end que se conectam ao Centro de Eventos. Gerencie as teclas SAS separadas para cada aplicação de back-end e apenas forneça as permissões necessárias - Enviar, Receber ou Gerir para elas.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Use fichas de recursos para ligar ao Cosmos DB sempre que possível

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Um token de recurso está associado a um recurso de permissão DB Azure Cosmos e captura a relação entre o utilizador de uma base de dados e a permissão que o utilizador tem para um recurso específico de aplicação Azure Cosmos DB (por exemplo, recolha, documento). Utilize sempre um token de recursos para aceder ao DB Azure Cosmos se o cliente não for confiado a chaves de manejamento ou apenas de leitura - como uma aplicação de utilizador final como um cliente móvel ou de desktop. Utilize teclas Master ou apenas de leitura a partir de aplicações de backend que podem armazenar estas chaves de forma segura.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Permitir a gestão de acesso de grãos finos à Assinatura Azure utilizando o RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira da Confiança Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Passos** | O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acesso de grãos finos para o Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Restringir o acesso do cliente a operações de cluster usando o RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Controlo de acesso baseado em funções para clientes de Tecidos de Serviço](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Passos** | <p>A Azure Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes que estão ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso permite ao administrador do cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais seguro.</p><p>Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, apenas leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços.</p><p>Especifica as duas funções de cliente (administrador e cliente) no momento da criação do cluster, fornecendo certificados separados para cada um.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Realizar modelação de segurança e utilizar a Segurança do Nível de Campo sempre que necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Realizar modelação de segurança e utilizar a Segurança do Nível de Campo sempre que necessário|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Realizar modelação de segurança das contas do portal tendo em conta que o modelo de segurança do portal difere do resto do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Portal crm dinâmico | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Realizar modelação de segurança das contas do portal tendo em conta que o modelo de segurança do portal difere do resto do CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Conceder permissão de grãos finos a uma série de entidades no Armazenamento de MesaS Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | StorageType - Tabela |
| **Referências**              | [Como delegar o acesso a objetos na sua conta de armazenamento Azure usando SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Passos** | Em certos cenários comerciais, o Azure Table Storage pode ser necessário para armazenar dados sensíveis que atendam a diferentes partes. Por exemplo, dados sensíveis relativos a diferentes países/regiões. Nesses casos, as assinaturas SAS podem ser construídas especificando as gamas de chaves de partição e linha, de modo a que um utilizador possa aceder a dados específicos de um determinado país/região.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Permitir Role-Based Controlo de Acesso (RBAC) para a conta de armazenamento Azure usando O Gestor de Recursos Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como garantir a sua conta de armazenamento com Role-Based Access Control (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Passos** | <p>Quando criar uma nova conta de armazenamento, selecione um modelo de implementação do Classic ou do Azure Resource Manager. O modelo clássico de criação de recursos em Azure só permite o acesso total ou nada à subscrição e, por sua vez, à conta de armazenamento.</p><p>Com o modelo Azure Resource Manager, coloca a conta de armazenamento num grupo de recursos e controla o acesso ao plano de gestão dessa conta de armazenamento específica utilizando o Azure Ative Directory. Por exemplo, pode dar a utilizadores específicos a possibilidade de acederem às chaves da conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não conseguem aceder às chaves da conta de armazenamento.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementar jailbreak implícito ou deteção de enraizamento

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A aplicação deve salvaguardar a sua própria configuração e dados do utilizador no caso de o telefone estar enraizado ou a cadeia quebrada. A rutura de raiz/prisão implica acesso não autorizado, o que os utilizadores normais não fazem nos seus próprios telefones. Por isso, a aplicação deve ter lógica implícita de deteção no arranque da aplicação, para detetar se o telefone está enraizado.</p><p>A lógica de deteção pode ser simplesmente aceder a ficheiros que normalmente só o utilizador raiz pode aceder, por exemplo:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/sistema/bin/su</li><li>/sistema/xbin/su</li><li>/dados/local/xbin/su</li><li>/dados/local/bin/su</li><li>/sistema/sd/xbin/su</li><li>/sistema/bin/failsafe/su</li><li>/dados/local/su</li></ul><p>Se a aplicação puder aceder a qualquer um destes ficheiros, denota que a aplicação está a funcionar como utilizador principal.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Referência de classe fraca no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>O sistema utiliza uma referência de classe fraca, que pode permitir que um intruso execute código não autorizado. O programa refere uma classe definida pelo utilizador que não é identificada de forma única. Quando .NET carrega esta classe fracamente identificada, o carregador de tipo CLR procura a classe nos seguintes locais na ordem especificada:</p><ol><li>Se a montagem do tipo for conhecida, o carregador procura as localizações de redirecionamento do ficheiro de configuração, GAC, o conjunto atual usando informações de configuração e o diretório de base de aplicação</li><li>Se o conjunto for desconhecido, o carregador procura a montagem atual, o mscorlib, e a localização devolvida pelo manipulador de eventos TypeResolve</li><li>Esta ordem de pesquisa CLR pode ser modificada com ganchos como o mecanismo de encaminhamento do tipo e o evento AppDomain.TypeResolve</li></ol><p>Se um intruso explorar a ordem de pesquisa CLR criando uma classe alternativa com o mesmo nome e colocando-a num local alternativo que o CLR carregará primeiro, o CLR executará involuntariamente o código fornecido pelo intruso</p>|

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF a adicionar uma classe de comportamento personalizado a uma determinada extensão wcf.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
A utilização de nomes totalmente qualificados (fortes) identifica um tipo e aumenta ainda mais a segurança do seu sistema. Utilize nomes de montagem totalmente qualificados ao registar tipos nos ficheiros machine.config e app.config.

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF a adicionar classe de comportamento personalizado fortemente referenciada a uma determinada extensão wcf.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Controlo de autorização de implementação do WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>Este serviço não utiliza um controlo de autorização. Quando um cliente liga para um determinado serviço WCF, o WCF fornece vários esquemas de autorização que verificam se o chamador tem permissão para executar o método de serviço no servidor. Se os controlos de autorização não estiverem ativados para os serviços wcf, um utilizador autenticado pode alcançar uma escalada de privilégio.</p>|

### <a name="example"></a>Exemplo
A seguinte configuração instrui a WCF a não verificar o nível de autorização do cliente ao executar o serviço:
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
Utilize um sistema de autorização de serviço para verificar se o autor do serviço está autorizado a fazê-lo. O WCF fornece dois modos e permite a definição de um esquema de autorização personalizado. O modo UseWindowsGroups utiliza funções e utilizadores windows e o modo UseAspNetRoles utiliza um fornecedor de funções ASP.NET, como o SQL Server, para autenticar.

### <a name="example"></a>Exemplo
A seguinte configuração instrui o WCF a certificar-se de que o cliente faz parte do grupo de Administradores antes de executar o serviço Add:
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

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementar mecanismo de autorização adequado na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/A, Fornecedor de Identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | [Autenticação e Autorização em ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Passos** | <p>As informações de função para os utilizadores da aplicação podem ser derivadas de Azure AD ou ADFS alegações se a aplicação depender deles como fornecedor de identidade ou a própria aplicação pode fornecer. Em qualquer um destes casos, a implementação da autorização personalizada deve validar a informação da função do utilizador.</p><p>As informações de função para os utilizadores da aplicação podem ser derivadas de Azure AD ou ADFS alegações se a aplicação depender deles como fornecedor de identidade ou a própria aplicação pode fornecer. Em qualquer um destes casos, a implementação da autorização personalizada deve validar a informação da função do utilizador.</p>

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
Todos os controladores e métodos de ação que precisam de ser protegidos devem ser decorados com atributo supramotado.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Efetue verificações de autorização no dispositivo se suportar várias ações que requerem diferentes níveis de permissão

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O Dispositivo deve autorizar o autor da chamada a verificar se o autor da chamada tem as permissões necessárias para realizar a ação solicitada. Por exemplo, digamos que o dispositivo é um Smart Door Lock que pode ser monitorizado a partir da nuvem, além de que fornece funcionalidades como trancar remotamente a porta.</p><p>O Smart Door Lock só fornece uma funcionalidade de desbloqueio quando alguém se aproxima fisicamente da porta com um Cartão. Neste caso, a implementação do comando e do controlo remoto deve ser feita de modo a não fornecer qualquer funcionalidade para desbloquear a porta, uma vez que o gateway de nuvem não está autorizado a enviar um comando para destrancar a porta.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Efetue verificações de autorização no Field Gateway se apoiar várias ações que requerem diferentes níveis de permissão

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Field Gateway deve autorizar o autor da chamada a verificar se o autor da chamada tem as permissões necessárias para realizar a ação solicitada. Por exemplo, devem existir permissões diferentes para uma interface de utilizador/API administração utilizada para configurar um gateway de campo v/s dispositivos que se ligam a ele.|
