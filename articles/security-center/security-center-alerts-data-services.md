---
title: Deteção de serviços de dados no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: Este tópico apresenta os alertas de dados dos serviços disponíveis no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626293"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Deteção de ameaças para serviços de dados no Centro de segurança do Azure

 Centro de segurança analisa os registos de serviços de armazenamento de dados e aciona alertas quando Deteta uma ameaça aos seus recursos de dados. Este tópico lista os alertas que gera o Centro de segurança para os seguintes serviços:

* [Base de dados SQL do Azure e SQL Data Warehouse](#data-sql)
* [Armazenamento do Azure](#azure-storage)

## Base de dados SQL do Azure e SQL Data Warehouse <a name="data-sql"></a>

A deteção de Deteta atividades anómalas que invulgares e potencialmente prejudiciais de ameaças SQL tentarem aceder ou explorar bases de dados. Centro de segurança analisa os registos de auditoria SQL e é executada nativamente no motor de SQL.

|Alerta|Descrição|
|---|---|
|**Vulnerabilidade a Injeção de SQL**|Um aplicativo gerou uma instrução SQL defeituosa na base de dados. Isto pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Existem dois motivos possíveis para a geração de uma instrução defeituosa: De um defeito no código da aplicação construído a instrução SQL defeituosa. Em alternativa, código do aplicativo ou procedimentos armazenados não limpar entradas do usuário ao construir a instrução SQL defeituosa, que pode ser explorada para Injeção de SQL.|
|**Potencial injeção de SQL**|Ocorreu uma exploração ativa em relação a uma aplicação identificada vulnerável a injeção de SQL. Isso significa que um atacante está a tentar injetar instruções SQL maliciosas usando o código de aplicativo vulnerável ou procedimentos armazenados.|
|**Acesso a partir de uma localização invulgar**|Houve uma alteração no padrão de acesso ao SQL server, em que alguém iniciou sessão para o SQL server a partir de uma localização geográfica invulgar. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).|
|**Acesso a partir de principal invulgar**|Houve uma alteração no padrão de acesso para o SQL server - alguém iniciou sessão no SQL server utilizar um principal invulgar (utilizador SQL). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).|
|**Acesso a partir de uma aplicação potencialmente prejudicial**|Foi utilizada uma aplicação potencialmente prejudicial para acessar o banco de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.|
|**Credenciais SQL de força bruta**|Ocorreu um número anormalmente elevado de inícios de sessão falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.|

Para obter mais informações sobre o veja de alertas de deteção de ameaças SQL,[deteção de ameaças da base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)e leia a seção de alertas de deteção de ameaças. Consulte também [Centro de segurança do Azure como ajuda a revelar um Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) para ver um exemplo de como o Centro de segurança utilizado a deteção de atividade SQL maliciosa para detetar um ataque.

## Armazenamento do Azure<a name="azure-storage"></a>

>[!NOTE]
> Proteção avançada contra ameaças do armazenamento do Azure só está atualmente disponível para armazenamento de Blobs. 

O Advanced Threat Protection para o Armazenamento do Azure proporciona uma camada adicional de inteligência de segurança que deteta tentativas invulgares e potencialmente danosas de acesso ou exploração de contas de armazenamento. Esta camada de proteção permite-lhe a ameaças de endereço sem a necessidade de ser um especialista em segurança e a gestão de sistemas de monitorização de segurança.

Centro de segurança analisa os registos de diagnóstico de leitura, escrita e pedidos de eliminação para o armazenamento de BLOBs para detetar ameaças e aciona alertas quando ocorrem anomalias na atividade. Para obter mais informações, consulte a [configurar o registo de análise de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) para obter mais informações.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Anomalias de acesso de localização invulgar**|Análise de tráfego de rede de amostragem detetou comunicações de protocolo RDP (Remote Desktop) de saída anómalas provenientes de um recurso na sua implementação. Esta atividade é considerada anormal para este ambiente e pode indicar que o seu recurso foi comprometido e agora é utilizado para o ponto final RDP de força bruta do externo. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Anomalias de acesso de aplicação**|Indica que um aplicativo fora do comum acedeu esta conta de armazenamento. Uma causa potencial é que um atacante acedeu a sua conta de armazenamento através de uma nova aplicação.|
|**Anomalias de acesso anónimo**|Indica que existe uma alteração no padrão de acesso a uma conta de armazenamento. Por exemplo, a conta tem sido acedida anonimamente (sem qualquer autenticação), que é inesperado em comparação com o padrão de acesso recentes nesta conta. Uma causa potencial é que um invasor ter explorado acesso de leitura público para um contentor de retenções blob (s) armazenamento.|
|**Anomalias de filtragem de dados**|Indica que foi extraída uma excecionalmente grande quantidade de dados em comparação com a atividade recente deste contentor de armazenamento. Uma causa potencial é que um invasor tiver extraído uma grande quantidade de dados de um contentor de retenções blob (s) armazenamento.|
|**Anomalias de eliminação inesperado**|Indica que uma ou mais operações de eliminação inesperado ocorreu numa conta de armazenamento, em comparação com a atividade recente nesta conta. Uma causa potencial é que um invasor tiver eliminado dados da sua conta de armazenamento.|
|**Carregar o pacote de serviço Cloud do Azure**|Indica que um pacote de serviço Cloud do Azure (ficheiro. cspkg) foi carregado para uma conta de armazenamento de forma invulgar, em comparação comparada a atividade recente nesta conta. Uma causa potencial é que um invasor tem sido Preparando para implantar o código malicioso da sua conta de armazenamento a um serviço cloud do Azure.|
|**Anomalias de acesso de permissão**|Indica que as permissões de acesso desse contêiner de armazenamento foram alteradas de forma invulgar. Causas possíveis é que um invasor mudou enfraquecer a sua postura de segurança ou para persistência de obter as permissões de contentor.|
|**Anomalias de acesso de inspeção**|Indica que as permissões de acesso das contas de armazenamento tem sido inspecioná-los de forma invulgar, em comparação comparada a atividade recente nesta conta. Uma causa potencial é que um atacante efetuou reconhecimento de um ataque de futuro.|
|**Anomalias de exploração de dados**|Indica que tiverem sido enumerados blobs ou contentores numa conta de armazenamento de forma anormal, em comparação comparada a atividade recente nesta conta. Uma causa potencial é que um atacante efetuou reconhecimento de um ataque de futuro.|

>[!NOTE]
>Proteção avançada contra ameaças do armazenamento do Azure não está atualmente disponível nas regiões de cloud soberana e do Azure government.

Para obter mais informações sobre os alertas para o armazenamento, consulte a [proteção avançada contra ameaças do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) artigo e reveja a secção de alertas de proteção.
