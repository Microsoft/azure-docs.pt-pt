---
title: Vídeo indexado, aplicação de SQL de SaaS do Azure | Documentos da Microsoft
description: Este artigo indexa vários pontos de tempo em nossa minutos 81 vídeos sobre design de aplicativos de inquilinos de BD de SaaS, da conferência Ignite mantida 11 de Outubro de 2017. Pode avançar diretamente para a parte que lhe interessa. Pelo menos 3 padrões são descritos. Funcionalidades do Azure que simplificam o desenvolvimento e gestão são descritas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: bbe220780a3c21e7bfb15d0568904af4ed47f765
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487284"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Vídeo indexado e anotada para a aplicação de SaaS de multi-inquilino com a base de dados do Azure SQL

Este artigo é um índice anotado para as localizações de tempo de um minuto 81 vídeo sobre os modelos de inquilinos de SaaS ou os padrões. Este artigo permite-lhe ignorar para trás ou no vídeo para o qual a parte a partir de agora lhe interessa. O vídeo explica as opções de design principais para uma aplicação de base de dados do multi-inquilino na base de dados do Azure SQL. O vídeo inclui demonstrações, orientações passo a passo do código de gestão e às vezes mais detalhadamente informado por experiência que pode ser na nossa documentação escrita.

O vídeo amplifica as informações na nossa documentação escrita, encontrada em: 
- *Conceitual:* [Padrões de inquilinos de bases de dados de SaaS de multi-inquilino][saas-concept-design-patterns-563e]
- *Tutoriais:* [A aplicação Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

O vídeo e os artigos descrevem as muitas fases de criação de uma aplicação multi-inquilino no banco de dados do Azure SQL na cloud. Recursos especiais de base de dados SQL do Azure tornam mais fácil desenvolver e implementar aplicações multi-inquilino que são ambos mais fácil de gerenciar e fiável com bom desempenho.

Iremos atualizar regularmente nossa documentação escrita. O vídeo não é editado ou atualizado, por isso, eventualmente, mais de seus detalhes podem tornar-se desatualizados.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sequência de 38 capturas de ecrã indexada de tempo

Esta secção indexa a localização do tempo de 38 discussões durante o minuto 81 vídeo. Cada índice de tempo está anotado com uma captura de ecrã do vídeo e, às vezes, com informações adicionais.

Cada índice de tempo está no formato *h:mm*. Por exemplo, o segundo indexados a localização de tempo, o nome **objetivos de sessão**, começa no local de tempo aproximado **0:03 minutos e 11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compact links para localizações de tempo do vídeo indexado

Os títulos seguintes são links para suas correspondentes anotadas secções mais adiante neste artigo:

- [1. **(Start)**  Slide de boas-vindas, 0:00:03](#anchor-image-wtip-min00001)
- [2. Objetivos de sessão, 0:03 minutos e 11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. Aplicação web do multi-inquilino, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formulário da web de aplicação em ação, 0:05:55](#anchor-image-wtip-min00555)
- [6. O custo (escala, isolamento, recuperação), 0 por inquilino: 09:31](#anchor-image-wtip-min00931)
- [7. Modelos de base de dados para o multi-inquilino: prós e contras, 0:11:59](#anchor-image-wtip-min01159)
- [8. Modelo híbrida combina benefícios do MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Multi-inquilino de inquilino único vs: prós e contras, 0:16:44](#anchor-image-wtip-min01644)
- [10. Os conjuntos são económicos para cargas de trabalho imprevisíveis, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demonstração de base de dados por inquilino e híbrida ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live formulário de aplicação que mostra Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. O MYOB e não um DBA no campo de visão, 0:28 minutos e 54](#anchor-image-wtip-min02854)
- [14. Exemplo de utilização do conjunto elástico MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Aprendendo com o MYOB e outros ISVs, 0:31:36](#anchor-image-wtip-min03136)
- [16. Padrões de compõem em cenário E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. Aplicação de SaaS de multi-inquilino híbrida canônico, 0:47:33](#anchor-image-wtip-min04733)
- [18. Aplicação de exemplo do Wingtip SaaS, 0:48:10](#anchor-image-wtip-min04810)
- [19. Cenários e padrões exploradas nos tutoriais, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demonstração de tutoriais e repositório do GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. Repositório do GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Explorar os padrões, 0:56:20](#anchor-image-wtip-min05620)
- [23. Aprovisionar inquilinos e integração, 0:57:44](#anchor-image-wtip-min05744)
- [24. Aprovisionar inquilinos e ligação de aplicação, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demonstração da gestão de scripts de aprovisionamento de um único inquilino, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell para aprovisionar e catalogar, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Gerir cargas de trabalho imprevisível do inquilino, 1:04:36](#anchor-image-wtip-min10436)
- [29. Conjunto elástico de monitorização, 1:06:39](#anchor-image-wtip-min10639)
- [30. Geração de carga e monitorização de desempenho, 1:09:42](#anchor-image-wtip-min10942)
- [31. Gestão de esquemas em escala, 1:10:33](#anchor-image-wtip-min11033)
- [32. Consultas distribuídas entre bases de dados do inquilino, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demonstração da geração de pedido de suporte, 1:12:32](#anchor-image-wtip-min11232)
- [34. Análise ad hoc do SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrair dados de inquilino para o armazém de dados do SQL, 1:16:32](#anchor-image-wtip-min11632)
- [36. Gráfico de distribuição de venda diariamente, 1:16:48](#anchor-image-wtip-min11648)
- [37. Empacotar e chamar a ação, 1:19:52](#anchor-image-wtip-min11952)
- [38. Recursos para obter mais informações, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Localizações de tempo de índice anotado no vídeo

Clicar em qualquer imagem de captura de ecrã, leva-o para a localização de hora exata a que o vídeo.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)*  Slide de boas-vindas, 0:00:01

*Aprendendo com o MYOB: Padrões de design para aplicações de SaaS no Azure SQL Database - BRK3120*

[![Slide de boas-vindos][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Título: Aprendendo com o MYOB: Padrões de design para aplicações SaaS na base de dados do Azure SQL
- Bill.Gibson@microsoft.com
- Gestor de programa principal, base de dados SQL do Azure
- Sessão do Microsoft Ignite BRK3120, Orlando, FL EUA, / 11 de Outubro de 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Objetivos de sessão, 0:01:53
[![Objetivos de sessão][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Modelos de alternativos para aplicações multi-inquilino, com os prós e contras.
- Padrões de SaaS para reduzir os custos de desenvolvimento, gestão e recursos.
- Uma aplicação de exemplo + scripts.
- Funcionalidades de PaaS + padrões SaaS fazem da base de dados SQL uma plataforma de dados altamente dimensionável e económica para SaaS de multi-inquilino.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Aplicação web do multi-inquilino, 0: GMT+05:00
[![Aplicação SaaS Wingtip: Aplicação web do multi-inquilino][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formulário da web de aplicação em ação, 0:05:39
[![Formulário da web de aplicação em ação][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. O custo (escala, isolamento, recuperação), 0 por inquilino: 06:58
[![Por inquilino custo, escala, isolamento, recuperação][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Modelos de base de dados para o multi-inquilino: prós e contras, 0:09:52
[![Modelos de base de dados para o multi-inquilino: prós e contras][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Modelo híbrida combina benefícios do MT/ST, 0:12:29
[![Modelo híbrida combina benefícios do MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Multi-inquilino de inquilino único vs: prós e contras, 0:13 minutos e 11
[![Multi-inquilino de inquilino único vs: prós e contras][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Os conjuntos são económicos para cargas de trabalho imprevisíveis, 0:17:49
[![Os conjuntos são económicos para cargas de trabalho imprevisíveis][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demonstração de base de dados por inquilino e híbrida ST/MT, 0:19:59
[![Demonstração de base de dados por inquilino e híbrida ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live formulário de aplicação que mostra Dojo, 0:20:10
[![Formulário de aplicação em direto que mostra Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. O MYOB e não um DBA no campo de visão, 0:25:06
[![O MYOB e não um DBA vistos][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Exemplo de utilização do conjunto elástico MYOB, 0:29:30
[![Exemplo de utilização do conjunto elástico MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Aprendendo com o MYOB e outros ISVs, 0:31:25
[![Aprendendo com o MYOB e outros ISVs][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Padrões de compõem em cenário E2E SaaS, 0:31:42
[![Padrões de compõem em cenário SaaS de E2E][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Aplicação de SaaS de multi-inquilino híbrida canônico, 0:46:04
[![Aplicação de SaaS multi-inquilino híbrido Canonical][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Aplicação de exemplo do Wingtip SaaS, 0:48:01
[![Aplicação de exemplo do Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Cenários e padrões exploradas nos tutoriais, 0:49:00
[![Cenários e padrões exploradas nos tutoriais][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demonstração de tutoriais e repositório do GitHub, 0:50:12
[![Tutoriais de demonstração e o repositório do GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Repositório do GitHub Microsoft/WingtipSaaS, 0:50:32
[![Repositório do GitHub Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Explorar os padrões, 0:56:15
[![Explorar os padrões de][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Aprovisionar inquilinos e integração, 0:56:19
[![Aprovisionamento e integração de inquilinos][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Aprovisionar inquilinos e ligação de aplicação, 0:57:52
[![Aprovisionar inquilinos e ligação de aplicação][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demonstração da gestão de scripts de aprovisionamento de um único inquilino, 0:59:36
[![Demonstração de scripts de gerenciamento de aprovisionamento de um único inquilino][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell para aprovisionar e catalogar, 0:59:56
[![PowerShell para aprovisionar e catalogar][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Gerir cargas de trabalho imprevisível do inquilino, 1:03:34
[![Gerir cargas de trabalho imprevisível do inquilino][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Conjunto elástico de monitorização, 1:06:32
[![Monitorização do conjunto elástico][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Geração de carga e monitorização de desempenho, 1:09:37
[![Monitorização de desempenho e de geração de carga][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Gestão de esquemas em escala, 1:09:40
[![Gestão de esquemas em escala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Consultas distribuídas entre bases de dados do inquilino, 1:11:18
[![Consultas distribuídas entre bases de dados do inquilino][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demonstração da geração de pedido de suporte, 1:12:28
[![Demonstração da geração de pedido de suporte][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Análise ad hoc do SSMS, 1:12:35
[![Análise do SSMS ad hoc][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrair dados de inquilino para o armazém de dados do SQL, 1:15:46
[![Extrair dados de inquilino para o SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Gráfico de distribuição de venda diariamente, 1:16:38
[![Gráfico de distribuição de venda diária][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Empacotar e chamar a ação, 1:17:43
[![Empacotar e plano de ação][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Recursos para obter mais informações, 1:20:35
[![Recursos para obter mais informações][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Mensagem de blogue, 22 de Maio de 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Conceitual:* [Padrões de inquilinos de bases de dados de SaaS de multi-inquilino][saas-concept-design-patterns-563e]

- *Tutoriais:* [A aplicação Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

- Repositórios do GitHub para tipos de aplicação Wingtip Tickets SaaS inquilinos:
    - [Repositório do GitHub para - modelo de aplicativo autônomo][github-wingtip-standaloneapp].
    - [Repositório do GitHub para - modelo do DB por inquilino][github-wingtip-dbpertenant].
    - [Repositório do GitHub para - modelo do multi-inquilino DB][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Passos Seguintes

- [Primeiro artigo de tutorial][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slide de boas-vindos"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Objetivos de sessão."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplicação SaaS Wingtip: Aplicação web do multi-inquilino"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formulário da web de aplicação em ação"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Por inquilino custo, escala, isolamento, recuperação"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modelos de base de dados para o multi-inquilino: prós e contras"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Modelo híbrida combina benefícios do MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Multi-inquilino de inquilino único vs: prós e contras"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Os conjuntos são económicos para cargas de trabalho imprevisíveis"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demonstração de base de dados por inquilino e híbrida ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulário de aplicação em direto que mostra Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "O MYOB e não um DBA vistos"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Exemplo de utilização do conjunto elástico MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Aprendendo com o MYOB e outros ISVs"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Padrões de compõem em cenário SaaS de E2E"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Aplicação de SaaS multi-inquilino híbrido Canonical"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Aplicação de exemplo do Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Cenários e padrões exploradas nos tutoriais"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demonstração de tutoriais e repositório do GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repositório do GitHub Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Explorar os padrões de"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Aprovisionamento e integração de inquilinos"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Aprovisionar inquilinos e ligação de aplicação"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demonstração de scripts de gerenciamento de aprovisionamento de um único inquilino"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell para aprovisionar e catalogar"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Gerir cargas de trabalho imprevisível do inquilino"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorização do conjunto elástico"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Monitorização de desempenho e de geração de carga"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Gestão de esquemas em escala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Consultas distribuídas entre bases de dados do inquilino"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demonstração da geração de pedido de suporte"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Análise do SSMS ad hoc"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrair dados de inquilino para o SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Gráfico de distribuição de venda diária"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Empacotar e plano de ação"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Recursos para obter mais informações"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

