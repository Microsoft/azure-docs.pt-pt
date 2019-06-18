---
title: Definições de política do Azure monitorizadas no Centro de segurança do Azure | Documentos da Microsoft
description: Definições de política do Azure monitorizadas no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428328"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Políticas de segurança do Azure monitorizadas pelo centro de segurança
Este artigo fornece uma lista de definições de política do Azure de monitorização no Centro de segurança do Azure. Para obter mais informações sobre as políticas de segurança, consulte [trabalhar com políticas de segurança](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Políticas de segurança disponíveis

Para saber mais sobre as diretivas internas que são monitorizadas pelo centro de segurança, consulte a tabela seguinte:

| Política | O que faz a política |
| --- | --- |
|Os registos de diagnóstico em conjuntos de dimensionamento de Máquina Virtual devem ser ativados|Recomendamos que ative os registos para que um registo de atividade está disponível para investigação após um incidente ou o comprometimento.|
|Todas as regras de autorização, exceto RootManageSharedAccessKey devem ser removidas do espaço de nomes do Hub de eventos|Os clientes de Hubs de eventos do Azure não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o modelo de segurança de privilégio mínimo, deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|Regras de autorização na entidade Hub de eventos devem ser definidas|A existência de regras de autorização de entidades de Hubs de eventos para conceder acesso do menor privilégio de auditoria.|
|Acesso a contas de armazenamento com firewall e as configurações de rede virtual deve ser restrito|Auditar o acesso de rede sem restrições nas definições de firewall da conta de armazenamento. Configure regras de rede para que apenas as aplicações de redes permitidas consegue aceder à conta de armazenamento. Para permitir ligações de internet específico ou de clientes no local, conceda acesso para tráfego de redes virtuais do Azure específicos ou para a internet pública de endereço IP, intervalos.|
|Auditar a utilização de regras personalizadas de RBAC|Funções incorporadas, tais como "Proprietário, Contribuidor, leitor" em vez de funções de controlo (RBAC) de acesso baseado em funções personalizados, que são suscetíveis a erros de auditoria. Utilização de funções personalizadas é tratada como uma exceção e requer revisão rigorosa e a Modelagem de ameaças.|
|Os registos de diagnóstico no Azure Stream Analytics devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Deve ser ativada para contas de armazenamento com transferência segura|Requisitos de transferência segura na sua conta de armazenamento de auditoria. Transferência segura é uma opção que força a sua conta de armazenamento para aceitar pedidos apenas a partir de ligações seguras (HTTPS). Utilização de HTTPS garante que a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito contra ataques de camada de rede, por exemplo, o man-in-the-middle, interceptação e seqüestro de sessão.|
|O administrador do Azure AD para o SQL server deve ser aprovisionado|Auditoria de aprovisionamento de um administrador do Azure Active Directory (Azure AD) para o SQL Server ativar a autenticação do Azure AD. Autenticação do Azure AD suporta a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.|
|Todas as regras de autorização, exceto RootManageSharedAccessKey devem ser removidas do espaço de nomes do Service Bus|Clientes do Service Bus do Azure não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para alinhar com o modelo de segurança de privilégios mínimos, crie políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|Os registos de diagnóstico no Service Bus devem estar ativados|Ativar dos registos de auditoria e mantenha-se para obter um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Deve ser definida a propriedade ClusterProtectionLevel EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção para a comunicação de nó para nó que utiliza um certificado de cluster principal: ' None ', ' início de sessão e EncryptAndSign '. Defina o nível de proteção para garantir que todas as mensagens de nó para nó são criptografadas e assinadas digitalmente.|
|Autenticação de cliente deve utilizar o Azure Active Directory|Auditar a utilização da autenticação de cliente apenas através do Azure AD no Service Fabric.|
|Registos do diagnóstico na pesquisa de serviços devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Devem ser ativadas apenas ligações seguras para a Cache de Redis|Auditoria que ative apenas as ligações através de SSL para a Cache do Azure para Redis. Utilização de ligações seguras garante que a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito contra ataques de camada de rede, por exemplo, o man-in-the-middle, interceptação e seqüestro de sessão.|
|Registos de diagnóstico no Logic Apps devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Os registos de diagnóstico no Cofre de chaves devem estar ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Os registos de diagnóstico no Hub de eventos devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Os registos de diagnóstico do Azure Data Lake Store devem ser ativados|Ativar registos de auditoria e mantê-los até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Os registos de diagnóstico no Data Lake Analytics devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Contas de armazenamento devem ser migradas para novos recursos de AzureRM|Utilize o Azure Resource Manager para as suas contas de armazenamento para fornecer aprimoramentos de segurança. Estas incluem: <br>-Controlo de acesso mais forte (RBAC)<br>-Auditoria melhor<br>-Governação e implementação baseados no Resource Manager as do azure<br>-Acesso a identidades geridas<br>-Acesso ao Azure Key Vault para segredos<br>-Autenticação baseada no AD as do azure<br>-Suporte para etiquetas e grupos de recursos para facilitar a gestão segurança|
|Máquinas virtuais devem ser migradas para novos recursos de AzureRM|Utilize o Azure Resource Manager para as suas máquinas virtuais para fornecer aprimoramentos de segurança.  Estas incluem: <br>-Controlo de acesso mais forte (RBAC)<br>-Auditoria melhor<br>-Governação e implementação baseados no Resource Manager as do azure<br>-Acesso a identidades geridas<br>-Acesso ao Azure Key Vault para segredos<br>-Autenticação baseada no AD as do azure<br>-Suporte para etiquetas e grupos de recursos para facilitar a gestão segurança|
|Regras de alerta de métrica devem ser configuradas em contas do Batch|Configuração de auditoria de regras de alerta de métrica em contas do Azure Batch para ativar a métrica necessária.|
|Os registos de diagnóstico em contas do Batch devem ser ativados|Ativar registos de auditoria e mantê-los para até um ano. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Deve ser ativada a encriptação em variáveis de conta de automatização|É importante ativar a encriptação de recursos de variável de conta de automatização do Azure, ao armazenar dados confidenciais.|
|Registos de diagnósticos nos serviços de aplicação devem ser ativados|Registos de auditoria que ative o diagnóstico na aplicação. Esta ação cria registos de atividade para investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.|
|Deve ser ativada a encriptação de dados transparente nas bases de dados SQL|Estado de encriptação de dados transparente para bases de dados SQL de auditoria.|
|Auditoria do SQL server deve ser ativada|Auditar a existência de auditoria de SQL ao nível do servidor.|
|\[Pré-visualização]: Monitorizar a base de dados SQL não encriptada no Centro de segurança do Azure|Centro de segurança do Azure monitoriza servidores SQL não encriptados ou de bancos de dados conforme recomendado.|
|\[Pré-visualização]: Monitorizar a base de dados SQL não auditada no Centro de segurança do Azure|Centro de segurança do Azure monitoriza os servidores do SQL Server e bases de dados que não têm a auditoria de SQL ativada conforme recomendado.|
|\[Pré-visualização]: Atualizações do sistema devem ser instaladas nos seus computadores|Centro de segurança do Azure monitoriza atualizações de sistema de segurança em falta nos seus servidores conforme recomendado.|
|\[Pré-visualização]: Auditar encriptação de BLOBs em falta para contas de armazenamento|Auditoria a contas de armazenamento que não utilizam a encriptação de Blobs. Isto aplica-se apenas a tipos de recursos de armazenamento do Microsoft, não armazenamento a partir de outros fornecedores de serviços. Centro de segurança do Azure monitoriza possível just-in-time acesso à rede, tal como recomendado.|
|\[Pré-visualização]: Controlo de acesso de rede Just-In-Time deve ser aplicado em máquinas virtuais|Centro de segurança do Azure monitoriza possível just-in-time acesso à rede, tal como recomendado.|
|\[Pré-visualização]: Controlos de aplicações adaptativos deve ser ativados em máquinas virtuais|Configuração de lista de permissões de aplicações possíveis de monitores de centro de segurança do Azure.|
|\[Pré-visualização]: Falta de grupos de segurança de rede para máquinas virtuais deve ser configurada|Centro de segurança do Azure monitoriza os grupos de segurança com regras demasiado permissivas, de rede conforme recomendado.|
|\[Pré-visualização]: Vulnerabilidades na configuração de segurança nas suas máquinas devem ser resolvidas|Centro de segurança do Azure monitoriza os servidores que não satisfaçam a linha de base configurada conforme recomendado.| 
|\[Pré-visualização]: Proteção de ponto final deve ser instalada em máquinas virtuais|Centro de segurança do Azure monitoriza os servidores que não tenham um agente do Microsoft System Center Endpoint Protection instalado conforme recomendado.|
|\[Pré-visualização]: Encriptação de disco deve ser aplicada em máquinas virtuais|Centro de segurança do Azure monitoriza as máquinas virtuais que não tem a encriptação de disco ativada conforme recomendado.|
|\[Pré-visualização]: Vulnerabilidades devem ser resolvidas por uma solução de avaliação de vulnerabilidade|Monitorizar vulnerabilidades que são detetadas pela solução de avaliação de vulnerabilidades e VMs que não têm uma solução de avaliação de vulnerabilidades no Centro de segurança do Azure, tal como recomendado.|
|\[Pré-visualização]: Monitorizar aplicações web desprotegidas no Centro de segurança do Azure|Centro de segurança do Azure monitoriza aplicações web que não têm proteção de firewall de aplicação web, tal como recomendado.|
|\[Pré-visualização]: Solução de proteção de ponto final deve ser instalada em máquinas virtuais|Pontos de extremidade que não têm a proteção de firewall de geração seguinte conforme recomendado de rede do Centro de segurança do Azure monitoriza.|
|\[Pré-visualização]: Vulnerabilidades nos seus bancos de dados do SQL devem ser resolvidas|Avaliação de vulnerabilidades de monitor resultados da verificação e recomendar como remediar vulnerabilidades de base de dados.|
|\[Pré-visualização]: Um máximo de 3 proprietários deve ser designado para a sua subscrição|Recomendamos que designar até três proprietários de subscrições para reduzir a possibilidade de violação por um proprietário comprometido.|
|\[Pré-visualização]: Deve haver mais do que um proprietário atribuído à sua subscrição|Recomendamos que designar mais do que um proprietário da subscrição para garantir o acesso de administrador redundância.|
|\[Pré-visualização]: MFA deve ser ativada em contas com permissões de proprietário na sua subscrição |Autenticação multifator (MFA) deve estar ativada para todas as contas de subscrição que tem permissões de proprietário para evitar falhas de segurança de contas ou recursos.|
|\[Pré-visualização]: MFA deve ser ativada em suas contas de subscrição com permissões de escrita|Autenticação multifator deve estar ativada para todas as contas de subscrição que tem permissões de escrita para evitar a violação de contas ou recursos.|
|\[Pré-visualização]: MFA deve ser ativada em suas contas de subscrição com permissões de leitura|Autenticação multifator deve estar ativada para todas as contas de subscrição que tem permissões de leitura para evitar a violação de contas ou recursos.|
|\[Pré-visualização]: As contas preteridas com permissões de proprietário devem ser removidas da sua subscrição|As contas preteridas que têm permissões de proprietário devem ser removidas da sua subscrição. As contas preteridas foram bloqueadas de iniciar sessão.|
|\[Pré-visualização]: As contas preteridas devem ser removidas da sua subscrição|As contas preteridas devem ser removidas das suas subscrições. As contas preteridas foram bloqueadas de iniciar sessão.|
|\[Pré-visualização]: As contas externas com permissões de proprietário devem ser removidas da sua subscrição|As contas externas com permissões de proprietário devem ser removidas da sua subscrição para impedir o acesso de permissões.|
|\[Pré-visualização]: As contas externas com permissões de escrita devem ser removidas da sua subscrição|As contas externas que tenham escrevem as permissões devem ser removidas da sua subscrição para impedir o acesso não monitorizado.|
|\[Pré-visualização]: As contas externas com permissões de leitura devem ser removidas da sua subscrição|As contas externas que tem permissões de leitura devem ser removidas da sua subscrição para impedir o acesso não monitorizado.|




## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais sobre o Centro de segurança, veja os artigos seguintes.

* [Guia de operações e planeamento do Centro de segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planear e compreender as considerações de design no Centro de segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros no Centro de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [FAQ do Centro de segurança do Azure](security-center-faq.md): Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

Para saber mais sobre o Azure Policy, veja [o que é o Azure Policy?](../governance/policy/overview.md).
