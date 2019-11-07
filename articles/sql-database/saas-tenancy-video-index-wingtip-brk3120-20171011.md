---
title: 'Vídeo indexado, aplicativo SQL SaaS do Azure '
description: Este artigo indexa vários pontos de tempo em nosso vídeo de 81 minutos sobre o design do aplicativo de aluguel de banco de informações do SaaS, da conferência Ignite mantida em 11 de outubro de 2017. Você pode pular para a parte que lhe interessa. Pelo menos 3 padrões são descritos. Os recursos do Azure que simplificam o desenvolvimento e o gerenciamento são descritos.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 3d9b9c5af1994e2d0b3fd8f720def06489f3669d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691907"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Vídeo indexado e anotado para o aplicativo SaaS multilocatário usando o banco de dados SQL do Azure

Este artigo é um índice anotado sobre os locais de tempo de um vídeo de 81 minutos sobre modelos ou padrões de locação de SaaS. Este artigo permite que você pule para trás ou para frente no vídeo para qual parte lhe interessa. O vídeo explica as principais opções de design para um aplicativo de banco de dados multilocatário no banco de dados SQL do Azure. O vídeo inclui demonstrações, orientações de código de gerenciamento e, às vezes, mais detalhes informados por experiência do que pode estar em nossa documentação escrita.

O vídeo amplifica as informações em nossa documentação escrita, encontradas em: 
- *Conceitual:* [padrões de locação de banco de dados SaaS multilocatário][saas-concept-design-patterns-563e]
- *Tutoriais:* [o aplicativo Wingtip tickets SaaS][saas-how-welcome-wingtip-app-679t]

O vídeo e os artigos descrevem as muitas fases da criação de um aplicativo multilocatário no banco de dados SQL do Azure na nuvem. Os recursos especiais do banco de dados SQL do Azure facilitam o desenvolvimento e a implementação de aplicativos multilocatários que são mais fáceis de gerenciar e com desempenho confiável.

Atualizamos nossa documentação escrita rotineiramente. O vídeo não é editado ou atualizado, portanto, eventualmente, mais detalhes podem se tornar desatualizados.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sequência de 38 capturas de tela indexadas por tempo

Esta seção indexa o local de hora de 38 discussões em todo o vídeo de 81 minutos. Cada índice de tempo é anotado com uma captura de tela do vídeo e, às vezes, com informações adicionais.

Cada índice de tempo está no formato *h:mm: SS*. Por exemplo, o segundo local de tempo indexado, rotulado **objetivos de sessão**, começa no local de tempo aproximado de **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Links de compactação para locais de tempo indexados em vídeo

Os títulos a seguir são links para suas seções anotadas correspondentes posteriormente neste artigo:

- [1. **(início)** slide de boas-vindas, 0:00:03](#anchor-image-wtip-min00001)
- [2. objetivos da sessão, 0:03:11](#anchor-image-wtip-min00311)
- [3. agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. aplicativo Web multilocatário, 0:05:05](#anchor-image-wtip-min00505)
- [5. formulário da Web do aplicativo em ação, 0:05:55](#anchor-image-wtip-min00555)
- [6. custo por locatário (escala, isolamento, recuperação), 0:09:31](#anchor-image-wtip-min00931)
- [7. modelos de banco de dados para multilocatário: prós e contras, 0:11:59](#anchor-image-wtip-min01159)
- [8. o modelo híbrido combina os benefícios de MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. threads de locatário único vs multilocatário: prós e contras, 0:16:44](#anchor-image-wtip-min01644)
- [10. os pools são econômicos para cargas de trabalho imprevisíveis, 0:19:36](#anchor-image-wtip-min01936)
- [11. demonstração de banco de dados por locatário e ST/MT híbrido, 0:20:08](#anchor-image-wtip-min02008)
- [12. formulário de aplicativo ao vivo mostrando dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB e não um DBA em vista, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB exemplo de uso de pool elástico, 0:29:40](#anchor-image-wtip-min02940)
- [15. aprendendo com MYOB e outros ISVs, 0:31:36](#anchor-image-wtip-min03136)
- [16. padrões compõem o cenário de SaaS E2E, 0:43:15](#anchor-image-wtip-min04315)
- [17. aplicativo SaaS de multilocatário híbrido canônico, 0:47:33](#anchor-image-wtip-min04733)
- [aplicativo de exemplo SaaS de 18. Wingtip, 0:48:10](#anchor-image-wtip-min04810)
- [19. cenários e padrões explorados nos tutoriais, 0:49:10](#anchor-image-wtip-min04910)
- [20. demonstração de tutoriais e repositório GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. repositório GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. explorando os padrões, 0:56:20](#anchor-image-wtip-min05620)
- [23. Provisionando locatários e integração, 0:57:44](#anchor-image-wtip-min05744)
- [24. Provisionando locatários e conexão de aplicativo, 0:58:58](#anchor-image-wtip-min05858)
- [25. demonstração de scripts de gerenciamento provisionando um único locatário, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell para provisionar e catalogar, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * de TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Gerenciando cargas de trabalho de locatário imprevisíveis, 1:04:36](#anchor-image-wtip-min10436)
- [29. monitoramento de pool elástico, 1:06:39](#anchor-image-wtip-min10639)
- [30. geração de carga e monitoramento de desempenho, 1:09:42](#anchor-image-wtip-min10942)
- [31. gerenciamento de esquema em escala, 1:10:33](#anchor-image-wtip-min11033)
- [32. consulta distribuída em bancos de dados de locatário, 1:12:21](#anchor-image-wtip-min11221)
- [33. demonstração da geração de tíquete, 1:12:32](#anchor-image-wtip-min11232)
- [34. análise adhoc do SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. extrair dados de locatário no SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. grafo de distribuição de venda diária, 1:16:48](#anchor-image-wtip-min11648)
- [37. conclusão e plano de ação, 1:19:52](#anchor-image-wtip-min11952)
- [38. recursos para obter mais informações, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Locais de tempo de índice anotado no vídeo

Clicar em qualquer imagem de captura de tela leva você até o local de tempo exato no vídeo.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(início)* slide de boas-vindas, 0:00:01

*Aprendendo com o MYOB: padrões de design para aplicativos SaaS no banco de dados SQL do Azure-BRK3120*

[![Slide de boas-vindas][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Título: aprendendo com MYOB: padrões de design para aplicativos SaaS no banco de dados SQL do Azure
- Bill.Gibson@microsoft.com
- Gerente de programa principal, banco de dados SQL do Azure
- Microsoft Ignite Session BRK3120, Orlando, FL USA, outubro/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. objetivos da sessão, 0:01:53
[![Objetivos da sessão][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Modelos alternativos para aplicativos multilocatários, com prós e contras.
- Padrões de SaaS para reduzir os custos de desenvolvimento, gerenciamento e recursos.
- Um aplicativo de exemplo + scripts.
- Os recursos de PaaS + padrões de SaaS tornam o banco de dados SQL uma plataforma altamente escalonável e econômica para o SaaS de vários locatários.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. agenda, 0:04:09
[![Telefônica][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. aplicativo Web multilocatário, 0:05:00
[![Aplicativo Wingtip SaaS: aplicativo Web multilocatário][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. formulário da Web do aplicativo em ação, 0:05:39
[![Formulário da Web do aplicativo em ação][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. custo por locatário (escala, isolamento, recuperação), 0:06:58
[![Custo por locatário, escala, isolamento, recuperação][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. modelos de banco de dados para multilocatário: prós e contras, 0:09:52
[![Modelos de banco de dados para multilocatário: prós e contras][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. o modelo híbrido combina os benefícios de MT/ST, 0:12:29
[![O modelo híbrido combina os benefícios do MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. threads de locatário único vs multilocatário: prós e contras, 0:13:11
[![Multilocatário vs de locatário único: prós e contras][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. os pools são econômicos para cargas de trabalho imprevisíveis, 0:17:49
[![Os pools são econômicos para cargas de trabalho imprevisíveis][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. demonstração de banco de dados por locatário e ST/MT híbrido, 0:19:59
[![Demonstração de banco de dados por locatário e ST/MT híbrido][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. formulário de aplicativo ao vivo mostrando dojo, 0:20:10
[![Formulário de aplicativo ao vivo mostrando dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB e não um DBA em vista, 0:25:06
[![MYOB e não um DBA em vista][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB exemplo de uso de pool elástico, 0:29:30
[![Exemplo de uso de pool elástico MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. aprendendo com MYOB e outros ISVs, 0:31:25
[![Aprendendo com o MYOB e outros ISVs][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. padrões compõem o cenário de SaaS E2E, 0:31:42
[![Padrões compõem no cenário SaaS E2E][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. aplicativo SaaS de multilocatário híbrido canônico, 0:46:04
[![Aplicativo SaaS de multilocatários híbridos canônicos][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>aplicativo de exemplo SaaS de 18. Wingtip, 0:48:01
[![Aplicativo de exemplo SaaS Wingtip][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. cenários e padrões explorados nos tutoriais, 0:49:00
[![Cenários e padrões explorados nos tutoriais][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. demonstração de tutoriais e repositório GitHub, 0:50:12
[![Tutoriais de demonstração e repositório GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. repositório GitHub Microsoft/WingtipSaaS, 0:50:32
[![Repositório GitHub Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. explorando os padrões, 0:56:15
[![Explorando os padrões][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Provisionando locatários e integração, 0:56:19
[![Provisionamento de locatários e integração][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Provisionando locatários e conexão de aplicativo, 0:57:52
[![Provisionamento de locatários e conexão de aplicativo][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. demonstração de scripts de gerenciamento provisionando um único locatário, 0:59:36
[![Demonstração de scripts de gerenciamento provisionando um único locatário][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell para provisionar e catalogar, 0:59:56
[![PowerShell para provisionar e catalogar][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * de TenantsExtended, 1:03:25
[![T-SQL SELECT * de TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Gerenciando cargas de trabalho de locatário imprevisíveis, 1:03:34
[![Gerenciando cargas de trabalho de locatário imprevisíveis][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. monitoramento de pool elástico, 1:06:32
[![Monitoramento de pool elástico][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. geração de carga e monitoramento de desempenho, 1:09:37
[![Geração de carga e monitoramento de desempenho][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. gerenciamento de esquema em escala, 1:09:40
[![Gerenciamento de esquema em escala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. consulta distribuída em bancos de dados de locatário, 1:11:18
[![Consulta distribuída entre bancos de dados de locatário][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. demonstração da geração de tíquete, 1:12:28
[![Demonstração da geração de tíquetes][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. análise adhoc do SSMS, 1:12:35
[![Análise ad hoc do SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. extrair dados de locatário no SQL DW, 1:15:46
[![Extrair dados de locatário para o SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. grafo de distribuição de venda diária, 1:16:38
[![Grafo de distribuição de venda diária][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. conclusão e plano de ação, 1:17:43
[![Conclusão e plano de ação][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. recursos para obter mais informações, 1:20:35
[![Recursos para obter mais informações][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Postagem no blog, 22 de maio de 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Conceitual:* [padrões de locação de banco de dados SaaS multilocatário][saas-concept-design-patterns-563e]

- *Tutoriais:* [o aplicativo Wingtip tickets SaaS][saas-how-welcome-wingtip-app-679t]

- Repositórios do GitHub para tipos do aplicativo de locação do Wingtip tickets SaaS:
    - [Repositório GitHub para o modelo de aplicativo autônomo][github-wingtip-standaloneapp].
    - [Repositório GitHub para-DB por modelo de locatário][github-wingtip-dbpertenant].
    - [Repositório do GitHub para o modelo de banco de BD de vários locatários][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Passos seguintes

- [Primeiro artigo do tutorial][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slide de boas-vindas"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Objetivos da sessão."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Telefônica."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplicativo Wingtip SaaS: aplicativo Web multilocatário"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formulário da Web do aplicativo em ação"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Custo por locatário, escala, isolamento, recuperação"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modelos de banco de dados para multilocatário: prós e contras"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "O modelo híbrido combina os benefícios do MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Multilocatário vs de locatário único: prós e contras"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Os pools são econômicos para cargas de trabalho imprevisíveis"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demonstração de banco de dados por locatário e ST/MT híbrido"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulário de aplicativo ao vivo mostrando dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB e não um DBA em vista"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Exemplo de uso de pool elástico MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Aprendendo com o MYOB e outros ISVs"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Padrões compõem no cenário SaaS E2E"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Aplicativo SaaS de multilocatários híbridos canônicos"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Aplicativo de exemplo SaaS Wingtip"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Cenários e padrões explorados nos tutoriais"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demonstração de tutoriais e repositório GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repositório GitHub Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Explorando os padrões"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Provisionamento de locatários e integração"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Provisionamento de locatários e conexão de aplicativo"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demonstração de scripts de gerenciamento provisionando um único locatário"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell para provisionar e catalogar"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * de TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Gerenciando cargas de trabalho de locatário imprevisíveis"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitoramento de pool elástico"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Geração de carga e monitoramento de desempenho"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Gerenciamento de esquema em escala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Consulta distribuída entre bancos de dados de locatário"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demonstração da geração de tíquetes"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Análise ad hoc do SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrair dados de locatário para o SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Grafo de distribuição de venda diária"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Conclusão e plano de ação"

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

