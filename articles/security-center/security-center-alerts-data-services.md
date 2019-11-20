---
title: Detecção de ameaças para serviços de dados na central de segurança do Azure | Microsoft Docs
description: Este artigo apresenta os alertas dos serviços de dados disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 8b99d89e8895cd1c8d8e9fe3961f0db9855fb7ee
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196131"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detecção de ameaças para serviços de dados na central de segurança do Azure

 A central de segurança do Azure analisa os logs dos serviços de armazenamento de dados e dispara alertas quando detecta uma ameaça aos seus recursos de dados. Este artigo lista os alertas que a central de segurança gera para os seguintes serviços:

* [Banco de dados SQL do Azure e Azure SQL Data Warehouse](#data-sql)
* [Storage do Azure](#azure-storage)
* [BD do Cosmos para o Azure](#cosmos-db)

## Banco de dados SQL e SQL Data Warehouse<a name="data-sql"></a>

A detecção de ameaças do SQL identifica atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. 

|Alerta|Descrição|
|---|---|
|**Uma possível vulnerabilidade à injeção de SQL**|Um aplicativo gerou uma instrução SQL com falha no banco de dados. Isso pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para uma instrução FAULTED. Um defeito no código do aplicativo pode ter construído a instrução SQL com falha. Ou, o código do aplicativo ou os procedimentos armazenados não corrigiram a entrada do usuário ao construir a instrução SQL com falha, que pode ser explorada para injeção de SQL.|
|**Possível injeção de SQL**|Uma exploração ativa ocorreu em um aplicativo identificado vulnerável à injeção de SQL. Isso significa que um invasor está tentando injetar instruções SQL mal-intencionadas usando o código do aplicativo vulnerável ou procedimentos armazenados.|
|**Fazer logon de um local incomum**|Houve uma alteração no padrão de acesso para SQL Server, em que alguém entrou no servidor a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Em outros casos, o alerta detecta uma ação mal-intencionada (um funcionário antigo ou invasor externo).|
|**Fazer logon por uma entidade de segurança não familiar**|Houve uma alteração no padrão de acesso para SQL Server. Alguém entrou no servidor usando uma entidade de segurança incomum (usuário). Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Em outros casos, o alerta detecta uma ação mal-intencionada (um funcionário antigo ou invasor externo).|
|**Tentativa de logon por um aplicativo potencialmente prejudicial**|Um aplicativo potencialmente prejudicial foi usado para acessar o banco de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Em outros casos, o alerta detecta um ataque que usa ferramentas comuns.|
|**Possível tentativa de força bruta de SQL**|Ocorreu um número anormalmente alto de entradas com falha com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Em outros casos, o alerta detecta um ataque de força bruta.|

Para obter mais informações sobre alertas de detecção de ameaças do SQL, consulte [detecção de ameaças do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Em particular, examine a seção alertas de detecção de ameaças. Veja também [como a central de segurança do Azure ajuda a revelar um cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) para exibir um exemplo de como a central de segurança usou a detecção de atividades mal-INTENCIONADAS do SQL para descobrir um ataque.

## Armazenamento do Azure<a name="azure-storage"></a>

>[!NOTE]
> A proteção avançada contra ameaças para armazenamento está disponível no momento apenas para armazenamento de BLOBs.

A proteção avançada contra ameaças para armazenamento fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem exigir que você seja um especialista em segurança e gerencie sistemas de monitoramento de segurança.

A central de segurança analisa os logs de diagnóstico de solicitações de leitura, gravação e exclusão para o armazenamento de BLOBs para detectar ameaças e dispara alertas quando ocorrem anomalias na atividade. Para obter mais informações, consulte [configurar log de análise de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Acesso de um local incomum a uma conta de armazenamento**|Indica que houve uma alteração no padrão de acesso para uma conta de armazenamento do Azure. Alguém acessou essa conta de um endereço IP considerado desconhecido quando comparado com a atividade recente. Um invasor obteve acesso à conta ou um usuário legítimo se conectou de um local geográfico novo ou incomum. Um exemplo do último é a manutenção remota de um novo aplicativo ou desenvolvedor.|
|**Aplicativo incomum acessado uma conta de armazenamento**|Indica que um aplicativo incomum acessou essa conta de armazenamento. Uma causa potencial é que um invasor acessou sua conta de armazenamento usando um novo aplicativo.|
|**Acesso anônimo a uma conta de armazenamento**|Indica que há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, a conta foi acessada anonimamente (sem nenhuma autenticação), o que é inesperado em comparação com o padrão de acesso recente nessa conta. Uma causa potencial é que um invasor explorou o acesso de leitura público a um contêiner que mantém o armazenamento de BLOBs.|
|**Acesso de um nó de saída do Tor a uma conta de armazenamento**|Indica que essa conta foi acessada com êxito de um endereço IP que é conhecido como um nó de saída ativo de Tor (um proxy de anonimato). A severidade desse alerta considera o tipo de autenticação usado (se houver) e se este é o primeiro caso desse acesso. As possíveis causas podem ser um invasor que tenha acessado sua conta de armazenamento usando o Tor ou um usuário legítimo que tenha acessado sua conta de armazenamento usando o Tor.|
|**Quantidade incomum de dados extraídos de uma conta de armazenamento**|Indica que uma quantidade incomum de dados muito grande foi extraída em comparação com a atividade recente neste contêiner de armazenamento. Uma causa potencial é que um invasor extraiu uma grande quantidade de dados de um contêiner que mantém o armazenamento de BLOBs.|
|**Exclusão incomum em uma conta de armazenamento**|Indica que uma ou mais operações de exclusão inesperadas ocorreram em uma conta de armazenamento, em comparação com a atividade recente nesta conta. Uma causa potencial é que um invasor excluiu dados de sua conta de armazenamento.|
|**Carregamento incomum de. cspkg para uma conta de armazenamento**|Indica que um pacote de serviços de nuvem do Azure (arquivo. cspkg) foi carregado em uma conta de armazenamento de maneira incomum, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor está se preparando para implantar código mal-intencionado de sua conta de armazenamento em um serviço de nuvem do Azure.|
|**Alteração incomum de permissões de acesso em uma conta de armazenamento**|Indica que as permissões de acesso deste contêiner de armazenamento foram alteradas de maneira incomum. Uma causa potencial é que um invasor alterou as permissões de contêiner para enfraquecer sua postura de segurança ou para obter persistência.|
|**Inspeção de acesso incomum em uma conta de armazenamento**|Indica que as permissões de acesso de uma conta de armazenamento foram inspecionadas de forma incomum, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor executou o reconhecimento para um ataque futuro.|
|**Exploração de dados incomum em uma conta de armazenamento**|Indica que os BLOBs ou contêineres em uma conta de armazenamento foram enumerados de forma anormal, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor executou o reconhecimento para um ataque futuro.|
|**VISUALIZAÇÃO – malware potencial carregado para uma conta de armazenamento**|Indica que um blob que contém o malware potencial foi carregado em uma conta de armazenamento. As possíveis causas podem incluir um upload de malware intencional por um invasor ou um upload não intencional, de um blob potencialmente mal-intencionado, por um usuário legítimo.|

>[!NOTE]
>A proteção avançada contra ameaças para armazenamento não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

Para obter mais informações sobre os alertas de armazenamento, consulte [proteção avançada contra ameaças para o armazenamento do Azure](../storage/common/storage-advanced-threat-protection.md). Em particular, examine a seção "alertas de proteção".

## Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas a seguir são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Azure Cosmos DB:

|Alerta|Descrição|
|---|---|
|**Acesso de um local incomum a uma conta de Cosmos DB**|Indica que houve uma alteração no padrão de acesso para uma conta de Azure Cosmos DB. Alguém acessou essa conta a partir de um endereço IP desconhecido, em comparação com a atividade recente. Um invasor acessou a conta ou um usuário legítimo o acessou de uma localização geográfica nova e incomum. Um exemplo do último é a manutenção remota de um novo aplicativo ou desenvolvedor.|
|**Quantidade incomum de dados extraídos de uma conta de Cosmos DB**|Indica que houve uma alteração no padrão de extração de dados de uma conta de Azure Cosmos DB. Alguém extraiu uma quantidade incomum de dados em comparação com a atividade recente. Um invasor pode ter extraído uma grande quantidade de dados de um banco de dado Azure Cosmos DB (por exemplo, vazamento ou vazamento de dados ou uma transferência não autorizada de dados). Ou, um usuário ou aplicativo legítimo pode ter extraído uma quantidade incomum de dados de um contêiner (por exemplo, para a atividade de backup de manutenção).|

Para obter mais informações, consulte [proteção avançada contra ameaças para Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).
