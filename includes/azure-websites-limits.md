---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 1080e3488483610af0f0c9afadaf000895021821
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657846"
---
| Resource | Livre | Partilhado | Básica | Standard | Premium (v2) | Plano Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos Web, móveis ou de API](https://azure.microsoft.com/services/app-service/) por [Azure app plano de serviço](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Partilhado |Partilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escalar horizontalmente](../articles/app-service/manage-scale-up.md) (máximo de instâncias) |1 compartilhado |1 compartilhado |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados<sup>3</sup>|100 dedicado<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a>|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |
| Memória (1 hora) |1\.024 MB por plano do serviço de aplicativo |1\.024 MB por aplicativo |N/A |N/D |N/D |N/A |
| Largura de banda |165 MB |Ilimitado, [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) se aplicam |Ilimitado, [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) se aplicam |Ilimitado, [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) se aplicam |Ilimitado, [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) se aplicam |Ilimitado, [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) se aplicam |
| Arquitetura da aplicação |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web Sockets por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Conexões de [depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| Certificados do serviço de aplicativo por assinatura<sup>10</sup>| Não suportado | Não suportado |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio azurewebsites.net)|500 |500 |500 |500 |500 |
| Suporte a [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) de domínio personalizado |Sem suporte, certificado curinga para *. azurewebsites.net disponível por padrão|Sem suporte, certificado curinga para *. azurewebsites.net disponível por padrão|Conexões SSL SNI ilimitadas |Conexões SSL SNI e 1 IP SSL inclusas incluídas |Conexões SSL SNI e 1 IP SSL inclusas incluídas | Conexões SSL SNI e 1 IP SSL inclusas incluídas|
| Balanceador de carga integrado | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backups agendados](../articles/app-service/manage-backup.md) | | | | Backups agendados a cada 2 horas, no máximo 12 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) |
| [Dimensionamento Automático](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| Suporte [do Agendador do Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitorização de pontos finais](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de preparo](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup> As cotas de armazenamento e aplicativos são por plano do serviço de aplicativo, a menos que indicado o contrário.  
<sup>2</sup> O número real de aplicativos que você pode hospedar nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias de máquina e da utilização de recursos correspondente.  
<sup>3</sup> As instâncias dedicadas podem ter tamanhos diferentes. Para obter mais informações, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Mais permissões são permitidas mediante solicitação.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano do serviço de aplicativo.  
<sup>6</sup> Esses recursos são restritos por recursos físicos nas instâncias dedicadas (o tamanho da instância e o número de instâncias).  
<sup>7</sup> Se você dimensionar um aplicativo na camada básica para duas instâncias, terá 350 conexões simultâneas para cada uma das duas instâncias. Para a camada Standard e superior, não há limites teóricos para os soquetes da Web, mas outros fatores podem limitar o número de soquetes da Web. Por exemplo, as solicitações simultâneas máximas permitidas `maxConcurrentRequestsPerCpu`(definidas por) são: 7.500 por VM pequena, 15.000 por VM média (7.500 x 2 núcleos) e 75.000 por VM grande (18.750 x 4 núcleos).  
<sup>8</sup> Execute executáveis personalizados e/ou scripts sob demanda, em um agendamento ou continuamente como uma tarefa em segundo plano em sua instância do serviço de aplicativo. Sempre Ativo é necessário para a execução contínua do WebJobs. É necessário o Azure Scheduler, Gratuito ou Padrão, para WebJobs agendados. Não há nenhum limite predefinido para o número de trabalhos Web que podem ser executados em uma instância do serviço de aplicativo. Há limites práticos que dependem do que o código do aplicativo está tentando fazer.  
<sup>9</sup> Serviço do Aplicativo Isolado SKUs podem ter balanceamento de carga internamente (ILB) com Azure Load Balancer, portanto, não há conectividade pública da Internet. Como resultado, algumas funcionalidades de um Serviço de Aplicações Isolado ILB têm de ser utilizadas a partir de computadores com acesso direto ao ponto final da rede ILB.  
<sup>10</sup> O limite de cota de Certificado do Serviço de Aplicativo por assinatura pode ser aumentado por meio de uma solicitação de suporte para um limite máximo de 200.  