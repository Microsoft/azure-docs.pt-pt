---
title: Recomendações de máquina virtual na central de segurança do Azure | Microsoft Docs
description: Este documento explica as recomendações da central de segurança do Azure sobre como ajudar a proteger suas máquinas virtuais e computadores e seus aplicativos Web e ambientes de serviço de aplicativo.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: 798e027ca611905766b1fb8bcdb89cba4aeaf9b2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531334"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Entender as recomendações de recursos da central de segurança do Azure


## <a name="recommendations"></a>Recomendações
Use as tabelas abaixo como referência para ajudá-lo a entender as recomendações de computação e serviços de aplicativos disponíveis e o que cada um faz se você o aplicar.

### <a name="computers"></a>Computadores
| Recomendação | Descrição |
| --- | --- |
| Ativar a recolha de dados de subscrições|Recomenda-se que ative a recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições. |
| Ativar a encriptação para a Conta de Armazenamento do Azure| Recomenda que você habilite o Azure Criptografia do Serviço de Armazenamento para dados em repouso. O Criptografia do Serviço de Armazenamento (SSE) funciona criptografando os dados quando eles são gravados no armazenamento do Azure e são descriptografados antes da recuperação. No momento, o SSE está disponível apenas para o serviço blob do Azure e pode ser usado para BLOBs de blocos, blobs de páginas e blobs de acréscimo. Para saber mais, confira [criptografia do serviço de armazenamento para dados em repouso](../storage/common/storage-service-encryption.md).</br>Só há suporte para SSE em contas de armazenamento do Resource Manager. Atualmente, não há suporte para contas de armazenamento clássicas. Para entender os modelos de implantação clássico e do Resource Manager, consulte modelos de implantação do Azure. |
| Remediar configurações de segurança|Recomenda que você Alinhe as configurações do sistema operacional com as regras de configuração de segurança recomendadas, por exemplo, não permitir que as senhas sejam salvas. |
| Aplicar atualizações do Sistema |Recomenda-se que implemente o sistema de segurança em falta e atualizações críticas nas VMs. |
| Aplicar um controlo de acesso à rede Just-In-Time| Recomenda que você aplique o acesso just in time à VM. O recurso just in time está em visualização e disponível na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança. |
| Reiniciar após as atualizações do sistema|Recomenda-se que reinicie uma VM para concluir o processo de aplicar atualizações do sistema. |
| Instalar Endpoint Protection|Recomenda-se que aprovisione programas de antimalware em VMs (apenas VMs do Windows). |
| Ativar Agente VM |Permite-lhe ver as VMs que necessitam do Agente de VM. O Agente de VM tem de estar instalado em VMs para aprovisionar a análise de patch, a análise de linha de base e os programas de antimalware. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| Aplicar encriptação de discos|Recomenda-se que encripte os discos da VM com o Azure Disk Encryption (VMs Windows e Linux). A encriptação é recomendada para o SO e os volumes de dados na sua VM. |
| Atualizar a versão do SO|Recomenda que você atualize a versão do sistema operacional (SO) do seu serviço de nuvem para a versão mais recente disponível para sua família de sistemas operacionais.  Para saber mais sobre os serviços de nuvem, consulte a visão geral dos serviços de nuvem. |
| Avaliação de vulnerabilidades não instalada|Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Corrigir vulnerabilidades) |Permite-lhe ver as vulnerabilidades de sistema e de aplicações detetadas pela solução de avaliação de vulnerabilidade instalada na sua VM. |

### Serviços de aplicativos<a name="app-services"></a>
| Recomendação | Descrição |
| --- | --- |
| O serviço de aplicativo só deve ser acessível via HTTPS | Recomenda que você limite o acesso do serviço de aplicativo somente por HTTPS. |
| Web Sockets devem ser desativados para a aplicação Web| Recomenda que você revise cuidadosamente o uso de Web Sockets em aplicativos Web.  O protocolo de Web Sockets está vulnerável a diferentes tipos de ameaças de segurança. |
| Utilizar domínios personalizados para a sua aplicação Web | Recomenda que você use domínios personalizados para proteger um aplicativo Web contra ataques comuns, como phishing e outros ataques relacionados a DNS. |
| Configurar restrições de IP para a aplicação Web | Recomenda que você defina uma lista de endereços IP que têm permissão para acessar seu aplicativo.  Utilização de restrições de IP de protege as aplicações web contra ataques comuns. |
| Não permitir que todos os ('* ') recursos para aceder à sua aplicação | Recomenda que você não defina o parâmetro WEBSITE_LOAD_CERTIFICATES como ' *'. Definir o parâmetro como '* ' significa que todos os certificados serão carregados no repositório de certificados pessoal de seus aplicativos Web.  Isto pode levar ao abuso do princípio de privilégio mínimo porque é improvável que o site necessita de acesso a todos os certificados no tempo de execução. |
| O CORS não deve permitir que todos os recursos acessem seu aplicativo | Recomenda que você permita que apenas os domínios necessários interajam com seu aplicativo Web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação web. |
| Utilize a versão mais recente do .NET Framework suportada para a aplicação Web | Recomenda que você use a versão mais recente do .NET Framework para as classes de segurança mais recentes. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| Utilizar a versão de Java mais recente suportada para a aplicação Web | Recomenda que você use a versão mais recente do Java para as classes de segurança mais recentes. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| Utilizar a versão PHP mais recente suportada para a aplicação Web | Recomenda que você use a versão mais recente do PHP para as classes de segurança mais recentes. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md) |Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web. Uma recomendação WAF é mostrada para qualquer IP voltado para o público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associado com portas da Web de entrada abertas (80.443).</br></br>A central de segurança recomenda que você provisione um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos Web em máquinas virtuais e em Ambiente do Serviço de Aplicativo. Um Ambiente do Serviço de Aplicativo (ASE) é uma opção de plano de serviço [Premium](https://azure.microsoft.com/pricing/details/app-service/) do serviço de Azure app que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos de serviço do Azure app. Para saber mais sobre o ASE, consulte a [documentação do ambiente do serviço de aplicativo](../app-service/environment/intro.md).</br></br>Você pode proteger vários aplicativos Web na central de segurança adicionando esses aplicativos às suas implantações WAF existentes. |
| [Finalizar a proteção das aplicações](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de um WAF, o tráfego deve ser redirecionado para o dispositivo WAF. Seguir essa recomendação conclui as alterações de instalação necessárias. |
| Utilizar a versão de node. js mais recente suportada para a aplicação Web | Recomenda que você use a versão mais recente do node. js para as classes de segurança mais recentes. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| CORS não deve permitir que todos os recursos de aceder à sua aplicação de função | Recomenda que você permita que apenas os domínios necessários interajam com seu aplicativo Web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação de função. |
| Utilizar domínios personalizados para a aplicação de funções | Recomenda que você use domínios personalizados para proteger um aplicativo de funções contra ataques comuns, como phishing e outros ataques relacionados a DNS. |
| Configurar restrições de IP para a aplicação de função | Recomenda que você defina uma lista de endereços IP que têm permissão para acessar seu aplicativo. Utilização de restrições de IP protege uma aplicação de funções de ataques comuns. |
| Função de aplicação só deve estar acessível através de HTTPS | Recomenda que você limite o acesso de aplicativos de função somente por HTTPS. |
| A depuração remota deve estar desativada para a Function App | Recomenda que você desative a depuração para Aplicativo de funções se não precisar mais usá-la. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções. |
| Web Sockets devem ser desativados para a aplicação de função | Recomenda que você revise cuidadosamente o uso de Web Sockets em aplicativos de funções. O protocolo de Web Sockets está vulnerável a diferentes tipos de ameaças de segurança. |


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorizar a identidade e acesso no Centro de segurança do Azure](security-center-identity-access.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
