---
title: Controlos de amostras de plantas de mídia
description: Controle o mapeamento das amostras da planta dos media. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201907"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapeamento de controlo da amostra de projeto dos Media

O seguinte artigo detalha como o projeto de amostra sinuoso do Azure Blueprints Media mapeia os controlos dos media. Para obter mais informações sobre os controlos, consulte [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os seguintes mapeamentos são para os controlos dos meios de **comunicação.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a ** \[Preview\]: Audit Media controla** iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Controlo de Acesso

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Certifique-se de que não existe chave de acesso à raiz

- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não contenham os certificados especificados em Raiz fidedigna

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - Palavras-passe, PINs e Tokens devem ser protegidos

- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não limitem o comprimento mínimo da palavra-passe a 14 caracteres

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - O acesso a conta partilhada é proibido

- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nome do Autocarro de Serviço

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -Sistema deve restringir o acesso aos utilizadores autorizados.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 - O sistema deve impor direitos de acesso.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Atribuição de Direitos de Utilizador'

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 - Impedir o acesso não autorizado a informações ou funções relevantes para a segurança.

- \[Pré-visualização\]: Mostrar os resultados da auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Definições do Sistema'

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - A separação de funções deve ser executada através de uma atribuição adequada de funções.

- [Pré-visualização\]: Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos serviços kubernetes

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40- Certifique-se de que os sistemas não estão a ligar redes fidedignas e redes não fidedignas ao mesmo tempo.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Acesso à Rede'

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 - O acesso remoto a não empregados deve ser restrito para permitir o acesso apenas a sistemas de informação especificamente aprovados

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que permitem ligações remotas a partir de contas sem senhas

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50- Registar eventos relacionados com a segurança de todos os componentes do sistema de informação.

- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54- Garantir que a autenticação de vários fatores (MFA) está ativada para todos os utilizadores de consolas em nuvem.

- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma violação de contas ou recursos.

## <a name="auditing--logging"></a>Auditoria & Logging

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1- Eventos bem sucedidos e mal sucedidos devem ser registados.

- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - Os dispositivos/instâncias de rede devem registar qualquer evento classificado como um evento de segurança crítico por esse dispositivo/instância de rede (ELBs, firewalls de aplicação web, etc.)

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Contas'

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- Servidores/instâncias devem registar qualquer evento classificado como um evento de segurança crítico por esse servidor/instância

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Contas'

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Os eventos de domínio devem registar qualquer evento classificado como um evento crítico ou de alta segurança pelo software de gestão de domínios

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Contas'
- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Cliente da Rede Microsoft'

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20- Os eventos de domínio devem registar qualquer evento classificado como um evento de segurança crítico por controlos de segurança de domínio

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Contas'

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- Os eventos de domínio devem registar qualquer acesso ou alterações ao registo de domínio

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Opções de Segurança - Consola de recuperação'

## <a name="cryptographic-controls"></a>Controlos Criptográficos

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2- As aplicações e sistemas devem utilizar soluções criptográficas atuais para proteger dados.

- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e satisfazer os requisitos de conformidade

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- Os certificados digitais devem ser assinados por uma Autoridade de Certificados aprovada.

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que contenham certificados que expirem dentro do número especificado de dias

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- Os certificados digitais devem ser atribuídos exclusivamente a um utilizador ou dispositivo.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs do Windows que contenham certificados que expirem dentro do número especificado de dias

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7- O material criptográfico deve ser armazenado para permitir a desencriptação dos registos durante o período de tempo em que os registos são mantidos.

- A encriptação do disco deve ser aplicada em máquinas virtuais
- VMs sem encriptação de disco ativado serão monitorizados pelo Azure Security Center como recomendações

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- As chaves secretas e privadas devem ser guardadas de forma segura.

- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e satisfazer os requisitos de conformidade

## <a name="change--config-management"></a>Alterar & Gestão Config

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Apenas os utilizadores autorizados podem implementar alterações aprovadas no sistema.

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12- Mantenha uma configuração de base atualizada, completa, precisa e prontamente disponível do sistema de informação.

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13- Utilize ferramentas automatizadas para manter uma configuração de base do sistema de informação.

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14- Identificar e desativar funções, portos, protocolos e serviços desnecessários e/ou não seguros.

- As interfaces de rede devem desativar o encaminhamento ip
- \[Pré-visualização\]: O reencaminhamento ip da sua máquina virtual deve ser desativado

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19- Monitoraltera as definições de configuração de segurança.

- Implementar definições de diagnóstico para grupos de segurança de rede

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22- Certifique-se de que apenas software autorizado e atualizações são instalados nos sistemas da Empresa.

- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações

## <a name="identity--authentication"></a>Autenticação & de identidade

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- As contas dos utilizadores devem ser exclusivamente atribuídas a indivíduos para acesso a informações que não sejam classificadas como Públicas. Os IDs de conta devem ser construídos utilizando um formato lógico padronizado.

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado.

## <a name="network-security"></a>Segurança de Rede

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2- O acesso à funcionalidade de gestão de dispositivos de rede está restrito aos utilizadores autorizados.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Acesso à Rede'

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Todos os dispositivos de rede devem ser configurados utilizando as suas configurações mais seguras.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Acesso à Rede'

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5- Todas as ligações de rede a um sistema através de uma firewall devem ser aprovadas e auditadas regularmente.

- \[Pré-visualização\]: Mostrar resultados de auditoria das configurações dos VMs do Windows em 'Windows Firewall Properties'

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Os controlos adequados devem estar presentes em qualquer fronteira entre uma rede fidedigna e qualquer rede não confiável ou pública.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Windows Firewall Properties'

## <a name="security-planning"></a>Planeamento de Segurança

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3- Devem ser identificadas ameaças que possam ter um impacto negativo na confidencialidade, integridade ou disponibilidade de informações e conteúdos da Empresa, juntamente com a probabilidade da sua ocorrência.

- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' em definições avançadas de segurança de dados geridas pela SQL

### <a name="security-continuity"></a>Continuidade de Segurança

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5- Os dados relativos ao armazenamento a longo prazo devem ser acessíveis durante todo o período de retenção e protegidos contra a degradação dos meios de comunicação social e as alterações tecnológicas.

- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.
- Audite servidores SQL configurados com um período de retenção de auditoria inferior a 90 dias.

## <a name="system-integrity"></a>Integridade do sistema

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3- Só o pessoal autorizado pode monitorizar as atividades da rede e do utilizador.

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Monitorize os resultados e recomendações da Avaliação de Vulnerabilidades sobre como remediar as vulnerabilidades da base de dados.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4- Os sistemas virados para a Internet devem ter deteção de intrusões.

- Implementar deteção de ameaças em servidores SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - O software anti-malware gerido centralmente por padronização deve ser implementado em toda a empresa.

- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- O software anti-malware deve digitalizar computadores e meios de comunicação semanalmente no mínimo.

- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4- Certifique-se de que as aplicações são digitalizadas para vulnerabilidades mensalmente.

- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Audite as vulnerabilidades do Snos nos seus conjuntos de escala de máquinavirtual para protegê-las de ataques.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5- Certifique-se de que as vulnerabilidades são identificadas, emparelhadas com ameaças e avaliadas para o risco.

- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Audite as vulnerabilidades do Snos nos seus conjuntos de escala de máquinavirtual para protegê-las de ataques.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6- Certifique-se de que as vulnerabilidades identificadas foram remediadas dentro de uma linha temporal mutuamente acordada.

- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Audite as vulnerabilidades do Snos nos seus conjuntos de escala de máquinavirtual para protegê-las de ataques.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7- O acesso e utilização de sistemas de gestão de vulnerabilidades deve ser restrito ao pessoal autorizado.

- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- Audite as vulnerabilidades do Snos nos seus conjuntos de escala de máquinavirtual para protegê-las de ataques.

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Revistei o mapeamento de controlo da amostra de projeto dos Media. Em seguida, visite os seguintes artigos para saber sobre a visão geral e como implementar esta amostra:

> [!div class="next step action"]
> [Projeto de mídia - Visão geral](./control-mapping.md)
> [Modelo de Mídia - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
