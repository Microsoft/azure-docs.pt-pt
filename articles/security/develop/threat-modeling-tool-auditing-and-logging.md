---
title: Auditoria e Exploração Madeireira - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
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
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728418"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Quadro de Segurança: Auditoria e Exploração Madeireira [ Atenuações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificar entidades sensíveis na sua solução e implementar auditoria de alteração](#sensitive-entities)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira são aplicadas na aplicação](#auditing)</li><li>[Certifique-se de que a rotação e a separação do tronco estão no lugar](#log-rotation)</li><li>[Certifique-se de que a aplicação não regista dados sensíveis do utilizador](#log-sensitive-data)</li><li>[Certifique-se de que os ficheiros de auditoria e registo têm acesso restrito](#log-restricted-access)</li><li>[Certifique-se de que os Eventos de Gestão de Utilizadores estão registados](#user-management)</li><li>[Certifique-se de que o sistema tem defesas incorporadas contra o uso indevido](#inbuilt-defenses)</li><li>[Ativar diagnósticos de login para aplicações web no Serviço de Aplicações Azure](#diagnostics-logging)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que a auditoria de login está ativada no Servidor SQL](#identify-sensitive-entities)</li><li>[Ativar a deteção de ameaças no Azure SQL](#threat-detection)</li></ul> |
| **Storage do Azure** | <ul><li>[Utilizar o Azure Storage Analytics para auditar o acesso ao Armazenamento Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar registo suficiente](#sufficient-logging)</li><li>[Implementar manipulação suficiente de falhas de auditoria](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira são aplicadas na Web API](#logging-web-api)</li></ul> |
| **Gateway de campo iot** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Field Gateway](#logging-field-gateway)</li></ul> |
| **Gateway da nuvem iot** | <ul><li>[Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Cloud Gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identificar entidades sensíveis na sua solução e implementar auditoria de alteração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Identificar entidades na sua solução que contenham dados sensíveis e implementar a auditoria de alteração dessas entidades e campos |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Certifique-se de que a auditoria e a exploração madeireira são aplicadas na aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | Ativar a auditoria e o registo em todos os componentes. Os registos de auditoria devem capturar o contexto do utilizador. Identifique todos os eventos importantes e inicie esses eventos. Implementar a exploração madeireira centralizada |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Certifique-se de que a rotação e a separação do tronco estão no lugar

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>A rotação de registos é um processo automatizado utilizado na administração do sistema no qual os ficheiros de registo datados são arquivados. Os servidores que executam grandes aplicações registam frequentemente todos os pedidos: face a registos volumosos, a rotação de registos é uma forma de limitar o tamanho total dos registos, permitindo ainda a análise de eventos recentes. </p><p>A separação de registos significa basicamente que tem de armazenar os seus ficheiros de registo numa divisória diferente, como onde o seu SISTEMA/aplicação está a decorrer de forma a evitar um ataque de Negação de serviço ou a degradação da sua aplicação o seu desempenho</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Certifique-se de que a aplicação não regista dados sensíveis do utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Verifique se não regista quaisquer dados sensíveis que um utilizador submeta ao seu site. Verifique se a exploração madeireira intencional, bem como os efeitos secundários causados por problemas de conceção. Exemplos de dados sensíveis incluem:</p><ul><li>Credenciais de utilizador</li><li>Número de Segurança Social ou outra informação de identificação</li><li>Números de cartão de crédito ou outras informações financeiras</li><li>Informação sobre saúde</li><li>Chaves privadas ou outros dados que possam ser usados para desencriptar informações encriptadas</li><li>Informações do sistema ou aplicação que podem ser usadas para atacar mais eficazmente a aplicação</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Certifique-se de que os ficheiros de auditoria e registo têm acesso restrito

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Verifique se os direitos de acesso aos ficheiros de registo estão devidamente definidos. As contas de candidatura devem ter acesso apenas por escrito e os operadores e o pessoal de apoio devem ter acesso apenas à leitura, se necessário.</p><p>As contas dos administradores são as únicas contas que devem ter acesso total. Verifique o Windows ACL nos ficheiros de registo para garantir que estão devidamente restringidos:</p><ul><li>As contas de inscrição devem ter acesso apenas por escrito</li><li>Os operadores e o pessoal de apoio devem ter acesso apenas à leitura, se necessário.</li><li>Administradores são as únicas contas que devem ter acesso total</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Certifique-se de que os Eventos de Gestão de Utilizadores estão registados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Certifique-se de que a aplicação monitoriza eventos de gestão de utilizadores, tais como logins de utilizador bem sucedidos e falhados, resets de passwords, alterações de password, bloqueio de conta, registo do utilizador. Fazer isto ajuda a detetar e reagir a comportamentos potencialmente suspeitos. Permite também recolher dados de operações; por exemplo, para rastrear quem está a aceder à aplicação</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Certifique-se de que o sistema tem defesas incorporadas contra o uso indevido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos**                   | <p>Devem ser utilizados controlos que aditam exceções à segurança em caso de utilização abusiva. Por exemplo, Se a validação da entrada estiver em vigor e um intruso tentar injetar código malicioso que não corresponda ao regex, pode ser lançada uma exceção de segurança que pode ser um indicativo de má utilização do sistema</p><p>Por exemplo, recomenda-se que haja exceções à segurança registadas e ações tomadas para as seguintes questões:</p><ul><li>Validação de entradas</li><li>Violações da CSRF</li><li>Força bruta (limite máximo para o número de pedidos por utilizador por recurso)</li><li>Violações de upload de ficheiros</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Ativar diagnósticos de login para aplicações web no Serviço de Aplicações Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | AmbienteType - Azure |
| **Referências**              | N/D  |
| **Passos** | <p>O Azure fornece diagnósticos incorporados para ajudar a depurar uma aplicação web do App Service. Aplica-se também a aplicações API e aplicações móveis. As aplicações web do Serviço de Aplicações fornecem funcionalidades de diagnóstico para registar informações tanto do servidor web como da aplicação web.</p><p>Estes são logicamente separados em diagnósticos de servidores web e diagnósticos de aplicações</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Certifique-se de que a auditoria de login está ativada no Servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Configure auditoria de login](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Passos** | <p>A auditoria de login do Servidor de Dados deve ser ativada para detetar/confirmar ataques de adivinhação de palavras-passe. É importante capturar tentativas falhadas de login. Capturar tentativas de login bem sucedidas e falhadas proporciona benefícios adicionais durante investigações forenses</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Ativar a deteção de ameaças no Azure SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Começar com deteção de ameaças de base de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passos** |<p>A Deteção de Ameaças deteta atividades anómalas na base de dados que indicam potenciais ameaças à segurança na base de dados. Fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas.</p><p>Os utilizadores podem explorar os eventos suspeitos usando a Auditoria de Base de Dados Azure SQL para determinar se resultam de uma tentativa de acesso, violação ou exploração de dados na base de dados.</p><p>A Deteção de Ameaças torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Utilizar o Azure Storage Analytics para auditar o acesso ao Armazenamento Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D |
| **Referências**              | [Utilização de Storage Analytics para monitorizar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passos** | <p>Para cada conta de armazenamento, pode-se permitir que o Azure Storage Analytics realize dados de registo e armazenamento de métricas. Os registos de análise de armazenamento fornecem informações importantes, como o método de autenticação utilizado por alguém quando acedem ao armazenamento.</p><p>Isto pode ser muito útil se estiver a guardar fortemente o acesso ao armazenamento. Por exemplo, no Blob Storage pode definir todos os recipientes para privados e implementar a utilização de um serviço SAS ao longo das suas aplicações. Em seguida, pode verificar regularmente os registos para ver se as suas bolhas são acedidas através das chaves da conta de armazenamento, o que pode indicar uma quebra de segurança, ou se as bolhas são públicas, mas não deveriam ser.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementar registo suficiente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Passos** | <p>A falta de um rasto de auditoria adequado depois de um incidente de segurança pode dificultar os esforços forenses. A Windows Communication Foundation (WCF) oferece a capacidade de registar tentativas de autenticação bem sucedidas e/ou falhadas.</p><p>As tentativas de autenticação falhadas podem alertar os administradores de potenciais ataques à força bruta. Da mesma forma, registar eventos de autenticação bem sucedidos pode fornecer um rasto de auditoria útil quando uma conta legítima é comprometida. Ativar a funcionalidade de auditoria de segurança de serviço da WCF |

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

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementar manipulação suficiente de falhas de auditoria

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Passos** | <p>A solução desenvolvida está configurada para não gerar uma exceção quando não escreve para um registo de auditoria. Se o WCF estiver configurado para não lançar uma exceção quando não puder escrever para um registo de auditoria, o programa não será notificado da falha e a auditoria de eventos críticos de segurança pode não ocorrer.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do ficheiro de configuração wcf abaixo instrui o WCF a não notificar o pedido quando o WCF não escrever a um registo de auditoria.
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
Configure o WCF para notificar o programa sempre que não puder escrever a um registo de auditoria. O programa deve ter um sistema de notificação alternativo em vigor para alertar a organização de que os trilhos de auditoria não estão a ser mantidos. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Certifique-se de que a auditoria e a exploração madeireira são aplicadas na Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Ativar auditoria e registo em APIs web. Os registos de auditoria devem capturar o contexto do utilizador. Identifique todos os eventos importantes e inicie esses eventos. Implementar a exploração madeireira centralizada |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Field Gateway

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Quando vários dispositivos se ligarem a um Field Gateway, certifique-se de que as tentativas de ligação e o estado de autenticação (sucesso ou falha) para dispositivos individuais são registados e mantidos no Field Gateway.</p><p>Além disso, nos casos em que o Field Gateway mantém as credenciais Do IoT Hub para dispositivos individuais, certifique-se de que a auditoria é realizada quando estas credenciais são recuperadas. Desenvolver um processo para carregar periodicamente os registos para o Hub/armazenamento Azure IoT para retenção a longo prazo.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Certifique-se de que a auditoria e a exploração madeireira adequadas são aplicadas no Cloud Gateway

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Introdução à monitorização das operações do IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passos** | <p>Conceção para recolha e armazenamento de dados de auditoria recolhidos através da Monitorização de Operações do Hub IoT. Ativar as seguintes categorias de monitorização:</p><ul><li>Operações de identidade do dispositivo</li><li>Comunicações dispositivo-nuvem</li><li>Comunicações Cloud-to-device</li><li>Ligações</li><li>Uploads de ficheiros</li></ul>|