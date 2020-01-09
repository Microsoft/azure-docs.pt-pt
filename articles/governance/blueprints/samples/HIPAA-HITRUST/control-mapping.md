---
title: Controles de exemplo do HIPAA HITRUST Blueprint
description: Controle o mapeamento dos exemplos do HIPAA HITRUST Blueprint. Cada controle é mapeado para uma ou mais políticas do Azure que auxiliam na avaliação.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472311"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapeamento de controle da amostra do HIPAA HITRUST Blueprint

O artigo a seguir fornece detalhes sobre como o exemplo do plano gráfico do HIPAA HITRUST do Azure é mapeado para os controles do HIPAA HITRUST. Para obter mais informações sobre os controles, consulte [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os mapeamentos a seguir são para os controles de **HITRUST da HIPAA** . Use a navegação à direita para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione a **\[visualização\]: auditar** a iniciativa de política interna dos controles HITRUST da HIPAA.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controle contra código mal-intencionado

Este projeto ajuda você a gerenciar o Endpoint Protection, incluindo a proteção de código mal-intencionado, atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitorem a proteção de ponto de extremidade ausente em máquinas virtuais na central de segurança do Azure e impõem a solução antimalware da Microsoft em máquinas virtuais do Windows.

- Implantar a extensão antimalware do Microsoft IaaS padrão para o Windows Server
- Os logs de diagnóstico em contas do lote devem ser habilitados
- As atualizações de sistema devem ser instaladas nos computadores


## <a name="management-of-removable-media"></a>Gerenciamento de mídia removível

A organização, com base no nível de classificação de dados, registra a mídia (incluindo laptops) antes do uso, coloca restrições razoáveis sobre como essa mídia deve ser usada e fornece um nível apropriado de proteção física e lógica (incluindo criptografia) para mídia que contém informações cobertas até que seja corretamente destruída ou corrigido.

- Exigir criptografia em contas de Data Lake Store
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- A encriptação de disco deve ser aplicada em máquinas virtuais
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas


## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

Os provedores de serviço de nuvem usam uma plataforma de virtualização reconhecida pela indústria e formatos de virtualização padrão (por exemplo, Open Virtualization Format, OVF) para ajudar a garantir a interoperabilidade e documentaram as alterações personalizadas feitas em qualquer hipervisor em uso e todas ganchos de virtualização específicos da solução disponíveis para revisão do cliente.

- Implantar pré-requisitos para auditar VMs do Windows que não têm os aplicativos especificados instalados

## <a name="control-of-operational-software"></a>Controle do software operacional 

A organização identifica o software não autorizado no sistema de informações, incluindo servidores, estações de trabalho e laptops, emprega uma política permitir tudo, negação por exceção para proibir a execução de software não autorizado conhecido no sistema de informações e revisa e atualiza a lista de softwares não autorizados periodicamente, mas não menos do que anualmente.

- \[visualização\] mostrar os resultados de auditoria das configurações de VMs do Windows em "opções de segurança-auditoria"
- \[visualização\] mostrar os resultados de auditoria das configurações de VMs do Windows em "políticas de auditoria do sistema-gerenciamento de conta"

## <a name="change-control-procedures"></a>Procedimentos de controle de alterações

A integridade de todas as imagens de máquina virtual é garantida o tempo todo registrando e gerando um alerta para quaisquer alterações feitas em imagens de máquina virtual e disponibilizando para os proprietários de negócios e/ou clientes por meio de métodos eletrônicos (por exemplo, portais ou alertas) os resultados de uma alteração ou movimentação e a validação subsequente da integridade da imagem.

- \[visualização\] mostrar os resultados da auditoria da configuração de VMs do Windows em ' políticas de auditoria do sistema-controle detalhado '

## <a name="inventory-of-assets"></a>Inventário de ativos 

Um inventário de ativos e serviços é mantido

- Os logs de diagnóstico nos serviços de pesquisa devem ser habilitados.
- \[visualização\] requisitos de implantação para auditar configurações de VMs do Windows em "opções de segurança-servidor de rede da Microsoft"
- \[visualização\] requisitos de implantação para auditar configurações de VMs do Windows em "Modelos Administrativos Rede"

## <a name="control-of-technical-vulnerabilities"></a>Controle de vulnerabilidades técnicas 

Existe um padrão de configuração protegida para todos os componentes de sistema e de rede.

- Auditar máquinas virtuais sem recuperação de desastres configuradas
- A avaliação de vulnerabilidade deve ser habilitada em suas instâncias gerenciadas do SQL
- A vulnerabilidade deve ser corrigida por uma solução de avaliação de vulnerabilidade

## <a name="segregation-in-networks"></a>Diferenciar redes

Os gateways de segurança da organização (por exemplo, firewalls) impõem políticas de segurança e são configurados para filtrar o tráfego entre domínios, bloquear o acesso não autorizado e são usados para manter a diferenciação entre a conexão interna com fio, interno e externo segmentos de rede (por exemplo, a Internet), incluindo DMZs e impõem políticas de controle de acesso para cada um dos domínios.

- Provisionamento automático do agente de monitoramento de segurança
- Implantar o observador de rede quando redes virtuais forem criadas

## <a name="input-data-validation"></a>Validação de dados de entrada

Para todos os aplicativos Web voltados para o público, os firewalls de nível de aplicativo são implementados para controlar o tráfego. Para aplicativos voltados para o público que não são baseados na Web, a organização implementou um firewall baseado em rede específico ao tipo de aplicativo. Se o tráfego para o aplicativo voltado ao público for criptografado, o dispositivo ficará atrás da criptografia ou será capaz de descriptografar o tráfego antes da análise.

- \[visualização\] mostrar os resultados da auditoria da configuração de VMs do Windows em "Propriedades do firewall do Windows"


## <a name="network-connection-control"></a>Controle de conexão de rede

O tráfego de rede é controlado de acordo com a política de controle de acesso da organização por meio do firewall e de outras restrições relacionadas à rede para cada ponto de acesso à rede ou interface gerenciada do serviço de telecomunicação externa.

- O acesso por meio de pontos de extremidade voltados para a Internet deve ser restrito
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- A depuração remota deve ser desativada para aplicativos de API

## <a name="network-controls"></a>Controles de rede

A organização usa canais de comunicação segura e criptografada ao migrar servidores físicos, aplicativos ou dados para servidores virtualizados.

- A criptografia de disco deve ser aplicada em VMs
- O protetor de TDE do servidor SQL deve estar encriptado com a sua própria chave
- \[visualização\] mostrar resultados de auditoria das configurações de VMs do Windows em "opções de segurança-acesso à rede"
- Auditar o acesso irrestrito à rede para contas de armazenamento
- \[visualização\] mostrar resultados de auditoria da configuração de VMs do Windows em "Propriedades do firewall do Windows"
- Implantar configurações de diagnóstico de grupos de segurança de rede
- O acesso por meio de pontos de extremidade voltados para a Internet deve ser restrito

## <a name="sensitive-system-isolation"></a>Isolamento de sistema confidencial

Recursos compartilhados do sistema (por exemplo, registros, memória principal, armazenamento secundário) são liberados de volta para o sistema, protegidos contra a divulgação de outros sistemas/aplicativos/usuários, e os usuários não podem acessar vestígios de informações intencionalmente ou intencionalmente.

- As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager

## <a name="security-of-network-services"></a>Segurança dos serviços de rede

Os serviços acordados fornecidos por um provedor de serviços de rede/gerente são formalmente gerenciados e monitorados para garantir que sejam fornecidos com segurança.

- As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager

## <a name="network-routing-control"></a>Controle de roteamento de rede

Os controles de roteamento são implementados por meio de gateways de segurança (por exemplo, firewalls) usados entre redes internas e externas (por exemplo, a Internet e redes de terceiros).

- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

A organização limita o uso da mídia de armazenamento portátil controlada pela organização por indivíduos autorizados em sistemas de informações externas.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- A depuração remota deve ser desativada para aplicativos Web
- O aplicativo de APi só deve ser acessível via HTTPS

## <a name="electronic-messaging"></a>Mensagens eletrônicas

As aprovações são obtidas antes do uso de serviços públicos externos, incluindo mensagens instantâneas ou compartilhamento de arquivos.

- \[visualização\] mostrar os resultados de auditoria de VMs Linux que não têm as permissões de arquivo de senha definidas como 0644

## <a name="on-line-transactions"></a>Transações online

A organização requer o uso de criptografia entre o e o uso de assinaturas eletrônicas por, cada uma das partes envolvidas na transação. A organização garante que o armazenamento dos detalhes da transação esteja localizado fora de qualquer ambiente acessível publicamente (por exemplo, em uma plataforma de armazenamento existente na intranet da organização) e não mantida e exposta em um meio de armazenamento diretamente acessível pela Internet. onde uma autoridade confiável é usada (por exemplo, para a emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e incorporada ao todo o gerenciamento de certificados/assinaturas de ponta a ponta Process.

- A criptografia de disco deve ser aplicada em VMs
- \[visualização\] mostrar resultados de auditoria de VMs do Windows que não contêm os certificados especificados na raiz confiável

## <a name="password-management"></a>Gestão de palavra-passe

As senhas são criptografadas durante a transmissão e o armazenamento em todos os componentes do sistema.

- \[visualização\] mostrar resultados de auditoria de VMs do Windows que não têm a configuração de complexidade de senha habilitada

## <a name="user-authentication-for-external-connections"></a>Autenticação de usuário para conexões externas

Métodos de autenticação fortes, como multifator, RADIUS ou Kerberos (para acesso privilegiado) e o CHAP (para criptografia de credenciais para métodos de dial-up) são implementados para todas as conexões externas com a rede de organizações.

- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- O controle de acesso à rede just in time deve ser aplicado em VMs

## <a name="user-identification-and-authentication"></a>Identificação e autenticação de usuário

Os usuários que executaram funções privilegiadas (por exemplo, administração do sistema) usam contas separadas ao executar essas funções privilegiadas. Os métodos de autenticação multifator são usados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- O controle de acesso à rede just in time deve ser aplicado em VMs

## <a name="privilege-management"></a>Gerenciamento de privilégios

O acesso a funções de gerenciamento ou consoles administrativos para sistemas que hospedam sistemas virtualizados é restrito ao pessoal com base no princípio de privilégios mínimos e com suporte por meio de controles técnicos.

- O controle de acesso à rede just in time deve ser aplicado em VMs
- \[visualização\] controle de acesso baseado em função (RBAC) deve ser usado nos serviços Kubernetess

## <a name="review-of-user-access-rights"></a>Revisão dos direitos de acesso do usuário

A organização mantém uma lista documentada de usuários autorizados de ativos de informações.

- \[visualização\] mostrar resultados de auditoria das configurações de VMs do Windows em "opções de segurança-contas"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Diagnóstico remoto e proteção da porta de configuração

Portas, serviços e aplicativos semelhantes instalados em um computador ou em sistemas de rede, que não são necessários especificamente para a funcionalidade comercial, são desabilitados ou removidos.

- As portas de gestão devem estar fechadas nas suas máquinas virtuais
- Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas

## <a name="audit-logging"></a>Log de auditoria

Os logs de mensagens enviadas e recebidas são mantidos, incluindo a data, a hora, a origem e o destino da mensagem, mas não seu conteúdo. A auditoria está sempre disponível enquanto o sistema está ativo e rastreia eventos-chave, acesso a dados com falha/falhas, alterações de configuração de segurança do sistema, uso privilegiado ou de utilitário, quaisquer alarmes gerados, ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação e criação e exclusão de

- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas

## <a name="monitoring-system-use"></a>Monitoramento do uso do sistema

Os sistemas automatizados implantados em todo o ambiente da organização são usados para monitorar eventos importantes e atividades anormais e analisar logs do sistema, os resultados que são revisados regularmente. O monitoramento inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acessar contas desativadas e alertas ou falhas do sistema.

- Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados

## <a name="segregation-of-duties"></a>Diferenciação de tarefas

A separação de tarefas é usada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas. Nenhuma pessoa única é capaz de acessar, modificar ou usar sistemas de informações sem autorização ou detecção. O acesso para indivíduos responsáveis por administrar e acessar controles é limitado ao mínimo necessário, com base na função e nas responsabilidades de cada usuário, e esses indivíduos não podem acessar funções de auditoria relacionadas a esses controles.

- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura

## <a name="administrator-and-operator-logs"></a>Logs do operador e do administrador

A organização garante que o registro em log apropriado esteja habilitado para auditar as atividades do administrador; e revisa os logs do operador e do administrador do sistema regularmente.

- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação de riscos relacionados a partes externas

As conexões de acesso remoto entre a organização e as partes externas são criptografadas

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="business-continuity-and-risk-assessment"></a>Continuidade dos negócios e avaliação de riscos

A organização identifica seus processos críticos de negócios e integra os requisitos de gerenciamento de segurança de informações de continuidade de negócios com outros requisitos de continuidade relacionados a tais aspectos como operações, pessoal, materiais, transporte e instalações.

- \[visualização\] mostrar os resultados da auditoria da configuração de VMs do Windows em "opções de segurança – console de recuperação"

> [!NOTE]
> A disponibilidade de definições de Azure Policy específicas pode variar no Azure governamental e em outras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Você analisou o mapeamento de controle da amostra do HIPAA HITRUST Blueprint. Em seguida, visite os artigos a seguir para saber mais sobre a visão geral e como implantar este exemplo:

> [!div class="next step action"]
> [HIPAA HITRUST Blueprint-visão geral](./control-mapping.md)
> [HIPAA HITRUST Blueprint – etapas de implantação](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
