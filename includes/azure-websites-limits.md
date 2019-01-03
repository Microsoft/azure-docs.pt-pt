---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 6bb7db5d57508d448f88a30623b7f30e88a0a821
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736867"
---
| Recurso | Gratuito | Partilhado | Básica | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicações Web, móveis ou API](https://azure.microsoft.com/services/app-service/) por [plano do App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) |1 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Partilhado |Partilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escalamento horizontal](../articles/app-service/web-sites-scale.md) (máximo de instâncias) |1 partilhado |1 partilhado |3 dedicado<sup>3</sup> |10 dedicado<sup>3</sup> |20 dedicado<sup>3</sup>|100 dedicado<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 min)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitado, pague no padrão [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Memória (1 hora) |1024 MB por plano do serviço de aplicações |1024 MB por aplicação |N/A |N/D |N/D |N/A |
| Largura de Banda |165 MB |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicam-se |
| Arquitetura da aplicação |32 bits |32 bits |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |
| Web Sockets por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Em simultâneo [ligações do depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) por aplicação |1 |1 |1 |5 |5 |5 |
| Certificados de serviço de aplicação por subscrição<sup>10</sup>| Não suportado | Não suportado |10 |10 |10 |10 |
| Domínios personalizados por aplicação</a> |0 (apenas para subdomínio azurewebsites.net)|500 |500 |500 |500 |500 |
| Domínio personalizado [suporte SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Não suportado. Certificado de caráter universal para *. azurewebsites.net disponível por predefinição.|Não suportado. Certificado de caráter universal para *. azurewebsites.net disponível por predefinição.|Ligações de SNI SSL ilimitadas |SSL de SNI ilimitados e 1 ligações de IP SSL incluídos |SSL de SNI ilimitados e 1 ligações de IP SSL incluídos | SSL de SNI ilimitados e 1 ligações de IP SSL incluídos|
| Load Balancer Integrado | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Cópias de segurança agendadas](../articles/app-service/manage-backup.md) | | | | Cópias de segurança agendadas cada 2 horas, um máximo de 12 cópias de segurança por dia (manual + agendado) | Cópias de segurança agendadas cada hora, um máximo de 50 cópias de segurança por dia (manual + agendado) | Cópias de segurança agendadas cada hora, um máximo de 50 cópias de segurança por dia (manual + agendado) |
| [Dimensionamento automático](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [O Azure Scheduler](https://azure.microsoft.com/services/scheduler/) de suporte | |X |X |X |X |X |
| [Monitorização de pontos finais](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Ranhuras de teste](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,9% |99,95%|99,95%|99,95%|  

<sup>1</sup> aplicações e quotas de armazenamento são por plano do serviço de aplicações, salvo indicação em contrário.  
<sup>2</sup> o número real de aplicações que pode alojar nessas máquinas depende da atividade das aplicações, o tamanho das instâncias da máquina e a utilização de recursos correspondente.  
<sup>3</sup> instâncias dedicadas podem ser de tamanhos diferentes. Ver [preços do App Service](https://azure.microsoft.com/pricing/details/app-service/) para obter mais detalhes.  
<sup>4</sup> mais permitido mediante pedido.  
<sup>5</sup> o limite de armazenamento é o tamanho total do conteúdo em todas as aplicações no mesmo plano de serviço de aplicações.  
<sup>6</sup> estes recursos estão restritos pelo recursos físicos nas instâncias de dedicado (o tamanho da instância e o número de instâncias).  
<sup>7</sup> se dimensionar uma aplicação no escalão básico para duas instâncias, tem 350 conexões simultâneas para cada uma das duas instâncias.  
<sup>8</sup> execute executáveis personalizados e/ou scripts a pedido, com base numa agenda, ou continuamente como tarefa em segundo plano no seu serviço de aplicações instância. Sempre Ativo é necessário para a execução contínua do WebJobs. É necessário o Azure Scheduler, Gratuito ou Padrão, para WebJobs agendados. Não tem qualquer limite predefinido no número de WebJobs que podem ser executadas numa instância de serviço de aplicações, mas há limites práticos que dependem de que o código do aplicativo é a tentando fazer.  
<sup>9</sup> SKUs isolados do serviço de aplicações têm a capacidade de carga interno (ILB) com balanceamento com o Azure Load Balancer, que significa que nenhuma conectividade pública da internet. Como resultado, algumas funcionalidades de um Serviço de Aplicações Isolado ILB têm de ser utilizadas a partir de computadores com acesso direto ao ponto final da rede ILB.  
<sup>10</sup> limite de quota do certificado de serviço de aplicações por subscrição pode ser aumentado através de um pedido de suporte para um limite máximo de 200.  