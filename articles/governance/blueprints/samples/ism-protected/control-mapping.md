---
title: Visão geral da amostra de planta protegida do governo australiano
description: Visão geral da amostra de plantas protegidas do Governo australiano. Esta amostra de plantas ajuda os clientes a avaliar controlos protegidos ism específicos.
ms.date: 03/10/2020
ms.topic: sample
ms.openlocfilehash: e2f64943e9db207feb3f59a905cad994553a6cd6
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595032"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Mapeamento de controlo da amostra de plantas protegidas do Governo australiano

O seguinte artigo detalha como o Azure Blueprints Australian Government ISM PROTEGEu os mapas de amostras de plantas para os controlos protegidos ism. Para obter mais informações sobre os controlos, consulte [ISM PROTECTED](https://www.cyber.gov.au/ism).

Os seguintes mapeamentos são para os controlos **PROTEGIDOS ISM.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a ** \[Pré-visualização\]: Audite os controlos PROTEGIDOS DO GOVERNO Australiano e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo.
> Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Restrições de Localização

Este plano ajuda-o a restringir a localização para a implantação de todos os recursos e grupos de recursos para "Australia Central", "Australia Central2", "Australia East" e "Australia Southeast" atribuindo as seguintes definições de Política Azure:

- Locais permitidos (tem sido codificado duramente para "Australia Central", "Australia Central2", "Australia East" e "Australia Southeast")
- Locais permitidos para grupos de recursos (foi codificado duramente para "Australia Central", "Australia Central2", "Australia East" e "Australia Southeast")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Orientações para a Segurança do Pessoal - Acesso aos sistemas e seus recursos

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 O pessoal que concedeu acesso a um sistema e os seus recursos são identificáveis de forma única

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 O acesso padrão a sistemas, aplicações e repositórios de dados limita-se ao necessário para que o pessoal assuma as suas funções

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição
- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 O acesso privilegiado a sistemas, aplicações e repositórios de dados é validado quando solicitado e revalidado anualmente ou mais frequente

- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 O acesso privilegiado a sistemas, aplicações e repositórios de dados limita-se ao necessário para que o pessoal assuma as suas funções

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição
- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 A utilização de contas de utilizador partilhadas é rigorosamente controlada, e o pessoal que utiliza essas contas é exclusivamente identificável

- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 Os utilizadores privilegiados recebem uma conta privilegiada dedicada a ser utilizada exclusivamente para tarefas que exijam acesso privilegiado

- Mostrar resultados de auditoria de VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 O acesso a sistemas, aplicações e repositórios de dados é removido ou suspenso no mesmo dia em que o pessoal já não tem um requisito legítimo de acesso

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 Quando o pessoal tem acesso temporário a um sistema, são implementados controlos de segurança eficazes para restringir o seu acesso apenas às informações necessárias para que exervam as suas funções.

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Diretrizes para endurecimento do sistema - Endurecimento do sistema operativo

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 A versão mais recente (N), ou N-1, de um sistema operativo é utilizada para ambientes operacionais standard (SOEs)

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 Contas de sistema operativo desnecessários, software, componentes, serviços e funcionalidades são removidas ou desativadas

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 Uma solução de whitelisting de aplicação é implementada em todos os servidores para restringir a execução de executáveis, bibliotecas de software, scripts e instaladores para um conjunto aprovado

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 O software Antivírus é implementado em estações de trabalho e servidores e configurado com: deteção baseada em assinaturas ativada e definida para um alto nível, deteção baseada em heurística habilitada e definida para um alto nível, assinaturas de deteção verificadas para moeda e atualizadas em pelo menos uma base diária, digitalização automática e regular configurada para todos os discos fixos e meios amovíveis

- Extensão Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Diretrizes para endurecimento do sistema - Endurecimento da autenticação

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Os utilizadores são autenticados antes de lhes ser concedido o acesso a um sistema e aos seus recursos

- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs Linux que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que têm contas sem senhas
- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs Linux que tenham contas sem senhas

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 A autenticação multifactor é usada para autenticar utilizadores padrão

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 A autenticação multifactor é usada para autenticar todos os utilizadores privilegiados e quaisquer outras posições de confiança

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 As frases-passe utilizadas para a autenticação de um único fator são um mínimo de 14 caracteres com complexidade, idealmente como 4 palavras aleatórias

- \[Pré-visualização\]: Mostrar os resultados da auditoria das configurações dos VMs do Windows em 'Definições de Segurança - Políticas de Conta'
- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Definições de Segurança - Políticas de Conta'

## <a name="guidelines-for-system-management---system-administration"></a>Orientações para gestão de sistemas - Administração do sistema

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 A autenticação multifactor é usada para autenticar utilizadores sempre que realizam ações privilegiadas

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 O tráfego de gestão só é permitido originário de zonas de rede que são utilizadas para administrar sistemas e aplicações

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- Depuração remota deve ser desligada para Apps API
- Depuração remota deve ser desligada para aplicações de função
- Depuração remota deve ser desligada para aplicações web

## <a name="guidelines-for-system-management---system-patching"></a>Diretrizes para gestão de sistemas - Correção de sistemas

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 Vulnerabilidades de segurança em aplicações e condutores avaliados como risco extremo são corrigidos, atualizados ou atenuados no prazo de 48 horas após as vulnerabilidades de segurança serem identificadas por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 Vulnerabilidades de segurança em aplicações e motoristas avaliados como de alto risco são corrigidos, atualizados ou atenuados no prazo de duas semanas após a vulnerabilidade de segurança ser identificada por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 Vulnerabilidades de segurança em aplicações e condutores avaliados como risco moderado ou baixo são corrigidos, atualizados ou atenuados no prazo de um mês após a vulnerabilidade de segurança ser identificada por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 Vulnerabilidades de segurança em sistemas operativos e firmware avaliados como risco extremo são corrigidos, atualizados ou atenuados no prazo de 48 horas após as vulnerabilidades de segurança serem identificadas por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 Vulnerabilidades de segurança em sistemas operativos e firmware avaliados como de alto risco são corrigidos, atualizados ou atenuados no prazo de duas semanas após a vulnerabilidade de segurança ser identificada por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 As vulnerabilidades de segurança em sistemas operativos e firmware avaliados como moderados ou de baixo risco são corrigidos, atualizados ou atenuados no prazo de um mês após a vulnerabilidade de segurança ser identificada por fornecedores, terceiros independentes, gestores de sistemas ou utilizadores

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As definições de avaliação de vulnerabilidade para o servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Diretrizes para Gestão de Sistemas - Backup de dados e restauro

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 Cópias de segurança de informações importantes, configurações de software e configuração são realizadas pelo menos diariamente

- Auditar máquinas virtuais sem recuperação de desastres configurada

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Diretrizes para monitorização do sistema - Registo de eventos e auditoria

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 Uma instalação de exploração madeireira centralizada é implementada e os sistemas são configurados para salvar os registos de eventos para a instalação de exploração madeireira centralizada o mais rapidamente possível após cada evento ocorrer

- As subscrições do Azure devem ter um perfil de registo para o Registo de Atividades

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 Os seguintes eventos são registados para sistemas operativos: acesso a dados e processos importantes, falhas de aplicações e quaisquer mensagens de erro, tentativas de utilização de privilégios especiais, alterações nas contas, alterações na política de segurança, alterações nas configurações do sistema, Sistema de Nomede Domínio (DNS) e Pedidos de Protocolo de Transferência de Hipertextos (HTTP), tentativas falhadas de acesso a dados e recursos do sistema, falhas de serviço e reinícios, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema e encerramento, arranque do sistema , transferência de dados para meios externos, gestão de utilizadores ou grupos, utilização de privilégios especiais

- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não listada
- \[Pré-visualização\]: Audit Log Analytics Workspace for VM - Report Mismatch
- Definição de diagnóstico de auditoria

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 Os seguintes eventos são registados para bases de dados: acesso a informações particularmente importantes, adição de novos utilizadores, especialmente utilizadores privilegiados, qualquer consulta que contenha comentários, qualquer consulta que contenha múltiplas consultas incorporadas, quaisquer alertas ou falhas de base de dados, tentativas de elevar privilégios, tentativade acesso que seja bem sucedido ou mal sucedido, alterações na estrutura da base de dados, alterações nas funções de utilizador ou permissões de base de dados, ações de administrador de base de dados , logotipos de base de dados e logoffs, modificações de dados, utilização de comandos executáveis

- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Definição de diagnóstico de auditoria
- A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Diretrizes para monitorização do sistema - Gestão de vulnerabilidades

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 Avaliações de vulnerabilidade e testes de penetração são realizados por pessoal devidamente qualificado antes de um sistema ser implementado, após uma alteração significativa para um sistema, e pelo menos anualmente ou conforme especificado pelo proprietário do sistema

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- \[Pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas

## <a name="guidelines-for-database-systems-management---database-servers"></a>Diretrizes para Gestão de Sistemas de Base de Dados - Servidores de bases de dados

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 Discos rígidos de servidores de base de dados são encriptados usando encriptação completa do disco

- A encriptação do disco deve ser aplicada em máquinas virtuais
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 A informação comunicada entre servidores de base de dados e aplicações web é encriptada

- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- A transferência segura para contas de armazenamento deve ser ativada
- Mostre resultados de auditoria de servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Implemente pré-requisitos para auditar servidores web do Windows que não estão a usar protocolos de comunicação seguros

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Diretrizes para gestão de sistemas de base de dados - Software de sistema de gestão de bases de dados

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Contas de administrador de base de dados predefinidas são desativadas, renomeadas ou têm as suas palavras-passe alteradas

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 Administradores de bases de dados têm contas únicas e identificáveis

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 Contas de administrador de base de dados não são partilhadas em diferentes bases de dados

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 As contas de administrador de base de dados são utilizadas exclusivamente para tarefas administrativas, com contas de base de dados padrão utilizadas para interações gerais com base de dados

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 O acesso do administrador de base de dados é restrito a funções definidas em vez de contas com permissões administrativas por defeito, ou todas as permissões

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Diretrizes para a utilização da criptografia - fundamentos criptográficos

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 Software de encriptação usado para dados em repouso implementa encriptação completa do disco, ou encriptação parcial onde os controlos de acesso apenas permitirão a escrita para a partição encriptada

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Diretrizes para a utilização da criptografia - Segurança da Camada de Transporte

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 Apenas a versão mais recente do TLS é usada

- A versão TLS mais recente deve ser utilizada na sua app API
- A versão TLS mais recente deve ser utilizada na sua Web App
- A versão TLS mais recente deve ser usada na sua App de Funções
- Implemente pré-requisitos para auditar servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Mostre resultados de auditoria de servidores web do Windows que não estão a usar protocolos de comunicação seguros

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Diretrizes para transferências de dados e filtragem de conteúdos - Filtragem de conteúdo

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 A varredura antivírus, utilizando vários motores de digitalização diferentes, é realizada em todos os conteúdos

- Extensão Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Diretrizes para transferências de dados e filtragem de conteúdos - desenvolvimento de aplicações web

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 Todos os conteúdos de aplicação web são oferecidos exclusivamente usando HTTPS

- App de funções só deve ser acessível através de HTTPS
- A Aplicação API só deve ser acessível em HTTPS
- Aplicação Web só deve ser acessível em HTTPS
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Controlos de segurança baseados no navegador web são implementados para aplicações web de forma a ajudar a proteger tanto as aplicações web como os seus utilizadores

- O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Diretrizes para gestão de rede - Design e configuração de rede

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 Os controlos de acesso à rede são implementados em redes para impedir a ligação de dispositivos de rede não autorizados

- Auditoria sem restrições ao acesso da rede às contas de armazenamento

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 São implementados controlos de acesso à rede para limitar o tráfego dentro e entre segmentos de rede apenas aos que são necessários para fins comerciais

- As máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança da rede
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Recomendações adapttivas de endurecimento da rede devem ser aplicadas na Internet viradas para máquinas virtuais

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Orientações para gestão de redes - continuidade de serviços para serviços online

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 São discutidas estratégias de prevenção e mitigação de ataques de negação de serviços com prestadores de serviços, especificamente: a sua capacidade de resistir a ataques de negação de serviço, quaisquer custos suscetíveis de serem incorridos pelos clientes resultantes de ataques de negação de serviço, limiares para notificar os clientes ou desligar os seus serviços online durante ataques de negação de serviço, ações pré-aprovadas que podem ser realizadas durante ataques de negação de serviço, acordos de prevenção de ataques de negação de serviço com fornecedores a montante para bloquear o tráfego malicioso o mais alto possível

- Norma de proteção DDoS deve ser ativada


> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).