---
title: Auditoria e Registo - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a auditoria e a mitigação do registo na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.openlocfilehash: bac17073650736df9ec48e951290852e082e9417
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87542998"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Quadro de Segurança: Auditoria e RegistoS Registados / Mitigações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifique entidades sensíveis na sua solução e implemente a auditoria de mudança](#sensitive-entities)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira são aplicadas na aplicação](#auditing)</li><li>[Certifique-se de que a rotação e separação de registos estão no lugar](#log-rotation)</li><li>[Certifique-se de que a aplicação não regista dados confidenciais do utilizador](#log-sensitive-data)</li><li>[Certifique-se de que os ficheiros de auditoria e de registo têm acesso restrito](#log-restricted-access)</li><li>[Certifique-se de que os Eventos de Gestão de Utilizadores estão registados](#user-management)</li><li>[Certifique-se de que o sistema tem defesas incorporadas contra o uso indevido](#inbuilt-defenses)</li><li>[Ativar o registo de diagnósticos para aplicações web no Azure App Service](#diagnostics-logging)</li></ul> |
| **Base de dados** | <ul><li>[Certifique-se de que a auditoria de login está ativada no SQL Server](#identify-sensitive-entities)</li><li>[Ativar a deteção de ameaças no Azure SQL](#threat-detection)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Use a Azure Storage Analytics para auditar o acesso ao Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar registos suficientes](#sufficient-logging)</li><li>[Implementar o tratamento suficiente da falha de auditoria](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a auditoria e o registo de madeira são aplicados na API Web](#logging-web-api)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Field Gateway](#logging-field-gateway)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Cloud Gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identifique entidades sensíveis na sua solução e implemente a auditoria de mudança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Identifique as entidades na sua solução que contenha dados sensíveis e implemente a auditoria de alteração a essas entidades e áreas |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Certifique-se de que a auditoria e a exploração madeireira são aplicadas na aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Ativar a auditoria e o registo de todos os componentes. Os registos de auditoria devem capturar o contexto do utilizador. Identifique todos os eventos importantes e regista esses eventos. Implementar a exploração madeireira centralizada |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Certifique-se de que a rotação e separação de registos estão no lugar

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>A rotação de registos é um processo automatizado utilizado na administração do sistema no qual os ficheiros de registo datados são arquivados. Os servidores que executam grandes aplicações registam frequentemente todos os pedidos: face a registos volumosos, a rotação de registos é uma forma de limitar o tamanho total dos registos, permitindo ainda a análise de eventos recentes. </p><p>Basicamente, a separação de registos significa que tem de armazenar os seus ficheiros de registo numa partição diferente, tal como no local onde o seu SISTEMA/aplicação está a funcionar de forma a evitar um ataque de Negação de Serviço ou a degradação da sua aplicação.</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Certifique-se de que a aplicação não regista dados confidenciais do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Verifique se não regista quaisquer dados sensíveis que um utilizador submeta ao seu site. Verifique se há registos intencionais, bem como efeitos secundários causados por problemas de conceção. Exemplos de dados sensíveis incluem:</p><ul><li>Credenciais de utilizador</li><li>Número da Segurança Social ou outra informação de identificação</li><li>Números de cartões de crédito ou outras informações financeiras</li><li>Informações de saúde</li><li>Chaves privadas ou outros dados que possam ser usados para desencriptar informações encriptadas</li><li>Informações de sistema ou aplicações que podem ser usadas para atacar mais eficazmente a aplicação</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Certifique-se de que os ficheiros de auditoria e de registo têm acesso restrito

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Verifique se os direitos de acesso aos ficheiros de registo estão devidamente definidos. As contas de candidatura devem ter acesso apenas por escrito e os operadores e o pessoal de apoio devem ter acesso apenas à leitura, se necessário.</p><p>As contas dos administradores são as únicas contas que devem ter acesso total. Verifique o Windows ACL em ficheiros de registo para garantir que estão devidamente restringidos:</p><ul><li>As contas de candidatura devem ter acesso apenas por escrito</li><li>Os operadores e o pessoal de apoio devem ter acesso apenas à leitura, se necessário</li><li>Os administradores são as únicas contas que devem ter acesso total</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Certifique-se de que os Eventos de Gestão de Utilizadores estão registados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Certifique-se de que a aplicação monitoriza eventos de gestão de utilizadores, tais como logins de utilizador bem sucedidos e falhados, resets de palavra-passe, alterações de passwords, bloqueio de conta, registo do utilizador. Isto ajuda a detetar e reagir a comportamentos potencialmente suspeitos. Permite também recolher dados de operações; por exemplo, para rastrear quem está a aceder à aplicação</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Certifique-se de que o sistema tem defesas incorporadas contra o uso indevido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Devem ser efetuadas controlos que atirem exceções à segurança em caso de utilização indevida da aplicação. Por exemplo, se a validação de entrada estiver em vigor e um intruso tentar injetar código malicioso que não corresponda ao regex, pode ser lançada uma exceção de segurança que pode ser um indicativo de uso indevido do sistema</p><p>Por exemplo, recomenda-se que tenham exceções de segurança registadas e que se ações tenham sido tomadas para as seguintes questões:</p><ul><li>Validação de entradas</li><li>Violações do CSRF</li><li>Força bruta (limite máximo para o número de pedidos por utilizador por recurso)</li><li>Violações do upload de ficheiros</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Ativar o registo de diagnósticos para aplicações web no Azure App Service

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | N/D  |
| **Passos** | <p>O Azure fornece diagnósticos incorporados para ajudar a depurar uma aplicação web do Serviço de Aplicações. Aplica-se também a aplicações API e aplicações móveis. As aplicações web do Serviço de Aplicações fornecem funcionalidade de diagnóstico para registar informações tanto do servidor web como da aplicação web.</p><p>Estes são logicamente separados em diagnósticos de servidores web e diagnósticos de aplicações</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Certifique-se de que a auditoria de login está ativada no SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Configure Login Auditing](https://msdn.microsoft.com/library/ms175850.aspx) (Configurar a Auditoria de Início de Sessão) |
| **Passos** | <p>A auditoria de login do Servidor de Base de Dados deve ser ativada para detetar/confirmar ataques de adivinhação por palavra-passe. É importante capturar tentativas falhadas de login. Capturar tentativas de login bem sucedidas e falhadas proporciona benefícios adicionais durante investigações forenses</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Ativar a deteção de ameaças no Azure SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Começar com deteção de ameaças de base de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passos** |<p>A Deteção de Ameaças deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados. Fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas.</p><p>Os utilizadores podem explorar os eventos suspeitos usando a Azure SQL Database Auditing para determinar se resultam de uma tentativa de aceder, violar ou explorar dados na base de dados.</p><p>A Deteção de Ameaças torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou de gerir sistemas avançados de monitorização de segurança</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Use a Azure Storage Analytics para auditar o acesso ao Azure Storage

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D |
| **Referências**              | [Utilização de Storage Analytics para monitorizar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passos** | <p>Para cada conta de armazenamento, pode-se permitir que o Azure Storage Analytics execute dados de métricas de registo e armazenamento. Os registos de análise de armazenamento fornecem informações importantes, como o método de autenticação utilizado por alguém quando acedem ao armazenamento.</p><p>Isto pode ser muito útil se estiver a guardar firmemente o acesso ao armazenamento. Por exemplo, no Blob Storage pode configurar todos os recipientes para privados e implementar a utilização de um serviço SAS ao longo das suas aplicações. Em seguida, pode verificar regularmente os registos para ver se as suas bolhas são acedidas usando as chaves da conta de armazenamento, o que pode indicar uma quebra de segurança, ou se as bolhas são públicas, mas não deveriam ser.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementar registos suficientes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Passos** | <p>A falta de um rasto de auditoria adequado depois de um incidente de segurança pode dificultar os esforços forenses. A Windows Communication Foundation (WCF) oferece a capacidade de registar tentativas de autenticação bem sucedidas e/ou falhadas.</p><p>As tentativas de autenticação falhadas podem alertar os administradores de potenciais ataques de força bruta. Da mesma forma, registar eventos de autenticação bem-sucedidos pode fornecer um rasto de auditoria útil quando uma conta legítima é comprometida. Ativar a funcionalidade de auditoria de segurança de serviços da WCF |

### <a name="example"></a>Exemplo
Segue-se uma configuração de exemplo com auditoria ativada
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementar o tratamento suficiente da falha de auditoria

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reino fortificado](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Passos** | <p>A solução desenvolvida é configurada para não gerar uma exceção quando não escreve para um registo de auditoria. Se o WCF estiver configurado para não lançar uma exceção quando não puder escrever para um registo de auditoria, o programa não será notificado da falha e a auditoria de eventos críticos de segurança pode não ocorrer.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do ficheiro de configuração do WCF abaixo instrui o WCF a não notificar o pedido quando o WCF não escrever para um registo de auditoria.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Configure o WCF para notificar o programa sempre que não puder escrever para um registo de auditoria. O programa deve ter um sistema de notificação alternativo para alertar a organização de que os rastos de auditoria não estão a ser mantidos. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Certifique-se de que a auditoria e o registo de madeira são aplicados na API Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Ativar a auditoria e o registo em APIs web. Os registos de auditoria devem capturar o contexto do utilizador. Identifique todos os eventos importantes e regista esses eventos. Implementar a exploração madeireira centralizada |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Field Gateway

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Quando vários dispositivos se ligarem a um Gateway de Campo, certifique-se de que as tentativas de ligação e o estado de autenticação (sucesso ou falha) para dispositivos individuais são registados e mantidos no Gateway de Campo.</p><p>Além disso, nos casos em que o Field Gateway mantém as credenciais do IoT Hub para dispositivos individuais, certifique-se de que a auditoria é realizada quando estas credenciais são recuperadas. Desenvolva um processo para carregar periodicamente os registos para Azure IoT Hub/armazenamento para retenção a longo prazo.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Cloud Gateway

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Introdução ao acompanhamento das operações do IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passos** | <p>Desenho para recolha e armazenamento de dados de auditoria recolhidos através do IoT Hub Operations Monitoring. Ativar as seguintes categorias de monitorização:</p><ul><li>Operações de identidade do dispositivo</li><li>Comunicações dispositivo-nuvem</li><li>Comunicações nuvem-para-dispositivo</li><li>Ligações</li><li>Uploads de ficheiros</li></ul>|