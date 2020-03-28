---
title: Controlos de amostras de plantas HIPAA HITRUST
description: Mapeamento de controlo das amostras de plantas HIPAA HITRUST. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472311"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapeamento de controlo da amostra de plantas HIPAA HITRUST

O seguinte artigo detalha como o projeto de amostra sinuoso do Azure Blueprints HIPAA HITRUST mapeia os controlos HIPAA HITRUST. Para obter mais informações sobre os controlos, consulte [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os seguintes mapeamentos são para os controlos **HITRUST da HIPAA.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a ** \[Pré-Visualização\]: AudithipA A HITRUST controla** a iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controlo contra código malicioso

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo proteção de código sucucionada, atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais windows.

- Implementar extensão antimalware padrão Microsoft IaaS para servidor windows
- Os registos de diagnóstico nas contas do Lote devem ser ativados
- As atualizações de sistema devem ser instaladas nos seus computadores


## <a name="management-of-removable-media"></a>Gestão de Meios Amovíveis

A organização, com base no nível de classificação de dados, regista os meios de comunicação (incluindo portáteis) antes da sua utilização, impõe restrições razoáveis sobre a forma como esses meios de comunicação são utilizados e fornece um nível adequado de proteção física e lógica (incluindo encriptação) para meios de comunicação que contenham informações cobertas até que sejam devidamente destruídos ou desineados.

- Exigir encriptação nas contas da Data Lake Store
- SQL gerido exemplo TDE protetor deve ser encriptado com a sua própria chave
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada


## <a name="information-exchange-policies-and-procedures"></a>Políticas e procedimentos de intercâmbio de informações

Os fornecedores de serviços em nuvem utilizam uma plataforma de virtualização reconhecida pela indústria e formatos de virtualização padrão (por exemplo, Formato de Virtualização Aberta, OVF) para ajudar a garantir a interoperabilidade, e documentou alterações personalizadas feitas a qualquer hipervisor em uso e todos os ganchos de virtualização específicos para soluções disponíveis para revisão do cliente.

- Implementar pré-requisitos para auditar VMs do Windows que não tenham as aplicações especificadas instaladas

## <a name="control-of-operational-software"></a>Controlo do Software Operacional 

A organização identifica software não autorizado no sistema de informação, incluindo servidores, estações de trabalho e portáteis, emprega uma política de permitir tudo, negar por exceção para proibir a execução de software não autorizado conhecido no sistema de informação, e avalia e atualiza periodicamente a lista de software não autorizado, mas não menos do que anualmente.

- \[Preview\] Mostrar resultados da auditoria das configurações dos VMs do Windows em "Opções de Segurança-Auditoria"
- \[Preview\] Mostrar resultados da auditoria das configurações dos VMs do Windows em "System Audit Policies- Account Management"

## <a name="change-control-procedures"></a>Procedimentos de Controlo de Alterações

A integridade de todas as imagens de máquinas virtuais é assegurada a todo o momento através da sessão e do levantamento de um alerta para quaisquer alterações nas imagens de máquinas virtuais, e disponibilizando ao(s) e/ou cliente do empresário através de métodos eletrónicos (por exemplo, portais ou alertas) os resultados de uma alteração ou mudança e a subsequente validação da integridade da imagem.

- \[Preview\] Mostrar resultados da auditoria da configuração dos VMs do Windows em 'System Audit Policies - Tracking Detalhado'

## <a name="inventory-of-assets"></a>Inventário de Ativos 

Um inventário de ativos e serviços é mantido

- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados.
- \[Requisitos de\] implementação de implementação para auditar configurações de VMs do Windows em "Opções de Segurança- Microsoft Network Server"
- \[Requisitos de\] implementação de implementação para auditar configurações de VMs do Windows em "Modelos Administrativos- Rede"

## <a name="control-of-technical-vulnerabilities"></a>Controlo de Vulnerabilidades Técnicas 

Existe um padrão de configuração endurecido para todos os componentes do sistema e da rede.

- Auditar Máquinas Virtuais sem recuperação de desastres configurada
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- Vulnerabilidade deve ser remediada por uma solução de Avaliação de Vulnerabilidades

## <a name="segregation-in-networks"></a>Segregação em Redes

Os gateways de segurança da organização (por exemplo, firewalls) aplicam as políticas de segurança e são configurados para filtrar o tráfego entre domínios, bloquear acesso não autorizado, e são usados para manter a segregação entre fios internos, sem fios internos e externos segmentos de rede (por exemplo, internet) incluindo DMZs e aplicação de políticas de controlo de acesso para cada um dos domínios.

- Fornecimento Automático de Agente de Monitorização de Segurança
- Implementar o observador da rede quando as redes virtuais são criadas

## <a name="input-data-validation"></a>Validação de dados de entrada

Para quaisquer aplicações Web viradas para o público, são implementadas firewalls de nível de aplicação para controlar o tráfego. Para aplicações viradas para o público que não são baseadas na Web, a organização implementou uma firewall baseada em rede específica do tipo de aplicação. Se o tráfego para a aplicação virada para o público for encriptado, o dispositivo ou fica atrás da encriptação ou é capaz de desencriptar o tráfego antes da análise.

- \[Preview\] Mostrar resultados da auditoria da configuração dos VMs do Windows em "Windows Firewall Properties"


## <a name="network-connection-control"></a>Controlo de Ligação de Rede

O tráfego de rede é controlado de acordo com a política de controlo de acesso da organização através de firewall e outras restrições relacionadas com a rede para cada ponto de acesso à rede ou interface gerida pelo serviço de telecomunicações externo.

- O acesso através da Internet a pontos finais virados para a Internet deve ser restringido
- SQL gerido exemplo TDE protetor deve ser encriptado com a sua própria chave
- Depuração remota deve ser desligada para aplicações API

## <a name="network-controls"></a>Controlos de Rede

A organização utiliza canais de comunicação seguros e encriptados quando migra servidores físicos, aplicações ou dados para servidores virtualizados.

- A encriptação do disco deve ser aplicada em VMs
- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave
- \[Preview\] Show audits resulta de configurações de VMs do Windows em "Opções de Segurança- Acesso à Rede"
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- \[Preview\] Show audits resulta da configuração de VMs do Windows em "Windows Firewall Properties"
- Implementar definições de diagnóstico de grupos de segurança da rede
- O acesso através da Internet a pontos finais virados para a Internet deve ser restringido

## <a name="sensitive-system-isolation"></a>Isolamento sensível do sistema

Os recursos do sistema partilhado (por exemplo, registos, memória principal, armazenamento secundário) são devolvidos ao sistema, protegidos da divulgação a outros sistemas/aplicações/utilizadores, e os utilizadores não podem aceder intencionalmente ou não intencionalmente aos restos de informação.

- Máquinas Virtuais devem ser migradas para novos recursos do Gestor de Recursos Azure

## <a name="security-of-network-services"></a>Segurança dos Serviços de Rede

Os serviços acordados fornecidos por um prestador de serviços de rede/gestor são formalmente geridos e monitorizados para garantir que são fornecidos de forma segura.

- Máquinas Virtuais devem ser migradas para novos recursos do Gestor de Recursos Azure

## <a name="network-routing-control"></a>Controlo de Encaminhamento de Rede

Os controlos de encaminhamento são implementados através de gateways de segurança (por exemplo, firewalls) utilizados entre redes internas e externas (por exemplo, a Internet e as redes de terceiros).

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="information-exchange-policies-and-procedures"></a>Políticas e procedimentos de intercâmbio de informações

A organização limita o uso de meios de armazenamento portáteis controlados pela organização por indivíduos autorizados em sistemas de informação externos.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Depuração remota deve ser desligada para aplicações Web
- A Aplicação APi só deve ser acessível em HTTPS

## <a name="electronic-messaging"></a>Mensagens Electrónicas

As aprovações são obtidas antes da utilização de serviços públicos externos, incluindo mensagens instantâneas ou partilha de ficheiros.

- \[Preview\] Mostrar resultados de auditoria de VMs Linux que não têm as permissões de ficheiro de senha definidas para 0644

## <a name="on-line-transactions"></a>Transações on-line

A organização requer o uso da encriptação entre, e o uso de assinaturas eletrónicas por, cada uma das partes envolvidas na transação. A organização garante que o armazenamento dos dados da transação está localizado fora de quaisquer ambientes acessíveis ao público (por exemplo, numa plataforma de armazenamento existente na intranet da organização) e não retido e exposto num meio de armazenamento diretamente acessível a partir da Internet.Quando uma autoridade de confiança é utilizada (por exemplo, para efeitos de emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e incorporada em toda a gestão final/certificação final/certificação final), a segurança é integrada e incorporada em toda a gestão final/certificação final/certificação final), a segurança é integrada e incorporada em toda a gestão final/certificação final/certificação final), a segurança é integrada e incorporada em toda a gestão final/certificação final/certificação final/ processo.

- A encriptação do disco deve ser aplicada em VMs
- \[Preview\] Mostrar resultados de auditoria de VMs do Windows que não contêm os certificados especificados na raiz fidedigna

## <a name="password-management"></a>Gestão de palavra-passe

As palavras-passe são encriptadas durante a transmissão e armazenamento em todos os componentes do sistema.

- \[Preview\] Mostrar resultados de auditoria de VMs windows que não têm a definição de complexidade da palavra-passe ativada

## <a name="user-authentication-for-external-connections"></a>Autenticação do utilizador para ligações externas

São implementados métodos de autenticação forte, tais como multifactores, Radius ou Kerberos (para acesso privilegiado) e CHAP (para encriptação de credenciais para métodos de acesso telefónico) para todas as ligações externas à rede de organizações.

- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- O controlo de acesso à rede Just In Time deve ser aplicado em VMs

## <a name="user-identification-and-authentication"></a>Identificação e Autenticação do Utilizador

Os utilizadores que desempenharam funções privilegiadas (por exemplo, administração do sistema) utilizam contas separadas no desempenho dessas funções privilegiadas. Os métodos de autenticação de vários fatores são utilizados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- O controlo de acesso à rede Just In Time deve ser aplicado em VMs

## <a name="privilege-management"></a>Gestão de Privilégios

O acesso a funções de gestão ou consolas administrativas para sistemas de acolhimento de sistemas virtualizados é restrito ao pessoal com base no princípio do menor privilégio e suportado através de controlos técnicos.

- O controlo de acesso à rede Just In Time deve ser aplicado em VMs
- \[O\] Controlo de Acesso Baseado em Funções de Pré-visualização (RBAC) deve ser utilizado nos serviços da Kubernetes

## <a name="review-of-user-access-rights"></a>Revisão dos Direitos de Acesso ao Utilizador

A organização mantém uma lista documentada de utilizadores autorizados de ativos de informação.

- \[Preview\] Mostrar auditorias resulta das configurações de VMs do Windows em "Opções de Segurança- Contas"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Proteção remota de portas de diagnóstico e configuração

Portas, serviços e aplicações semelhantes instaladas num computador ou sistemas de rede, que não são especificamente necessários para a funcionalidade do negócio, são desativadas ou removidas.

- As portas de gestão devem ser fechadas nas suas máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala das suas máquinas virtuais devem ser remediadas

## <a name="audit-logging"></a>Registo de auditoria

Os registos de mensagens enviadas e recebidas são mantidos, incluindo a data, hora, origem e destino da mensagem, mas não o seu conteúdo. A auditoria está sempre disponível enquanto o sistema estiver ativo e rastreia eventos-chave, acesso de sucesso/falha de dados, alterações de configuração de segurança do sistema, utilização privilegiada ou útil, quaisquer alarmes levantados, ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação, e criação e eliminação de objetos ao nível do sistema.

- Os registos de diagnóstico no Event Hub devem ser ativados
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas

## <a name="monitoring-system-use"></a>Monitorização do sistema de utilização

Os sistemas automatizados implantados em todo o ambiente da organização são utilizados para monitorizar eventos-chave e atividade anómala, e analisar registos de sistemas, os resultados dos quais são revistos regularmente. A monitorização inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acesso a contas desativadas e alertas ou falhas do sistema.

- Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados

## <a name="segregation-of-duties"></a>Segregação de Deveres

A separação de direitos é utilizada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas. Nenhuma pessoa pode aceder, modificar ou utilizar sistemas de informação sem autorização ou deteção. O acesso dos indivíduos responsáveis pela gestão e controlo de acesso limita-se ao mínimo necessário com base no papel e responsabilidades de cada utilizador e estes indivíduos não podem aceder a funções de auditoria relacionadas com estes controlos.

- MFA deve ser ativado contas com permissões de escrita na sua subscrição

## <a name="administrator-and-operator-logs"></a>Registos de Administrador e Operador

A organização garante que a exploração madeireira adequada é ativada para auditar as atividades do administrador; e revê os registos regulares do administrador do sistema e do operador.

- MFA deve ser ativado contas com permissões de escrita na sua subscrição

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação dos riscos relacionados com partes externas

As ligações de acesso remoto entre a organização e as partes externas são encriptadas

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="business-continuity-and-risk-assessment"></a>Continuidade do Negócio e Avaliação de Riscos

A organização identifica os seus processos de negócio críticos e integra os requisitos de gestão da segurança da informação da continuidade do negócio com outros requisitos de continuidade relacionados com aspetos como operações, pessoal, materiais, transportes e instalações.

- \[Preview\] Mostrar resultados da auditoria da configuração dos VMs do Windows em "Opções de Segurança- Consola de Recuperação"

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Reviu o mapeamento de controlo da amostra de plantas HIPAA HITRUST. Em seguida, visite os seguintes artigos para saber sobre a visão geral e como implementar esta amostra:

> [!div class="next step action"]
> [HipAA HITRUST blueprint - Visão geral](./control-mapping.md)
> [HIPAA HITRUST blueprint - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
