---
title: Recomendações de máquina virtual no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento explica as recomendações do Centro de segurança do Azure para saber como ajudar a proteger as suas máquinas virtuais e computadores e suas aplicações web e ambientes de serviço de aplicações.
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
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544998"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Compreender as recomendações de recursos do Centro de segurança do Azure


## <a name="recommendations"></a>Recomendações
Utilize a tabela abaixo como referência para ajudar a compreender a computação disponível e de serviços aplicacionais recomendações e o que cada um deles faz se as aplicar.

### <a name="computers"></a>Computadores
| Recomendação | Descrição |
| --- | --- |
| [Ativar a recolha de dados para subscrições](security-center-enable-data-collection.md) |Recomenda-se que ative a recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições. |
| [Ativar a encriptação da conta de armazenamento do Azure](security-center-enable-encryption-for-storage-account.md) | Recomenda-se de que ativa a encriptação do serviço de armazenamento do Azure para dados inativos. Encriptação de serviço de armazenamento (SSE) funciona ao encriptar os dados quando ele é escrito para o armazenamento do Azure e desencripta antes da obtenção. O SSE está atualmente disponível apenas para o serviço de Blobs do Azure e pode ser utilizado para blobs de blocos, blobs de páginas e blobs de acréscimo. Para obter mais informações, consulte [encriptação do serviço de armazenamento para dados Inativos](../storage/common/storage-service-encryption.md).</br>O SSE só é suportado em contas de armazenamento do Resource Manager. Contas de armazenamento clássicas não são atualmente suportadas. Para compreender o clássico e modelos de implementação do Resource Manager, veja [modelos de implementação do Azure](../azure-classic-rm.md). |
| [Remediar configurações de segurança](security-center-remediate-os-vulnerabilities.md) |Recomenda que se que Alinhe as configurações do SO com as regras de configuração de segurança recomendadas, por exemplo, não permitir palavras-passe sejam guardadas. |
| [Aplicar atualizações do sistema](security-center-apply-system-updates.md) |Recomenda-se que implemente o sistema de segurança em falta e atualizações críticas nas VMs. |
| [Aplicar um Just-In-Time controlo de acesso de rede](security-center-just-in-time.md) | Recomenda-se de que aplicar just-in-acesso à VM do tempo. A apenas no tempo funcionalidade está em pré-visualização e está disponível no escalão Standard do Centro de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança. |
| [Reiniciar após atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Recomenda-se que reinicie uma VM para concluir o processo de aplicar atualizações do sistema. |
| [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md) |Recomenda-se que aprovisione programas de antimalware em VMs (apenas VMs do Windows). |
| [Ativar o Agente de VM](security-center-enable-vm-agent.md) |Permite-lhe ver as VMs que necessitam do Agente de VM. O Agente de VM tem de estar instalado em VMs para aprovisionar a análise de patch, a análise de linha de base e os programas de antimalware. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| [Aplicar encriptação de discos](security-center-apply-disk-encryption.md) |Recomenda-se que encripte os discos da VM com o Azure Disk Encryption (VMs Windows e Linux). A encriptação é recomendada para o SO e os volumes de dados na sua VM. |
| [Atualizar a versão do SO](security-center-update-os-version.md) |Recomenda-se de que Atualize a versão do sistema operativo (SO) do serviço em nuvem para a versão mais recente disponível para sua família de SO.  Para saber mais sobre os serviços Cloud, veja a [descrição geral dos serviços de Cloud](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidades não instalada](security-center-vulnerability-assessment-recommendations.md) |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Permite-lhe ver as vulnerabilidades de sistema e de aplicações detetadas pela solução de avaliação de vulnerabilidade instalada na sua VM. |

### Serviços de aplicações <a name="app-services"></a>
| Recomendação | Descrição |
| --- | --- |
| Serviço de aplicações só devem estar acessível através de HTTPS | Recomenda-se de que limite o acesso do serviço de aplicações através de HTTPS apenas. |
| Web Sockets devem ser desativados para a aplicação Web| Recomenda-se de que examinar cuidadosamente a utilização de Web Sockets nas aplicações web.  O protocolo de Web Sockets está vulnerável a diferentes tipos de ameaças de segurança. |
| Utilizar domínios personalizados para a sua aplicação Web | Recomenda o uso de domínios personalizados para proteger uma aplicação web de ataques comuns, como o phishing e outros ataques relacionados com DNS. |
| Configurar restrições de IP para a aplicação Web | Recomenda-se de que definir uma lista de endereços IP que têm permissão para aceder à sua aplicação.  Utilização de restrições de IP de protege as aplicações web contra ataques comuns. |
| Não permitir que todos os ('* ') recursos para aceder à sua aplicação | Recomenda que não definir o parâmetro WEBSITE_LOAD_CERTIFICATES ' *'. Definição do parâmetro '* "significa que todos os certificados serão carregados para o arquivo de certificados pessoais de aplicações web.  Isto pode levar ao abuso do princípio de privilégio mínimo porque é improvável que o site necessita de acesso a todos os certificados no tempo de execução. |
| CORS não deve permitir que todos os recursos aceder à sua aplicação | Recomenda-se de que permite que apenas necessários domínios interagir com a sua aplicação web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação web. |
| Utilize a versão mais recente do .NET Framework suportada para a aplicação Web | Recomenda-se de que use a versão mais recente do .NET Framework para as classes de segurança mais recente. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| Utilizar a versão de Java mais recente suportada para a aplicação Web | Recomenda-se de que use a versão mais recente do Java para as classes de segurança mais recente. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| Utilizar a versão PHP mais recente suportada para a aplicação Web | Recomenda-se de que use a versão mais recente do PHP para as classes de segurança mais recente. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md) |Recomenda-se de que implante uma firewall de aplicações web (WAF) para pontos finais de web. Uma recomendação de WAF é apresentada para qualquer IP destinado ao público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associado com portas de web de entrada aberta (80,443).</br></br>Centro de segurança recomenda que Aprovisiona uma WAF para ajudar na defesa contra ataques que visam as suas aplicações web em máquinas virtuais e no ambiente de serviço de aplicações. Um ambiente de serviço de aplicações (ASE) é uma [Premium](https://azure.microsoft.com/pricing/details/app-service/) service opção do plano do serviço de aplicações do Azure que fornece um ambiente totalmente isolado e dedicado para uma execução segura de aplicações do App Service do Azure. Para saber mais sobre o ASE, veja a [documentação do ambiente de serviço de aplicações](../app-service/environment/intro.md).</br></br>Pode proteger várias aplicações web no Centro de segurança com a adição desses aplicativos para as implementações existentes do WAF. |
| [Finalizar a proteção das aplicações](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de uma WAF, tráfego deve ser reencaminhado para a aplicação do WAF. Seguir essa recomendação conclui as alterações de configuração necessários. |
| Utilizar a versão de node. js mais recente suportada para a aplicação Web | Recomenda-se de que use a versão mais recente do node. js para as classes de segurança mais recente. Utilizar classes mais antigas e de tipos pode fazer com que seu aplicativo vulnerável. |
| CORS não deve permitir que todos os recursos de aceder à sua aplicação de função | Recomenda-se de que permite que apenas necessários domínios interagir com a sua aplicação web. Entre os recursos de origens (CORS) de partilha não deve permitir que todos os domínios acedam à sua aplicação de função. |
| Utilizar domínios personalizados para a aplicação de funções | Recomenda o uso de domínios personalizados para proteger uma aplicação de funções de ataques comuns, como o phishing e outros ataques relacionados com DNS. |
| Configurar restrições de IP para a aplicação de função | Recomenda-se de que definir uma lista de endereços IP que têm permissão para aceder à sua aplicação. Utilização de restrições de IP protege uma aplicação de funções de ataques comuns. |
| Função de aplicação só deve estar acessível através de HTTPS | Recomenda-se de que limite o acesso de aplicações de funções através de HTTPS apenas. |
| Depuração remota deve ser desativada para a aplicação de funções | Recomenda-se de que desativa a depuração para a aplicação de função se já não precisar de utilizá-lo. Depuração remota necessita de portas de entrada estar abertas na aplicação de funções. |
| Web Sockets devem ser desativados para a aplicação de função | Recomenda-se de que examinar cuidadosamente a utilização de Sockets Web nas aplicações de função. O protocolo de Web Sockets está vulnerável a diferentes tipos de ameaças de segurança. |


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
