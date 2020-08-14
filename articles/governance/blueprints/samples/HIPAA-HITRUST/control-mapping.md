---
title: Controlos de amostras de plantas HIPAA HITRUST
description: Controlo do mapeamento das amostras de plantas DO HIPAA HITRUST. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209420"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Controlo do mapeamento da amostra de planta HIPAA HITRUST

O artigo seguinte detalha como o Azure Blueprints HIPAA HITRUST mapeia a amostra de amostra para os controlos HIPAA HITRUST. Para obter mais informações sobre os controlos, consulte [o HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Os seguintes mapeamentos são para os controlos **HIPAA HITRUST.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a ** \[ Pré-visualização \] : Audit HIPAA HITRUST controla** a iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controlo contra código malicioso

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo a proteção de código malicioso, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais Windows.

- O Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção
- Monitor que falta proteção de ponto final no Centro de Segurança Azure
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais


## <a name="management-of-removable-media"></a>Gestão de Mídia Amovível

A organização, com base no nível de classificação de dados, regista meios de comunicação (incluindo portáteis) antes da utilização, coloca restrições razoáveis sobre a forma como esses meios de comunicação são utilizados e fornece um nível adequado de proteção física e lógica (incluindo encriptação) para os meios que contenham informações cobertas até serem devidamente destruídos ou higienizados.

- A encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Os discos não ligados devem ser encriptados
- Exigir encriptação nas contas da Data Lake Store
- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave
- O protetor TDE de caso gerido pela SQL deve ser encriptado com a sua própria chave

## <a name="control-of-operational-software"></a>Controlo de Software Operacional 

A organização identifica software não autorizado no sistema de informação, incluindo servidores, estações de trabalho e portáteis, emprega uma política de autorização, negação por exceção, para proibir a execução de software não autorizado conhecido no sistema de informação, e analisa e atualiza a lista de software não autorizado periodicamente, mas não menos do que anualmente.

- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="change-control-procedures"></a>Alterar procedimentos de controlo

A integridade de todas as imagens de máquinas virtuais é assegurada a todo o momento, registando e levantando um alerta para quaisquer alterações feitas às imagens de máquinas virtuais, e disponibilizando ao(s) proprietário(s) e/ou clientes através de métodos eletrónicos (por exemplo, portais ou alertas) os resultados de uma alteração ou movimento e a subsequente validação da integridade da imagem.

- \[Preview \] Show resultados da auditoria do Windows VMs em 'Políticas de Auditoria do Sistema - Rastreio Detalhado'
- \[Pré-visualização \] : Mostrar resultados de auditoria das configurações do Windows VMs em 'Políticas de Auditoria do Sistema - Rastreio Detalhado'

## <a name="control-of-technical-vulnerabilities"></a>Controlo de Vulnerabilidades Técnicas 

Existe um padrão de configuração endurecido para todos os componentes do sistema e da rede.

- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL
- \[A \] avaliação da vulnerabilidade de pré-visualização deve ser ativada em Máquinas Virtuais
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- \[Pré-visualização \] : As políticas de segurança do Pod devem ser definidas nos Serviços Kubernetes

## <a name="segregation-in-networks"></a>Segregação em Redes

Os gateways de segurança da organização (por exemplo, firewalls) aplicam políticas de segurança e estão configurados para filtrar o tráfego entre domínios, bloquear o acesso não autorizado, e são usados para manter a segregação entre segmentos internos de rede com fios, sem fios e externos (por exemplo, a Internet) incluindo DMZs e impor políticas de controlo de acesso para cada um dos domínios.

- As subnetas devem ser associadas a um Grupo de Segurança de Rede
- Máquinas virtuais devem ser ligadas a uma rede virtual aprovada
- As máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede
- Service Bus deve usar um ponto final de serviço de rede virtual
- O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual
- O SQL Server deve utilizar um ponto final de serviço de rede virtual
- O Event Hub deve utilizar um ponto final de serviço de rede virtual
- Cosmos DB deve usar um ponto final de serviço de rede virtual
- O Key Vault deve usar um ponto final de serviço de rede virtual
- As sub-redes gateway não devem ser configuradas com um grupo de segurança de rede
- As contas de armazenamento devem usar um ponto final de serviço de rede virtual
- \[Pré-visualização \] : O registo de contentores deve utilizar um ponto final de serviço de rede virtual
- Recomendações de endurecimento de rede adaptativa devem ser aplicadas em máquinas virtuais internas viradas para a face

## <a name="network-connection-control"></a>Controlo de Ligação à Rede

O tráfego de rede é controlado de acordo com a política de controlo de acesso da organização através de firewall e outras restrições relacionadas com a rede para cada ponto de acesso à rede ou interface gerida pelo serviço de telecomunicações externo.

- A transferência segura para contas de armazenamento deve ser ativada
- A versão TLS mais recente deve ser usada no seu API Ap
- A versão mais recente do TLS deve ser usada na sua Web App
- A versão TLS mais recente deve ser usada na sua App de Função
- A App de função só deve estar acessível através do HTTPS
- A Aplicação Web só deve ser acessível em HTTPS
- A API App só deve estar acessível em HTTPS
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- As subnetas devem ser associadas a um Grupo de Segurança de Rede
- As regras do NSGs para aplicações web em IaaS devem ser endurecidas
- As regras do Grupo de Segurança da Rede para máquinas virtuais que enfrentam a Internet devem ser endurecidas
- Máquinas virtuais devem ser ligadas a uma rede virtual aprovada
- As máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede

## <a name="network-controls"></a>Controlos de rede

A organização utiliza canais de comunicação seguros e encriptados ao migrar servidores físicos, aplicações ou dados para servidores virtualizados.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- Recomendações de endurecimento de rede adaptativa devem ser aplicadas em máquinas virtuais internas viradas para a face
- Service Bus deve usar um ponto final de serviço de rede virtual
- O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual
- O SQL Server deve utilizar um ponto final de serviço de rede virtual
- O Event Hub deve utilizar um ponto final de serviço de rede virtual
- Cosmos DB deve usar um ponto final de serviço de rede virtual
- O Key Vault deve usar um ponto final de serviço de rede virtual
- Auditoria acesso ilimitado à rede a contas de armazenamento
- As contas de armazenamento devem usar um ponto final de serviço de rede virtual
- \[Pré-visualização \] : O registo de contentores deve utilizar um ponto final de serviço de rede virtual

## <a name="security-of-network-services"></a>Segurança dos Serviços de Rede

Os serviços acordados prestados por um prestador/gestor de serviços de rede são formalmente geridos e monitorizados para garantir que são fornecidos de forma segura.

- \[Pré-visualização \] : O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais do Windows
- \[Pré-visualização \] : O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais Linux Network Watcher deve ser ativado

## <a name="information-exchange-policies-and-procedures"></a>Políticas e procedimentos de intercâmbio de informações

A organização limita o uso de suportes de armazenamento portáteis controlados pela organização por indivíduos autorizados em sistemas de informação externos.

- Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'
- O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web
- O CORS não deve permitir que todos os recursos acedam às suas Apps de Função
- O CORS não deve permitir que todos os recursos acedam à sua App API
- Depuragem remota deve ser desligada para aplicações web
- Depuragem remota deve ser desligada para apps de funções
- Depuragem remota deve ser desligada para apps da API

## <a name="on-line-transactions"></a>Transações on-line

A organização exige o uso da encriptação entre, e o uso de assinaturas eletrónicas por cada uma das partes envolvidas na transação. A organização garante que o armazenamento dos dados da transação está localizado fora de quaisquer ambientes acessíveis ao público (por exemplo, numa plataforma de armazenamento existente na intranet da organização) e não retida e exposta num meio de armazenamento diretamente acessível a partir da Internet.Sempre que seja utilizada uma autoridade de confiança (por exemplo, para efeitos de emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e incorporada em todo o processo de gestão de certificados/assinaturas de ponta a ponta.

- A transferência segura para contas de armazenamento deve ser ativada
- A versão TLS mais recente deve ser usada no seu API Ap
- A versão mais recente do TLS deve ser usada na sua Web App
- A versão TLS mais recente deve ser usada na sua App de Função
- A App de função só deve estar acessível através do HTTPS
- A Aplicação Web só deve ser acessível em HTTPS
- A API App só deve estar acessível em HTTPS
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- \[Pré-visualização \] : Implementar pré-requisitos para auditar VMs do Windows que não contenham os certificados especificados em Raiz Fidedigna
- \[Pré-visualização \] : Mostrar resultados de auditoria de VMs do Windows que não contenham os certificados especificados em Raiz Fidedigna

## <a name="user-password-management"></a>Gestão de passwords de utilizador

As palavras-passe são encriptadas durante a transmissão e armazenamento em todos os componentes do sistema.

- \[Pré-visualização \] de VMs de auditoria com definições de segurança de senha insegura

## <a name="user-authentication-for-external-connections"></a>Autenticação do utilizador para ligações externas

São implementados métodos de autenticação fortes como multi-factor, Radius ou Kerberos (para acesso privilegiado) e CHAP (para encriptação de credenciais para métodos de acesso intermitente) para todas as ligações externas à rede de organizações.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="user-identification-and-authentication"></a>Identificação e Autenticação do Utilizador

Os utilizadores que desempenharam funções privilegiadas (por exemplo, administração do sistema) utilizam contas separadas no desempenho dessas funções privilegiadas. Os métodos de autenticação de vários fatores são utilizados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo de administradores contém qualquer um dos membros especificados
- Mostrar os resultados da auditoria do Windows VMs em que o grupo de administradores contém qualquer um dos membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Mostrar os resultados da auditoria dos VM do Windows em que o grupo de administradores não contém todos os membros especificados
- Implementar pré-requisitos para auditar VMs do Windows em que o grupo de administradores não contém apenas os membros especificados
- Mostrar os resultados da auditoria dos VM do Windows em que o grupo de administradores não contém apenas os membros especificados

## <a name="privilege-management"></a>Gestão de Privilégios

O acesso a funções de gestão ou consolas administrativas para sistemas que acolhem sistemas virtualizados limita-se ao pessoal com base no princípio do menor privilégio e suportado através de controlos técnicos.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- As portas de gestão devem ser fechadas nas suas máquinas virtuais
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- Auditar o uso das regras personalizadas do RBAC
- O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes

## <a name="review-of-user-access-rights"></a>Revisão dos Direitos de Acesso ao Utilizador

A organização mantém uma lista documentada de utilizadores autorizados de ativos de informação.

- Auditar o uso das regras personalizadas do RBAC

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Proteção remota da porta de diagnóstico e configuração

Portas, serviços e aplicações similares instaladas num computador ou sistemas de rede, que não são especificamente necessárias para a funcionalidade do negócio, são desativadas ou removidas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- As portas de gestão devem ser fechadas nas suas máquinas virtuais
- Depuragem remota deve ser desligada para aplicações web
- Depuragem remota deve ser desligada para apps de funções
- Depuragem remota deve ser desligada para apps da API
- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="audit-logging"></a>Registo de Auditoria

Os registos de mensagens enviadas e recebidas são mantidos, incluindo a data, hora, origem e destino da mensagem, mas não o seu conteúdo. A auditoria está sempre disponível enquanto o sistema estiver ativo e rastreie eventos-chave, acesso de dados de sucesso/falha, alterações na configuração da segurança do sistema, utilização privilegiada ou de utilidade, quaisquer alarmes levantados, ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação, e criação e eliminação de objetos de nível de sistema.

- Os registos de diagnóstico na Azure Data Lake Store devem ser ativados
- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados 
- Os registos de diagnóstico no IoT Hub devem ser ativados 
- Os registos de diagnóstico nas contas do Lote devem ser ativados 
- Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados 
- Os registos de diagnóstico no Centro de Eventos devem ser ativados 
- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados 
- Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados 
- Os registos de diagnóstico em Data Lake Analytics devem ser ativados 
- Os registos de diagnóstico no Cofre de Chaves devem ser ativados 
- Os registos de diagnóstico no Service Bus devem ser ativados
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- A auditoria no servidor SQL deve ser ativada
- Definição de diagnóstico de auditoria
- O Azure Monitor deve recolher registos de atividade de todas as regiões

## <a name="monitoring-system-use"></a>Utilização do sistema de monitorização

Os sistemas automatizados implantados em todo o ambiente da organização são utilizados para monitorizar eventos-chave e atividade anómala, e analisar registos de sistemas, dos quais os resultados são revistos regularmente. A monitorização inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acesso a contas desativadas e alertas ou falhas do sistema.

- O Azure Monitor deve recolher registos de atividade de todas as regiões
- O agente Log Analytics deve ser instalado em máquinas virtuais
- O agente Log Analytics deve ser instalado em conjuntos de escala de máquinas virtuais
- \[Pré-visualização \] : Implementar pré-requisitos para auditar VMs do Windows em que o agente Log Analytics não está ligado como esperado
- \[Pré-visualização \] : Mostrar os resultados da auditoria dos VMs do Windows nos quais o agente Log Analytics não está ligado como esperado
- O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'
- O provisionamento automático do agente de monitorização Log Analytics deve ser ativado na sua subscrição

## <a name="segregation-of-duties"></a>Segregação de Deveres

A separação de direitos é utilizada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas. Nenhuma pessoa é capaz de aceder, modificar ou usar sistemas de informação sem autorização ou deteção. O acesso aos indivíduos responsáveis pela gestão e controlo de acesso limita-se ao mínimo necessário com base no papel e responsabilidades de cada utilizador e estes indivíduos não podem aceder a funções de auditoria relacionadas com esses controlos.

- O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes
- Auditar o uso das regras personalizadas do RBAC
- \[Pré-visualização \] : Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Atribuição de Direitos de Utilizador'
- \[Pré-visualização \] : Mostrar os resultados da auditoria das configurações do Windows VMs em 'Atribuição de Direitos de Utilizador'
- \[Pré-visualização \] : Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Controlo de Contas de Utilizador'
- \[Pré-visualização \] : Mostrar resultados de auditoria das configurações do Windows VMs em 'Opções de Segurança - Controlo de Contas de Utilizador'
- As funções de proprietário de subscrição personalizada não devem existir

## <a name="administrator-and-operator-logs"></a>Registos de administrador e operador

A organização garante que a exploração madeireira adequada é ativada para auditar atividades de administrador; e revê regularmente os registos do administrador do sistema e do operador.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação dos riscos relacionados com partes externas

As ligações de acesso remoto entre a organização e as partes externas são encriptadas

- A transferência segura para contas de armazenamento deve ser ativada
- A App de função só deve estar acessível através do HTTPS
- A Aplicação Web só deve ser acessível em HTTPS
- A API App só deve estar acessível em HTTPS
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL
- Apenas devem ser ativadas ligações seguras à sua Cache Redis

## <a name="business-continuity-and-risk-assessment"></a>Continuidade do negócio e Avaliação de Riscos

A organização identifica os seus processos de negócio críticos e integra os requisitos de gestão da segurança da informação da continuidade do negócio com outros requisitos de continuidade relativos a aspetos como operações, pessoal, materiais, transportes e instalações.

- Auditar máquinas virtuais sem recuperação de desastres configurada
- Os objetos do Cofre chave devem ser recuperáveis
- \[Pré-visualização \] : Implementar pré-requisitos para auditar configurações de VMs do Windows em 'Opções de Segurança - Consola de recuperação'
- \[Pré-visualização \] Mostrar resultados da auditoria do Windows VMs em "Opções de Segurança- Consola de Recuperação"

## <a name="back-up"></a>Apoiar

Esta planta atribui definições de Política Azure que auditam as informações de backup do sistema da organização para o local de armazenamento alternativo eletronicamente. Para envio físico de metadados de armazenamento, considere a utilização da Caixa de Dados Azure.

- Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL
- Backup geo-redundante deve ser ativado para Azure Database para MySQL
- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL
- Backup geo-redundante deve ser ativado para Azure Database for MariaDB
- A azure Backup deve ser ativado para máquinas virtuais

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Reviu o mapeamento de controlo da amostra de planta DA HIPAA HITRUST. Em seguida, visite os seguintes artigos para saber sobre a visão geral e como implementar esta amostra:

> [!div class="next step action"]
> [Projeto HIPAA HITRUST - Visão geral](./control-mapping.md) 
>  [Projeto HIPAA HITRUST - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
