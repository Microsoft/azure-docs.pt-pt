---
title: Controlos de amostrade modelo de benchmark de segurança Azure
description: Mapeamento de controlo da amostra de projeto de benchmark de segurança Azure para a Política Azure.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691296"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mapeamento de controlo da amostra de projeto de benchmark de segurança Azure

O seguinte artigo detalha como a amostra de projeto de referência de segurança Azure Blueprints Azure mapeia os controlos de benchmark de segurança do Azure. Para obter mais informações sobre os controlos, consulte [o Azure Security Benchmark](../../../../security/benchmarks/overview.md).

Os seguintes mapeamentos são para os controlos de benchmark de **segurança Azure.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Os controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione as ** \[recomendações de\]Pré-visualização : Audite Azure Security Benchmark e implemente** iniciativapolítica específica de apoio a extensões vm incorporadas.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade pode incluir controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua rede virtual

- As subredes devem ser associadas a um Grupo de Segurança da Rede
- Recomendações adapttivas de endurecimento da rede devem ser aplicadas na Internet viradas para máquinas virtuais
- As máquinas virtuais devem ser ligadas a uma rede virtual aprovada
- As máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança da rede
- Service Bus deve usar um ponto final de serviço de rede virtual
- O Serviço de Aplicações deve utilizar um ponto final do serviço de rede virtual
- O SQL Server deve utilizar um ponto final de serviço de rede virtual
- O Event Hub deve usar um ponto final do serviço de rede virtual
- Cosmos DB deve usar um ponto final de serviço de rede virtual
- O Key Vault deve utilizar um ponto final de serviço de rede virtual
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Contas de Armazenamento devem usar um ponto final do serviço de rede virtual
- O Registo de Contentores deve utilizar um ponto final do serviço de rede virtual
- As redes virtuais devem utilizar o gateway de rede virtual especificado
- As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes
- \[Pré-visualização\]: O reencaminhamento ip da sua máquina virtual deve ser desativado
- As máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança da rede
- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- As portas de gestão devem ser fechadas nas suas máquinas virtuais

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Monitor e registar a configuração e o tráfego de Vnets, Subnets e NICs

- O Observador da Rede deve ser ativado

## <a name="13-protect-critical-web-applications"></a>1.3 Proteja aplicações web críticas

- Certifique-se de que a aplicação WEB tem 'Certificados de cliente (certificados de cliente de entrada)' definidos para 'On'
- O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web
- O CORS não deve permitir que todos os recursos acedam às suas Aplicações de Função
- O CORS não deve permitir que todos os recursos acedam à sua App API
- Depuração remota deve ser desligada para aplicações web
- Depuração remota deve ser desligada para aplicações de função
- Depuração remota deve ser desligada para Apps API

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Negar comunicações com endereços IP maliciosos conhecidos

- Norma de proteção DDoS deve ser ativada
- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- Recomendações adapttivas de endurecimento da rede devem ser aplicadas na Internet viradas para máquinas virtuais

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Pacotes de rede de registos e registos de fluxo

- O Observador da Rede deve ser ativado

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

- Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Acesso à Rede'
- Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Cliente da Rede Microsoft'
- Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Segurança da Rede'
- Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Segurança da Rede'
- Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Microsoft Network Server'
- Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Microsoft Network Server'
- Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Modelos Administrativos - Rede'
- Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Modelos Administrativos - Rede'

## <a name="22-configure-central-security-log-management"></a>2.2 Configurar a gestão central de registos de segurança

- O agente Log Analytics deve ser instalado em máquinas virtuais
- O agente Log Analytics deve ser instalado em conjuntos de escala de máquina virtual
- Implemente pré-requisitos para auditar VMs do Windows em que o agente Log Analytics não está ligado como esperado
- Mostre os resultados da auditoria dos VMs do Windows nos quais o agente Log Analytics não está ligado como esperado
- O perfil de registo do Monitor Azure deve recolher registos para categorias 'escrever', 'excluir' e 'agir'
- O Monitor Azure deve recolher registos de atividade sacar registos de atividades de todas as regiões
- O fornecimento automático do agente de monitorização Log Analytics deve ser ativado na sua subscrição

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Permitir a exploração de auditoria aos recursos do Azure

- Os registos de diagnóstico na Azure Data Lake Store devem ser ativados
- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados
- Os registos de diagnóstico no IoT Hub devem ser ativados
- Os registos de diagnóstico nas contas do Lote devem ser ativados
- Os registos de diagnóstico em conjuntos de escala de máquinas virtuais devem ser ativados
- Os registos de diagnóstico no Event Hub devem ser ativados
- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados
- Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados
- Os registos de diagnóstico no Data Lake Analytics devem ser ativados
- Os registos de diagnóstico no Cofre-Chave devem ser ativados
- Os registos de diagnóstico no Ônibus de Serviço devem ser ativados
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- A auditoria no servidor SQL deve ser ativada
- Definição de diagnóstico de auditoria

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Recolher registos de segurança dos sistemas operativos

- O fornecimento automático do agente de monitorização Log Analytics deve ser ativado na sua subscrição
- O agente Log Analytics deve ser instalado em máquinas virtuais
- O agente Log Analytics deve ser instalado em conjuntos de escala de máquina virtual
- Implemente pré-requisitos para auditar VMs do Windows em que o agente Log Analytics não está ligado como esperado
- Mostre os resultados da auditoria dos VMs do Windows nos quais o agente Log Analytics não está ligado como esperado

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Ativar alertas para atividades anómalas

- Nível de preços padrão do Centro de Segurança deve ser selecionado
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' em definições avançadas de segurança de dados geridas pela SQL

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centralize a exploração anti-malware

- Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente assinaturas de proteção
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Manter um inventário das contas administrativas

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Utilizar contas administrativas dedicadas

- Implemente pré-requisitos para auditar Os VMs do Windows em que o grupo administradores não contenha apenas os membros especificados
- Mostrar os resultados da auditoria dos VMs do Windows em que o grupo administradores não contém apenas os membros especificados
- Implemente pré-requisitos para auditar Os VMs do Windows em que o grupo administradores não contenha todos os membros especificados
- Mostrar os resultados da auditoria dos VMs do Windows nos quais o grupo administradores não contém todos os membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Utilize a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Log e alerta sobre atividade suspeite a partir de contas administrativas

- Nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="39-use-azure-active-directory"></a>3.9 Utilizar o Diretório Ativo Azure

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente
- Certifique-se de que o Register with Azure Ative Directory está ativado na aplicação API
- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na Web App
- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na App de Funções

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Rever regularmente e conciliar regularmente o acesso ao utilizador

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Manter um inventário de informação sensível

- Os dados sensíveis nas suas bases de dados SQL devem ser classificados

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Criptografe todas as informações sensíveis em trânsito

- A transferência segura para contas de armazenamento deve ser ativada
- A versão TLS mais recente deve ser utilizada na sua app API
- A versão TLS mais recente deve ser utilizada na sua Web App
- A versão TLS mais recente deve ser usada na sua App de Funções
- App de funções só deve ser acessível através de HTTPS
- Aplicação Web só deve ser acessível em HTTPS
- A Aplicação API só deve ser acessível em HTTPS
- Impor a ligação SSL deve ser ativada para servidores de base de dados MySQL
- Impor a ligação SSL deve ser ativada para servidores de base de dados PostgreSQL
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

- Os dados sensíveis nas suas bases de dados SQL devem ser classificados
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Utilizar o Azure RBAC para controlar o acesso aos recursos

- O Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos serviços kubernetes
- Utilização de auditoria de regras rbac personalizadas

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Encriptar informações sensíveis em repouso

- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Os discos não ligados devem ser encriptados
- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave
- SQL gerido exemplo TDE protetor deve ser encriptado com a sua própria chave
- Variáveis da conta de automação devem ser encriptadas
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Log e alerta sobre alterações aos recursos críticos do Azure

- O Monitor Azure deve recolher registos de atividade sacar registos de atividades de todas as regiões

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Executar ferramentas de digitalização automática de vulnerabilidades

- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Avaliação de vulnerabilidade de pré-visualização\] deve ser ativada em máquinas virtuais
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Implementar solução automatizada de gestão de patch do sistema operativo

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas
- Certifique-se de que a versão '.NET Framework' é a mais recente, se utilizada como parte da App de Funções
- Certifique-se de que a versão '.NET Framework' é a mais recente, se utilizada como parte da aplicação Web
- Certifique-se de que a versão '.NET Framework' é a mais recente, se utilizada como parte da aplicação DaPI

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Implementar solução automatizada de gestão de patch de software de terceiros

- Certifique-se de que 'versão PHP' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'versão PHP' é a mais recente, se usada como parte da aplicação WEB
- Certifique-se de que 'versão PHP' é a mais recente, se utilizada como parte da aplicação Fun
- Certifique-se de que 'versão Java' é a mais recente, se usada como parte da aplicação Web
- Certifique-se de que 'versão Java' é a mais recente, se usada como parte da aplicação Fun
- Certifique-se de que 'versão Java' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Web
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Fun
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da app Api
- Os serviços kubernetes devem ser atualizados para uma versão kubernetes não vulnerável

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas

## <a name="68-use-only-approved-applications"></a>6.8 Utilize apenas pedidos aprovados

- Nível de preços padrão do Centro de Segurança deve ser selecionado
- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="69-use-only-approved-azure-services"></a>6.9 Utilize apenas serviços Azure aprovados

- Máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Do Azure
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos do Azure

## <a name="610-implement-approved-application-list"></a>6.10 Implementar lista de candidaturas aprovada

- Nível de preços padrão do Centro de Segurança deve ser selecionado
- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Manter configurações seguras de recursos Azure

- \[Pré-visualização\]: As Políticas de Segurança do Pod devem ser definidas nos Serviços Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Manter configurações seguras do sistema operativo

- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementar monitorização automatizada de configuração para os serviços Do Azure

- \[Pré-visualização\]: As Políticas de Segurança do Pod devem ser definidas nos Serviços Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementar monitorização automatizada de configuração para sistemas operativos

- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas

## <a name="711-manage-azure-secrets-securely"></a>7.11 Gerir os segredos do Azure de forma segura

- Objetos do cofre da chave devem ser recuperáveis

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Gerir as identidades de forma segura e automática

- A identidade gerida deve ser utilizada na sua App de Funções
- A identidade gerida deve ser utilizada na sua Web App
- A identidade gerida deve ser utilizada na sua App API

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Utilize software anti-malware gerido centralmente

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Ficheiros pré-digitalizados a serem carregados para recursos não computacionais do Azure

- Nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Garantir que software anti-malware e assinaturas são atualizados

- Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente assinaturas de proteção

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Garantir back ups automáticos regulares

- Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL
- Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL
- Backup geo-redundante deve ser ativado para Base de Dados Azure para PostgreSQL
- Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB
- Backup Azure deve ser ativado para máquinas virtuais

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Execute cópias de segurança completas do sistema e cópia de segurança de quaisquer chaves geridas pelo cliente

- Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL
- Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL
- Backup geo-redundante deve ser ativado para Base de Dados Azure para PostgreSQL
- Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB
- Backup Azure deve ser ativado para máquinas virtuais

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

- Objetos do cofre da chave devem ser recuperáveis

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Criar um procedimento de pontuação e priorização de incidentes

- Nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

- Um endereço de e-mail de contacto de segurança deve ser fornecido para a sua subscrição
- Um número de telefone de contato de segurança deve ser fornecido para a sua subscrição
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- As definições avançadas de segurança de dados para a instância gerida pela SQL devem conter um endereço de e-mail para receber alertas de segurança
- As notificações de e-mail para administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados do servidor SQL
- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados geridas pela SQL

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto Azure Security Benchmark, visite a Política Azure no portal Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).