---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305068"
---
| Recurso | Gratuito | Partilhada | Básica | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicações Web, móveis ou API](https://azure.microsoft.com/services/app-service/) por plano de serviço de [aplicações Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância computacional |Partilhada |Partilhada |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escala para fora](../articles/app-service/manage-scale-up.md) (instâncias máximas) |1 partilhado |1 partilhado |3<sup>dedicado3</sup> |10<sup>dedicados 3</sup> |30<sup>dedicados 3</sup>|100<sup>dedicados 4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a>|
| Hora do CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |
| Memória (1 hora) |1\.024 MB por plano de serviço de aplicações |1\.024 MB por app |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Tarifas ilimitadas de [transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Tarifas ilimitadas de [transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Tarifas ilimitadas de [transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Tarifas ilimitadas de [transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Tarifas ilimitadas de [transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |
| Arquitetura da aplicação |32 bits |32 bits |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |
| Tomadas web por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Ligações IP | 600 | 600 | Depende do tamanho da instância<sup>8</sup> | Depende do tamanho da instância<sup>8</sup> | Depende do tamanho da instância<sup>8</sup> | 16,000 |
| Conexões de [debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicação |1 |1 |1 |5 |5 |5 |
| Certificados de serviço de aplicações por subscrição<sup>9</sup>| Não suportado | Não suportado |10 |10 |10 |10 |
| Domínios personalizados por app</a> |0 (apenas subdomínio azurewebsites.net)|500 |500 |500 |500 |500 |
| Suporte [sSL](../articles/app-service/configure-ssl-certificate.md) de domínio personalizado |Não suportado, certificado wildcard para *.azurewebsites.net disponível por padrão|Não suportado, certificado wildcard para *.azurewebsites.net disponível por padrão|Ligações SNI SSL ilimitadas |Conexões Ilimitadas SNI SSL e 1 IP SSL incluídas |Conexões Ilimitadas SNI SSL e 1 IP SSL incluídas | Conexões Ilimitadas SNI SSL e 1 IP SSL incluídas|
| Conexões híbridas por plano | | | 5 | 25 | 200 | 200 |
| Equilibrador de carga integrado | |X |X |X |X |X<sup>10</sup> |
| [Sempre ligado](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backups programados](../articles/app-service/manage-backup.md) | | | | Backups programados a cada 2 horas, um máximo de 12 backups por dia (manual + agendado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + agendado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + agendado) |
| [Dimensionamento Automático](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitorização de pontos finais](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de encenação](../articles/app-service/deploy-staging-slots.md) por app| | | |5 |20 |20 |
| SLA | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup> Aplicações e quotas de armazenamento são de acordo com o plano de Serviço de Aplicações, a menos que se note o contrário.  
<sup>2</sup> O número real de aplicações que pode alojar nestas máquinas depende da atividade das aplicações, do tamanho das instâncias da máquina e da utilização correspondente de recursos.  
<sup>3</sup> Casos dedicados podem ter tamanhos diferentes. Para mais informações, consulte os preços do Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Mais são permitidos mediante pedido.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todas as aplicações no mesmo plano de serviço app. O tamanho total do conteúdo de todas as aplicações em todos os planos de serviço seletiva de um único grupo de recursos e região não pode exceder 500GB.  
<sup>6</sup> Estes recursos são limitados por recursos físicos nas instâncias dedicadas (a dimensão da instância e o número de instâncias).  
<sup>7</sup> Se escalar uma aplicação no nível Básico para dois casos, tem 350 ligações simultâneas para cada uma das duas instâncias. Para o nível Standard e acima, não existem limites teóricos para as tomadas web, mas outros fatores podem limitar o número de tomadas web. Por exemplo, os pedidos máximos simultâneos permitidos (definidos por `maxConcurrentRequestsPerCpu`) são: 7.500 por pequeno VM, 15.000 por VM médio (7.500 x 2 núcleos) e 75.000 por VM grande (18.750 x 4 núcleos).  
<sup>8</sup> As ligações IP máximas são por exemplo e dependem do tamanho da instância: 1.920 por instância B1/S1/P1V2, 3.968 por instância B2/S2/P2V2, 8.064 por instância B3/S3/P3V2.  
<sup>9</sup> O limite de quota de certificado de serviço de aplicação por subscrição pode ser aumentado através de um pedido de suporte para um limite máximo de 200.  
<sup>10</sup> Serviço de aplicações SKUs isolados podem ser equilibrados internamente (ILB) com Azure Load Balancer, por isso não há conectividade pública da internet. Como resultado, algumas funcionalidades de um Serviço de Aplicações Isoladas ILB devem ser utilizadas a partir de máquinas que tenham acesso direto ao ponto final da rede ILB.  
<sup>11</sup> Execute executáveis e/ou scripts personalizados a pedido, numa programação, ou continuamente como uma tarefa de fundo dentro da sua instância de Serviço de Aplicações. Always On é necessário para execução contínua de WebJobs. Não existe um limite predefinido para o número de WebJobs que podem ser executados numa instância do Serviço de Aplicações. Existem limites práticos que dependem do que o código de aplicação está a tentar fazer.  
