---
title: Consórcio de prova de autoridade Ethereum-Azure
description: Usar a solução de consórcio de prova de autoridade do Ethereum para implantar e configurar uma rede Ethereum do consórcio de vários membros
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 04/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 01b9f7f74077737ea95a56bbe81f440db425bf0c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698461"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Consórcio de prova de autoridade Ethereum

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
[Essa solução](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) foi projetada para facilitar a implantação, a configuração e o controle de uma rede de Ethereum de prova de autoridade do consórcio de vários membros com conhecimento mínimo do Azure e do Ethereum.

Com algumas entradas de usuário e uma implantação de clique único por meio do portal do Azure, cada membro pode provisionar uma superfície de rede, usando Microsoft Azure computação, rede e serviços de armazenamento em todo o mundo. A superfície de rede de cada membro consiste em um conjunto de nós de validador com balanceamento de carga com os quais um aplicativo ou usuário pode interagir para enviar transações Ethereum.

## <a name="concepts"></a>Conceitos

### <a name="terminology"></a>Terminologia

-   **Consenso** : o ato de sincronizar dados em toda a rede distribuída por meio da validação e da criação de bloco.

-   **Membro do consórcio** -uma entidade que participa de consenso na rede Blockchain.

-   **Administrador** – uma conta do Ethereum que é usada para gerenciar a participação para um determinado membro do Consortium.

-   **Validador** – um computador associado a uma conta do Ethereum que participa de consenso em nome de um administrador.

### <a name="proof-of-authority"></a>Prova de autoridade

Para aqueles que são novos na Comunidade blockchain, o lançamento dessa solução é uma ótima oportunidade de aprender sobre a tecnologia de maneira fácil e configurável no Azure. A prova de trabalho é um mecanismo de resistência Sybil que aproveita os custos de computação para autoregularização da rede e permitir a participação justa. Isso funciona muito bem em redes anônimas, abertas blockchain, em que a competição por criptomoeda promove a segurança na rede. No entanto, em redes privadas/Consortium, o Ether subjacente não tem valor. Um protocolo alternativo, uma prova de autoridade, é mais adequado para redes permitidas em que todos os participantes de consenso são conhecidos e respeitáveis. Sem a necessidade de mineração, a prova de autoridade é mais eficiente enquanto ainda mantém a tolerância a falhas símbolos.

### <a name="consortium-governance"></a>Governança do consórcio

Como a prova de autoridade depende de uma lista de autoridades de rede permitidas para manter a rede íntegra, é importante fornecer um mecanismo justo para fazer modificações nessa lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e o portal para governança na cadeia dessa lista de permissão. Depois que uma alteração proposta alcança um voto de maioria por membros do consórcio, a alteração é aplicada. Isso permite que os participantes de novos consenso sejam adicionados ou comprometidos a serem removidos de uma maneira transparente que incentiva uma rede honesta.

### <a name="admin-account"></a>Conta de admin

Durante a implantação dos nós de prova de autoridade, você será solicitado a fornecer um endereço de Ethereum de administrador. Você pode usar vários mecanismos diferentes para gerar e proteger essa conta do Ethereum. Depois que esse endereço for adicionado como uma autoridade na rede, você poderá usar essa conta para participar da governança. Essa conta de administrador também será usada para delegar a participação de consenso aos nós do validador que são criados como parte dessa implantação. Como apenas o endereço Ethereum público é usado, cada administrador tem a flexibilidade de proteger suas chaves privadas de uma forma que segue o modelo de segurança desejado.

### <a name="validator-node"></a>Nó do validador

No protocolo de prova de autoridade, os nós do validador assumem o lugar dos nós Miner tradicionais. Cada validador tem uma identidade Ethereum exclusiva que é adicionada a uma lista de permissões de contrato inteligente. Depois que um validador estiver nessa lista, ele poderá participar do processo de criação de bloco. Para saber mais sobre esse processo, consulte a documentação da paridade sobre o [consenso de ida](https://wiki.parity.io/Aura)e volta da autoridade. Cada membro do consórcio pode provisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Nós de validador se comunicam com outros nós de validador para que cheguem ao estado do razão distribuído subjacente.
Para garantir a participação justa na rede, cada membro do consórcio é proibido de usar mais validadores do que o primeiro membro na rede (se o primeiro membro implantar três validadores, cada membro poderá ter até três validadores).

### <a name="identity-store"></a>Repositório de identidade

Como cada membro terá vários nós de validador em execução simultaneamente e cada nó deve ter uma identidade permitida, é importante que os validadores possam adquirir com segurança uma identidade ativa exclusiva na rede. Para tornar isso mais fácil, criamos um repositório de identidades que é implantado na assinatura de cada membro que mantém com segurança as identidades Ethereum geradas. Após a implantação, o contêiner de orquestração irá gerar uma chave privada Ethereum para cada validador e armazená-lo em Azure Key Vault. Antes da inicialização do nó de paridade, ele primeiro adquire uma concessão em uma identidade não utilizada para garantir que a identidade não seja selecionada por outro nó. A identidade é fornecida ao cliente, o que lhe dá autoridade para começar a criar blocos. Se a VM de hospedagem sofrer uma interrupção, a concessão de identidade será liberada, permitindo que um nó de substituição retome sua identidade no futuro.

### <a name="bootnode-registrar"></a>Registrador de Bootnode

Para habilitar a facilidade de conectividade, cada membro hospedará um conjunto de informações de conexão no [ponto de extremidade da API de dados](#data-api). Esses dados incluem uma lista de bootnodes que são fornecidos como nós de emparelhamento para o membro de junção. Como parte dessa API de dados, mantemos essa lista bootnode atualizada

### <a name="bring-your-own-operator"></a>Traga seu próprio operador

Geralmente, um membro do consórcio desejará participar da governança de rede, mas não quer operar e manter sua infraestrutura. Ao contrário dos sistemas tradicionais, ter um único operador na rede funciona com o modelo descentralizado dos sistemas blockchain. Em vez de contratar um intermediário centralizado para operar uma rede, cada membro do consórcio pode delegar o gerenciamento de infraestrutura para o operador de sua escolha. Isso permite que um modelo híbrido em que cada membro possa escolher operar sua própria infraestrutura ou delegar operação para um parceiro diferente. O fluxo de trabalho da operação delegada funciona da seguinte maneira:

1.  O **membro do consórcio** gera um endereço Ethereum (mantém a chave privada)

2.  O **membro do consórcio** fornece o endereço Ethereum público para o **operador**

3.  O **operador** implanta e configura os nós do validador POA usando nossa solução de Azure Resource Manager

4.  O **operador** fornece o ponto de extremidade de gerenciamento e RPC para o **membro do consórcio**

5.  O **membro do Consortium** usa sua chave privada para assinar uma solicitação aceitando que o **operador** de nós do validador tenha implantado para participar de seu nome

### <a name="azure-monitor"></a>Azure Monitor

Essa solução também vem com Azure Monitor para rastrear estatísticas de nó e de rede. Para desenvolvedores de aplicativos, isso fornece visibilidade do blockchain subjacente para rastrear estatísticas de geração de bloco. Os operadores de rede podem usar Azure Monitor para detectar e impedir rapidamente interrupções de rede por meio de estatísticas de infraestrutura e logs consultáveis. Para obter mais informações, consulte [monitoramento de serviço](#service-monitoring).

### <a name="deployment-architecture"></a>Arquitetura de implantação

#### <a name="description"></a>Descrição

Essa solução pode implantar uma rede única ou multimembro Ethereum Consortium com base em várias regiões. Por padrão, os pontos de extremidade RPC e de emparelhamento são acessíveis por IP público para permitir conectividade simplificada entre assinaturas e nuvens. É recomendável aproveitar os [contratos de permissão da paridade](https://wiki.parity.io/Permissioning) para controles de acesso no nível do aplicativo. Também damos suporte a redes implantadas atrás de VPNs, que aproveitam gateways de VNet para conectividade entre assinaturas. Essas implantações são mais complexas, portanto, é recomendável começar com o modelo de IP público primeiro.

#### <a name="consortium-member-overview"></a>Visão geral do membro do consórcio

Cada implantação de membro do consórcio inclui:

-   Máquinas virtuais para executar os validadores do PoA

-   Azure Load Balancer para distribuir solicitações de DApp de RPC, emparelhamento e governança

-   Azure Key Vault para proteger as identidades do validador

-   Armazenamento do Azure para hospedar informações de rede persistentes e coordenar a concessão

-   Azure Monitor para agregar logs e estatísticas de desempenho

-   Gateway de VNet (opcional) para permitir conexões VPN entre VNets particulares

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Aproveitamos os contêineres do Docker para confiabilidade e modularidade. Usamos o registro de contêiner do Azure para hospedar e fornecer imagens com versão como parte de cada implantação. As imagens de contêiner consistem em:

-   Orchestrator

    -   É executado uma vez durante a implantação

    -   Gera identidades e contratos de governança

    -   Armazena identidades no repositório de identidades

-   Cliente de paridade

    -   Concede a identidade da loja de identidades

    -   Descobre e se conecta a pares

-   Agente EthStats

    -   Coleta logs e estatísticas locais por meio de RPC e envios por push para Azure Monitor

-   DApp de governança

    -   Interface da Web para interagir com contratos de governança

## <a name="how-to-guides"></a>Guias de procedimentos
### <a name="governance-dapp"></a>DApp de governança

No coração da prova de autoridade está a governança descentralizada. O DApp de governança é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) previamente implantados e um aplicativo Web que é usado para controlar as autoridades na rede.
As autoridades são divididas em identidades de administrador e nós de validador.
Os administradores têm a capacidade de delegar a participação em consenso a um conjunto de nós de validador. Os administradores também podem votar outros administradores dentro ou fora da rede.

![dapp de governança](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Governança descentralizada-** As alterações nas autoridades de rede são administradas por meio de votação na cadeia por meio de administradores selecionados.

-   **Delegação de validador-** As autoridades podem gerenciar seus nós de validador que são configurados em cada implantação do PoA.

-   **Histórico de alterações auditável-** Cada alteração é registrada no blockchain, fornecendo transparência e auditoria.

#### <a name="getting-started-with-governance"></a>Introdução à governança
Para executar qualquer tipo de transação por meio do DApp de governança, você precisará aproveitar uma carteira de Ethereum.  A abordagem mais simples é usar uma carteira no navegador, como metamask [](https://metamask.io); no entanto, como esses são contratos inteligentes implantados na rede, você também pode automatizar suas interações com o contrato de governança.

Depois de instalar a metamáscara, navegue até o DApp de governança no navegador.  Você pode localizar a URL no email de confirmação da implantação ou por meio de portal do Azure na saída da implantação.  Se você não tiver uma carteira no navegador instalada, não poderá executar nenhuma ação; no entanto, você ainda pode ler o estado do administrador.  

#### <a name="becoming-an-admin"></a>Tornando-se um administrador
Se você for o primeiro membro implantado na rede, você se tornará automaticamente um administrador e seus nós de paridade serão listados como validadores.  Se estiver ingressando na rede, você precisará entrar como administrador por uma maioria (maior que 50%) do conjunto de administradores existente.  Se você optar por não se tornar um administrador, os nós ainda serão sincronizados e validará o blockchain; no entanto, eles não participarão do processo de criação de blocos. Para iniciar o processo de votação para se tornar um administrador, clique em __indicado__ e insira seu endereço Ethereum e alias.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Candidatos
A seleção da guia __candidatos__ mostrará o conjunto atual de administradores candidatos.  Depois que um candidato atingir um voto de maioria dos administradores atuais, o candidato será promovido a um administrador.  Para votar em um candidato, selecione a linha e clique em "votar em" na parte superior.  Se você mudar de ideia em um voto, poderá selecionar o candidato e clicar em "rescindir voto".

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administradores
A guia __Administradores__ mostrará o conjunto atual de administradores e fornecerá a você a capacidade de votar.  Quando um administrador perder mais de 50% de suporte, eles serão removidos como um administrador na rede.  Qualquer nó validador que esse administrador possui perderá o status do validador e se tornará nós de transação na rede.  Um administrador pode ser removido por vários motivos; no entanto, cabe ao consórcio concordar sobre uma política com antecedência.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Validadores
A seleção da guia validadores no menu à esquerda exibirá os nós de paridade atualmente implantados para essa instância e seu status atual (tipo de nó).  Cada membro do consórcio terá um conjunto diferente de validadores nesta lista, já que essa exibição representa o membro do consórcio implantado atual.  Se esta for uma instância implantada recentemente e você ainda não tiver adicionado seus validadores, você verá a opção "Adicionar validadores".  Selecionar isso irá escolher automaticamente um conjunto de nós de paridade com balanceamento de região e atribuí-los ao seu conjunto de validadores.  Se você tiver implantado mais nós do que a capacidade permitida, os nós restantes se tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente por meio do [repositório](#identity-store) de identidades no Azure.  Se um nó falhar, ele ocupará sua identidade, permitindo que outro nó em sua implantação assuma seu lugar.  Isso garante que sua participação no consenso esteja altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nome do consórcio
Qualquer administrador pode atualizar o nome do consórcio, exibido na parte superior da página.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

#### <a name="account-menu"></a>Menu da conta
Na parte superior direita está seu alias de conta do Ethereum e Identicon.  Se você for um administrador, terá a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Implantar a prova de autoridade do Ethereum

Aqui está um exemplo de um fluxo de implantação de várias partes:

1.  Três membros cada um geram uma conta Ethereum usando a metamáscara

2.  *O membro A* implanta Ethereum POA, fornecendo seu endereço público Ethereum

3.  O *membro a* fornece a URL do consórcio para o *membro B* e o *membro C*

4.  *Membro B* e implantação do *membro C* , Ethereum POA, fornecendo seu endereço público Ethereum e a URL do consórcio do *membro A*

5.  *Membro A* votos no *membro B* como administrador

6.  O *membro A* e o *membro B* votam o *membro C* como um administrador

Esse processo requer uma assinatura do Azure que possa dar suporte à implantação de várias máquinas virtuais e de discos gerenciados. Se necessário, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) para começar.

Quando uma assinatura estiver protegida, vá para portal do Azure. Selecione ' + ', Marketplace (' Ver todos ') e pesquise por Ethereum PoA Consortium.

A seção a seguir explicará como configurar a superfície do primeiro membro na rede. O fluxo de implantação é dividido em cinco etapas: Noções básicas, regiões de implantação, tamanho de rede e desempenho, configurações de Ethereum Azure Monitor.

#### <a name="basics"></a>Noções Básicas

Em **noções básicas**, especifique valores para parâmetros padrão para qualquer implantação, como assinatura, grupo de recursos e propriedades de máquina virtual básica.

Segue uma descrição detalhada de cada parâmetro:

Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
---|---|---|---
Criar uma nova rede ou ingressar na rede existente?|Criar uma nova rede ou ingressar em uma rede consórcio pré-existente|Criar nova junção existente|Criar Novo
Endereço de email (opcional)|Você receberá uma notificação por email quando sua implantação for concluída com informações sobre sua implantação.|Endereço de email válido|ND
Nome de usuário da VM|Nome de usuário de administrador de cada VM implantada (somente caracteres alfanuméricos)|1-64 caracteres|ND
Tipo de autenticação|O método para autenticar a máquina virtual.|Senha ou chave pública SSH|Palavra-passe
Senha (tipo de autenticação = senha)|A senha da conta de administrador para cada uma das máquinas virtuais implantadas.  A senha deve conter 3 dos seguintes: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial. Embora todas as VMs tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12-72 caracteres|ND
Chave SSH (tipo de autenticação = chave pública)|A chave de shell seguro usada para logon remoto.||ND
Subscription|A assinatura na qual implantar a rede Consortium||ND
Grupo de Recursos|O grupo de recursos no qual implantar a rede Consortium.||ND
Location|A região do Azure para o grupo de recursos.||ND

Uma implantação de exemplo é mostrada ![abaixo: folha básica](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Regiões de implantação

Em seguida, em regiões de implantação, especifique entradas para o número de regiões para implantar a rede Consortium e a seleção de regiões do Azure com base no número de regiões fornecidas. O usuário pode implantar no máximo 5 regiões. É recomendável escolher a primeira região para corresponder à localização do grupo de recursos da seção básico. Para redes de desenvolvimento ou teste, é recomendável uma única região por membro. Para produção, recomendamos a implantação em duas ou mais regiões para alta disponibilidade.

Segue uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
  Número de região (s)|Número de regiões para implantar a rede Consortium|1, 2, 3, 4, 5|1
  Primeira região|Primeira região para implantar a rede Consortium|Todas as regiões do Azure permitidas|ND
  Segunda região|Segunda região para implantar a rede Consortium (visível somente quando o número de regiões é selecionado como 2)|Todas as regiões do Azure permitidas|ND
  Terceira região|Terceira região para implantar a rede do consórcio (visível somente quando o número de regiões é selecionado como 3)|Todas as regiões do Azure permitidas|ND
  Quarta região|Quarta região para implantar a rede Consortium (visível somente quando o número de regiões é selecionado como 4)|Todas as regiões do Azure permitidas|ND
  Quinta região|Quinta região para implantar a rede Consortium (visível somente quando o número de regiões é selecionado como 5)|Todas as regiões do Azure permitidas|ND

Uma implantação de exemplo é mostrada ![abaixo: regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Desempenho e tamanho da rede

Em seguida, em ' tamanho da rede e desempenho ', especifique entradas para o tamanho da rede do consórcio, como número e tamanho dos nós do validador.
O tamanho do armazenamento do nó do validador determinará o tamanho potencial do blockchain. Isso pode ser alterado após a implantação.

Segue uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
  Número de nós de validador com balanceamento de carga|O número de nós validadores para provisionar como parte da rede|2-15|2
  Desempenho de armazenamento do nó do validador|O tipo de disco gerenciado que faz o backup de cada um dos nós de validador implantados.|SSD Standard ou Premium|SSD Standard
  Tamanho da máquina virtual do nó do validador|O tamanho da máquina virtual usada para nós de validador.|Standard A, padrão D, Standard D-v2, série F Standard, DS padrão e Standard FS|Standard D1 v2

[Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/)

[Detalhes de preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

A máquina virtual e a camada de armazenamento afetarão o desempenho da rede.  Recomendamos as seguintes SKUs com base na eficiência de custo desejada:

  SKU da máquina virtual|Camada de armazenamento|Preço|Débito|Latência
  ---|---|---|---|---
  F1|SSD Standard|pequena|pequena|elevada
  D2_v3|SSD Standard|média|média|média
  F16s|Premium SSD|elevada|elevada|pequena

Uma implantação de exemplo é mostrada ![abaixo: tamanho e desempenho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Configurações de Ethereum

Em seguida, em configurações de Ethereum, especifique definições de configuração relacionadas a Ethereum, como a ID de rede e a senha da conta Ethereum ou o bloco Genesis.

Segue uma descrição detalhada de cada parâmetro:

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
ID do membro do consórcio|A ID associada a cada membro que participa da rede do consórcio usada para configurar espaços de endereço IP para evitar a colisão. No caso de uma rede privada, a ID de membro deve ser exclusiva em diferentes organizações na mesma rede.  Uma ID de membro exclusiva é necessária mesmo quando a mesma organização é implantada em várias regiões. Anote o valor desse parâmetro, pois você precisará compartilhá-lo com outros membros de ingresso para garantir que não haja colisão.|0-255|ND
ID da rede|A ID de rede para a rede do consórcio Ethereum que está sendo implantada.  Cada rede Ethereum tem sua própria ID de rede, com 1 sendo a ID da rede pública.|5-999.999.999|10101010
Endereço Ethereum do administrador|Endereço de conta do Ethereum que é usado para participar da governança do PoA.  É recomendável usar metamask para gerar um endereço Ethereum.|42 caracteres alfanuméricos começando com 0x|ND
Opções Avançadas|Opções avançadas para configurações de Ethereum|Habilitar ou desabilitar|Desativar
IP público (opções avançadas = habilitar)|Implanta a rede atrás de um gateway de VNet e remove o acesso de emparelhamento. Se essa opção for selecionada, todos os membros deverão usar um gateway de VNet para que a conexão seja compatível.|VNet privada de IP público|IP Público
Limite de gás de bloqueio (opções avançadas = habilitar)|O limite de gás de bloco inicial da rede|Qualquer numérico|50000000
Bloquear o período de Reseal (s)|A frequência na qual os blocos vazios serão criados quando não houver nenhuma transação na rede. Uma frequência mais alta terá uma finalização mais rápida, mas os custos de armazenamento aumentados.|Qualquer numérico|15
Contrato de permissão de transação (opções avançadas = habilitar)|Código de bytes para o contrato de permissão da transação. Restringe a implantação e a execução do contrato inteligente a uma lista permitida de contas do Ethereum.|Código de bytes do contrato|ND

Uma implantação de exemplo é mostrada ![abaixo: configurações de ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitorização

A folha monitoramento permite que você configure um recurso de logs de Azure Monitor para sua rede. O agente de monitoramento coletará e orientará as métricas e os logs úteis da sua rede, fornecendo a capacidade de verificar rapidamente a integridade da rede ou os problemas de depuração.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nome do parâmetro|Descrição|Valores permitidos|Valores predefinidos
  ---|---|---|---
Monitorização|Opção para habilitar o monitoramento|Habilitar ou desabilitar|Ativar
Conectar-se a logs de Azure Monitor existentes|Criar uma nova instância de logs de Azure Monitor ou ingressar em uma instância existente|Criar novo ou ingressar em existente|Criar novo
Local do monitor (conectar a logs de Azure Monitor existentes = criar novo)|A região em que a nova instância de logs de Azure Monitor será implantada|Todas as regiões de logs de Azure Monitor|ND
ID do espaço de trabalho do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|ID do espaço de trabalho da instância existente de logs de Azure Monitor||ND
Chave primária do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|A chave primária usada para se conectar à instância existente de logs de Azure Monitor||ND


Uma implantação de exemplo é mostrada ![abaixo: Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Resumo

Clique na folha Resumo para revisar as entradas especificadas e executar a validação de pré-implantação básica. Antes de implantar, você pode baixar o modelo e os parâmetros.

Examine os termos legais e de privacidade e clique em ' comprar ' para implantar. Se a implantação incluir gateways de VNet, a implantação ocupará de 45 a 50 minutos.

#### <a name="post-deployment"></a>Pós-implantação

##### <a name="deployment-output"></a>Saída de implantação

Depois que a implantação for concluída, você poderá acessar os parâmetros necessários por meio do email de confirmação ou por meio do portal do Azure. Nesses parâmetros, você encontrará:

-   Ponto de extremidade RPC Ethereum

-   URL do painel de governança

-   URL de Azure Monitor

-   URL de dados

-   ID de recurso do gateway de VNet (opcional)

##### <a name="confirmation-email"></a>Email de confirmação

Se você fornecer um endereço de email ([seção noções básicas](#basics)), um email será enviado para o endereço de email com as informações de saída da implantação.

![email de implantação](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Depois que a implantação for concluída com êxito e todos os recursos tiverem sido provisionados, você poderá exibir os parâmetros de saída em seu grupo de recursos.

1.  Localize seu grupo de recursos no portal

2.  Navegar até implantações

3.  Selecione a implantação principal com o mesmo nome que o seu grupo de recursos

4.  Selecionar *saídas*

### <a name="growing-the-consortium"></a>Aumentando o consórcio

Para expandir seu Consórcio, primeiro você deve conectar a rede física.
Usando a implantação pública baseada em IP, essa primeira etapa é simples. Se estiver implantando por trás de uma VPN, consulte a seção conectando o [Gateway de VNet](#connecting-vnet-gateways) para fazer a conexão de rede como parte da implantação do novo membro.  Quando a implantação for concluída, use o [DApp de governança](#governance-dapp) para se tornar um administrador de rede.

#### <a name="new-member-deployment"></a>Nova implantação de membro

1.  Compartilhe as informações a seguir com o membro de junção. Essas informações podem ser encontradas em seu email pós-implantação ou na saída de implantação do Portal.

    -  URL de dados do consórcio

    -  O número de nós que você implantou

    -  ID de recurso do gateway de VNet (se estiver usando VPN)

2.  O membro de implantação deve usar a [mesma solução](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) ao implantar sua presença de rede, mantendo o seguinte em mente:

    -  Selecionar *junção existente*

    -  Escolha o mesmo número de nós de validador que o restante dos membros na rede para garantir a representação justa

    -  Use o mesmo endereço Ethereum que foi fornecido na etapa anterior

    -  Passe a URL de *dados do consórcio* fornecida na guia *configurações do Ethereum*

    -  Se o restante da rede estiver atrás de uma VPN, selecione *VNet privada* na seção avançado

#### <a name="connecting-vnet-gateways"></a>Conectando gateways de VNet

Você poderá ignorar esta etapa se tiver implantado usando as configurações de IP público padrão. No caso de uma rede privada, os diferentes membros são conectados por meio de conexões de gateway de VNet. Antes que um membro possa ingressar na rede e ver o tráfego da transação, um membro existente deve fazer uma configuração final em seu gateway de VPN para aceitar a conexão. Isso significa que os nós Ethereum do membro de junção não serão executados até que uma conexão seja estabelecida. É recomendável criar conexões de rede redundantes (malha) no consórcio para reduzir as chances de um único ponto de falha.

Depois que o novo membro for implantado, o membro existente deverá concluir a conexão bidirecional Configurando uma conexão de gateway de VNet para o novo membro. Para conseguir isso, o membro existente precisará de:

1.  O ResourceId do gateway de VNet do membro de conexão (consulte a saída de implantação)

2.  A chave de conexão compartilhada

O membro existente deve executar o seguinte script do PowerShell para concluir a conexão. É recomendável usar Azure Cloud Shell localizado na barra de navegação superior direita no Portal.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

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
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitorização de serviços

Você pode localizar seu portal de Azure monitor seguindo o link no email de implantação ou localizando o parâmetro na \[URL\]do portal\_do\_OMS de saída de implantação.

O portal primeiro exibirá as estatísticas de rede de alto nível e a visão geral do nó.

![categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

A seleção da **visão geral do nó** o direcionará a um portal para exibir estatísticas de infraestrutura por nó.

![Estatísticas de nó](./media/ethereum-poa-deployment/node-stats.png)

A seleção de **Estatísticas de rede** irá direcioná-lo para exibir as estatísticas de rede Ethereum.

![Estatísticas de rede](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Consultas Kusto de exemplo

Por trás desses painéis, há um conjunto de logs brutos consultáveis. Você pode usar esses logs brutos para personalizar os painéis, investigar falhas ou configurar alertas de limite. Abaixo você encontrará um conjunto de consultas de exemplo que podem ser executadas na ferramenta de pesquisa de logs:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Lista os blocos que foram relatados por mais de um validador. Útil para ajudar a localizar bifurcações de cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Obter a contagem média de pares de um nó validador especificado com média de 5 minutos de buckets.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>Acesso SSH

Por motivos de segurança, o acesso à porta SSH é negado por uma regra de segurança de grupo de rede por padrão. Para acessar as instâncias de máquina virtual na rede POA, você precisará alterar essa regra para \"permitir\"

1.  Inicie na seção visão geral do grupo de recursos implantado em portal do Azure.

    ![Visão geral do ssh](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Selecione o grupo de segurança de rede para a região da VM que você deseja acessar

    ![NSG SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Selecione a \"regra Allow-\" SSH

    ![SSH-permitir](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Alterar \"ação\" para permitir

    ![permitir habilitação de SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Clique \"em\" salvar (as alterações podem levar alguns minutos para serem aplicadas)

Agora você pode se conectar remotamente às máquinas virtuais para os nós de validador via SSH com o nome de usuário do administrador fornecido e a chave de senha/SSH.
O comando ssh a ser executado para acessar o primeiro nó de validador está listado no parâmetro de saída de implantação de\_modelo como\_,\_'\_SSH para\_o primeiro REGION1 do nó VL ' (para a implantação de exemplo: ssh-p 4000 poaadmin\@leader4vb.eastus.cloudapp.Azure.com). Para obter os nós de transação adicionais, aumente o número da porta por um (por exemplo, o primeiro nó de transação está na porta 4000).

Se você implantou em mais de uma região, altere o comando acima para o nome DNS ou endereço IP do balanceador de carga nessa região. Para localizar o nome DNS ou o endereço IP das outras regiões, localize o recurso com \*a Convenção \* \* \* \*de nomenclatura-lbpip-\#reg e exiba suas propriedades de nome DNS e endereço IP.

### <a name="azure-traffic-manager-load-balancing"></a>Balanceamento de carga do Gerenciador de tráfego do Azure

O Gerenciador de tráfego do Azure pode ajudar a reduzir o tempo de inatividade e melhorar a capacidade de resposta da rede PoA roteando o tráfego de entrada entre várias implantações em regiões diferentes. As verificações de integridade internas e o novo roteamento automático ajudam a garantir a alta disponibilidade dos pontos de extremidade RPC e do DApp de governança. Esse recurso será útil se você tiver implantado em várias regiões e estiver pronto para produção.

Use o Gerenciador de tráfego para:

-   Melhorar a disponibilidade da rede PoA com failover automático.

-   Aumente a capacidade de resposta de suas redes roteando os usuários finais para o local do Azure com menor latência de rede.

Se você decidir criar um perfil do Gerenciador de tráfego, poderá usar o nome DNS do perfil para acessar sua rede. Depois que outros membros do Consortium tiverem sido adicionados à rede, o Gerenciador de tráfego também poderá ser usado para balancear a carga entre seus validadores implantados.

#### <a name="creating-a-traffic-manager-profile"></a>Criando um perfil do Gerenciador de tráfego

Pesquise e selecione \"o perfil\" do Gerenciador de tráfego depois \"de clicar no\" botão criar um recurso na portal do Azure.

![Pesquisar o Gerenciador de tráfego do Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

Dê um nome exclusivo ao perfil e selecione o grupo de recursos que foi criado durante a implantação do PoA. Clique no botão "criar" para implantar.

![criar Gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

Depois de implantado, selecione a instância no grupo de recursos. O nome DNS para acessar o Gerenciador de tráfego pode ser encontrado na guia Visão geral

![Localizar o DNS do Gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Selecione a guia pontos de extremidade e clique no botão Adicionar. Dê um nome exclusivo ao ponto de extremidade. Altere o tipo de recurso de destino para endereço IP público. Em seguida, selecione o endereço IP público do balanceador de carga da primeira região\'.

![Gerenciador de tráfego de roteamento](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implantada. Depois que os pontos de extremidade estiverem no \"status\" habilitado, eles serão carregados automaticamente e com a região balanceada no nome DNS do Gerenciador de tráfego. Agora você pode usar esse nome DNS \[no lugar do parâmetro URL\_\] de\_dados do consórcio em outras etapas do documento.

### <a name="data-api"></a>API de dados

Cada membro do Consortium hospeda as informações necessárias para que outras pessoas se conectem à rede. O membro existente fornecerá o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implantação, um membro de junção recuperará informações da interface JSON no seguinte ponto de extremidade:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta conterá informações úteis para ingressar Membros (bloco Genesis, ABI do contrato de conjunto de validadores, bootnodes) e informações úteis para o membro existente (endereços do validador). Incentivamos o uso dessa padronização para estender o Consórcio entre provedores de nuvem. Essa API retornará uma resposta formatada em JSON com a seguinte estrutura:
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

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interagindo programaticamente com um contrato inteligente

> [!WARNING]
> Nunca envie sua chave privada Ethereum pela rede! Verifique se cada transação é assinada localmente e se a transação assinada é enviada pela rede.

No exemplo a seguir, usamos *ethereumjs-carteira* para gerar um endereço Ethereum, *ethereumjs-TX* para entrar localmente e *Web3* para enviar a transação bruta para o ponto de extremidade RPC Ethereum.

Usaremos este contrato inteligente Hello World simples para este exemplo:

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

Este exemplo supõe que o contrato já está implantado. Você pode usar *SOLC* e *Web3* para implantar um contrato programaticamente. Primeiro instale os seguintes módulos de nó:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Este script nodeJS executará o seguinte:

-   Construir uma transação bruta: postMsg

-   Assinar a transação usando a chave privada gerada

-   Enviar a transação assinada para a rede Ethereum

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

### <a name="deploy-smart-contract-with-truffle"></a>Implantar um contrato inteligente com o Truffle

-   Instalar as bibliotecas necessárias

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   No Truffle. js, adicione o código a seguir para desbloquear sua conta de metamáscara e configurar o nó PoA como ponto de entrada fornecendo a frase mnemônico (metamask/configurações/revelar palavras de semente)

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

-   Implantar na rede PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Depurar o contrato inteligente com o Truffle

O Truffle tem uma rede local de desenvolvimento que está disponível para depuração de contrato inteligente. Você pode encontrar o tutorial completo [aqui](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Suporte a Webassembly (WASM)

O suporte ao Webassembly já está habilitado para você em redes PoA implantadas recentemente. Ele permite o desenvolvimento de um contrato inteligente em qualquer linguagem que seja transformada em assembly da Web (Rust, C++C,). Consulte os links abaixo para obter informações adicionais

-   Visão geral de paridade do Webassembly-<https://wiki.parity.io/WebAssembly-Home>

-   Tutorial do Parity Tech-<https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referência

### <a name="faq"></a>FAQ

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Eu observo que há muitas transações na rede que não enviei para eu\'. De onde eles estão vindos?

Não é seguro desbloquear a [API pessoal](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Os bots escutam contas desbloqueadas do Ethereum e tentam drenar os fundos. O bot pressupõe que essas contas contenham real-ether e tente ser a primeira a desviarr o saldo. Não habilite a API pessoal na rede. Em vez disso, assine as transações manualmente usando uma carteira, como metamask ou programaticamente, conforme descrito na seção interagindo programaticamente [com um contrato inteligente](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Como fazer SSH em uma VM?

A porta SSH não é exposta por motivos de segurança. Siga [este guia para habilitar a porta SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como fazer configurar um membro de auditoria ou nós de transação?

Os nós de transação são um conjunto de clientes de paridade emparelhados com a rede, mas que não estão participando de consenso. Esses nós ainda podem ser usados para enviar transações Ethereum e ler o estado do contrato inteligente.
Isso funciona bem como um mecanismo para fornecer a auditoria a membros de um consórcio que não sejam de autoridade na rede. Para conseguir isso, basta seguir a etapa 2 do crescimento do consórcio.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Por que as transações de metamáscara estão demorando muito tempo?

Para garantir que as transações sejam recebidas na ordem correta, cada transação Ethereum vem com um nonce de incremento. Se você usou uma conta em metamask em uma rede diferente, precisará redefinir o valor de nonce. Clique no ícone de configurações (barras 3), configurações, redefinir conta. O histórico de transações será limpo e agora você poderá reenviar a transação.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>É necessário especificar a taxa de gás em metamask?

O Ether não atende a uma finalidade no consórcio de prova de autoridade. Portanto, não é necessário especificar a taxa de gás ao enviar transações na metamáscara.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que devo fazer se a minha implantação falhar devido à falha ao provisionar o OMS do Azure?

O monitoramento é um recurso opcional. Em alguns casos raros em que a implantação falha devido à incapacidade de provisionar com êxito Azure Monitor recurso que você pode reimplantar sem Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implantações de IP público são compatíveis com implantações de rede privada?

Não, o emparelhamento requer comunicação bidirecional para que toda a rede seja pública ou privada.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é a taxa de transferência de transação esperada de prova de autoridade?

A taxa de transferência da transação será altamente dependente dos tipos de transações e da topologia de rede.  Usando transações simples, fizemos um benchmark de uma média de 400 transações por segundo com uma rede implantada em várias regiões.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como fazer assinar eventos de contrato inteligente?

A prova de autoridade do Ethereum agora dá suporte a soquetes da Web.  Verifique seu email de implantação ou saída de implantação para localizar a URL e a porta do soquete da Web.

## <a name="next-steps"></a>Passos seguintes

Comece usando a solução [consórcio de prova de autoridade Ethereum](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) .
