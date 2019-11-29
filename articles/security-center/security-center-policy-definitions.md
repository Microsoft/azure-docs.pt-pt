---
title: Definições de política do Azure monitoradas na central de segurança do Azure | Microsoft Docs
description: Este artigo fornece uma lista de definições de Azure Policy que você pode monitorar na central de segurança do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559267"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Políticas de segurança do Azure monitoradas pela central de segurança
Este artigo fornece uma lista de definições de Azure Policy que você pode monitorar na central de segurança do Azure. Para obter mais informações sobre políticas de segurança, consulte [trabalhando com políticas de segurança](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Políticas de segurança disponíveis

Para saber mais sobre as políticas internas que são monitoradas pela central de segurança, consulte a tabela a seguir:

| Política | O que faz a política |
| --- | --- |
|Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados|Recomendamos que você habilite os logs para que uma trilha de atividade esteja disponível para investigação após um incidente ou um comprometimento.|
|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos|Os clientes dos hubs de eventos do Azure não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|As regras de autorização na entidade do hub de eventos devem ser definidas|Auditar a existência de regras de autorização em entidades de hubs de eventos para conceder acesso com privilégios mínimos.|
|O acesso a contas de armazenamento com firewall e configurações de rede virtual deve ser restrito|Auditar o acesso irrestrito à rede nas configurações de firewall da sua conta de armazenamento. Configure regras de rede para que somente aplicativos de redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes locais ou da Internet específicos, conceda acesso ao tráfego de redes virtuais específicas do Azure ou a intervalos de endereços IP de Internet públicos.|
|Auditar o uso de regras personalizadas de RBAC|Auditar funções internas, como "proprietário, colaborador, leitor" em vez de funções de RBAC (controle de acesso baseado em função) personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e modelagem de risco.|
|Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|A transferência segura para contas de armazenamento deve ser habilitada|Requisitos de auditoria de transferência segura em sua conta de armazenamento. Transferência segura é uma opção que força sua conta de armazenamento a aceitar solicitações somente de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|
|O administrador do Azure AD para SQL Server deve ser provisionado|Auditar o provisionamento de um administrador de Azure Active Directory (Azure AD) para SQL Server para habilitar a autenticação do Azure AD. A autenticação do Azure AD dá suporte ao gerenciamento de permissões simplificado e ao gerenciamento de identidade centralizado de usuários de banco de dados e outros serviços da Microsoft|
|Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do barramento de serviço|Os clientes do barramento de serviço do Azure não devem usar uma política de acesso no nível de namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégios mínimos, crie políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso apenas à entidade específica.|
|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Auditar a habilitação de logs e mantê-los em um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|A propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric deve ser definida|O Service Fabric fornece três níveis de proteção para a comunicação de nó para nó que usa um certificado de cluster primário: nenhum, assinar e EncryptAndSign. Defina o nível de proteção para garantir que todas as mensagens de nó para nó sejam criptografadas e assinadas digitalmente.|
|A autenticação do cliente deve usar Azure Active Directory|Auditar o uso de autenticação de cliente somente por meio do Azure AD no Service Fabric.|
|Os logs de diagnóstico nos serviços de pesquisa devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Somente conexões seguras para o cache Redis devem ser habilitadas|Auditar a habilitação apenas de conexões via SSL para o cache do Azure para Redis. O uso de conexões seguras garante a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|
|Os logs de diagnóstico em aplicativos lógicos devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Os logs de diagnóstico no Key Vault devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Os logs de diagnóstico no Hub de eventos devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados|Auditar a habilitação de logs e mantê-los em um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Os logs de diagnóstico no Data Lake Analytics devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|As contas de armazenamento devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para suas contas de armazenamento para fornecer aprimoramentos de segurança. As regiões incluem: <br>-Controle de acesso mais forte (RBAC)<br>-Melhor auditoria<br>-Implantação e governança com base em Azure Resource Manager<br>-Acesso a identidades gerenciadas<br>-Acesso a Azure Key Vault para segredos<br>-Autenticação baseada no Azure AD<br>-Suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança|
|As máquinas virtuais devem ser migradas para novos recursos do AzureRM|Use Azure Resource Manager para suas máquinas virtuais para fornecer aprimoramentos de segurança. As regiões incluem: <br>-Controle de acesso mais forte (RBAC)<br>-Melhor auditoria<br>-Implantação e governança com base em Azure Resource Manager<br>-Acesso a identidades gerenciadas<br>-Acesso a Azure Key Vault para segredos<br>-Autenticação baseada no Azure AD<br>-Suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança|
|As regras de alerta de métrica devem ser configuradas em contas do lote|Auditar a configuração de regras de alerta de métricas em contas do lote do Azure para habilitar a métrica necessária.|
|Os logs de diagnóstico em contas do lote devem ser habilitados|Auditar a habilitação de logs e mantê-los por até um ano. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|A criptografia deve ser habilitada em variáveis de conta de automação|É importante habilitar a criptografia de ativos de variável de conta de automação do Azure ao armazenar dados confidenciais.|
|Os logs de diagnóstico nos serviços de aplicativos devem ser habilitados|Auditoria de habilitação de logs de diagnóstico no aplicativo. Isso cria trilhas de atividade para investigação quando ocorre um incidente de segurança ou sua rede é comprometida.|
|Transparent Data Encryption em bancos de dados SQL devem ser habilitadas|Auditar o status da Transparent Data Encryption para bancos de dados SQL.|
|A auditoria do SQL Server deve ser habilitada|Auditar a existência de auditoria do SQL no nível do servidor.|
|\[visualização]: monitorar o banco de dados SQL não criptografado na central de segurança do Azure|A central de segurança do Azure monitora servidores ou bancos de dados SQL não criptografados, conforme recomendado.|
|\[visualização]: monitorar o banco de dados SQL não auditado na central de segurança do Azure|A central de segurança do Azure monitora os servidores SQL e os bancos de dados que não têm a auditoria do SQL ativada conforme recomendado.|
|\[visualização]: as atualizações do sistema devem ser instaladas em seus computadores|A central de segurança do Azure monitora atualizações de sistema de segurança ausentes em seus servidores, conforme recomendado.|
|\[visualização]: auditoria de criptografia de blob ausente para contas de armazenamento|Auditar contas de armazenamento que não usam criptografia de BLOB. Isso se aplica somente aos tipos de recursos de armazenamento da Microsoft, não ao armazenamento de outros provedores. A central de segurança do Azure monitora o possível acesso just-in-time de rede, conforme recomendado.|
|\[visualização]: o controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais|A central de segurança do Azure monitora o possível acesso just-in-time de rede, conforme recomendado.|
|\[visualização]: controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais|A central de segurança do Azure monitora a configuração da lista de permissões do aplicativo.|
|\[visualização]: grupos de segurança de rede ausentes para máquinas virtuais devem ser configurados|A central de segurança do Azure monitora os grupos de segurança de rede que têm regras muito permissivas, conforme recomendado.|
|\[visualização]: vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas|A central de segurança do Azure monitora servidores que não atendem à linha de base configurada conforme recomendado.| 
|\[visualização]: o Endpoint Protection deve ser instalado em máquinas virtuais|A central de segurança do Azure monitora servidores que não têm um agente de Endpoint Protection do Microsoft System Center instalado, conforme recomendado.|
|\[visualização]: a criptografia de disco deve ser aplicada em máquinas virtuais|A central de segurança do Azure monitora as máquinas virtuais que não têm a criptografia de disco habilitada conforme recomendado.|
|\[visualização]: as vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade|Monitore as vulnerabilidades detectadas pela solução de avaliação de vulnerabilidade e VMs que não têm uma solução de avaliação de vulnerabilidade na central de segurança do Azure, conforme recomendado.|
|\[visualização]: monitorar o aplicativo Web desprotegido na central de segurança do Azure|A central de segurança do Azure monitora aplicativos Web que não têm proteção de firewall do aplicativo Web, conforme recomendado.|
|\[visualização]: a solução Endpoint Protection deve ser instalada em máquinas virtuais|A central de segurança do Azure monitora os pontos de extremidade de rede que não têm a proteção de firewall da próxima geração, conforme recomendado.|
|\[visualização]: as vulnerabilidades em seus bancos de dados SQL devem ser corrigidas|Monitore os resultados da verificação de avaliação de vulnerabilidade e recomende como corrigir vulnerabilidades de banco de dados.|
|\[visualização]: um máximo de 3 proprietários deve ser designado para sua assinatura|Recomendamos que você designe até três proprietários de assinatura para reduzir o potencial de violação por um proprietário comprometido.|
|\[visualização]: deve haver mais de um proprietário atribuído à sua assinatura|Recomendamos que você designe mais de um proprietário de assinatura para garantir a redundância de acesso de administrador.|
|\[visualização]: a MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura |A autenticação multifator (MFA) deve ser habilitada para todas as contas de assinatura que têm permissões de proprietário para evitar uma violação de contas ou recursos.|
|\[visualização]: a MFA deve ser habilitada em suas contas de assinatura com permissões de gravação|A autenticação multifator deve ser habilitada para todas as contas de assinatura que têm permissões de gravação para impedir a violação de contas ou recursos.|
|\[visualização]: a MFA deve ser habilitada em suas contas de assinatura com permissões de leitura|A autenticação multifator deve ser habilitada para todas as contas de assinatura que têm permissões de leitura para impedir a violação de contas ou recursos.|
|\[visualização]: contas preteridas com permissões de proprietário devem ser removidas da sua assinatura|Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura. As contas preteridas foram impedidas de entrar.|
|\[visualização]: as contas preteridas devem ser removidas da sua assinatura|As contas preteridas devem ser removidas das suas assinaturas. As contas preteridas foram impedidas de entrar.|
|\[visualização]: contas externas com permissões de proprietário devem ser removidas da sua assinatura|As contas externas que têm permissões de proprietário devem ser removidas da sua assinatura para impedir o acesso às permissões.|
|\[visualização]: as contas externas com permissões de gravação devem ser removidas da sua assinatura|Contas externas que têm permissões de gravação devem ser removidas da sua assinatura para evitar acesso não monitorado.|
|\[visualização]: as contas externas com permissões de leitura devem ser removidas da sua assinatura|Contas externas que têm permissões de leitura devem ser removidas da sua assinatura para evitar acesso não monitorado.|




## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais sobre a central de segurança, consulte os artigos a seguir.

* [Guia de planejamento e operações da central de segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design na central de segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitor partner solutions with Azure Security Center](security-center-partner-solutions.md) (Monitorizar soluções de parceiros com o Centro de Segurança do Azure): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ](security-center-faq.md) (FAQ do Centro de Segurança do Azure): obtenha respostas às perguntas mais frequentes sobre como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.

Para saber mais sobre Azure Policy, confira [o que é Azure Policy?](../governance/policy/overview.md).
