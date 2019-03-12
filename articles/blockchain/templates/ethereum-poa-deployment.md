---
title: Prova de autoridade da Ethereum Consortium - Azure
description: Utilizar a solução de consórcio Ethereum prova da autoridade para implementar e configurar uma rede Ethereum de consórcio com vários membros
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 50d24fd41a0a933d9cfec37477773463a918ca0a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549073"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum consortium de prova de autoridade

## <a name="overview"></a>Descrição geral
[Esta solução](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) foi projetado para facilitar a implantação, configuração e regular a uma rede de Ethereum com vários membros consortium prova da autoridade com o mínimo de conhecimento do Azure e Ethereum.

Com um punhado de entradas do utilizador e uma implementação de clique único através do portal do Azure, cada membro pode aprovisionar um volume de memória de rede, usando a computação do Microsoft Azure, rede e serviços de armazenamento em todo o mundo. Requisitos de espaço de rede de cada membro é composta por um conjunto de nós de validador com balanceamento de carga com que uma aplicação ou o utilizador pode interagir para submeter Ethereum transações.

## <a name="concepts"></a>Conceitos

### <a name="terminology"></a>Terminologia

-   **Consenso** -o ato de sincronização de dados através da rede distribuída por meio de validação do bloco e de criação.

-   **Membro de consórcio** - uma entidade que participa de consenso na rede de Blockchain.

-   **Administrador** -Ethereum uma conta que é utilizada para gerir a participação de um membro de determinado consortium.

-   **Validação do** -um computador associado a uma conta de Ethereum que participa de consenso em nome de um administrador.

### <a name="proof-of-authority"></a>Prova de autoridade

Para aqueles que são novos para a Comunidade de blockchain, a versão dessa solução é uma ótima oportunidade para saber mais sobre a tecnologia de maneira fácil e configurável no Azure. Prova de trabalho é um mecanismo de resistência Sybil que aproveita os custos de computação para Self-regular o da rede e permitir a participação justa. Isso funciona muito bem em redes de blockchain anônimo, abra onde competição de criptomoeda promove a segurança na rede. No entanto, em redes privadas/consortium o Ether subjacente tem nenhum valor. Um protocolo alternativo, prova de autoridade, é mais adequado para redes permitidas em que todos os participantes de consenso são conhecidos e fidedignos. Sem a necessidade de extração, prova da autoridade é mais eficiente ao mesmo tempo retendo a tolerância a falhas de símbolos.

### <a name="consortium-governance"></a>Governação de consórcio

Como prova da autoridade baseia-se uma lista de permitidos de autoridades de rede para manter o bom estado de funcionamento da rede, é importante fornecer um mecanismo justo fazer modificações a esta lista de permissão. Cada implementação inclui um conjunto de contratos inteligentes e portal de governação na cadeia desta lista de permitidos. Assim que uma alteração proposta atinge um voto de maioria por membros de consortium, a alteração é elaborada. Isso permite que novos participantes de consenso ser adicionado ou comprometido participantes a remover de forma transparente, o que incentiva a uma rede honesta.

### <a name="admin-account"></a>Conta de admin

Durante a implementação de nós de prova de autoridade, será perguntado para um endereço de Ethereum de administrador. Pode usar diversos mecanismos diferentes para gerar e proteger esta conta Ethereum. Assim que este endereço é adicionado como uma autoridade na rede, pode utilizar esta conta para participar na governação. Esta conta de administrador também irá ser utilizada para delegar a participação de consenso para os nós de validador que são criados como parte desta implementação. Uma vez que é utilizado apenas o endereço de Ethereum público, cada administrador tem a flexibilidade para proteger as respetivas chaves privadas de forma que se segue o modelo de segurança desejadas.

### <a name="validator-node"></a>Nó de validador

No protocolo prova da autoridade, nós de validador tomar o lugar de nós de miner tradicional. Cada validador tem uma identidade de Ethereum exclusiva que é adicionada a uma lista de permissão de smart-contrato. Assim que um validador estiver nessa lista, pode participar do processo de criação de blocos. Para saber mais sobre este processo, consulte a documentação da paridade sobre [consenso autoridade redondo](https://wiki.parity.io/Aura). Cada membro consortium pode aprovisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Nós de validador se comunicar com outros nós de validador até o consenso sobre o estado da razão distribuído subjacente.
Para garantir a justa participação na rede, cada membro consortium é proibido de utilizar o validadores mais do que o primeiro membro na rede (se o primeiro membro implementa três validadores, cada membro só pode ter até três validadores).

### <a name="identity-store"></a>Armazenamento de identidade

Uma vez que cada membro terá vários nós de validador em execução em simultâneo e cada nó tem de ter uma identidade permitida, é importante que os validadores com segurança podem adquirir uma identidade exclusiva de Active Directory na rede. Para facilitar este processo, criamos um Store de identidade que é implementado numa subscrição de cada membro que mantém em segurança as identidades de Ethereum geradas. Após a implementação, o contentor de orquestração irá gerar uma chave privada Ethereum para cada validador e armazená-la no Azure Key Vault. Antes do nó de paridade é iniciado, ele primeiro adquire uma concessão numa identidade não utilizada para garantir que a identidade não é detetada por outro nó. A identidade é fornecida ao cliente que concede-lhe a autoridade para iniciar a criação de blocos. Se a VM de alojamento sofrer um período de indisponibilidade, a concessão de identidade será lançada, permitindo que um nó de substituição retomar a respetiva identidade no futuro.

### <a name="bootnode-registrar"></a>Bootnode entidade de registo

Para ativar a facilidade de conectividade, cada membro irá alojar um conjunto de informações de ligação na [dados de ponto final de API](#data-api). Estes dados incluem uma lista de bootnodes que são fornecidos como nós de peering para o membro de junção. Como parte da API de dados, podemos manter esta lista bootnode atualizados

### <a name="bring-your-own-operator"></a>Traga seu próprio operador

Muitas vezes, um membro de consórcio deve participar de governação de rede, mas não quiser operar e manter a sua infra-estrutura. Ao contrário dos sistemas tradicionais, ter um operador único entre a rede de funciona com base no modelo descentralizado de sistemas de blockchain. Em vez de contratar um intermediário centralizado para operar uma rede, cada membro consortium pode delegar a gestão de infraestrutura para o operador de sua escolha. Isso permite que um modelo híbrido onde o cada membro pode optar por operar a sua própria infraestrutura ou delegar um parceiro diferente da operação. O fluxo de trabalho de operação delegada funciona da seguinte forma:

1.  **Membro de consórcio** gera um endereço de Ethereum (retenções de chave privada)

2.  **Membro de consórcio** fornece o endereço de Ethereum público para **operador**

3.  **Operador** implementa e configura os nós de validador PoA utilizando a nossa solução do Azure Resource Manager

4.  **Operador** fornece o ponto de extremidade RPC e gestão para **Consortium membro**

5.  **Membro de consórcio** utiliza a chave privada para iniciar um pedido de aceitar os nós de validador **operador** implantou para participar em seu nome

### <a name="azure-monitor"></a>Monitor do Azure

Esta solução também vem com o Azure Monitor para controlar as estatísticas de nó e de rede. Para os desenvolvedores de aplicativos, isso fornece visibilidade sobre o blockchain subjacente para controlar as estatísticas de geração de bloco. Operadores de rede podem utilizar o Azure Monitor para rapidamente detetar e prevenir falhas de rede por meio de estatísticas de infraestrutura e consultáveis registos. Para obter mais informações, consulte [monitorização de serviços](#service-monitoring).

### <a name="deployment-architecture"></a>Arquitetura de implantação

#### <a name="description"></a>Descrição

Esta solução pode implementar uma única ou várias regiões com base em rede de consórcio com vários membro Ethereum. Por predefinição, o RPC e pontos de extremidade de peering estão acessíveis através de IP público para permitir simplificada da conectividade entre subscrições e clouds. Recomendamos a tirar partido [contratos de concessão de permissões da paridade](https://wiki.parity.io/Permissioning) para a aplicação de controlos de acesso de nível. Também é suportada a redes implementados por trás de VPNs, que tiram partido de gateways de VNet para conectividade entre subscrições. Estas implementações são mais complexas, pelo que é recomendado para começar com o modelo IP público pela primeira vez.

#### <a name="consortium-member-overview"></a>Descrição geral de membro Consortium

Cada implementação de membro consortium inclui:

-   Máquinas virtuais para executar os validadores de PoA

-   O Azure Load Balancer para distribuição de RPC, peering e pedidos de governação DApp

-   O Azure Key Vault para proteger as identidades de validador

-   Armazenamento do Azure para alojar as informações de rede persistente e coordenar o leasing

-   O Azure Monitor para agregar estatísticas de desempenho e registos

-   Gateway de VNet (opcional) para permitir ligações de VPN entre VNets privadas

![Arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Podemos tirar partido dos contentores do Docker para confiabilidade e de modularidade. Utilizamos o Azure Container Registry para alojar e fornecer imagens com a versão como parte de cada implementação. As imagens de contentor consistem em:

-   Orchestrator

    -   É executada uma vez durante a implementação

    -   Gera a governação de identidades e contratos

    -   Armazenamentos de identidades na Store de identidade

-   Cliente de paridade

    -   As concessões a identidade do Store de identidade

    -   Deteta e liga-se para elementos da rede

-   Agente de EthStats

    -   Recolhe estatísticas e logs locais através de RPC e envia por push para o Azure Monitor

-   Governação DApp

    -   Interface da Web para interagir com os contratos de governação

## <a name="how-to-guides"></a>Guias de procedimentos
### <a name="governance-dapp"></a>Governação DApp

No cerne da prova de autoridade é descentralizada de governação. A governação DApp é um conjunto de previamente implantados [smart contratos](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) e uma aplicação web que são utilizados para regular as autoridades na rede.
Autoridades são divididas em identidades de administrador e nós de validador.
Os administradores têm a capacidade de delegar a participação de consenso para um conjunto de nós de validador. Os administradores também podem votar outros administradores para dentro ou fora da rede.

![governação dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Descentralizado governação -** alterações em autoridades de rede são administradas através de voto na cadeia por administradores selecionados.

-   **Delegação de validador -** autoridades podem gerir os respetivos nós de validador estão configurados em cada implantação PoA.

-   **Histórico de alterações auditável -** cada alteração é registrada no blockchain fornecer transparência e auditability.

#### <a name="getting-started-with-governance"></a>Guia de introdução governação
Para efetuar qualquer tipo de transações por meio do DApp de governação, terá de tirar partido de uma carteira de Ethereum.  A abordagem mais simples é usar uma carteira de no browser, tais como [MetaMask](https://metamask.io); no entanto, porque estes são os contratos inteligentes implementados na rede, pode também automatizar as suas interações ao contrato de governação.

Depois de instalar MetaMask, navegue para o DApp de governação no browser.  Pode localizar o URL no e-mail de confirmação de implementação ou através do portal do Azure na saída da implementação.  Se não tiver uma carteira de no browser instalada, não poderá executar quaisquer ações; No entanto, ainda pode ler o estado de administrador.  

#### <a name="becoming-an-admin"></a>Tornar-se um administrador
Se for o primeiro membro implementado na rede, em seguida, vai ficar automaticamente um administrador e os nós de paridade serão listados como Validadores.  Se estiver ingressando em rede, terá de obter votado como um administrador a por uma maioria (mais de 50%) do conjunto de administrador existente.  Se optar por não se tornar um administrador, em seguida, os nós ainda irão sincronizar e validar o blockchain; No entanto, não irá participar do processo de criação de blocos. Para iniciar o processo de voto para se tornar um administrador, clique em __Nominate__ e introduza o seu endereço de Ethereum e alias.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Candidatos
Selecionar o __candidatos__ separador mostrará a o conjunto atual de administradores do Release candidate.  Quando um candidato atingirem um voto de maioria, os administradores atuais, o candidato será é promovido para um administrador.  Votar num candidato, selecione a linha e clique em "Voto" na parte superior.  Se mudar de ideias sobre um voto, pode selecionar a Release candidate e clique em "Rescind voto".

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administradores
O __administradores__ guia irá mostrar o conjunto atual de administradores e fornecer-lhe a capacidade de votar em relação a.  Assim que um administrador de perde muito mais que o suporte de 50%, serão removidos como administrador da rede.  Quaisquer nós de validador que detém Este administrador irão perder o estado de validação e tornar-se nós de transação na rede.  Um administrador pode ser removido para qualquer número de motivos; No entanto, cabe o consortium concordem com uma política com antecedência.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Validadores
Selecionar o __Validadores__ separador no menu esquerdo irá apresentar os nós de paridade implementados atuais para esta instância e o respetivo estado atual (tipo de nó).  Cada membro consortium terá um conjunto diferente de validadores nesta lista, uma vez que esta vista representa o membro atual do consórcio implementado.  Se esta for uma instância recentemente implementada e ainda não adicionou ainda os validadores, será apresentada a opção para 'Adicionar validações'.  Selecionar esta será automaticamente escolha um conjunto regionalmente com balanceamento de nós de paridade e atribuí-los ao seu conjunto de validador.  Se tiver implementado mais nós que da capacidade permitida, os nós restantes tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente através da [armazenamento de identidade](#identity-store) no Azure.  Se um nó ficar inativo, ele será ceder a sua identidade, permitindo que o outro nó na sua implementação que ocupará seu lugar.  Isto garante que a sua participação de consenso está altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nome de consórcio
Qualquer administrador atualizar o nome de Consortium, apresentado na parte superior da página.  Selecione o ícone de engrenagem no canto superior esquerdo para atualizar o nome de Consortium.

#### <a name="account-menu"></a>Menu da conta
No canto superior direito é seu alias da conta Ethereum e identicon.  Se for um administrador terá a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Implementar a prova de autoridade da Ethereum

Eis um exemplo de um fluxo de implementação de múltiplos intervenientes:

1.  Três membros geram uma conta de Ethereum com MetaMask

2.  *O membro A* implementa Ethereum PoA, fornecendo o endereço público Ethereum

3.  *Membro de um* fornece o URL de consórcio para *membro B* e *C de membro*

4.  *Membro B* e *membro C* implementar, Ethereum PoA, fornecendo o endereço público Ethereum e *membro A*do consórcio URL

5.  *Membro de um* votos *membro B* como administrador

6.  *Membro de um* e *membro B* ambos os votos *C de membro* como administrador

Este processo requer uma subscrição do Azure que pode suportar a implementação de várias máquinas de virtuais e discos geridos. Se necessário, [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/) para começar.

Depois de uma subscrição está protegida, aceda ao portal do Azure. Selecione '+', o Marketplace ("ver tudo") e procure Ethereum PoA Consortium.

A secção seguinte explica como configurar requisitos de espaço do primeiro membro na rede. O fluxo de implementação está dividido em cinco etapas: Noções básicas, regiões de implementação, o tamanho de rede e desempenho, Ethereum definições, Azure Monitor.

#### <a name="basics"></a>Noções Básicas

Sob **Noções básicas**, especifique os valores de parâmetros padrão para qualquer implementação, tais como a subscrição, grupo de recursos e propriedades de máquinas virtuais básico.

Segue-se uma descrição detalhada de cada parâmetro:

Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
---|---|---|---
Criar uma nova rede ou de rede existente de associação?|Crie uma nova rede ou ingresse numa rede de consórcio já existente|Criar novo de junção existentes|Criar Novo
Endereço de e-mail (opcional)|Receberá uma notificação por e-mail quando a implementação é concluída com informações sobre a implementação.|Endereço de e-mail válido|N/D
Nome de utilizador VM|Nome de utilizador de administrador de cada VM implementada (apenas carateres alfanuméricos)|1 e 64 carateres|N/D
Tipo de autenticação|O método para autenticar para a máquina virtual.|Chave pública de palavra-passe ou SSH|Palavra-passe
Palavra-passe (tipo de autenticação = a palavra-passe)|A palavra-passe da conta de administrador para cada uma das máquinas virtuais implementadas.  A palavra-passe tem de conter 3 dos seguintes procedimentos: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial. Embora todas as VMs tenham inicialmente a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento.|12 e 72 carateres|N/D
Chave SSH (tipo de autenticação = a chave pública)|A chave secure shell utilizada para início de sessão remoto.||N/D
Subscrição|A subscrição ao qual pretende implementar a rede de consórcio||N/D
Grupo de recursos|O grupo de recursos ao qual pretende implementar a rede de consórcio.||N/D
Localização|A região do Azure para o grupo de recursos.||N/D

Uma implementação de exemplo é mostrada abaixo: ![painel básico](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Regiões de implementação

Em seguida, em regiões de implementação, especifique entradas para o número de regiões para implementar a rede de consórcio e seleção de regiões do Azure com base no número de regiões tendo em conta. Utilizador pode implementar no máximo de 5 regiões. Recomendamos que escolha a primeira região de acordo com a localização do grupo de recursos da secção Noções básicas. Para redes de desenvolvimento ou teste, recomenda-se uma única região por membro. Para a produção, recomendamos a implementação em duas ou mais regiões para elevada disponibilidade.

Segue-se uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
  Número de regiões|Número de regiões para implementar a rede de consórcio|1, 2, 3, 4, 5|1
  Primeira região|Primeira região para implementar a rede de consórcio|Permitido todas as regiões do Azure|N/D
  Segunda região|Segunda região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 2)|Permitido todas as regiões do Azure|N/D
  Terceira região|Terceira região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 3)|Permitido todas as regiões do Azure|N/D
  Região quarto|Quarto região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 4)|Permitido todas as regiões do Azure|N/D
  Quinto região|Quinto região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 5)|Permitido todas as regiões do Azure|N/D

Uma implementação de exemplo é mostrada abaixo: ![regiões de implementação](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Tamanho de rede e o desempenho

Em seguida, em 'tamanho de rede e o desempenho' Especifica entradas para o tamanho da rede consortium, como o número e tamanho de nós de validador.
O tamanho de armazenamento do nó de validador imponha o tamanho potencial do blockchain. Isso pode ser alterado após a implementação.

Segue-se uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
  Número de nós de validador com balanceamento de carga|O número de nós de validador para aprovisionar como parte da rede|2-15|2
  Desempenho de armazenamento do nó de validador|O tipo de disco gerido de segurança todos os nós de validador implementado.|Standard SSD ou Premium|SSD Standard
  Tamanho de máquina virtual do nó de validador|O tamanho de máquina virtual utilizado para nós de validador.|Padrão A, Standard D, Standard D-v2, Standard F série, DS padrão e Standard FS|Standard D1 v2

[Os detalhes dos preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/)

[Detalhes dos preços de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Máquina virtual e a camada de armazenamento irão afetar o desempenho da rede.  Recomendamos que os SKUs seguintes com base na relação custo-benefício pretendido:

  SKU da máquina virtual|Camada de armazenamento|Preço|Débito|Latência
  ---|---|---|---|---
  F1|SSD Standard|Baixa|Baixa|Alta
  D2_v3|SSD Standard|média|média|média
  F16s|SSD Premium|Alta|Alta|Baixa

Uma implementação de exemplo é mostrada abaixo: ![tamanho e o desempenho de rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum definições

Em seguida, em definições de Ethereum, especifique as definições de configuração relacionadas com o Ethereum, como a rede ID e Ethereum conta palavra-passe ou genesis o bloco de.

Segue-se uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
ID de membro Consortium|O ID associado a cada membro participar na rede de consórcio utilizada para configurar espaços de endereços IP para evitar a colisão. No caso de uma rede privada, ID de membro deve ser exclusivo em organizações diferentes na mesma rede.  Um ID de membro exclusivo é necessário, mesmo quando a mesma organização implementa em várias regiões. Tome nota do valor deste parâmetro, uma vez que precisará para partilhá-lo com outros membros de junção para garantir que não existe nenhum colisão.|0-255|N/D
ID da rede|O ID de rede para a rede de Ethereum consortium a ser implementado.  Cada rede Ethereum tem seu próprio ID de rede, com 1 indicador o ID para a rede pública.|5 - 999,999,999|10101010
Endereço de Ethereum de administrador|Endereço da conta Ethereum que é utilizado para participar no PoA governação.  Recomendamos que utilize MetaMask para gerar um endereço de Ethereum.|42 carateres alfanuméricos, começando com 0 x|N/D
Opções Avançadas|Opções avançadas para Ethereum definições|Ativar ou desativar|Desativar
IP público (as opções avançadas de = Enable)|Implementa a rede por trás de um Gateway de VNet e remove o acesso de peering. Se esta opção estiver selecionada, todos os membros tem de utilizar um Gateway de VNet para a ligação para ser compatível.|VNet privada de IP público|IP Público
Bloquear o limite de gás (opções avançadas = Enable)|O limite de gás bloco inicial da rede|Qualquer numérico|50,000,00
Período de Reseal de bloqueio (seg)|A frequência com que blocos vazios serão criados quando não há nenhuma transação na rede. Uma freqüência mais alta terá finality mais rápida, mas os custos de armazenamento maior.|Qualquer numérico|15
Contrato de permissão de transação (opções avançadas = Enable)|Bytecode para o contrato de concessão de permissões de transação. Restringe a implementação de contrato inteligente e execução para uma lista de permitidos de contas de Ethereum.|Contrato bytecode|N/D

Uma implementação de exemplo é mostrada abaixo: ![ethereum definições](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitorização

O painel de monitorização permite-lhe configurar um recurso de registos do Azure Monitor para a sua rede. O agente de monitorização irá recolher e superfície métricas úteis e os registos da sua rede, fornecendo a capacidade de verificar rapidamente o estado de funcionamento de rede ou a depuração de problemas.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
Monitorização|Opção para ativar a monitorização|Ativar ou desativar|Ativar
Ligue-se aos registos existentes do Azure Monitor|Crie uma nova instância de registos do Azure Monitor ou ingresse numa instância existente|Criar nova ou associar existente|Criar novo
Monitorizar a localização (ligar ao registos existentes do Azure Monitor = criar novos)|A região onde o novo Azure Monitor regista a instância será implementada|Regiões de registos de todos os Azure Monitor|N/D
ID de área de trabalho de análise de registo existente (Connect existente do Azure Monitor registos = Junte-se existente)|Instância de registos de ID de área de trabalho do Monitor do Azure existente||N/D
Chave primária do existente log analytics (ligar aos registos existentes do Azure Monitor = Junte-se existente)|A chave primária utilizada para ligar à instância de registos existente do Azure Monitor||N/D


Uma implementação de exemplo é mostrada abaixo: ![monitor do azure](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Resumo

Clique em Painel de resumo para rever as entradas especificadas e para executar a validação de pré-implementação básica. Antes de implementar, pode transferir o modelo e parâmetros.

Reveja os termos legais e privacidade e clique em compra para implementar. Se a implementação inclui os Gateways de VNet, a implementação irá demorar até 45 minutos 50.

#### <a name="post-deployment"></a>Após a implementação

##### <a name="deployment-output"></a>Saída de implementação

Depois de concluída a implementação, pode acessar os parâmetros necessários através do e-mail de confirmação ou através do portal do Azure. Nesses parâmetros, encontrará:

-   Ponto de extremidade Ethereum RPC

-   URL do Dashboard de governação

-   URL de Monitor do Azure

-   Data URL

-   ID de recurso de Gateway de VNet (opcional)

##### <a name="confirmation-email"></a>E-mail de confirmação

Se fornecer um endereço de e-mail ([secção Noções básicas](#basics)), seria enviado um e-mail para o endereço de e-mail com as informações de saída de implementação.

![e-mail de implementação](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Depois de concluída a implementação com êxito e todos os recursos aprovisionados pode ver os parâmetros de saída no seu grupo de recursos.

1.  Localize seu grupo de recursos no portal

2.  Navegue para *implementações*

3.  Selecione a implementação de principal com o mesmo nome que o grupo de recursos

4.  Selecione *saídas*

### <a name="growing-the-consortium"></a>O consórcio a crescer

Para expandir seu consortium, primeiro tem de ligar a rede física.
Neste primeiro passo usando a implantação com base no IP público é totalmente integrado. Se implementar por trás de uma VPN, consulte a secção [ligar o Gateway de VNet](#connecting-vnet-gateways) para fazer a ligação de rede como parte da implementação do membro novo.  Uma vez concluída a sua implementação utilize o [governação DApp](#governance-dapp) para se tornar uma rede de administrador.

#### <a name="new-member-deployment"></a>Nova implementação de membro

1.  Partilhe as seguintes informações com o membro de junção. Estas informações podem ser encontradas no seu e-mail de pós-implementação ou na saída da implementação do portal.

    -  Consortium Data Url

    -  O número de nós que implementou a

    -  ID de recurso de Gateway de VNet (se utilizar a VPN)

2.  O membro de implantação deve utilizar o [mesma solução](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) ao implementar a sua presença de rede com tendo o seguinte em mente:

    -  Selecione *associar existente*

    -  Escolha o mesmo número de nós de validador e o restante dos membros na rede para garantir a representação satisfatória

    -  Utilizar o mesmo Ethereum endereço que foi fornecido no passo anterior

    -  Passar fornecido *Url de dados de consórcio* sobre o *Ethereum definições* separador

    -  Se o resto da rede estiver atrás de uma VPN, selecione *VNet privada* na secção avançada

#### <a name="connecting-vnet-gateways"></a>Ligar os gateways de VNet

Pode ignorar este passo se tiver implementado através das predefinições de IP público. No caso de uma rede privada, os membros de diferentes ligados através de ligações de gateway de VNet. Antes de um membro pode associar a rede e ver o tráfego de transação, um membro existente tem de efetuar uma configuração final no seu gateway VPN para aceitar a ligação. Isso significa que os nós de Ethereum do membro junção não serão executados até que é estabelecida uma ligação. É recomendado para criar conexões de rede redundantes (malha) para o consortium para reduzir as chances de um ponto único de falha.

Depois de implementa o novo membro, o membro existente tem de concluir a ligação bidirecional ao configurar uma ligação de gateway de VNet para o novo membro. Para conseguir isso, será necessário membro existente:

1.  O gateway de VNet ResourceID do membro ao ligar (veja a saída de implementação)

2.  A chave de ligação partilhada

O membro existente tem de executar o seguinte script do PowerShell para concluir a ligação. É recomendável utilizar o Azure Cloud Shell localizado na barra de navegação da parte superior direita no portal.

![cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzureRmSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzureRmVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitorização de serviços

Pode localizar o seu portal do Azure Monitor ao seguir a ligação no e-mail de implementação ou ao localizar o parâmetro na saída da implementação \[OMS\_PORTAL\_URL\].

O portal primeiro exibirá a descrição geral de estatísticas e o nó de rede de alto nível.

![categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

Selecionando **descrição geral do nó** irá direcioná-lo para um portal para ver as estatísticas de infraestrutura por nó.

![estatísticas de nó](./media/ethereum-poa-deployment/node-stats.png)

Selecionando **estatísticas de rede** irá direcioná-lo para ver as estatísticas de rede Ethereum.

![estatísticas de rede](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Exemplos de consultas de Kusto

Por trás estes dashboards é um conjunto de registos não processados queryable. Pode utilizar estes registos não processados para personalizar os dashboards, investigar falhas ou programa de configuração de alertas de limiar. Veja a seguir, encontrará um conjunto de consultas de exemplo que pode ser executado na ferramenta de pesquisa de registos:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Apresenta uma lista de blocos relatados por mais do que um validador. Útil para ajudar a encontrar bifurcações de cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Obter a contagem de ponto a ponto média para um nó de validador especificado média mais de 5 minutos de registos.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH access

Por motivos de segurança, o acesso de porta SSH é negado por uma regra de segurança do grupo de rede por predefinição. Para aceder às instâncias de máquina virtual na rede PoA, terá de alterar esta regra para \"permitir\"

1.  Começar na secção Descrição geral do grupo de recursos implementados no portal do Azure.

    ![SSH descrição geral](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Selecione o grupo de segurança de rede para a região da VM que pretende aceder

    ![SSH nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Selecione o \"permitem-ssh\" regra

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Alteração \"ação\" para permitir

    ![SSH ativar permitir](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Clique em \"guardar\" (as alterações podem demorar alguns minutos a aplicar)

Pode agora ligar remotamente para as máquinas virtuais para os nós de validador através de SSH com a sua chave de nome de utilizador e palavra-passe/SSH de administrador fornecida.
O comando SSH para ser executada para aceder ao primeiro nó de validador está listado no parâmetro de saída de implementação de modelo como, "SSH\_TO\_primeiro\_VL\_nó\_REGION1" (para a implementação de exemplo: ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Para aceder a nós de transações adicionais, incrementar o número de porta por um (por exemplo, o primeiro nó de transação é na porta 4000).

Se tiver implementado a mais do que uma região, altere o comando acima para o nome DNS ou endereço IP do Balanceador de carga nessa região. Para localizar o nome DNS ou endereço IP das outras regiões, localizar o recurso com a Convenção de nomenclatura \* \* \* \* \*reg - lbpip\#e o respetivo nome DNS e endereços IP propriedades de exibição.

### <a name="azure-traffic-manager-load-balancing"></a>Balanceamento de carga de Gestor de tráfego do Azure

O Gestor de tráfego do Azure pode ajudar a reduzir o período de indisponibilidade e melhora a capacidade de resposta da rede PoA encaminhar o tráfego recebido entre múltiplas implementações em regiões diferentes. Verificações de integridade incorporadas e reencaminhar automática ajudam a garantir a elevada disponibilidade de pontos de extremidade RPC e o DApp de governação. Esta funcionalidade é útil se tiver implementado em várias regiões e estão prontos para produção.

Utilize o Gestor de tráfego para:

-   Melhore a disponibilidade de rede de PoA com ativação pós-falha automática.

-   Aumente sua capacidade de resposta de redes por encaminhamento utilizadores finais para a localização do Azure com latência de rede mais baixa.

Se optar por criar um perfil do Gestor de tráfego, pode utilizar o nome DNS do perfil para aceder à sua rede. Uma vez outros membros de consórcio foram adicionados à rede, o Gestor de tráfego também pode ser utilizado para balanceamento de carga entre os validadores implementados.

#### <a name="creating-a-traffic-manager-profile"></a>Criar um perfil do Gestor de tráfego

Procure e selecione \"perfil do Gestor de tráfego\" depois de clicar o \"criar um recurso\" botão no portal do Azure.

![Procure o Gestor de tráfego do azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

Atribua o perfil de um nome exclusivo e selecione o grupo de recursos que foi criado durante a implementação de PoA. Clique no botão "Criar" para implementar.

![criar o Gestor de tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

Depois de implementada, em seguida, selecione a instância no grupo de recursos. O nome DNS para aceder ao Gestor de tráfego pode ser encontrado na guia visão geral

![Localize o DNS do Gestor de tráfego](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Selecione o separador de pontos de extremidade e clique no botão Adicionar. Dê um nome exclusivo ao ponto final. Altere o tipo de recurso de destino para o endereço IP público. Em seguida, selecione o endereço IP público da primeira região\'Balanceador de carga de s.

![Gestor de tráfego de encaminhamento](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implementada. Depois que os pontos finais estiverem no \"ativada\" Estado, será possível carregar automaticamente e o nome de DNS do Gestor de tráfego com balanceamento de região. Agora, pode utilizar este nome DNS em vez do \[CONSORTIUM\_dados\_URL\] parâmetro nos outros passos do documento.

### <a name="data-api"></a>API de dados

Cada membro consortium hospeda as informações necessárias para outras pessoas se liguem à rede. O membro existente irá fornecer o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implementação, um membro de junção irá obter informações a partir da interface JSON no ponto final seguinte:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta irá conter informações úteis para ingressar em membros (bloco de Genesis, contrato de validador definido ABI, bootnodes) e informações úteis para o membro existente (endereços de validador). Incentivamos o uso de padronização de estender o consortium entre fornecedores de serviços cloud. Esta API irá devolver uma resposta JSON formatado com a seguinte estrutura:
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Tutoriais

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interagir programaticamente com um contrato de inteligente

> [!WARNING]
> Nunca envie a chave privada Ethereum através da rede! Certifique-se de que cada transação está conectada localmente em primeiro lugar e a transação assinada é enviada através da rede.

No exemplo a seguir, usamos *ethereumjs carteira* para gerar um endereço de Ethereum *ethereumjs tx* para iniciar sessão localmente, e *web3* para enviar a transação não processada para o Ponto de extremidade Ethereum RPC.

Usaremos esse contrato inteligente de Hello-World simple para este exemplo:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Este exemplo assume que o contrato já está implementado. Pode usar *solc* e *web3* para implementar um contrato de forma programática. Em primeiro lugar, instale os seguintes módulos do nó:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Este script de nodeJS irá executar o seguinte:

-   Construir uma transação não processada: postMsg

-   Inscrever-se a transação com a chave privada gerada

-   Submeter a transação assinada à rede Ethereum

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Implementar o contrato inteligente com Truffle

-   Instalar as bibliotecas necessárias

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   Na truffle.js, adicione o seguinte código para desbloquear a conta de MetaMask e configurar o nó de PoA como entrada de ponto, fornecendo a frase mnemônicos (MetaMask / definições / revelar palavras de Seed)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Implementar a rede de PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Depurar o contrato inteligente com Truffle

Truffle tem uma rede de desenvolver local que está disponível para contrato inteligente de depuração. Pode encontrar o tutorial completo [aqui](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Suporte de WebAssembly (WASM)

Suporte de WebAssembly já está ativado para em redes de PoA recentemente implementados. Permite que transpiles ao Assembly de Web (Rust, C, C++) para o desenvolvimento de smart-contrato em qualquer idioma. Veja as ligações abaixo para obter informações adicionais

-   Descrição geral de paridade de WebAssembly- <https://wiki.parity.io/WebAssembly-Home>

-   Tutorial do Tech paridade- <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referência

### <a name="faq"></a>FAQ

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Observe que há muitas transações na rede que eu durante esse\'t send. Onde elas vêm?

É inseguro para desbloquear o [pessoa API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html). Bots escutam desbloqueadas Ethereum contas e tentam drenar os fundos. O bot assume essas contas contêm real ether e tentarem seja o primeiro a siphon o saldo. Não ative a API pessoa na rede. Em vez disso, assinar previamente as transações manualmente utilizando uma carteira como MetaMask ou programaticamente, conforme descrito na secção [interagir programaticamente com um contrato de inteligente](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Como SSH para uma VM?

A porta SSH não é exposta por motivos de segurança. Siga [este guia para ativar a porta SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como configurar um membro ou a transação de nós auditoria?

Nós de transação são um conjunto de clientes de paridade que estão em modo de peering com a rede, mas não está participando de consenso. Estes nós ainda podem ser utilizados para submeter Ethereum transações e ler o estado de contrato inteligente.
Isso funciona bem como um mecanismo para fornecer auditability aos membros não autoridade consortium na rede. Para conseguir isso simplesmente siga passo 2 de aumentar o Consortium.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Por que motivo são transações MetaMask demorar muito tempo?

Para garantir a transações são recebidas na ordem correta, cada transação Ethereum vem com um valor de uso único incrementação. Se utilizou uma conta no MetaMask numa rede diferente, terá de repor o valor de valor de uso único. Clique no ícone das definições (barras 3), definições, reposição de conta. O histórico de transações serão limpos e agora pode submeter novamente a transação.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>É necessário especificar a taxa de gás no MetaMask?

Ether não cumprir uma finalidade em consortium prova da autoridade. Por conseguinte, não é necessário especificar a taxa de gás ao submeter transações em MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que devo fazer se a minha implementação falha devido uma falha ao aprovisionar o Azure OMS?

A monitorização é uma funcionalidade opcional. Em alguns casos raros em que a implementação falha devido a impossibilidade de com êxito, Aprovisione recursos do Azure Monitor, que pode implementar novamente sem o Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Implementações de IP públicas são compatíveis com implementações de rede privada?

Não, peering requer comunicação bidirecional, para que toda a rede tem de ser público ou privado.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>O que é o débito de transação esperado da prova de autoridade de?

O débito de transação será altamente dependente os tipos de transações e a topologia de rede.  Usando transações simples, nós já benchmarked uma média de 400 transações por segundo com uma rede implementada em várias regiões.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como subscrever a eventos de contrato inteligente?

Agora, Ethereum prova da autoridade suporta web sockets.  Verifique o seu e-mail de implementação ou a implementação para localizar o URL de web socket e a porta de saída.

## <a name="next-steps"></a>Passos Seguintes

Comece por utilizar o [Ethereum prova da autoridade Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) solução.
