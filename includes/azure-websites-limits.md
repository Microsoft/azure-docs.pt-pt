---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: 80d295d017b11d86df7a3fe4c14afc7a5665cd96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612941"
---
| Recurso | Gratuito | Partilhado | Básica | Standard | Premium (v1-v3) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos web, móveis ou API](https://azure.microsoft.com/services/app-service/) por [plano de Serviço de Aplicações Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano de Serviço de Aplicações](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância computacional |Partilhado |Partilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escala (instâncias](../articles/app-service/manage-scale-up.md) máximas) |1 compartilhado |1 compartilhado |3<sup>dedicados 3</sup> |10<sup>dedicados 3</sup> | 20 dedicados à v1 e v2; 30 dedicados à V3. <sup>3</sup>|100<sup>dedicados 4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Para mais de 250 GB, apresente um pedido de apoio. |1 TB<sup>5</sup> <br/><br/> A quota de armazenamento disponível é de 999 GB. |
| Tempo de CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a>|
| Hora do CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pagar a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |
| Memória (1 hora) |1.024 MB por plano de Serviço de Aplicações |1,024 MB por app |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Taxas [ilimitadas](https://azure.microsoft.com/pricing/details/data-transfers/) de transferência de dados aplicam-se |Taxas [ilimitadas](https://azure.microsoft.com/pricing/details/data-transfers/) de transferência de dados aplicam-se |Taxas [ilimitadas](https://azure.microsoft.com/pricing/details/data-transfers/) de transferência de dados aplicam-se |Taxas [ilimitadas](https://azure.microsoft.com/pricing/details/data-transfers/) de transferência de dados aplicam-se |Taxas [ilimitadas](https://azure.microsoft.com/pricing/details/data-transfers/) de transferência de dados aplicam-se |
| Arquitetura da aplicação |32 bits |32 bits |32 bit/64-bit |32 bit/64-bit |32 bit/64-bit |32 bit/64-bit |
| Tomadas web por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Conexões IP de saída por exemplo | 600 | 600 | Depende do tamanho do exemplo<sup>8</sup> | Depende do tamanho do exemplo<sup>8</sup> | Depende do tamanho do exemplo<sup>8</sup> | 16 000 |
| Conexões [de depuração](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicação |1 |1 |1 |5 |5 |5 |
| Certificados de Serviço de Aplicações por subscrição<sup>9</sup>| Não suportado | Não suportado |10 |10 |10 |10 |
| Domínios personalizados por app</a> |0 (apenas azurewebsites.net subdomínio)|500 |500 |500 |500 |500 |
| Suporte [SSL](../articles/app-service/configure-ssl-certificate.md) de domínio personalizado |Não suportado, certificado wildcard para \* .azurewebsites.net disponível por padrão|Não suportado, certificado wildcard para \* .azurewebsites.net disponível por padrão|Conexões SNI SSL ilimitadas |Conexões SNI SSL ilimitadas e 1 IP SSL incluídas |Conexões SNI SSL ilimitadas e 1 IP SSL incluídas | Conexões SNI SSL ilimitadas e 1 IP SSL incluídas|
| Ligações híbridas | | | 5 por plano | 25 por plano | 200 por app | 200 por app |
| [Integração da Rede Virtual](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Pontos finais privados](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 por app  |    |
| Equilibrador de carga integrado | |X |X |X |X |X<sup>10</sup> |
| [Restrições de acesso](../articles/app-service/networking-features.md#access-restrictions) | 512 regras por app | 512 regras por app | 512 regras por app | 512 regras por app | 512 regras por app | 512 regras por app |
| [Sempre ligado](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backups programados](../articles/app-service/manage-backup.md) | | | | Backups programados a cada 2 horas, no máximo 12 backups por dia (manual + programado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + programado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + programado) |
| [Dimensionamento Automático](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitorização de ponto final](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de encenação](../articles/app-service/deploy-staging-slots.md) por app| | | |5 |20 |20 |
| [Testes em Produção](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Registos de Diagnóstico](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Autenticação e Autorização](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [Certificados Geridos do Serviço de Aplicações (Visualização Pública)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95%|99,95%|99,95%|99,95%|

<sup>1</sup> Aplicações e quotas de armazenamento são por plano de Serviço de Aplicação, salvo indicação em contrário.

<sup>2</sup> O número real de aplicações que pode hospedar nestas máquinas depende da atividade das aplicações, do tamanho das instâncias da máquina e da utilização correspondente dos recursos.

<sup>3</sup> Casos dedicados podem ser de diferentes tamanhos. Para mais informações, consulte [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)

<sup>4</sup> São permitidos mais mediante pedido.

<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todas as aplicações no mesmo plano de serviço da App. O tamanho total do conteúdo de todas as aplicações em todos os planos de serviço da App num único grupo de recursos e região não pode exceder 500 GB. A quota do sistema de ficheiros para aplicações hospedadas no Serviço de Aplicações é determinada pelo agregado de planos de Serviço de Aplicações criados numa região e grupo de recursos.

<sup>6</sup> Estes recursos são limitados por recursos físicos nas instâncias dedicadas (a dimensão do caso e o número de ocorrências).

<sup>7</sup> Se escalar uma aplicação no nível Básico para duas instâncias, tem 350 ligações simultâneas para cada uma das duas instâncias. Para o nível standard e acima, não existem limites teóricos para tomadas web, mas outros fatores podem limitar o número de tomadas web. Por exemplo, os pedidos simultâneos máximos permitidos (definidos `maxConcurrentRequestsPerCpu` por) são: 7.500 por pequeno VM, 15.000 por VM médio (7.500 x 2 núcleos) e 75.000 por VM grande (18.750 x 4 núcleos).

<sup>8</sup> As ligações IP máximas são por instância e dependem do tamanho do caso: 1.920 por instância B1/S1/P1V3, 3.968 por instância B2/S2/P2V3, 8.064 por instância B3/S3/P3V3.

<sup>9</sup> O limite de quota de quota do Certificado de Serviço de Aplicação por subscrição pode ser aumentado através de um pedido de apoio para um limite máximo de 200.

<sup>10</sup> App Service SKUs isolados podem ser equilibrados internamente (ILB) com Azure Load Balancer, pelo que não existe conectividade pública a partir da internet. Como resultado, algumas funcionalidades de um Serviço de Aplicações Isolado ILB devem ser utilizadas a partir de máquinas que tenham acesso direto ao ponto final da rede ILB.

<sup>11</sup> Execute os executáveis personalizados e/ou scripts a pedido, num horário ou continuamente como uma tarefa de fundo dentro da sua instância do Serviço de Aplicações. Always On é necessário para a execução contínua do WebJobs. Não há um limite predefinido no número de WebJobs que podem ser executados numa instância do Serviço de Aplicações. Existem limites práticos que dependem do que o código de aplicação está a tentar fazer.

<sup>12</sup> domínios nus não são suportados. Apenas a emissão de certificados padrão (os certificados wildcard não estão disponíveis). Limitado a apenas um certificado gratuito por domínio personalizado.
