---
title: Autorização - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
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
ms.openlocfilehash: 3c078f7246140ee966f1d202d2248758dde49059
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888473"
---
# <a name="security-frame-authorization--mitigations"></a>Quadro de segurança: Autorização | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo](#acl-restricted-access)</li><li>[Certifique-se de que o conteúdo da aplicação do confidenciais específicas do usuário é armazenado no diretório do perfil de utilizador](#sensitive-directory)</li><li>[Certifique-se de que os aplicativos implantados são executados com privilégios mínimos](#deployed-privileges)</li></ul> |
| **Aplicação Web** | <ul><li>[Impor a ordem sequencial passo durante o processamento de fluxos de lógica de negócio](#sequential-logic)</li><li>[Implementar o mecanismo para impedir que a enumeração de limitação de taxas](#rate-enumeration)</li><li>[Certifique-se de que a autorização adequada se estiver em vigor e seguido do princípio de menos privilégios](#principle-least-privilege)</li><li>[Lógica e os recursos acesso autorização decisões de negócios não devem ser baseadas em parâmetros de pedido de entrada](#logic-request-parameters)</li><li>[Certifique-se de que o conteúdo e recursos não estão acessíveis através de navegação forçadas ou enumerable](#enumerable-browsing)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que as contas com privilégios mínimos são utilizadas para ligar ao servidor de base de dados](#privileged-server)</li><li>[Implementar a RLS de segurança de nível de linha para impedir que os inquilinos acedem aos dados uns dos outros](#rls-tenants)</li><li>[Função de administrador do sistema deverá ter apenas os utilizadores necessários válidos](#sysadmin-users)</li></ul> |
| **Gateway de Cloud da IoT** | <ul><li>[Ligar ao Gateway de Cloud com os tokens de acesso com privilégios mínimos](#cloud-least-privileged)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Utilizar um permissões só de envio da chave SAS para gerar tokens de dispositivo](#sendonly-sas)</li><li>[Não utilize tokens de acesso que fornecem acesso direto ao Hub de eventos](#access-tokens-hub)</li><li>[Ligar ao Hub de eventos utilizando chaves SAS com as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **DB de documentos do Azure** | <ul><li>[Utilizar tokens de recurso para ligar ao Azure Cosmos DB sempre que possível](#resource-docdb)</li></ul> |
| **Limite de fidedignidade do Azure** | <ul><li>[Ativar a gestão de acessos detalhada para a subscrição do Azure através do RBAC](#grained-rbac)</li></ul> |
| **Limite de fidedignidade do Service Fabric** | <ul><li>[Restringir o acesso do cliente para operações de cluster utilizando o RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Execute a Modelagem de segurança e usar a segurança de nível de campo onde for necessário](#modeling-field)</li></ul> |
| **Portal do Dynamics CRM** | <ul><li>[Execute a Modelagem de segurança de contas portais tendo em mente que o modelo de segurança para o portal é diferente do restante do CRM](#portal-security)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Conceder permissão detalhada numa ampla gama de entidades no armazenamento de tabelas do Azure](#permission-entities)</li><li>[Ativar com base em função de controlo de acesso (RBAC) para a conta de armazenamento do Azure com o Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar jailbreak implícita ou embota deteção](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fracos no WCF](#weak-class-wcf)</li><li>[Controlo de autorização de implementar do WCF](#wcf-authz)</li></ul> |
| **API Web** | <ul><li>[Implementar o mecanismo de autorização adequada na ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Executar verificações de autorização no dispositivo, se ele oferece suporte a várias ações que exigem níveis de permissão diferentes](#device-permission)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Executar verificações de autorização no Gateway de campo, se ele oferece suporte a várias ações que exigem níveis de permissão diferentes](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo|

## <a id="sensitive-directory"></a>Certifique-se de que o conteúdo da aplicação do confidenciais específicas do usuário é armazenado no diretório do perfil de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que o conteúdo da aplicação do confidenciais específicas do usuário é armazenado no diretório do perfil de utilizador. Este procedimento impede que múltiplos utilizadores da máquina de aceder aos dados uns dos outros.|

## <a id="deployed-privileges"></a>Certifique-se de que os aplicativos implantados são executados com privilégios mínimos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que a aplicação implementada é executada com privilégios mínimos. |

## <a id="sequential-logic"></a>Impor a ordem sequencial passo durante o processamento de fluxos de lógica de negócio

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Para verificar que neste estágio foi passam por um utilizador original que deseja impor o aplicativo só processo comercial fluxos de lógica na ordem sequencial do passo, com todos os passos a ser processados no tempo humano realista e não processar fora de ordem, ignorada passos , processadas passos a partir de outro utilizador ou demasiado rapidamente submetido transações.|

## <a id="rate-enumeration"></a>Implementar o mecanismo para impedir que a enumeração de limitação de taxas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os identificadores sensíveis são aleatórios. Implementar o controlo de CAPTCHA nas páginas de anônimos. Certifique-se de exceções e erros não devem revelar dados específicos|

## <a id="principle-least-privilege"></a>Certifique-se de que a autorização adequada se estiver em vigor e seguido do princípio de menos privilégios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O princípio significa dar uma conta de utilizador apenas esses privilégios que são essenciais para que os usuários trabalham. Por exemplo, um utilizador de cópia de segurança não é necessário instalar o software: por este motivo, o utilizador de cópia de segurança tem direitos de apenas para executar aplicações de cópia de segurança e relacionados com a cópia de segurança. Outros privilégios, como a instalação de software novo, são bloqueados. O princípio também se aplica a um utilizador do computador pessoal que normalmente funciona numa conta de usuário normal e abre-se de uma conta com privilégios, protegidos por senha (ou seja, um Superutilizador) apenas quando a situação absolutamente exige isso. </p><p>Este princípio também pode ser aplicado às suas aplicações web. Em vez de apenas consoante os métodos de autenticação baseada em funções usando sessões, em vez disso, queremos atribuir privilégios a utilizadores por meio de um sistema de autenticação baseada no banco de dados. Vamos continuar a utilizar sessões para identificar que o utilizador tenha sido registado corretamente, apenas agora em vez de atribuir esse utilizador com uma função específica em contato com ele, Atribuímos com privilégios para verificar quais ações, ele é o privilégio de executar no sistema. Também uma grande pro desse método é, sempre que um utilizador deve ser atribuída as suas alterações serão aplicadas imediatamente, uma vez que a atribuição não depende da sessão; caso contrário, que tinha a expirar-se primeiro de menos privilégios.</p>|

## <a id="logic-request-parameters"></a>Lógica e os recursos acesso autorização decisões de negócios não devem ser baseadas em parâmetros de pedido de entrada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Sempre que o que está a verificar se um usuário é restrito para rever certos dados, as restrições de acesso devem ser processado no servidor. O ID de utilizador deve ser armazenado dentro de uma variável de sessão no início de sessão e deve ser usada para recuperar dados de utilizador da base de dados |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Agora, um invasor possível pode não adulteração e alterar a operação de aplicação uma vez que é o identificador para obter os dados processados do lado do servidor.

## <a id="enumerable-browsing"></a>Certifique-se de que o conteúdo e recursos não estão acessíveis através de navegação forçadas ou enumerable

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Ficheiros confidenciais do estático e a configuração não devem ser mantidos na raiz do web. Para o conteúdo não é necessário para ser público, devem ser aplicadas a qualquer um dos controles de acesso adequados ou remoção do conteúdo em si.</p><p>Além disso, navegação forçadas é normalmente combinada com técnicas de força bruta para recolher informações ao tentar aceder aos URLs tantos possível enumerar os diretórios e ficheiros num servidor. Os atacantes podem verificar a existência de todas as variações de normalmente existente ficheiros. Por exemplo, uma pesquisa de ficheiros de palavra-passe deve abranger arquivos incluindo psswd.txt, password.htm, password.dat e outras variações.</p><p>Para atenuar isso, as capacidades de deteção de tentativas de força bruta devem ser incluídas.</p>|

## <a id="privileged-server"></a>Certifique-se de que as contas com privilégios mínimos são utilizadas para ligar ao servidor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Hierarquia de permissões de base de dados SQL](https://msdn.microsoft.com/library/ms191465), [capacidades de segurança de base de dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Contas com privilégios mínimos devem ser utilizadas para ligar à base de dados. Início de sessão de aplicativo deve ser restritas na base de dados e deve ser executado somente procedimentos armazenados selecionados. Início de sessão do aplicativo não deve ter nenhum acesso direto da tabela. |

## <a id="rls-tenants"></a>Implementar a RLS de segurança de nível de linha para impedir que os inquilinos acedem aos dados uns dos outros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Sql Azure, OnPrem |
| **Atributos**              | MsSQL2016 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Segurança ao nível da linha (RLS) do SQL Server](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Passos** | <p>A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução).</p><p>Segurança ao nível da linha (RLS) simplifica o design e programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.</p><p>A lógica de restrição de acesso está localizado na camada de base de dados em vez de ausente dos dados na outra camada de aplicativos. O sistema de base de dados aplica as restrições de acesso sempre que esse acesso a dados é tentado a partir de qualquer camada. Isso faz com que o sistema de segurança mais robusta e confiável, reduzindo a área de superfície do sistema de segurança.</p><p>|

Tenha em atenção que RLS como uma funcionalidade de base de dados de out-of-the-box é aplicável apenas a partir de 2016 do SQL Server e base de dados SQL do Azure. Se a funcionalidade RLS de out-of-the-box não está implementada, isso deve ser garantiu que o acesso a dados é restrito utilizar vistas e procedimentos

## <a id="sysadmin-users"></a>Função de administrador do sistema deverá ter apenas os utilizadores necessários válidos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Hierarquia de permissões de base de dados SQL](https://msdn.microsoft.com/library/ms191465), [capacidades de segurança de base de dados SQL](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Os membros da função de servidor fixa SysAdmin devem ser muito limitado e nunca conter contas utilizadas por aplicações.  Reveja a lista de utilizadores na função e remova as contas de desnecessárias|

## <a id="cloud-least-privileged"></a>Ligar ao Gateway de Cloud com os tokens de acesso com privilégios mínimos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Escolha de gateway - IoT Hub do Azure |
| **Referências**              | [Controlo de acesso do IOT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Passos** | Forneça, pelo menos, permissões de privilégio para vários componentes que se ligam ao Gateway de nuvem (IoT Hub). Exemplo típico é – o componente de aprovisionamento/gestão de dispositivos utiliza registryread/gravação, processador de eventos (ASA) utiliza a ligação de serviço. Dispositivos individuais ligarem com credenciais de dispositivo|

## <a id="sendonly-sas"></a>Utilizar um permissões só de envio da chave SAS para gerar tokens de dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Event Hubs autenticação e segurança visão geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Uma chave SAS é utilizada para gerar tokens de dispositivo individual. Utilizar uma chave SAS de permissões só de envio ao gerar o token do dispositivo para um determinado publicador|

## <a id="access-tokens-hub"></a>Não utilize tokens de acesso que fornecem acesso direto ao Hub de eventos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Event Hubs autenticação e segurança visão geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Um token que conceda acesso direto ao hub de eventos não deve ser concedido para o dispositivo. Através de um token com privilégios mínimos para o dispositivo que permita o acesso apenas a um fabricante poderia ajudar a identificar e a lista de bloqueio-lo se não for uma adesão ou comprometidos de dispositivo.|

## <a id="sas-minimum-permissions"></a>Ligar ao Hub de eventos utilizando chaves SAS com as permissões mínimas necessárias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Event Hubs autenticação e segurança visão geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Forneça, pelo menos, permissões de privilégio para várias aplicações de back-end que se ligam ao Hub de eventos. Gerar chaves SAS separadas para cada aplicação de back-end e fornecer apenas as permissões necessárias - o envio, receção ou gerir a eles.|

## <a id="resource-docdb"></a>Utilizar tokens de recurso para ligar ao Cosmos DB, sempre que possível

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | DB de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Um token de recurso está associado um recurso de permissão do Azure Cosmos DB e captura a relação entre o utilizador de uma base de dados e a permissão que o utilizador tem para um recurso de aplicação do Azure Cosmos DB específico (por exemplo, coleção, documentos). Utilize sempre um token de recurso para acessar o Azure Cosmos DB, se o cliente não são de confiança com processar chaves mestras ou só de leitura - como uma aplicação de utilizador final, como um cliente móvel ou ambiente de trabalho. Utilize a chave mestra ou chaves de só de leitura a partir de aplicações de back-end que podem armazenar essas chaves de forma segura.|

## <a id="grained-rbac"></a>Ativar a gestão de acessos detalhada para a subscrição do Azure através do RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Passos** | O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos.|

## <a id="cluster-rbac"></a>Restringir o acesso do cliente para operações de cluster utilizando o RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Controlo de acesso baseado em funções para clientes do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Passos** | <p>O Azure Service Fabric suporta dois tipos de controle de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e usuário. Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários, fazendo com que o cluster mais seguro.</p><p>Os administradores têm acesso total às capacidades de gestão (incluindo recursos de leitura/gravação). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços.</p><p>Especifique as funções do cliente de dois (cliente e de administrador) no momento da criação do cluster ao fornecer certificados separados para cada um.</p>|

## <a id="modeling-field"></a>Execute a Modelagem de segurança e usar a segurança de nível de campo onde for necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Execute a Modelagem de segurança e usar a segurança de nível de campo onde for necessário|

## <a id="portal-security"></a>Execute a Modelagem de segurança de contas portais tendo em mente que o modelo de segurança para o portal é diferente do restante do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Portal do Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Execute a Modelagem de segurança de contas portais tendo em mente que o modelo de segurança para o portal é diferente do restante do CRM|

## <a id="permission-entities"></a>Conceder permissão detalhada numa ampla gama de entidades no armazenamento de tabelas do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - tabela |
| **Referências**              | [Como delegar acesso a objetos na sua conta de armazenamento do Azure com SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Passos** | Em determinados cenários comerciais, armazenamento de tabelas do Azure poderá ser necessário para armazenar dados confidenciais que atende a partes diferentes. Por exemplo, os dados confidenciais relativas a diferentes países. Nesses casos, assinaturas SAS podem ser construídas, especificando os intervalos chave de partição e de linha, que um usuário pode acessar dados específicos de um determinado país.| 

## <a id="rbac-azure-manager"></a>Ativar com base em função de controlo de acesso (RBAC) para a conta de armazenamento do Azure com o Azure Resource Manager

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Passos** | <p>Quando cria uma nova conta de armazenamento, selecione um modelo de implementação de clássico ou do Azure Resource Manager. O modelo clássico de criação de recursos no Azure só permite o acesso de tudo ou nada para a subscrição e, por sua vez, a conta de armazenamento.</p><p>Com o modelo Azure Resource Manager, coloque a conta de armazenamento num grupo e controlar o acesso recursos para o plano de gestão dessa conta de armazenamento específica com o Azure Active Directory. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder as chaves de conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não é possível aceder as chaves de conta de armazenamento.</p>|

## <a id="rooting-detection"></a>Implementar jailbreak implícita ou embota deteção

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Aplicação deve salvaguardar os seus próprios dados de utilizador e a configuração no caso se o telefone está enraizado ou desbloqueado por jailbreak. Embota/desbloqueado por recentes implica o acesso não autorizado, os utilizadores que normal não fará em seus próprios telemóveis. Portanto o aplicativo deve ter a lógica de deteção implícita na inicialização do aplicativo, para detetar se o telefone foi desbloqueado por Root.</p><p>A lógica de deteção pode ser simplesmente aceder a ficheiros que utilizador raiz normalmente só pode aceder, por exemplo:</p><ul><li>/System/App/SuperUser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Se a aplicação pode aceder a qualquer um desses arquivos, ele indica que a aplicação está em execução como utilizador raiz.</p>|

## <a id="weak-class-wcf"></a>Referência de classe fracos no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>O sistema usa uma referência de classe fracas, que poderão permitir que um invasor execute código não autorizado. O programa faz referência a uma classe definida pelo utilizador que não seja identificada exclusivamente. Quando o .NET carrega essa classe identificado sem rigidez, o carregador de tipo CLR procura a classe nas seguintes localizações na ordem especificada:</p><ol><li>Se o assembly do tipo é conhecido, o carregador de pesquisa localizações de redirecionamento do ficheiro de configuração, GAC, o assembly atual usando informações de configuração e o diretório base do aplicativo</li><li>Se o assembly for desconhecido, o carregador de procura assembly atual e a localização retornado pelo manipulador de eventos TypeResolve mscorlib</li><li>Esta ordem de pesquisa do CLR pode ser modificada com conexões, como o mecanismo de reencaminhamento de tipo e o evento de AppDomain.TypeResolve</li></ol><p>Se um invasor explora a ordem de pesquisa do CLR, criando uma classe alternativa com o mesmo nome e colocá-lo numa localização alternativa que o CLR será carregado em primeiro lugar, o CLR irá inadvertidamente executar o código fornecido pelo invasor</p>|

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração de WCF abaixo Instrua o WCF para adicionar uma classe de comportamento personalizado para uma determinada extensão do WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Utilizar nomes totalmente qualificados de (sólidos) exclusivamente identifica um tipo e aumenta ainda mais a segurança do seu sistema. Utilize nomes de assemblagem completamente qualificado ao registar tipos nos arquivos Machine. config e o App. config.

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração de WCF abaixo Instrua o WCF para adicionar a classe de altamente referenciado pelo comportamento personalizado para uma determinada extensão do WCF.
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

## <a id="wcf-authz"></a>Controlo de autorização de implementar do WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Passos** | <p>Este serviço não utiliza um controlo de autorização. Quando um cliente chama um determinado serviço WCF, o WCF fornece vários esquemas de autorização que verifique se o chamador tem permissão para executar o método de serviço no servidor. Se os controlos de autorização não estão ativados para os serviços WCF, um utilizador autenticado pode alcançar o Escalamento de privilégios.</p>|

### <a name="example"></a>Exemplo
A seguinte configuração Instrua o WCF para não verificar o nível de autorização do cliente ao executar o serviço:
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
Utilize um esquema de autorização de serviço para verificar se o autor da chamada do método de serviço está autorizado a fazê-lo. O WCF fornece dois modos e permite a definição de um esquema de autorização personalizado. O modo de UseWindowsGroups utiliza funções do Windows e os utilizadores e o modo de UseAspNetRoles utiliza um provedor de função ASP.NET, como o SQL Server, para autenticar.

### <a name="example"></a>Exemplo
A seguinte configuração Instrua o WCF para se certificar de que o cliente é a parte do grupo Administradores antes de executar o serviço de adicionar:
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
O serviço, em seguida, está declarado como o seguinte:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementar o mecanismo de autorização adequada na ASP.NET Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/d, fornecedor de identidade do fornecedor - ADFS, de identidade - Azure AD |
| **Referências**              | [Autenticação e autorização na ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Passos** | <p>Informações de função para os utilizadores da aplicação podem ser derivadas do Azure AD ou afirmações ADFS, se o aplicativo baseia-se nos mesmos como fornecedor de identidade ou a própria aplicação poderá fornecido-lo. Em qualquer um desses casos, a implementação de autorização personalizado deve validar as informações de função de utilizador.</p><p>Informações de função para os utilizadores da aplicação podem ser derivadas do Azure AD ou afirmações ADFS, se o aplicativo baseia-se nos mesmos como fornecedor de identidade ou a própria aplicação poderá fornecido-lo. Em qualquer um desses casos, a implementação de autorização personalizado deve validar as informações de função de utilizador.</p>

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
Todos os controladores e métodos de ação que têm protegidos devem ser decorados com acima atributo.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Executar verificações de autorização no dispositivo, se ele oferece suporte a várias ações que exigem níveis de permissão diferentes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O dispositivo deve autorizar o autor da chamada para verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Para por exemplo, permite que o dispositivo é um bloqueio de porta inteligentes que podem ser monitorizados a partir da cloud, além de fornece funcionalidades, como a porta de bloqueio remotamente.</p><p>O Smart Lock de porta fornece uma funcionalidade de desbloqueio apenas quando alguém fisicamente vem junto a porta de entrada com um cartão. Neste caso, a implementação do comando remoto e controlo deve ser feita de forma que ele não fornece nenhuma funcionalidade para desbloquear a porta de entrada, como o gateway de cloud não está autorizado a enviar um comando para desbloquear a porta de entrada.</p>|

## <a id="field-permission"></a>Executar verificações de autorização no Gateway de campo, se ele oferece suporte a várias ações que exigem níveis de permissão diferentes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | O Gateway de campo deve autorizar o autor da chamada para verificar se o chamador tem as permissões necessárias para executar a ação solicitada. Por exemplo, deve ser permissões diferentes para uma administração utilizador interface/API utilizada para configurar um dispositivos de v/s de gateway de campo que se ligam à mesma.|
