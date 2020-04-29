---
title: Controlos de amostras de plantas SWIFT CSP-CSCF v2020
description: Mapeamento de controlo da amostra de plantas SWIFT CSP-CSCF v2020. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 09/24/2019
ms.topic: sample
ms.openlocfilehash: 1826e4820f06fc3a858ce6cd5f8906baaa9808cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74546516"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Mapeamento de controlo da amostra de plantas SWIFT CSP-CSCF v2020

O seguinte artigo detalha como os mapas de amostras de plantas SWIFT CSP-CSCF v2020 do Azure Blueprints SWIFT CSP-CSCF v2020 para os comandos SWIFT CSP-CSCF v2020. Para obter mais informações sobre os controlos, consulte [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Os seguintes mapeamentos são para os comandos **SWIFT CSP-CSCF v2020.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os ** \[controlos\]Preview : Audit SWIFT CSP-CSCF v2020 e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 e 5.1 Gestão de Conta

Este plano ajuda-o a rever contas que podem não cumprir os requisitos de gestão de conta da sua organização. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) que auditam contas externas com permissões de leitura, escrita e proprietário numa subscrição e contas depreciadas. Ao rever as contas auditadas por estas políticas, pode tomar as medidas adequadas para garantir que os requisitos de gestão de contas sejam cumpridos.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 e 6.5A Gestão de Contas [ Gestão de Conta] Esquemas baseados em papéis

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Esta planta atribui também definições de [Política Azure](../../../policy/overview.md) para auditar a autenticação do Diretório Ativo Azure para servidores SQL e Tecido de Serviço. A utilização da autenticação do Diretório Ativo Azure permite a gestão simplificada da permissão e a gestão centralizada da identidade dos utilizadores de bases de dados e de outros serviços da Microsoft. Além disso, este projeto atribui uma definição de Política Azure para auditar a utilização de regras rbac personalizadas. Compreender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do RBAC são propensas a erros.

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Utilização de auditoria de regras rbac personalizadas
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Gestão de Conta [ Gestão de Contas] Monitorização da Conta / Utilização Atípica

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. Todos os pedidos de Acesso a Máquinas Virtuais do JIT estão registados no Registo de Atividade, permitindo-lhe monitorizar para utilização atípica. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 e 6.4 Separação de direitos

Ter apenas um proprietário de assinatura Azure não permite o despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições do Azure pode aumentar o potencial de violação através de uma conta de proprietário comprometida. Este plano ajuda-o a manter um número adequado de proprietários de subscrições azure, atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam o número de proprietários para subscrições do Azure. Esta planta também atribui definições de Política Azure que o ajudam a controlar a adesão do grupo de Administradores em máquinas virtuais Windows. Gerir o proprietário da subscrição e permissões de administrador de máquinas virtuais pode ajudá-lo a implementar a separação adequada de deveres.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Mostrar os resultados da auditoria dos VMs do Windows nos quais o grupo administradores não contém todos os membros especificados
- Implemente pré-requisitos para auditar Os VMs do Windows em que o grupo administradores não contenha todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 e 6.4 Menos Privilégios Revisão dos Privilégios do Utilizador

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão. Rever estes indicadores de conta pode ajudá-lo a garantir que os controlos de menor privilégio sejam implementados.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Mostrar os resultados da auditoria dos VMs do Windows nos quais o grupo administradores não contém todos os membros especificados
- Implemente pré-requisitos para auditar Os VMs do Windows em que o grupo administradores não contenha todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="22-and-27-security-attributes"></a>2.2 e 2.7 Atributos de Segurança

A capacidade de descoberta e classificação de dados avançados para a Base de Dados Azure SQL fornece capacidades para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites. A segurança avançada de dados pode ajudá-lo a garantir informações como associados aos atributos de segurança apropriados para a sua organização. Esta planta atribui definições de [Política Azure](../../../policy/overview.md) para monitorizar e impor a utilização da segurança avançada de dados no servidor SQL. 

- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 e 6.1 Acesso remoto [ Monitorização/Controlo Automatizado

Esta planta ajuda-o a monitorizar e controlar o acesso remoto atribuindo definições de [Política Azure](../../../policy/overview.md) para monitorizar que a depuração remota para aplicação do Serviço de Aplicações Azure é desligada e definições de políticas que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem senhas. Esta planta também atribui uma definição de Política Azure que o ajuda a monitorizar o acesso ilimitado a contas de armazenamento. Monitorizar estes indicadores pode ajudá-lo a garantir que os métodos de acesso remoto satisfaçam a sua política de segurança.

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs Linux que permitem ligações remotas a partir de contas sem senhas
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Depuração remota deve ser desligada para App API
- Depuração remota deve ser desligada para app de função
- Depuração remota deve ser desligada para aplicação web

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 e 6.4 Conteúdo dos Registos de Auditoria [ Gestão Centralizada de Conteúdos De Registo de Auditoria Planeada

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho de Log Analytics que permite a configuração e gestão centralizadas. Este plano ajuda-o a garantir que os eventos são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala seletivas VM Do Windows (VMSS)
- \[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 e 6.4 Resposta a falhas de processamento de auditoria

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que monitorizam as configurações de auditoria e registo de eventos. Monitorizar estas configurações pode fornecer um indicador de uma falha ou configuração errada do sistema de auditoria e ajudá-lo a tomar medidas corretivas.

- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Definição de diagnóstico de auditoria
- Implementar auditoria no servidor SQL

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 e 6.4 Revisão, Análise e Relatório de Auditoria Análise e Análise Central

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho de Log Analytics que permite reportagens e análises centralizadas. Este plano ajuda-o a garantir que os eventos são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala seletivas VM Do Windows (VMSS)
- \[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 e 6.5A Geração de Auditoria

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam definições de registo nos recursos do Azure. Estas definições de políticas auditam e impõem a implantação do agente Log Analytics em máquinas virtuais Azure e configuração de definições de auditoria para outros tipos de recursos Azure. Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre as operações que são realizadas dentro dos recursos do Azure. Adicionalmente, a auditoria e a Segurança Avançada de Dados estão configuradas nos servidores SQL.

- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala seletivas VM Do Windows (VMSS)
- \[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows
- Definição de diagnóstico de auditoria
- Audite as definições de auditoria ao nível do servidor SQL
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- Implementar auditoria sql
- Implementar definições de diagnóstico para grupos de segurança de rede

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Menor funcionalidade [ Impedir a execução do programa

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo da aplicação pode ser executado num modo de execução que proíbe a aplicação não aprovada de funcionar. Esta planta atribui uma definição de Política Azure que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Menor funcionalidade [ Software autorizado / Whitelisting

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo de aplicações ajuda-o a criar listas de aplicações aprovadas para as suas máquinas virtuais. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="11-user-installed-software"></a>1.1 Software instalado pelo utilizador

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo de aplicações pode ajudá-lo a impor e monitorizar o cumprimento das políticas de restrição de software. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identificação e Autenticação (Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizaciona Acesso à Rede a Contas Privilegiadas

Este plano ajuda-o a restringir e controlar o acesso privilegiado através da atribuição de definições de [Política Azure](../../../policy/overview.md) para auditar contas com o proprietário e/ou escrever permissões que não tenham autenticação multifactor ativada. A autenticação de vários fatores ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação seja comprometida. Ao monitorizar contas sem autenticação multifactor ativada, pode identificar contas que possam ser mais suscetíveis de serem comprometidas.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de escrita na sua subscrição

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identificação e Autenticação (Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizacionais) [ Utilizadores Organizaciona Acesso à Rede a Contas Não Privilegiadas

Este plano ajuda-o a restringir e controlar o acesso atribuindo uma definição de [Política Azure](../../../policy/overview.md) para auditar contas com permissões de leitura que não tenham autenticação multifactor ativada. A autenticação de vários fatores ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação seja comprometida. Ao monitorizar contas sem autenticação multifactor ativada, pode identificar contas que possam ser mais suscetíveis de serem comprometidas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="23-and-41-authenticator-management"></a>2.3 e 4.1 Gestão do Autenticador

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem senhas e/ou têm permissões incorretas definidas no ficheiro passwd. Esta planta também atribui definições de política que auditam a configuração do tipo de encriptação de palavra-passe para máquinas virtuais windows. Monitorizar estes indicadores ajuda-o a garantir que os autenticadores do sistema cumprem a política de identificação e autenticação da sua organização.

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que não têm as permissões de ficheiro sinuosas definidas para 0644
- \[Pré-visualização\]: Implementar requisitos para auditar VMs Linux que não tenham as permissões de ficheiro sinuosas definidas para 0644
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que têm contas sem senhas
- \[Pré-visualização\]: Implementar requisitos para auditar VMs Linux que tenham contas sem senhas
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não armazenam palavras-passe usando encriptação reversível
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 e 4.1 Gestão do Autenticador / Autenticação baseada em palavra-passe

Este plano ajuda-o a impor senhas fortes atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. A consciencialização das máquinas virtuais em violação da política de força da palavra-passe ajuda-o a tomar medidas corretivas para garantir que as palavras-passe de todas as contas de utilizadores de máquinas virtuais cumpram a política de passwords da sua organização.

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que permitem a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não têm uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não têm uma idade mínima de senha de 1 dia
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não armazenam palavras-passe usando encriptação reversível
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que permitam a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não tenham uma idade mínima de senha de 1 dia
- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não limitem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não armazenem palavras-passe usando encriptação reversível

## <a name="22-and-27-vulnerability-scanning"></a>2.2 e 2.7 Digitalização de vulnerabilidade

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos azure implantados. Esta planta também atribui definições de política que auditam e impõem segurança avançada de dados em servidores SQL. A segurança avançada de dados incluiu avaliação de vulnerabilidade e capacidades avançadas de proteção contra ameaças para ajudá-lo a entender vulnerabilidades nos seus recursos implantados.

- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas 
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas

## <a name="13-denial-of-service-protection"></a>1.3 Proteção de Serviço

O nível padrão de negação de serviço distribuído da Azure (DDoS) fornece funcionalidades adicionais e capacidades de mitigação sobre o nível de serviço básico. Estas funcionalidades adicionais incluem a integração do Monitor Azure e a capacidade de rever relatórios de mitigação pós-ataque. Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que audita se o nível padrão DDoS estiver ativado. Compreender a diferença de capacidade entre os níveis de serviço pode ajudá-lo a selecionar a melhor solução para lidar com a negação das proteções de serviço para o seu ambiente Azure.

- Norma de proteção DDoS deve ser ativada

## <a name="11-and-61-boundary-protection"></a>1.1 e 6.1 Proteção de Fronteiras

Este plano ajuda-o a gerir e controlar o limite do sistema atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza as recomendações de endurecimento do grupo de segurança de rede no Azure Security Center. O Azure Security Center analisa os padrões de tráfego da Internet que enfrentam máquinas virtuais e fornece recomendações de regras do grupo de segurança da rede para reduzir a superfície de ataque potencial.
Além disso, este projeto também atribui definições de políticas que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas
- O acesso através da Internet virado para o ponto final deve ser restringido
- Auditoria sem restrições ao acesso da rede às contas de armazenamento

## <a name="29a-boundary-protection--access-points"></a>2.9A Proteção de Fronteiras [ Pontos de Acesso

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. O acesso à máquina virtual JIT ajuda-o a limitar o número de ligações externas aos seus recursos em Azure. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Proteção de Fronteiras [ Serviços Externos de Telecomunicações

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. O acesso à máquina virtual JIT ajuda-o a gerir exceções à sua política de fluxo de tráfego facilitando os processos de pedido de acesso e aprovação. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A e 2.6 Confidencialidade e Integridade da Transmissão ; Proteção Física Criptográfica ou Alternativa

Esta planta ajuda-o a proteger a confidencialidade e integridade das informações transmitidas, atribuindo definições de [Política Azure](../../../policy/overview.md) que o ajudam a monitorizar o mecanismo criptográfico implementado para protocolos de comunicações. Garantir que as comunicações estão devidamente encriptadas pode ajudá-lo a satisfazer os requisitos da sua organização ou proteger informações contra divulgação e modificação não autorizadas.

- A Aplicação API só deve ser acessível em HTTPS
- Mostre resultados de auditoria de servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Implemente pré-requisitos para auditar servidores web do Windows que não estão a usar protocolos de comunicação seguros
- App de funções só deve ser acessível através de HTTPS
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- A transferência segura para contas de armazenamento deve ser ativada
- Aplicação Web só deve ser acessível em HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 e 2.7 Proteção de Informação em Repouso [ Proteção Criptográfica

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos para proteger a informação em repouso, atribuindo definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e uso de auditoria de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ótimas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos estão configurados de acordo com a sua política de segurança de informação. Especificamente, as definições de política atribuídas por esta planta requerem encriptação para contas de armazenamento de data lake; requerem encriptação transparente de dados nas bases de dados SQL; e auditar a encriptação em falta nas bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação.

- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- Implementar encriptação transparente de dados SQL DB
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 e 2.7 Reparação de Falhas

Esta planta ajuda-o a gerir falhas no sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos azure implantados. Este projeto também atribui uma definição de política que garante a correção do sistema operativo para conjuntos de escala de máquinas virtuais.

- Remendar a imagem automática do OS em conjuntos de escala de máquina virtual
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas suas máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- As vulnerabilidades na configuração de segurança das suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas

## <a name="61-malicious-code-protection"></a>6.1 Proteção de Código malicioso

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo proteção de código sucucionada, atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais windows.

- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="61-malicious-code-protection--central-management"></a>6.1 Proteção de Código maliciosos Gestão Central

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo proteção de código sucucionada, atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades centralizadas de gestão e reporte que lhe permitem ter uma visão em tempo real sobre o estado de segurança dos recursos azure implantados.

- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 e 6.4 Monitorização do Sistema de Informação

Esta planta ajuda-o a monitorizar o seu sistema auditando e aplicando a segurança de registos e dados através dos recursos do Azure. Especificamente, as políticas atribuídas auditoria e aplicação do agente Log Analytics, e definições de segurança melhoradas para bases de dados SQL, contas de armazenamento e recursos de rede. Estas capacidades podem ajudá-lo a detetar comportamentos anómalos e indicadores de ataques para que possa tomar as medidas adequadas.

- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux
- \[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala seletivas VM Do Windows (VMSS)
- \[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- Implementar segurança avançada de dados em servidores SQL
- Implementar auditoria sql
- Implementar o observador da rede quando as redes virtuais são criadas
- Implementar deteção de ameaças em servidores SQL

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 e 2.8 Monitorização do Sistema de Informação [ Analisar tráfego / exfiltração secreta

A Proteção Avançada de Ameaças para armazenamento azure deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas de proteção incluem padrões de acesso anómalos, extratos/uploads anómalos e atividade de armazenamento suspeito. Estes indicadores podem ajudá-lo a detetar a exfiltração secreta de informação.

- Implementar deteção de ameaças em servidores SQL

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto SWIFT CSP-CSCF v2020, visite os seguintes artigos para saber sobre a planta e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Swift CSP-CSCF v2020 blueprint - Visão geral](./index.md)
> [SWIFT CSP-CSCF v2020 - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
