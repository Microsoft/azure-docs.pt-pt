---
title: Auditoria e registro - ferramenta de modelagem de ameaças Microsoft - Azure | Documentos da Microsoft
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
ms.openlocfilehash: 4843828c89b04e36b0bcc73dcedf9c5735b73729
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610864"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Quadro de segurança: Auditoria e registo | Atenuações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificar as entidades sensíveis na sua solução e implementar a auditoria de alteração](#sensitive-entities)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de que a auditoria e Registro é aplicada a aplicação](#auditing)</li><li>[Certifique-se de que a rotação do registo e a separação estão em vigor](#log-rotation)</li><li>[Certifique-se de que o aplicativo não regista dados de utilizador confidenciais](#log-sensitive-data)</li><li>[Certifique-se de que a auditoria e de ficheiros de registo têm acesso restrito](#log-restricted-access)</li><li>[Certifique-se de que os eventos de gestão de utilizador registados](#user-management)</li><li>[Certifique-se de que o sistema tem incorporadas defesas contra utilização indevida](#inbuilt-defenses)</li><li>[Ativar o registo de diagnóstico para aplicações web no App Service do Azure](#diagnostics-logging)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que a auditoria de início de sessão está ativada no SQL Server](#identify-sensitive-entities)</li><li>[Ativar a deteção de ameaças em SQL do Azure](#threat-detection)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Utilizar a análise de armazenamento do Azure para auditar o acesso do armazenamento do Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar o Registro em log suficiente](#sufficient-logging)</li><li>[Implementar o tratamento da falha de auditoria suficientes](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a auditoria e Registro é aplicada a Web API](#logging-web-api)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Certifique-se de que é aplicada apropriado de auditoria e registo no Gateway de campo](#logging-field-gateway)</li></ul> |
| **Gateway de Cloud da IoT** | <ul><li>[Certifique-se de que é aplicada apropriado de auditoria e registo no Gateway de Cloud](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identificar as entidades sensíveis na sua solução e implementar a auditoria de alteração

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | Identificar as entidades na sua solução que contêm dados confidenciais e implementar a auditoria de alteração nesses campos e entidades |

## <a id="auditing"></a>Certifique-se de que a auditoria e Registro é aplicada a aplicação

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | Ative auditoria e registo em todos os componentes. Registos de auditoria devem capturar o contexto de utilizador. Identifique todos os eventos importantes e registrar esses eventos. Implementar o registo centralizado |

## <a id="log-rotation"></a>Certifique-se de que a rotação do registo e a separação estão em vigor

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Rotação do registo é um processo automatizado utilizado na administração de sistema no qual os ficheiros de registo com data são arquivados. Servidores que executam aplicativos grandes, muitas vezes, registar cada pedido: diante de registos pesados, rotação do registo é uma forma de limitar o tamanho total dos logs de enquanto ainda permite que uma análise de eventos recentes. </p><p>Separação de registo, basicamente, significa que precisará armazenar seu log de arquivos numa partição diferente como onde seu SO/aplicação está em execução no fim de avert um ataque de negação de serviço ou fazer o downgrade de seu aplicativo, o desempenho</p>|

## <a id="log-sensitive-data"></a>Certifique-se de que o aplicativo não regista dados de utilizador confidenciais

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Verifique se não iniciar quaisquer dados confidenciais que um usuário envia para o seu site. Verifique o registo intencional, bem como os efeitos colaterais causados por problemas de design. Exemplos de dados confidenciais:</p><ul><li>Credenciais de utilizador</li><li>Número da Previdência ou outras informações de identificação</li><li>Números de cartão de crédito ou outras informações financeiras</li><li>Informações de estado de funcionamento</li><li>As chaves privadas ou outros dados que podem ser utilizados para desencriptar informações encriptadas</li><li>Informações de sistema ou aplicativo que podem ser utilizadas para atacar com mais eficiência a aplicação</li></ul>|

## <a id="log-restricted-access"></a>Certifique-se de que a auditoria e de ficheiros de registo têm acesso restrito

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Verifique se os direitos de acesso nos ficheiros de registo estão definidos corretamente. As contas de aplicativos devem ter acesso só de escrita e operadores e suporte técnico deve ter acesso só de leitura, conforme necessário.</p><p>Contas de administradores são as únicas contas que devem ter acesso total. Verifique a ACL do Windows em ficheiros de registo para garantir que eles são restritos corretamente:</p><ul><li>As contas de aplicativos devem ter acesso só de escrita</li><li>Operadores e suporte técnico deve ter acesso só de leitura conforme necessário</li><li>Os administradores são as únicas contas que devem ter acesso total</li></ul>|

## <a id="user-management"></a>Certifique-se de que os eventos de gestão de utilizador registados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Certifique-se de que a aplicação monitoriza os eventos de gestão de utilizador, tais como inícios de sessão do utilizador com êxito ou falhada, redefinição de senha, alterações de palavra-passe, o bloqueio de conta, o Registro do usuário. Fazer isso ajuda a detetar e reagir a comportamentos potencialmente suspeitos. Ele também lhe permite para recolher dados de operações; Por exemplo, para controlar quem acede à aplicação</p>|

## <a id="inbuilt-defenses"></a>Certifique-se de que o sistema tem incorporadas defesas contra utilização indevida

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Controles devem estar no local que lançar a exceção de segurança em caso de utilização indevida de aplicação. Por exemplo, se a validação de entrada for no local e um invasor tente injetar código malicioso que não corresponde ao regex, uma exceção de segurança pode ser lançada que pode ser um indicativo de utilização indevida de sistema</p><p>Por exemplo, é recomendável ter sessão iniciadas de exceções de segurança e ações executadas para os seguintes problemas:</p><ul><li>Validação de entradas</li><li>Violações de CSRF</li><li>Força bruta (limite superior para o número de pedidos por utilizador por recurso)</li><li>Violações de carregamento de ficheiros</li><ul>|

## <a id="diagnostics-logging"></a>Ativar o registo de diagnóstico para aplicações web no App Service do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | N/A  |
| **Passos** | <p>O Azure disponibiliza diagnósticos incorporados para ajudar a depurar as aplicações Web do Serviço de Aplicações. Também se aplica a aplicações API e aplicações móveis. Aplicações web do serviço de aplicações fornecem funcionalidade Diagnóstico de informações de registo do servidor web e a aplicação web.</p><p>Estes são logicamente separados para o diagnóstico do servidor web e ao application diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Certifique-se de que a auditoria de início de sessão está ativada no SQL Server

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Configurar a auditoria de início de sessão](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Passos** | <p>A auditoria de início de sessão do servidor de base de dados tem de estar ativada para ataques de adivinhação de senha detetar/confirmar. É importante capturar as tentativas de início de sessão. Captura de ambas as tentativas de início de sessão com êxito ou falhada fornece benefícios adicionais durante as investigações forenses</p>|

## <a id="threat-detection"></a>Ativar a deteção de ameaças em SQL do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Introdução à deteção de ameaças da base de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passos** |<p>Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados. Ele fornece uma nova camada de segurança, o que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem ao fornecer alertas de segurança relativamente a atividades anómalas.</p><p>Os utilizadores podem explorar os eventos suspeitos com a auditoria de base de dados SQL do Azure para determinar se estes resultam de uma tentativa de aceder, violar ou explorar dados na base de dados.</p><p>Deteção de ameaças facilita lidar com potenciais ameaças à base de dados sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança avançada</p>|

## <a id="analytics"></a>Utilizar a análise de armazenamento do Azure para auditar o acesso do armazenamento do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A |
| **Referências**              | [A utilização de análise de armazenamento para monitorizar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passos** | <p>Para cada conta de armazenamento, um pode ativar a análise de armazenamento do Azure efetuar o registo e armazenar dados de métricas. Registos de análise de armazenamento fornecem informações importantes, como o método de autenticação utilizado por uma pessoa quando acedem de armazenamento.</p><p>Isto pode ser realmente útil se rigidamente são protege o acesso ao armazenamento. Por exemplo, no armazenamento de BLOBs pode definir todos os contentores para particular e implementar a utilização de um serviço SAS ao longo de seus aplicativos. Em seguida, pode verificar os registos regularmente para ver se os blobs são acedidos utilizando as chaves de conta de armazenamento, que podem indicar uma violação de segurança, ou se os blobs sejam públicos, mas eles não devem ser.</p>|

## <a id="sufficient-logging"></a>Implementar o Registro em log suficiente

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Passos** | <p>A falta de uma trilha de auditoria adequados após um incidente de segurança pode hamper esforços forenses. Windows Communication Foundation (WCF) oferece a capacidade de registar as tentativas de autenticação com êxito e/ou falha.</p><p>Registo de tentativas falhadas de autenticação pode avisar os administradores de potenciais ataques de força bruta. Da mesma forma, registo de eventos de autenticação com êxito pode fornecer uma trilha de auditoria útil quando uma conta legítima for comprometida. Ativar a funcionalidade de auditoria de segurança de serviço do WCF |

### <a name="example"></a>Exemplo
Segue-se um exemplo de configuração com a auditoria ativada
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

## <a id="audit-failure-handling"></a>Implementar o tratamento da falha de auditoria suficientes

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Reforçamos Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Passos** | <p>Solução desenvolvida é configurada para não gerar uma exceção quando ocorre uma falha ao escrever um log de auditoria. Se o WCF é configurado para não gerar uma exceção quando for não é possível escrever um log de auditoria, o programa não será notificado da falha e não pode ocorrer a auditoria de eventos de segurança críticas.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do ficheiro de configuração de WCF abaixo Instrua o WCF não notificar o aplicativo quando o WCF não consegue escrever um log de auditoria.
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
Configure o WCF para notificar o programa sempre que for não é possível escrever um log de auditoria. O programa deve ter um esquema de notificação alternativo para alerta não estão a ser mantido a organização que registos de auditoria. 

## <a id="logging-web-api"></a>Certifique-se de que a auditoria e Registro é aplicada a Web API

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Ative auditoria e Registro em APIs da Web. Registos de auditoria devem capturar o contexto de utilizador. Identifique todos os eventos importantes e registrar esses eventos. Implementar o registo centralizado |

## <a id="logging-field-gateway"></a>Certifique-se de que é aplicada apropriado de auditoria e registo no Gateway de campo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Quando vários dispositivos se ligam a um Gateway de campo, certifique-se de que as tentativas de ligação e o estado de autenticação (êxito ou falha) para dispositivos individuais são registados e mantidos no Gateway de campo.</p><p>Além disso, nos casos em que o Gateway de campo é manter as credenciais do IoT Hub para dispositivos individuais, certifique-se de que a auditoria é executada quando estas credenciais são recuperadas. Desenvolva um processo para carregar periodicamente os registos para o IoT Hub/armazenamento do Azure para retenção a longo prazo.</p> |

## <a id="logging-cloud-gateway"></a>Certifique-se de que é aplicada apropriado de auditoria e registo no Gateway de Cloud

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Introdução à monitorização de operações do IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passos** | <p>Design para recolher e armazenar dados de auditoria coletados por meio de monitorização de operações do IoT Hub. Ative as seguintes categorias de monitorização:</p><ul><li>Operações de identidade de dispositivo</li><li>Comunicações de dispositivo para a cloud</li><li>Comunicações de cloud-para-dispositivo</li><li>Ligações</li><li>Carrega o ficheiro</li></ul>|