---
title: Recomendações da central de segurança do Azure para computadores & aplicativos
description: Recomendações de segurança da central de segurança do Azure que ajudam a proteger suas máquinas virtuais, computadores, aplicativos Web e ambientes de serviço de aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782167"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Recomendações de computação e aplicativo – guia de referência

Este artigo fornece a lista completa das recomendações que você pode ver na central de segurança do Azure em relação aos seguintes tipos de recursos:

* VMs e computadores
* Conjuntos de Dimensionamento de VM
* Serviços em Nuvem
* Serviços aplicacionais
* Contentores
* Recursos de computação

Para obter uma explicação de como encontrá-las e como resolvê-las, consulte [aqui](security-center-virtual-machine-protection.md).

## Recomendações de computação e aplicativo<a name="compute-and-app-recs"></a>
|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Serviço de aplicativo|20|O aplicativo Web só deve ser acessível via HTTPS|Limite o acesso de aplicativos Web somente por HTTPS.|
|Serviço de aplicativo|20|Aplicativo de funções só deve ser acessível via HTTPS|Limite o acesso de aplicativos de funções somente por HTTPS.|
|Serviço de aplicativo|5|Os logs de diagnóstico nos serviços de aplicativos devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Serviço de aplicativo|10|A depuração remota deve ser desativada para o aplicativo Web|Desative a depuração de aplicativos Web se você não precisar mais usá-lo. A depuração remota requer que as portas de entrada sejam abertas em um Aplicativo de funções.|
|Serviço de aplicativo|10|A depuração remota deve ser desativada para o aplicativo de funções|Desative a depuração para Aplicativo de funções se você não precisar mais usá-la. A depuração remota requer que as portas de entrada sejam abertas em um Aplicativo de funções.|
|Serviço de aplicativo|10|Não permitir que todos os recursos (' * ') acessem seu aplicativo| Não permita o conjunto de WEBSITE_LOAD_CERTIFICATES parâmetro como "". Definir o parâmetro como ' ' significa que todos os certificados são carregados no repositório de certificados pessoal de seus aplicativos Web. Isso pode levar ao abuso do princípio de menos privilégios, pois é improvável que o site precise de acesso a todos os certificados em tempo de execução.|
|Serviço de aplicativo|20|O CORS não deve permitir que todos os recursos acessem seus aplicativos Web|Permitir que somente os domínios necessários interajam com seu aplicativo Web. O CORS (compartilhamento de recursos entre origens) não deve permitir que todos os domínios acessem seu aplicativo Web.|
|Serviço de aplicativo|20|O CORS não deve permitir que todos os recursos acessem seu Aplicativo de funções| Permitir que somente os domínios necessários interajam com seu aplicativo de funções. O CORS (compartilhamento de recursos entre origens) não deve permitir que todos os domínios acessem seu aplicativo de funções.|
|Recursos de computação (lote)|1|As regras de alerta de métrica devem ser configuradas em contas do lote|Configure as regras de alerta de métrica na conta do lote e habilite os eventos completos de exclusão do pool de métricas e eventos de início de exclusão de pool|
|Recursos de computação (Service Fabric)|10|Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente|Execute a autenticação de cliente somente por meio de Azure Active Directory no Service Fabric.|
|Recursos de computação (conta de automação)|5|As variáveis da conta de automação devem ser criptografadas|Habilite a criptografia de ativos de variável de conta de automação ao armazenar dados confidenciais.|
|Recursos de computação (pesquisa)|5|Habilitação de auditoria de logs de diagnóstico para serviços de pesquisa|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (barramento de serviço)|5|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Stream Analytics)|5|Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (lote)|5|Habilitar logs de diagnóstico em contas do lote|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Hub de eventos)|5|Os logs de diagnóstico no Hub de eventos devem ser habilitados|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (aplicativos lógicos)|5|Habilitar logs de diagnóstico em aplicativos lógicos|Habilite os logs e mantenha-os em um ano. Isso permite que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou se sua rede estiver comprometida. |
|Recursos de computação (Service Fabric)|15|Defina a propriedade ClusterProtectionLevel como EncryptAndSign em Service Fabric|Service Fabric fornece três níveis de proteção (None, Sign e EncryptAndSign) para comunicação de nó para nó usando um certificado de cluster primário. Defina o nível de proteção para garantir que todas as mensagens de nó para nó sejam criptografadas e assinadas digitalmente. |
|Recursos de computação (barramento de serviço)|1|Remover todas as regras de autorização, exceto RootManageSharedAccessKey do namespace do barramento de serviço |Os clientes do barramento de serviço não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|Recursos de computação (Hub de eventos)|1|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos|Os clientes do hub de eventos não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|Recursos de computação (Hub de eventos)|5|As regras de autorização na entidade do hub de eventos devem ser definidas|Auditar regras de autorização na entidade do hub de eventos para conceder acesso com privilégios mínimos.|
|Máquina|50|Instalar o agente de monitoramento em seus computadores|Instale o agente de monitoramento para habilitar a coleta de dados, verificação de atualizações, verificação de linha de base e proteção de ponto de extremidade em cada computador.|
|Máquina|50|Habilitar provisionamento automático e coleta de dados para suas assinaturas |Habilite o provisionamento automático e a coleta de dados para computadores em suas assinaturas para habilitar a coleta de dados, verificação de atualizações, verificação de linha de base e proteção de ponto de extremidade em cada computador adicionado às suas assinaturas.|
|Máquina|40|Resolver problemas de integridade do agente de monitoramento em seus computadores|Para a proteção completa da central de segurança, resolva os problemas do agente de monitoramento em seus computadores seguindo as instruções no guia de solução de problemas| 
|Máquina|40|Resolver problemas de integridade do Endpoint Protection em seus computadores|Para a proteção completa da central de segurança, resolva os problemas do agente de monitoramento em seus computadores seguindo as instruções no guia de solução de problemas.|
|Máquina|40|Solucionar problemas de dados de verificação ausentes em seus computadores|Solucionar problemas de dados de verificação ausentes em máquinas virtuais e computadores. Os dados de verificação ausentes em seus computadores resultam em avaliações de segurança ausentes, como verificação de atualização, verificação de linha de base e verificação de solução ausente do Endpoint Protection.|
|Máquina|40|As atualizações do sistema devem ser instaladas em seus computadores|Instalar atualizações críticas e de segurança do sistema ausentes para proteger seus computadores e máquinas virtuais Windows e Linux
|Máquina|15|Adicionar uma firewall de aplicação Web| Implante uma solução de WAF (firewall do aplicativo Web) para proteger seus aplicativos Web. |
|Máquina|40|Atualizar a versão do sistema operacional para suas funções de serviço de nuvem|Atualize a versão do sistema operacional (SO) para suas funções de serviço de nuvem para a versão mais recente disponível para a família do sistema operacional.|
|Máquina|35|Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas|Corrija vulnerabilidades na configuração de segurança em seus computadores para protegê-las contra ataques.|
|Máquina|35|Corrigir vulnerabilidades na configuração de segurança em seus contêineres|Corrija vulnerabilidades na configuração de segurança em computadores com o Docker instalado para protegê-los contra ataques.|
|Máquina|25|Habilitar controles de aplicativo adaptáveis|Habilite o controle de aplicativo para controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Isso ajudará a proteger suas VMs contra malware. A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em cada VM e ajuda a aplicar regras de permissão usando essa inteligência. Esse recurso simplifica o processo de configuração e manutenção de regras de permissão de aplicativo.|
|Máquina|20|Instalar a solução Endpoint Protection em seus computadores|Instale uma solução de proteção de ponto de extremidade em suas máquinas virtuais para protegê-las contra ameaças e vulnerabilidades.|
|Máquina|20|Reinicie os computadores para aplicar atualizações do sistema|Reinicie os computadores para aplicar as atualizações do sistema e proteger a máquina contra vulnerabilidades.|
|Máquina|15|A criptografia de disco deve ser aplicada em máquinas virtuais|Criptografe seus discos de máquina virtual usando Azure Disk Encryption para máquinas virtuais Windows e Linux. Azure Disk Encryption (ADE) aproveita o recurso de BitLocker padrão do setor do Windows e o recurso DM-cript do Linux para fornecer criptografia de disco do sistema operacional e de dados para ajudar a proteger e proteger seus dados e ajudar a atender à segurança e à conformidade da organização compromissos no cofre de chaves do Azure do cliente. Quando seu requisito de conformidade e segurança exigir que você criptografe os dados de ponta a ponta usando suas chaves de criptografia, incluindo a criptografia do disco efêmero (temporário anexado localmente), use o Azure Disk Encryption. Como alternativa, por padrão, os Managed Disks são criptografados em repouso por padrão usando o Azure Criptografia do Serviço de Armazenamento em que as chaves de criptografia são chaves gerenciadas pela Microsoft no Azure. Se isso atender aos seus requisitos de conformidade e de segurança, você poderá aproveitar a criptografia de disco gerenciado padrão para atender às suas necessidades.|
|Máquina|30|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|
|Máquina|15|Adicionar uma firewall de aplicação Web| Implante uma solução de WAF (firewall do aplicativo Web) para proteger seus aplicativos Web. |
|Máquina|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para as quais uma solução de avaliação de vulnerabilidades de terceiros são implantadas estão sendo continuamente avaliadas em relação a vulnerabilidades de aplicativo e sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para obter mais informações como parte da recomendação.|
|Máquina|30|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|Instalar uma solução de avaliação de vulnerabilidade em suas máquinas virtuais|
|Máquina|1|As máquinas virtuais devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para que suas máquinas virtuais forneçam aprimoramentos de segurança como: RBAC (controle de acesso mais forte), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao key Vault para segredos, Autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança. |
|Máquina|30|Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|As máquinas virtuais para as quais uma solução de avaliação de vulnerabilidades de terceiros são implantadas estão sendo continuamente avaliadas em relação a vulnerabilidades de aplicativo e sistema operacional. Sempre que essas vulnerabilidades forem encontradas, elas estarão disponíveis para obter mais informações como parte da recomendação.|
|Conjuntos de dimensionamento de máquinas virtuais |4|Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados|Habilite os logs e mantenha-os por até um ano. Isso permite que você recrie trilhas de atividade para fins de investigação. Isso é útil quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Conjuntos de dimensionamento de máquinas virtuais|35|Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Corrija vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ataques. |
|Conjuntos de dimensionamento de máquinas virtuais|5|Corrigir falhas de integridade do Endpoint Protection em conjuntos de dimensionamento de máquinas virtuais|Corrija as falhas de integridade da proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-las contra ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|10|O Endpoint Protection deve ser instalado em máquinas virtuais|Instale uma solução de proteção de ponto de extremidade em seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ameaças e vulnerabilidades. |
|Conjuntos de dimensionamento de máquinas virtuais|40|As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Instale as atualizações críticas e de segurança do sistema ausentes para proteger seus conjuntos de dimensionamento de máquinas virtuais Windows e Linux. |
|


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo seu serviço SQL do Azure na central de segurança do Azure](security-center-sql-service-recommendations.md)
