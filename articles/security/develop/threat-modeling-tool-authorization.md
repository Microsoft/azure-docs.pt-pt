---
title: Autorização - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728374"
---
# <a name="security-frame-authorization--mitigations"></a>Quadro de Segurança: Autorização / Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança de máquina** | <ul><li>[Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo](#acl-restricted-access)</li><li>[Certifique-se de que o conteúdo sensível da aplicação específica do utilizador é armazenado no diretório do perfil do utilizador](#sensitive-directory)</li><li>[Certifique-se de que as aplicações implementadas são executadas com menos privilégios](#deployed-privileges)</li></ul> |
| **Aplicação Web** | <ul><li>[Impor a ordem de passo sequencial ao processar fluxos lógicos de negócio](#sequential-logic)</li><li>[Implementar mecanismo de limitação da taxa para evitar a enumeração](#rate-enumeration)</li><li>[Certifique-se de que a autorização adequada está em vigor e que o princípio dos menos privilégios é seguido](#principle-least-privilege)</li><li>[A lógica empresarial e as decisões de autorização de acesso a recursos não devem basear-se em parâmetros de pedido de entrada](#logic-request-parameters)</li><li>[Certifique-se de que os conteúdos e recursos não são enumerados ou acessíveis através de uma navegação forçada](#enumerable-browsing)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que as contas menos privilegiadas são usadas para ligar ao servidor base de dados](#privileged-server)</li><li>[Implementar RLS de segurança de nível de linha para evitar que os inquilinos acedam aos dados uns dos outros](#rls-tenants)</li><li>[A função Sysadmin apenas deve ter utilizadores válidos necessários](#sysadmin-users)</li></ul> |
| **Gateway da nuvem iot** | <ul><li>[Ligue-se ao Cloud Gateway usando fichas menos privilegiadas](#cloud-least-privileged)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Utilize uma chave SAS apenas com permissões de envio para gerar fichas de dispositivo](#sendonly-sas)</li><li>[Não utilize fichas de acesso que forneçam acesso direto ao Centro de Eventos](#access-tokens-hub)</li><li>[Ligue-se ao Event Hub utilizando teclas SAS que tenham as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **Documento Azure DB** | <ul><li>[Use fichas de recurso para ligar ao Azure Cosmos DB sempre que possível](#resource-docdb)</li></ul> |
| **Fronteira da Azure Trust** | <ul><li>[Permitir a gestão de acesso de grãos finos à Subscrição Azure utilizando o RBAC](#grained-rbac)</li></ul> |
| **Limite de confiança de tecido de serviço** | <ul><li>[Restringir o acesso do cliente a operações de cluster utilizando o RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Execute modelação de segurança e utilize a Segurança de Nível de Campo sempre que necessário](#modeling-field)</li></ul> |
| **Dynamics CRM Portal** | <ul><li>[Realizar modelação de segurança de contas portais tendo em conta que o modelo de segurança do portal difere do resto do CRM](#portal-security)</li></ul> |
| **Storage do Azure** | <ul><li>[Conceder permissão de grãos finos a uma série de entidades em Armazenamento de Mesa Azure](#permission-entities)</li><li>[Ativar o Controlo de Acesso baseado em funções (RBAC) à conta de armazenamento Azure utilizando o Gestor de Recursos Azure](#rbac-azure-manager)</li></ul> |
| **Cliente Móvel** | <ul><li>[Implementar jailbreak implícito ou deteção de raízes](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fraca no WCF](#weak-class-wcf)</li><li>[Controlo de autorização de implementação wCF](#wcf-authz)</li></ul> |
| **API Web** | <ul><li>[Implementar mecanismo de autorização adequado na ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Efetuar verificações de autorização no dispositivo se suportar várias ações que requerem diferentes níveis de permissão](#device-permission)</li></ul> |
| **Gateway de campo iot** | <ul><li>[Efetuar verificações de autorização no Field Gateway se apoiar várias ações que requerem diferentes níveis de permissão](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os ACLs adequados estão configurados para restringir o acesso não autorizado aos dados do dispositivo|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Certifique-se de que o conteúdo sensível da aplicação específica do utilizador é armazenado no diretório do perfil do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que o conteúdo sensível da aplicação específica do utilizador é armazenado no diretório do perfil do utilizador. Isto é para evitar que vários utilizadores da máquina acedam aos dados uns dos outros.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Certifique-se de que as aplicações implementadas são executadas com menos privilégios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que a aplicação implementada é executada com menos privilégios. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Impor a ordem de passo sequencial ao processar fluxos lógicos de negócio

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para verificar se esta fase foi executada por um utilizador genuíno, pretende impor a aplicação apenas para processar fluxos de lógica empresarial em ordem sequencial, com todos os passos a serem processados em tempo humano realista, e não processando fora de ordem, sem etapas ignoradas , passos processados de outro utilizador, ou transações muito rápidas.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementar mecanismo de limitação da taxa para evitar a enumeração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os identificadores sensíveis são aleatórios. Implementar o controlo captcha em páginas anónimas. Certifique-se de que erro e exceção não devem revelar dados específicos|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Certifique-se de que a autorização adequada está em vigor e que o princípio dos menos privilégios é seguido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O princípio significa dar a uma conta de utilizador apenas os privilégios que são essenciais para que os utilizadores trabalhem. Por exemplo, um utilizador de backup não precisa de instalar software: portanto, o utilizador de backup tem apenas direitos para executar aplicações relacionadas com backup e backup. Quaisquer outros privilégios, como a instalação de novos softwares, estão bloqueados. O princípio aplica-se também a um utilizador pessoal de computador que normalmente trabalha numa conta de utilizador normal, e abre uma conta privilegiada e protegida por palavra-passe (isto é, um superutilizador) apenas quando a situação o exige absolutamente. </p><p>Este princípio também pode ser aplicado às suas aplicações web. Em vez de depender apenas de métodos de autenticação baseados em papéis utilizando sessões, preferimos atribuir privilégios aos utilizadores através de um sistema de autenticação baseado em bases de dados. Ainda utilizamos sessões para identificar se o utilizador foi iniciado corretamente, só agora, em vez de atribuir esse utilizador com uma função específica, atribuímos-lhe privilégios para verificar que ações tem o privilégio de executar no sistema. Também um grande profissional deste método é, sempre que um utilizador tiver de ser atribuído menos privilégios as suas alterações serão aplicadas no voo, uma vez que a atribuição não depende da sessão que de outra forma teve de expirar primeiro.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>A lógica empresarial e as decisões de autorização de acesso a recursos não devem basear-se em parâmetros de pedido de entrada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Sempre que estiver a verificar se um utilizador está restrito a rever determinados dados, as restrições de acesso devem ser processadas do lado do servidor. O userID deve ser armazenado dentro de uma variável de sessão no login e deve ser utilizado para recolher dados do utilizador da base de dados |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Agora, um possível intruso não pode adulterar e alterar a operação da aplicação uma vez que o identificador para recuperar os dados é tratado do lado do servidor.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Certifique-se de que os conteúdos e recursos não são enumerados ou acessíveis através de uma navegação forçada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os ficheiros estáticos e de configuração sensíveis não devem ser mantidos na raiz web. Para que os conteúdos não sejam obrigados a ser públicos, devem ser aplicados controlos de acesso adequados ou a remoção do conteúdo em si.</p><p>Além disso, a navegação vigorosa é geralmente combinada com técnicas de Força Bruta para recolher informações, tentando aceder ao maior número possível de URLs para enumerar diretórios e ficheiros num servidor. Os atacantes podem verificar se há todas as variações dos ficheiros comummente existentes. Por exemplo, uma pesquisa de ficheiros de palavra-passe abrangeria ficheiros incluindo psswd.txt, password.htm, password.dat e outras variações.</p><p>Para mitigar isto, devem ser incluídas as capacidades de deteção de tentativas de força bruta.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Certifique-se de que as contas menos privilegiadas são usadas para ligar ao servidor base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [SQL Database perdoa hierarquia,](https://msdn.microsoft.com/library/ms191465)securíveis de base de [dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | As contas menos privilegiadas devem ser utilizadas para se ligarem à base de dados. O login da aplicação deve ser restringido na base de dados e apenas executar procedimentos armazenados selecionados. O login da aplicação não deve ter acesso direto à mesa. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementar RLS de segurança de nível de linha para evitar que os inquilinos acedam aos dados uns dos outros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Sql Azure |
| **Atributos**              | Versão SQL - V12, Versão SQL - MsSQL2016 |
| **Referências**              | [Segurança de nível de linha do servidor SQL (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Passos** | <p>A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução).</p><p>A Segurança de Nível De Linha (RLS) simplifica o design e codificação de segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.</p><p>A lógica de restrição de acesso está localizada no nível de base de dados e não longe dos dados de outro nível de aplicação. O sistema de base de dados aplica as restrições de acesso sempre que o acesso aos dados é tentado a partir de qualquer nível. Isto torna o sistema de segurança mais fiável e robusto, reduzindo a área de superfície do sistema de segurança.</p><p>|

Por favor, note que o RLS como uma funcionalidade de base de dados fora da caixa é aplicável apenas ao SQL Server a partir de 2016 e à base de dados Azure SQL. Se a funcionalidade RLS fora da caixa não for implementada, deve ser assegurado que o acesso aos dados é restrito utilizando pontos de vista e procedimentos

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>A função Sysadmin apenas deve ter utilizadores válidos necessários

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [SQL Database perdoa hierarquia,](https://msdn.microsoft.com/library/ms191465)securíveis de base de [dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Os membros da função de servidor fixo SysAdmin devem ser muito limitados e nunca conter contas utilizadas pelas aplicações.  Por favor, reveja a lista de utilizadores na função e remova quaisquer contas desnecessárias|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Ligue-se ao Cloud Gateway usando fichas menos privilegiadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Controlo de Acesso do Hub Iot](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Passos** | Forneça permissões de privilégio menos privilegiais a vários componentes que se ligam ao Cloud Gateway (IoT Hub). Exemplo típico é : O componente de gestão/provisionamento do dispositivo utiliza registo/escrita, processador de eventos (ASA) utiliza o Service Connect. Dispositivos individuais conectam-se usando credenciais do Dispositivo|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Utilize uma chave SAS apenas com permissões de envio para gerar fichas de dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e modelo de segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Uma tecla SAS é usada para gerar fichas individuais do dispositivo. Utilize uma chave SAS apenas com permissões de envio enquanto gera o token do dispositivo para um determinado editor|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Não utilize fichas de acesso que forneçam acesso direto ao Centro de Eventos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e modelo de segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Um símbolo que conceda acesso direto ao centro do evento não deve ser dado ao dispositivo. Usar um símbolo menos privilegiado para o dispositivo que dá acesso apenas a uma editora ajudaria a identificá-lo e a colocar a lista negra se se constatasse que era um dispositivo fraudulento ou comprometido.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Ligue-se ao Event Hub utilizando teclas SAS que tenham as permissões mínimas necessárias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e modelo de segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Forneça permissões de privilégio menos privilegiais a várias aplicações de back-end que se ligam ao Centro de Eventos. Gere chaves SAS separadas para cada aplicação de back-end e apenas forneça as permissões necessárias - Enviar, Receber ou Gerir.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Use fichas de recursos para ligar ao Cosmos DB sempre que possível

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Um token de recurso está associado a um recurso de permissão Azure Cosmos DB e captura a relação entre o utilizador de uma base de dados e a permissão que o utilizador tem para um recurso específico de aplicação Azure Cosmos DB (por exemplo, recolha, documento). Utilize sempre um símbolo de recurso para aceder ao Azure Cosmos DB se não for de confiança do cliente com chaves master ou apenas de leitura - como uma aplicação de utilizador final como um cliente móvel ou desktop. Utilize a tecla Master ou as chaves apenas de leitura de aplicações de backend que podem armazenar estas chaves de forma segura.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Permitir a gestão de acesso de grãos finos à Subscrição Azure utilizando o RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira da Azure Trust | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Passos** | O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Restringir o acesso do cliente a operações de cluster utilizando o RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Controlo de acesso baseado em funções para clientes de Tecido de Serviço](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Passos** | <p>O Azure Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso permite ao administrador do cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais seguro.</p><p>Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, só leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços.</p><p>Especifica as duas funções de cliente (administrador e cliente) no momento da criação do cluster, fornecendo certificados separados para cada um.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Execute modelação de segurança e utilize a Segurança de Nível de Campo sempre que necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Execute modelação de segurança e utilize a Segurança de Nível de Campo sempre que necessário|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Realizar modelação de segurança de contas portais tendo em conta que o modelo de segurança do portal difere do resto do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM Portal | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Realizar modelação de segurança de contas portais tendo em conta que o modelo de segurança do portal difere do resto do CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Conceder permissão de grãos finos a uma série de entidades em Armazenamento de Mesa Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Tipo de armazenamento - Tabela |
| **Referências**              | [Como delegar acesso a objetos na sua conta de armazenamento Azure usando sas](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Passos** | Em certos cenários de negócio, o Armazenamento de Mesa Azure pode ser necessário para armazenar dados sensíveis que atendam a diferentes partes. Por exemplo, dados sensíveis relativos a diferentes países/regiões. Nesses casos, as assinaturas SAS podem ser construídas especificando as gamas de divisórias e de linha, de modo a que um utilizador possa aceder a dados específicos de um determinado país/região.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Ativar o Controlo de Acesso baseado em funções (RBAC) à conta de armazenamento Azure utilizando o Gestor de Recursos Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Como garantir a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Passos** | <p>Ao criar uma nova conta de armazenamento, selecione um modelo de implementação de Classic ou Azure Resource Manager. O modelo Clássico de criação de recursos no Azure apenas permite o acesso total ou nada à subscrição e, por sua vez, à conta de armazenamento.</p><p>Com o modelo Do Gestor de Recursos Azure, coloca a conta de armazenamento num grupo de recursos e controla o acesso ao plano de gestão dessa conta de armazenamento específica utilizando o Azure Ative Directory. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder às chaves da conta de armazenamento, enquanto outros utilizadores podem visualizar informações sobre a conta de armazenamento, mas não conseguem aceder às chaves da conta de armazenamento.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementar jailbreak implícito ou deteção de raízes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A aplicação deve salvaguardar a sua própria configuração e os dados do utilizador no caso de o telefone estar enraizado ou a cadeia estar avariada. A violação/rutura da cadeia implica acesso não autorizado, o que os utilizadores normais não fazem nos seus próprios telemóveis. Por isso, a aplicação deve ter uma lógica implícita de deteção no arranque da aplicação, para detetar se o telefone está enraizado.</p><p>A lógica de deteção pode simplesmente estar a aceder a ficheiros que normalmente só o utilizador de raiz pode aceder, por exemplo:</p><ul><li>/sistema/app/Superuser.apk</li><li>/sbin/su</li><li>/sistema/bin/su</li><li>/sistema/xbin/su</li><li>/dados/local/xbin/su</li><li>/dados/local/bin/su</li><li>/sistema/sd/xbin/su</li><li>/sistema/bin/failsafe/su</li><li>/dados/local/su</li></ul><p>Se a aplicação puder aceder a qualquer um destes ficheiros, denota que a aplicação está a funcionar como utilizador-raiz.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Referência de classe fraca no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>O sistema usa uma referência de classe fraca, o que pode permitir que um intruso execute código não autorizado. O programa refere uma classe definida pelo utilizador que não é identificada de forma única. Quando .NET carrega esta classe fracamente identificada, o carregador do tipo CLR procura a classe nos seguintes locais na ordem especificada:</p><ol><li>Se o conjunto do tipo for conhecido, o carregador procura nos locais de redirecionamento do ficheiro de configuração, GAC, no conjunto atual utilizando informações de configuração e o diretório base de aplicação</li><li>Se o conjunto for desconhecido, o carregador procura na montagem atual, mscorlib, e a localização devolvida pelo manipulador de eventos TypeResolve</li><li>Esta ordem de pesquisa CLR pode ser modificada com ganchos como o mecanismo de reencaminhamento de tipo e o evento AppDomain.TypeResolve</li></ol><p>Se um intruso explorar a ordem de pesquisa do CLR criando uma classe alternativa com o mesmo nome e colocando-a num local alternativo que o CLR carregará primeiro, o CLR executará involuntariamente o código fornecido pelo intruso</p>|

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração wCF abaixo instrui o WCF a adicionar uma classe de comportamento personalizado a uma determinada extensão WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
A utilização de nomes (fortes) totalmente qualificados identifica um tipo e aumenta ainda mais a segurança do seu sistema. Utilize nomes de montagem totalmente qualificados ao registar os tipos nos ficheiros machine.config e app.config.

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração wCF abaixo instrui o WCF a adicionar uma classe de comportamento personalizado fortemente referenciada a uma determinada extensão wcf.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Controlo de autorização de implementação wCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>Este serviço não utiliza um controlo de autorização. Quando um cliente liga para um determinado serviço WCF, a WCF fornece vários esquemas de autorização que verificam que o chamador tem permissão para executar o método de serviço no servidor. Se os controlos de autorização não estiverem ativados para os serviços WCF, um utilizador autenticado pode alcançar uma escalada de privilégios.</p>|

### <a name="example"></a>Exemplo
A seguinte configuração instrui a WCF a não verificar o nível de autorização do cliente na execução do serviço:
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
Utilize um regime de autorização de serviço para verificar se o autor do método de serviço está autorizado a fazê-lo. A WCF fornece dois modos e permite a definição de um regime de autorização personalizada. O modo UseWindowsGroups utiliza funções e utilizadores do Windows e o modo UseAspNetRoles utiliza um fornecedor de funções ASP.NET, como o SQL Server, para autenticar.

### <a name="example"></a>Exemplo
A seguinte configuração instrui a WCF a certificar-se de que o cliente faz parte do grupo administradores antes de executar o serviço Add:
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
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/A, Fornecedor de Identidade - ADFS, Fornecedor de Identidade - Azure AD |
| **Referências**              | [Autenticação e Autorização na API web ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Passos** | <p>As informações de papel para os utilizadores da aplicação podem ser obtidas a partir de reclamações da AD Azure ou da ADFS se a aplicação depender deles como fornecedor de identidade ou a própria aplicação pode fornecer-lhe. Em qualquer um destes casos, a implementação da autorização personalizada deve validar a informação sobre a função do utilizador.</p><p>As informações de papel para os utilizadores da aplicação podem ser obtidas a partir de reclamações da AD Azure ou da ADFS se a aplicação depender deles como fornecedor de identidade ou a própria aplicação pode fornecer-lhe. Em qualquer um destes casos, a implementação da autorização personalizada deve validar a informação sobre a função do utilizador.</p>

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
Todos os controladores e métodos de ação que precisam de ser protegidos devem ser decorados com atributo acima.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Efetuar verificações de autorização no dispositivo se suportar várias ações que requerem diferentes níveis de permissão

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O Dispositivo deve autorizar o chamador a verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Para, por exemplo, digamos que o dispositivo é um Smart Door Lock que pode ser monitorizado a partir da nuvem, além de que fornece funcionalidades como bloquear remotamente a porta.</p><p>O Smart Door Lock fornece funcionalidade de desbloqueio apenas quando alguém se aproxima fisicamente da porta com um Cartão. Neste caso, a implementação do comando e controlo remoto deve ser feita de modo a que não forneça qualquer funcionalidade para desbloquear a porta, uma vez que o portal da nuvem não está autorizado a enviar um comando para desbloquear a porta.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Efetuar verificações de autorização no Field Gateway se apoiar várias ações que requerem diferentes níveis de permissão

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Gateway de Campo deve autorizar o chamador a verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Para, por exemplo, deve haver permissões diferentes para uma interface de utilizador/API de administração utilizada para configurar um gateway de campo v/s dispositivos que se ligam a ele.|
