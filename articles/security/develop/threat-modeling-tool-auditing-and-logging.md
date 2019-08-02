---
title: Auditoria e registro em log-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728418"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Quadro de segurança: Auditoria e registro em log | Mitigações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificar entidades confidenciais em sua solução e implementar a auditoria de alteração](#sensitive-entities)</li></ul> |
| **Aplicativo Web** | <ul><li>[Verifique se a auditoria e o log são impostos no aplicativo](#auditing)</li><li>[Verifique se a rotação de log e a separação estão em vigor](#log-rotation)</li><li>[Verifique se o aplicativo não registra em log os dados confidenciais do usuário](#log-sensitive-data)</li><li>[Garantir que os arquivos de auditoria e de log tenham acesso restrito](#log-restricted-access)</li><li>[Verifique se os eventos de gerenciamento de usuário estão registrados](#user-management)</li><li>[Certifique-se de que o sistema tenha defesas embutidas contra uso indevido](#inbuilt-defenses)</li><li>[Habilitar o log de diagnóstico para aplicativos Web no serviço Azure App](#diagnostics-logging)</li></ul> |
| **Base de Dados** | <ul><li>[Verifique se a auditoria de logon está habilitada no SQL Server](#identify-sensitive-entities)</li><li>[Habilitar a detecção de ameaças no SQL do Azure](#threat-detection)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Usar Análise de Armazenamento do Azure para auditar o acesso do armazenamento do Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar registro em log suficiente](#sufficient-logging)</li><li>[Implementar tratamento de falha de auditoria suficiente](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Garantir que a auditoria e o log sejam impostos na API da Web](#logging-web-api)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Verifique se a auditoria apropriada e o registro em log são aplicados no gateway de campo](#logging-field-gateway)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Verifique se a auditoria apropriada e o registro em log são aplicados no gateway de nuvem](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identificar entidades confidenciais em sua solução e implementar a auditoria de alteração

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | Identifique entidades em sua solução contendo dados confidenciais e implemente a auditoria de alterações nessas entidades e campos |

## <a id="auditing"></a>Verifique se a auditoria e o log são impostos no aplicativo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | Habilite a auditoria e o log em todos os componentes. Os logs de auditoria devem capturar o contexto do usuário. Identifique todos os eventos importantes e registre esses eventos. Implementar o registro em log centralizado |

## <a id="log-rotation"></a>Verifique se a rotação de log e a separação estão em vigor

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>A rotação de log é um processo automatizado usado na administração do sistema no qual os arquivos de log com data são arquivados. Os servidores que executam grandes aplicativos geralmente registram cada solicitação: diante de logs em massa, a rotação de log é uma maneira de limitar o tamanho total dos logs e ainda permitir a análise de eventos recentes. </p><p>A separação de log basicamente significa que você precisa armazenar os arquivos de log em uma partição diferente em que o seu sistema operacional/aplicativo está sendo executado para evitar um ataque de negação de serviço ou o downgrade de seu aplicativo, seu desempenho</p>|

## <a id="log-sensitive-data"></a>Verifique se o aplicativo não registra em log os dados confidenciais do usuário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Verifique se você não registra dados confidenciais que um usuário envia para seu site. Verifique o registro em log intencional, bem como os efeitos colaterais causados por problemas de design. Exemplos de dados confidenciais incluem:</p><ul><li>Credenciais de Utilizador</li><li>Número do seguro social ou outras informações de identificação</li><li>Números de cartão de crédito ou outras informações financeiras</li><li>Informações de integridade</li><li>Chaves privadas ou outros dados que podem ser usados para descriptografar informações criptografadas</li><li>Informações do sistema ou do aplicativo que podem ser usadas para atacar o aplicativo com mais eficiência</li></ul>|

## <a id="log-restricted-access"></a>Garantir que os arquivos de auditoria e de log tenham acesso restrito

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Verifique se os direitos de acesso aos arquivos de log estão definidos adequadamente. As contas de aplicativo devem ter acesso somente gravação e operadores e a equipe de suporte deve ter acesso somente leitura, conforme necessário.</p><p>As contas de administradores são as únicas contas que devem ter acesso completo. Verifique a ACL do Windows nos arquivos de log para garantir que eles sejam devidamente restritos:</p><ul><li>As contas de aplicativo devem ter acesso somente gravação</li><li>Os operadores e a equipe de suporte devem ter acesso somente leitura, conforme necessário</li><li>Os administradores são as únicas contas que devem ter acesso completo</li></ul>|

## <a id="user-management"></a>Verifique se os eventos de gerenciamento de usuário estão registrados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Verifique se o aplicativo monitora eventos de gerenciamento de usuários, como logons de usuário bem-sucedidos e com falha, redefinições de senha, alterações de senha, bloqueio de conta, registro de usuário. Isso ajuda a detectar e reagir a comportamentos potencialmente suspeitos. Ele também permite coletar dados de operações; por exemplo, para controlar quem está acessando o aplicativo</p>|

## <a id="inbuilt-defenses"></a>Certifique-se de que o sistema tenha defesas embutidas contra uso indevido

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos**                   | <p>Os controles devem estar em vigor, o que gera a exceção de segurança no caso de uso indevido do aplicativo. Por exemplo, se a validação de entrada estiver em vigor e um invasor tentar injetar um código mal-intencionado que não corresponda ao Regex, uma exceção de segurança poderá ser gerada, o que pode ser um indício do uso indevido do sistema</p><p>Por exemplo, é recomendável ter exceções de segurança registradas e ações executadas para os seguintes problemas:</p><ul><li>Validação de entradas</li><li>Violações de CSRF</li><li>Força bruta (limite superior para o número de solicitações por usuário por recurso)</li><li>Violações de upload de arquivo</li><ul>|

## <a id="diagnostics-logging"></a>Habilitar o log de diagnóstico para aplicativos Web no serviço Azure App

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambientetype-Azure |
| **Referências**              | N/A  |
| **Passos** | <p>O Azure disponibiliza diagnósticos incorporados para ajudar a depurar as aplicações Web do Serviço de Aplicações. Ele também se aplica a aplicativos de API e aplicativos móveis. Os aplicativos Web do serviço de aplicativo fornecem funcionalidade de diagnóstico para registrar informações do servidor Web e do aplicativo Web.</p><p>Eles são separados logicamente no diagnóstico do servidor Web e no Application Diagnostics</p>|

## <a id="identify-sensitive-entities"></a>Verifique se a auditoria de logon está habilitada no SQL Server

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Configurar auditoria de logon](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Passos** | <p>A auditoria de logon do servidor de banco de dados deve ser habilitada para detectar/confirmar ataques de adivinhação de senha. É importante capturar tentativas de logon com falha. Capturar tentativas de logon bem-sucedidas e com falha fornece benefícios adicionais durante investigações forenses</p>|

## <a id="threat-detection"></a>Habilitar a detecção de ameaças no SQL do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL-V12 |
| **Referências**              | [Introdução à detecção de ameaças do banco de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passos** |<p>A detecção de ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Ele fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais.</p><p>Os usuários podem explorar os eventos suspeitos usando a auditoria do banco de dados SQL do Azure para determinar se eles resultam de uma tentativa de acessar, violar ou explorar os dados no banco de dado.</p><p>A detecção de ameaças torna simples lidar com ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados</p>|

## <a id="analytics"></a>Usar Análise de Armazenamento do Azure para auditar o acesso do armazenamento do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A |
| **Referências**              | [Usando Análise de Armazenamento para monitorar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passos** | <p>Para cada conta de armazenamento, é possível habilitar a Análise de Armazenamento do Azure para executar logs e armazenar dados de métricas. Os logs de análise de armazenamento fornecem informações importantes, como o método de autenticação usado por alguém quando acessam o armazenamento.</p><p>Isso pode ser realmente útil se você estiver protegendo rigidamente o acesso ao armazenamento. Por exemplo, no armazenamento de BLOBs, você pode definir todos os contêineres para privado e implementar o uso de um serviço SAS em todos os seus aplicativos. Em seguida, você pode verificar os logs regularmente para ver se os BLOBs são acessados usando as chaves da conta de armazenamento, o que pode indicar uma violação de segurança ou se os BLOBs são públicos, mas não deveriam estar.</p>|

## <a id="sufficient-logging"></a>Implementar registro em log suficiente

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Passos** | <p>A falta de uma trilha de auditoria adequada após um incidente de segurança pode dificultar os esforços forenses. O Windows Communication Foundation (WCF) oferece a capacidade de registrar tentativas de autenticação bem-sucedidas e/ou com falha.</p><p>O log de tentativas de autenticação com falha pode avisar os administradores sobre possíveis ataques de força bruta. Da mesma forma, o log de eventos de autenticação bem-sucedidos pode fornecer uma trilha de auditoria útil quando uma conta legítima é comprometida. Habilitar o recurso de auditoria de segurança de serviço do WCF |

### <a name="example"></a>Exemplo
Veja a seguir um exemplo de configuração com a auditoria habilitada
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

## <a id="audit-failure-handling"></a>Implementar tratamento de falha de auditoria suficiente

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/A  |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecer o Reino Unido](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Passos** | <p>A solução desenvolvida está configurada para não gerar uma exceção quando ela não consegue gravar em um log de auditoria. Se o WCF estiver configurado para não lançar uma exceção quando não for capaz de gravar em um log de auditoria, o programa não será notificado sobre a falha e a auditoria de eventos críticos de segurança poderá não ocorrer.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do arquivo de configuração do WCF abaixo instrui o WCF a não notificar o aplicativo quando o WCF não conseguir gravar em um log de auditoria.
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
Configure o WCF para notificar o programa sempre que não for possível gravar em um log de auditoria. O programa deve ter um esquema de notificação alternativo em vigor para alertar a organização de que as trilhas de auditoria não estão sendo mantidas. 

## <a id="logging-web-api"></a>Garantir que a auditoria e o log sejam impostos na API da Web

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Habilite a auditoria e o log em APIs da Web. Os logs de auditoria devem capturar o contexto do usuário. Identifique todos os eventos importantes e registre esses eventos. Implementar o registro em log centralizado |

## <a id="logging-field-gateway"></a>Verifique se a auditoria apropriada e o registro em log são aplicados no gateway de campo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Quando vários dispositivos se conectam a um gateway de campo, verifique se as tentativas de conexão e o status de autenticação (êxito ou falha) para dispositivos individuais são registrados e mantidos no gateway de campo.</p><p>Além disso, nos casos em que o gateway de campo está mantendo as credenciais do Hub IoT para dispositivos individuais, certifique-se de que a auditoria seja executada quando essas credenciais forem recuperadas. Desenvolva um processo para carregar periodicamente os logs no Hub IoT/armazenamento do Azure para retenção de longo prazo.</p> |

## <a id="logging-cloud-gateway"></a>Verifique se a auditoria apropriada e o registro em log são aplicados no gateway de nuvem

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Introdução ao monitoramento de operações do Hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passos** | <p>Design para coletar e armazenar dados de auditoria coletados por meio do monitoramento de operações do Hub IoT. Habilite as seguintes categorias de monitoramento:</p><ul><li>Operações de identidade do dispositivo</li><li>Comunicações do dispositivo para a nuvem</li><li>Comunicações da nuvem para o dispositivo</li><li>Ligações</li><li>Carrega o ficheiro</li></ul>|