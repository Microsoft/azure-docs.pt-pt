---
title: Modelo de solução de prova de trabalho consortium Ethereum
description: Utilizar o modelo de solução de consórcio Ethereum prova de trabalho para implementar e configurar uma rede Ethereum de consórcio com vários membros
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 6530e86f59124e324953e4ca082c247ebec3bc91
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650925"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Modelo de solução de prova de trabalho consortium Ethereum

O modelo de solução de consórcio Ethereum prova de trabalho foi concebido para que seja mais fácil e rápido implementar e configurar uma rede Ethereum de consórcio com vários membros com o mínimo de conhecimento do Azure e Ethereum.

Utilizar um modelo Azure Resource Manager, pode aprovisionar cada membro seus requisitos de espaço da rede, usando a computação do Microsoft Azure, rede e serviços de armazenamento. Requisitos de espaço de rede de cada membro é composta por um conjunto de nós de transação com balanceamento de carga com que uma aplicação ou o utilizador interage para submeter transações, um conjunto de nós de extração para registrar transações e um gateway de VPN. Após a implementação, ligar os gateways para criar uma rede de totalmente configurado com vários membros blockchain.

## <a name="about-blockchain"></a>Sobre blockchain

Para aqueles que são novos para a Comunidade de blockchain, a versão dessa solução é uma ótima oportunidade para saber mais sobre a tecnologia de maneira fácil e configurável no Azure. No entanto, para começar a utilizar, é recomendável implementar a topologia de rede do autónomo Ethereum mais simples com este instruções orientadas, antes de criar redes de consórcio com vários membros.

### <a name="mining-node-details"></a>Detalhes do nó de extração

Nós que exploramos as transações são separadas dos nós que aceitam transações para garantir que as duas ações não estejam competindo para os mesmos recursos.

Um membro de consortium pode aprovisionar até cinco regiões que contém um ou mais nós de extração, apoiados por um disco gerido. Um ou mais nós na região são configurados como um nó de arranque para oferecer suporte a capacidade de deteção dinâmica de nós na rede. Nós de extração se comunicar com outros nós de extração até o consenso sobre o estado da razão distribuído subjacente. Não é necessário para a sua aplicação conhecer ou se comunicar com esses nós. Ao concentrar-se em redes privadas, os nós de extração são o modo isolados do tráfego de internet pública entrada para adicionar uma camada secundária de proteção. Tráfego de saída é permitido, mas não para a porta de deteção Ethereum.

Todos os nós têm uma versão estável do cliente aceda Ethereum (Geth) e estão configurados para serem nós de extração. Se não fornece um bloco de genesis personalizada, todos os nós usam o mesmo Ethereum endereço e um par de chaves protegido pela palavra-passe da conta de Ethereum. A frase de acesso de Ethereum fornecida é utilizado para gerar a conta predefinida (coinbase) para cada nó de extração. Como nós de extração, extrair, eles cobrar as tarifas de que são adicionadas a esta conta.

O número de nós de extração por membro consortium depende o tamanho geral de rede pretendido e a quantidade de energia de hash que é dedicada a cada membro. Redes maiores necessitam de mais nós para ficar comprometido para obter uma vantagem injusta. O modelo suporta até 15 nós de extração por região aprovisionados com conjuntos de dimensionamento de máquina virtual.

### <a name="transaction-node-details"></a>Detalhes do nó de transação

Um membro de consortium também tem um conjunto de nós de transação com balanceamento de carga. Estes nós estão acessíveis a partir de fora da rede virtual, para que os aplicativos podem usar esses nós para submeter transações ou executar contratos inteligentes dentro da rede de blockchain. Todos os nós têm uma versão estável do cliente aceda Ethereum (Geth) e estão configurados para manter uma cópia completa do livro razão distribuído. Se não for fornecido um bloco de genesis personalizado, estes nós utilizam a mesma conta de Ethereum, protegida pela palavra-passe da conta de Ethereum.

Os nós de transação são com balanceamento de carga no conjunto de disponibilidade para manter uma elevada disponibilidade. O modelo suporta até cinco nós de transação aprovisionados com conjuntos de dimensionamento de máquina virtual.

### <a name="azure-monitor-logs-details"></a>Monitor do Azure regista os detalhes

Cada implementação também cria uma nova instância de registos do Azure Monitor ou pode associar uma instância existente. Registos de Monitor do Azure permite a monitorização de várias métricas de desempenho de cada máquina virtual que constitui a rede implementada.

## <a name="deployment-architecture"></a>Arquitetura de implantação

### <a name="description"></a>Descrição

Este modelo de solução pode implementar única ou de múltiplas rede de consórcio com base na região várias membro Ethereum. A rede virtual de cada região está ligada a outra região numa topologia de cadeia com os gateways de VNET e recursos de ligação. Ele também fornece uma entidade de registo, que contém as informações necessárias de todos os nós de transações e Miner implementado em cada região.

### <a name="standalone-and-consortium-leader-overview"></a>Descrição geral do líder autónomo e consortium

![Autónomo e a descrição geral do consórcio líder](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Descrição geral de membro de consórcio junção

![Descrição geral de membro de consórcio junção](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Introdução

Este processo requer uma subscrição do Azure que pode suportar a implementação de vários conjuntos de dimensionamento de máquinas virtuais e discos geridos. Se necessário, crie uma conta gratuita do Azure para começar.

Depois de uma subscrição está protegida, aceda ao portal do Azure. Selecione **+ criar um recurso**, Marketplace (ver todos) e procure **Ethereum prova de trabalho Consortium**.

A implementação do modelo explica como configurar requisitos de espaço do primeiro membro na rede. O fluxo de implementação está dividido em cinco etapas: Noções básicas, Operations Management Suite, regiões de implementação, o tamanho de rede e desempenho, Ethereum definições.

### <a name="basics"></a>Noções básicas

Sob **Noções básicas**, especifique os valores de parâmetros padrão para qualquer implementação, tais como a subscrição, grupo de recursos e propriedades de máquinas virtuais básico.

![Noções básicas](./media/ethereum-deployment/sample-deployment.png)

Nome do Parâmetro|Descrição| Valores Permitidos|Valores predefinidos
---|---|---|---
Criar uma nova rede ou de rede existente de associação?|Crie uma nova rede ou ingresse numa rede de consórcio já existente|Criar novo de junção existentes|Criar Nova
Implementar uma rede que vai fazer parte de um consórcio?|Uma rede de consórcio permite que as futuras Implantações aderir a esta rede (visível quando *criar novo* é selecionado acima)|Consortium autónomo|Autónomo
Prefixo de recursos |A cadeia de caracteres usada como base para atribuir nomes a recursos (carateres de alfanuméricos de 2 a 4). Um valor hash exclusivo é anexado à cadeia de caracteres para alguns recursos, enquanto as informações de recursos específicos são acrescentadas.|Carateres de alfanuméricos com comprimento 2 a 4|ND
Nome de utilizador VM| Nome de utilizador de administrador de cada VM implementada (apenas carateres alfanuméricos)|1 - 64 carateres |gethadmin
Tipo de autenticação|O método para autenticar para a máquina virtual. |Chave pública de palavra-passe ou SSH|Palavra-passe
Palavra-passe (tipo de autenticação = a palavra-passe)|A palavra-passe da conta de administrador para cada uma das máquinas virtuais implementadas. A palavra-passe tem de conter 3 dos seguintes requisitos: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial. <br />Embora todas as VMs tenham inicialmente a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento.|12 - 72 carateres|ND
Chave SSH (tipo de autenticação = a chave pública)|A chave secure shell utilizada para início de sessão remoto.|| ND
Subscrição| A subscrição ao qual pretende implementar a rede de consórcio||ND
Grupo de Recursos| O grupo de recursos ao qual pretende implementar a rede de consórcio.||ND
Localização| A região do Azure para o grupo de recursos. ||ND

### <a name="operations-management-suite"></a>Operations Management Suite

Operations Management Suite (OMS) permite-lhe configurar um recurso OMS para a sua rede. O OMS recolherá e superfície métricas úteis e os registos da sua rede, fornecendo a capacidade de verificar rapidamente o estado de funcionamento de rede ou a depuração de problemas. A oferta gratuita do OMS simplesmente falhará assim que a capacidade for atingida.

![Criar novo OMS](./media/ethereum-deployment/new-oms.png)

Nome do Parâmetro|Descrição| Valores Permitidos|Valores predefinidos
---|---|---|---
Ligar ao OMS existente|Crie uma nova instância de registos do Azure Monitor ou ingresse numa instância existente|Criar novo de junção existentes|Criar novo local de registos do Azure Monitor|A região onde serão implementados novos registos de Azure Monitor (estará visível se estiver *criar novo* está selecionado)
Id de área de trabalho do OMS existente|ID da área de trabalho da instância existente (estará visível se estiver *associar existente* está selecionado) escalão de serviço do OMS|Escolha o escalão de preço para a nova instância. Obter mais informações em https://azure.microsoft.com/pricing/details/log-analytics/ (estará visível se estiver *associar existente* está selecionado)|Autônomo gratuito por nó|Gratuito
Chave primária de OMS existente|A chave primária utilizada para ligar à instância OMS existente (estará visível se estiver *associar existente* está selecionado)

### <a name="deployment-regions"></a>Regiões de implementação

Em seguida, em **regiões de implementação**, especifique as entradas para **número de regiões** para implementar a rede de consórcio e seleção de regiões do Azure com base no número de regiões tendo em conta. Utilizador pode implementar no máximo, cinco regiões.

![Regiões de implementação](./media/ethereum-deployment/deployment-regions.png)

Nome do Parâmetro| Descrição| Valores Permitidos |Valores predefinidos
---|---|---|---
Número de regiões| Número de regiões para implementar a rede de consórcio|1, 2, 3, 4, 5| 2
Primeira região| Primeira região para implementar a rede de consórcio|Permitido todas as regiões do Azure| EUA Oeste
Segunda região |Segunda região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 2)|Permitido todas as regiões do Azure| EUA Leste
Terceira região| Terceira região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 3)|Permitido todas as regiões do Azure| EUA Central
Região quarto| Quarto região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 4)|Permitido todas as regiões do Azure| EUA Leste 2
Quinto região| Quinto região para implementar a rede de consórcio (visível apenas quando o número de regiões está selecionado como 5)|Permitido todas as regiões do Azure| EUA Oeste 2

### <a name="network-size-and-performance"></a>Tamanho de rede e o desempenho

Em seguida, em **tamanho e o desempenho de rede** especificar entradas para o tamanho da rede de consórcio. Como o número e tamanho de nós de extração e os nós de transação.

![Tamanho de rede e o desempenho](./media/ethereum-deployment/network-size-performance.png)

Nome do Parâmetro |Descrição |Valores Permitidos| Valores predefinidos
---|---|---|---
Número de nós de extração|O número de nós de extração implementado por região|2 - 15| 2
Desempenho de armazenamento do nó de extração|O tipo de disco gerido de segurança todos os nós de extração implementado.|Standard ou Premium|Standard
Tamanho de máquina virtual do nó de extração|O tamanho de máquina virtual utilizado para nós de extração.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Série F Standard, <br />Standard DS, <br />e o Standard FS|Standard D1v2
Número de nós de transação com balanceamento de carga|O número de nós de transação para aprovisionar como parte da rede.|1 - 5| 2
Desempenho de armazenamento do nó de transação|O tipo de disco gerido de segurança todos os nós de transação implementado.|Standard ou Premium|Standard
Tamanho de máquina virtual do nó de transação|O tamanho de máquina virtual utilizado para nós de transação.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Série F Standard, <br />Standard DS, <br />e o Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Ethereum definições

Em seguida, em **Ethereum definições**, especificar definições de configuração relacionadas com o Ethereum, como o ID e Ethereum conta palavra-passe ou genesis bloco de rede.

![Ethereum definições](./media/ethereum-deployment/standalone-leader-deployment.png)

Nome do Parâmetro |Descrição |Valores Permitidos|Valores predefinidos
---|---|---|---
ID de ConsortiumMember|O ID associado a cada membro participar na rede de consórcio utilizada para configurar espaços de endereços IP para evitar a colisão. <br /><br />ID de membro deve ser exclusivo em organizações diferentes na mesma rede. Um ID de membro exclusivo é necessário, mesmo quando a mesma organização implementa em várias regiões.<br /><br />Tome nota do valor deste parâmetro, uma vez que terá de partilhá-lo com outros membros da junção.|0 - 255
ID de rede Ethereum|O ID de rede para a rede de Ethereum consortium a ser implementado. Cada rede Ethereum tem seu próprio ID de rede, com 1 indicador o ID para a rede pública. Embora o acesso de rede é restrito para nós de extração, ainda Recomendamos que utilize um grande número para evitar colisões.|5 - 999,999,999| 10101010
Bloco de genesis personalizado|Opção de automaticamente gerar um bloco de genesis ou fornecer um personalizado.|Sim/Não| Não
Palavra-passe da conta de Ethereum (bloco de genesis personalizado = não)|A palavra-passe de administrador utilizada para proteger a conta de Ethereum importada para cada nó. Tem de conter a palavra-passe: 1 caráter em maiúsculas, 1 carater em minúsculas e 1 número.|12 ou mais carateres|ND
Ethereum frase de acesso privado chave (bloco de genesis personalizado = não)|A frase de acesso utilizada para gerar a chave privada de ECC associada à conta Ethereum predefinido que é gerada. Uma chave privada gerada não precisa de ser passados explicitamente.<br /><br />Considere uma frase de acesso com aleatoriedade suficiente para garantir uma chave privada forte e sem sobreposição com outros membros consortium. A frase de acesso tem de conter pelo menos: 1 caráter em maiúsculas, 1 carater em minúsculas e 1 número.<br /><br />Tenha em atenção de que se dois membros de usar a mesma frase de acesso as contas geradas será o mesmo. A mesma frase de acesso é útil se uma única organização está a tentar implementar em várias regiões e pretende partilhar uma única conta (moeda base) em todos os nós.|12 ou mais carateres|ND
Bloco de Genesis (bloco de genesis personalizado = Yes)|Cadeia de caracteres JSON que representa o bloco de genesis personalizado. Pode encontrar mais detalhes sobre o formato do bloco genesis aqui, em redes de personalizado.<br /><br />Ainda é criada uma conta de Ethereum ao fornecer um bloco de genesis personalizado. Considere especificar uma conta de Ethereum prefunded no bloco de genesis não aguardar a extração.|Valid JSON |ND
A chave para a ligação partilhada|Uma chave partilhada para a ligação entre os gateways VNET.| 12 ou mais carateres|ND
Consortium Data URL|O URL que aponta para os dados de configuração de consórcio relevantes fornecidos pela implementação de outro membro. <br /><br />Estas informações são fornecidas por um membro já ligado que tenha uma implementação. Se tiver implementado o restante da rede, o URL é a saída de modelos de implementação, dados de CONSÓRCIO com o nome.||ND
Gateway de VNet para ligar a|O caminho de recurso do Gateway de VNet para estabelecer ligação.<br />Estas informações são fornecidas por um membro já ligado que tenha uma implementação. Se tiver implementado o restante da rede, o URL é na saída de implementação do modelo, com o nome CONSORTIUM_MEMBER_GATEWAY_ID. Nota: Tem de ser utilizado consortium URL e o Gateway de VNet recurso de dados o mesmo membro.||ND
Ponto final da entidade de registo de informações de ponto a ponto|Ponto final de informações de ponto a ponto fornecida pela implementação de outro membro|Ponto final válido do primeiro membro no consortium|ND
Entidade de registo de informações de chave de ponto a ponto|Chave de principal de informações de ponto a ponto fornecida pela implementação de outro membro|Chave primária válida do primeiro membro no consortium|ND

### <a name="summary"></a>Resumo

Clique em Resumo para rever as entradas especificadas e para executar a validação de pré-implementação básica.

![Resumo](./media/ethereum-deployment/summary.png)

Reveja os termos legais e privacidade e clique em **Compra** para implementar. Se a implementação tem mais do que uma região ou destina-se uma rede de consórcio, em seguida, este modelo previamente implementa os gateways de VPN necessários para suportar a conectividade de rede com outros membros. Implementação do gateway pode demorar até 45 minutos 50.

## <a name="post-deployment-sanity-checks"></a>Verificações de sanidade de implementação de publicação

### <a name="administrator-page"></a>Página de administrador

Depois de concluída a implementação com êxito e todos os recursos aprovisionados, pode ir para a página de administrador para obter uma vista de sua rede de blockchain e sanidade verificar o estado de implementação. O URL da página de administração é o nome DNS do Balanceador de carga; ele está presente na secção de saída da implementação do modelo com o nome como ADMIN_SITE.

Para encontrá-lo, selecione o grupo de recursos que foi implementado. Em seguida, selecione **descrição geral**e clique no link imediatamente sob **implementações** que mostra o número que foi concluída com êxito.

![Descrição geral do grupo de recursos](./media/ethereum-deployment/resource-overview.png)

O novo ecrã mostra o histórico de implementação. Selecione o primeiro recurso de implantação (por exemplo, microsoft-azure-blockchain.azure-blockchain-servi...) e procure o **saídas** secção na metade inferior da página. Verá o URL para a página de administrador listado no parâmetro de saída de implementação de modelo como ADMIN_SITE.

![Implementações de recursos](./media/ethereum-deployment/resource-deployments.png)

![Saída de implementação](./media/ethereum-deployment/deployment-output.png)

Para obter a página de administração, copie os **SITE de administração** de saída e abri-lo no outro separador.

Na página de administração, pode obter uma visão geral sobre a topologia que implementou ao rever a secção de estado do nó Ethereum. Ele inclui todos os nomes de anfitrião do nó, sua contagem de ponto a ponto e o bloco mais recente visto. A contagem de ponto a ponto para cada nó é entre o mínimo de um menos do que o *total de contagem de nós* e a contagem de ponto a ponto de máximo configurado. Tenha em atenção que a contagem de ponto a ponto não limitar o número de nós que pode ser implementado na rede.
Ocasionalmente, verá a contagem de ponto a ponto flutuam e ser inferior a (número total de nós - 1). A diferença na contagem nem sempre é um sinal de que os nós são mau estado de funcionamento, uma vez que bifurcações no razão podem fazer com que algumas pequenas alterações na contagem de ponto a ponto. Por fim, pode inspecionar o bloco mais recente visto por cada nó na rede para determinar o bifurcações ou lags no sistema.

![Estado do nó](./media/ethereum-deployment/node-status.png)

O estado do nó é atualizado a cada 10 segundos. Recarregue a página por meio do navegador ou **recarregar** botão para atualizar a vista.

### <a name="oms-portal"></a>Portal do OMS

Pode localizar o seu portal do OMS ao seguir o link na saída de implementação (OMSPORTALURL) ou ao selecionar o recurso do OMS no seu grupo de recursos implementados.

![OMS URL](./media/ethereum-deployment/oms-url.png)

![Recursos do OMS](./media/ethereum-deployment/oms-resource.png)

O portal primeiro irá apresentar as estatísticas de rede de alto nível como uma descrição geral.

![Descrição geral do OMS](./media/ethereum-deployment/oms-overview.png)

Ao clicar na descrição geral irá direcioná-lo para um portal para ver estatísticas por nó.

![Estatísticas de por nó](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Aceder a nós

Pode ligar remotamente para as máquinas virtuais para os nós de transação através de SSH com a sua chave de nome de utilizador e palavra-passe/SSH de administrador fornecida. Uma vez que o nó de transação VMs não têm seus próprios endereços IP públicos, terá de passar pelo balanceador de carga e especifique o número de porta. O comando SSH para ser executada para aceder ao primeiro nó de transação está listado no parâmetro de saída de implementação de modelo como, **SSH_TO_FIRST_TX_NODE** (para a implementação de exemplo: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Para aceder a nós de transações adicionais, incrementar o número de porta por um (por exemplo, o primeiro nó de transação é na porta 4000).

Uma vez que as máquinas virtuais em que os nós de extração executam não estão acessíveis externamente, terá de passar por um de nós de transação. Assim que tiver uma sessão SSH para um nó de transação, instale a chave privada no nó de transação ou utilize a palavra-passe para iniciar uma sessão SSH em qualquer um de nós de extração.

**Nota**

Os nomes de anfitrião podem ser obtidos a partir do Site de administração ou do portal do Azure. No portal do Azure, os nomes de anfitrião de nós presentes no recurso de conjunto (VMSS) de dimensionamento da máquina virtual está listado em **instâncias**, sendo assim diferente dos nomes de anfitrião real. Por exemplo, o nome de anfitrião no portal do Azure pode ter o seguinte aspeto **mn-asdfmv-reg1_0** , mas o nome do anfitrião real seria como **mn-asdfmv-reg**.

Por exemplo:

Nome de anfitrião do portal do Azure| Nome do anfitrião real
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Adicionar um novo membro de consórcio

### <a name="sharing-data"></a>Partilha de dados

Como o primeiro membro (ou um membro ligado) do consortium, terá de fornecer algumas partes de informações de outros membros para que possam associar e estabelecer sua conexão. Especificamente:

1. **Dados de configuração de consórcio partilhados**: Existe um conjunto de dados que são utilizados para orquestrar a ligação de Ethereum entre dois membros. As informações necessárias, incluindo o bloco de genesis, ID de rede de consórcio e nós de arranque, são escritas num ficheiro em nós de transação do coordenador ou outro membro implementado. A localização desse arquivo está listada no parâmetro de saída de implementação de modelo com o nome **dados de CONSÓRCIO**.
2. **Configurar o peering em ponto final de informações**: O ponto de extremidade da entidade de registo de informações ponto a ponto para obter informações de todos os nós já ligado à rede Ethereum a partir do que os líderes ou implementação de outro membro. Os arquivos de DB um conjunto de informações sobre cada nó ligado, na rede, informações como o nome de anfitrião do nó, privada, o endereço IP etc. Este é o parâmetro de saída de implementação do modelo com o nome **PEER_INFO_ENDPOINT**.
3. **Configurar o peering em informações de chave primária**: Chave primária de entidade de registo de informações do elemento de rede é utilizada para obter acesso à chave do informações de ponto a ponto principal do coordenador ou outro membro. Este é o parâmetro de saída de implementação do modelo com o nome **PEER_INFO_PRIMARY_KEY**.


4. **Gateway VNET**: Cada membro estabelece uma ligação à rede de blockchain inteira por meio de um membro existente. Para ligar a VNET, tem do caminho de recurso para o Gateway de VNET do membro ao qual está a ligar. Este é o parâmetro de saída de implementação do modelo com o nome **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Chave partilhada**: Um segredo previamente estabelecido entre dois membros da rede de consórcio que estão a estabelecer uma ligação. Esta é uma cadeia alfanumérica (entre 1 e 128 carateres) que tenha sido combinado entre fora do contexto da implantação. (Por exemplo, **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Aceitação do novo membro

Este passo deve ser feito após o membro de junção foi implantado corretamente sua rede. Antes de um membro pode associar a rede e ver o tráfego de transação, um membro existente tem de efetuar uma configuração final no seu Gateway de VPN para aceitar a ligação. Isso significa que os nós de Ethereum do membro junção serão executada quando é estabelecida uma ligação. Esta configuração pode ser feita através do PowerShell ou o xPlat CLI. Um script CLI do PowerShell módulo e xPlat também são armazenadas no nó de transação, juntamente com os dados de consórcio. A localização do script é os parâmetros de saída de implementação com o nome **par-GATEWAY-PS-MODULE** e **par-GATEWAY-AZURE-CLISCRIPT**, respectivamente.

**Configuração do PowerShell/CLI**

Obter mais informações sobre como começar com cmdlets do Azure PowerShell e xPlat do Azure CLI pode ser encontrado na documentação do Azure.

Terá a versão mais recente dos cmdlets do Azure instalados localmente e uma sessão aberta. Certifique-se iniciar sessão para a sessão com as suas credenciais de subscrição do Azure.

**PowerShell: Estabelecer ligação**

Transferir o módulo do PowerShell e armazená-lo localmente. A localização do módulo do PowerShell está especificada como a **par-GATEWAY-PS-MODULE** parâmetro de saída de implementação do modelo.

Se ainda não estiver ativada, utilize o **Set-ExecutionPolicy** cmdlet para a sessão local permitir a execução de um módulo não assinado.

**Set-ExecutionPolicy irrestrito CurrentUser**

Início de sessão seguinte, a sua subscrição do Azure que implementou a através de implementação de líder

**Login-AzureRmAccount**

Em seguida, importe o módulo:

**Import-Module <filepath to downloaded file>**

Por fim, execute a função com a entrada apropriada:

- **MyGatewayResourceId:** Caminho do recurso do Gateway. Este é o parâmetro de saída de implementação do modelo com o nome **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Caminho do recurso do gateway de junção do membro. Isto é fornecido pelo membro junção e é o parâmetro de saída de implementação do modelo de também denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName:** Um nome para identificar esta ligação de Gateway.
- **Chave partilhada:** O segredo previamente estabelecido entre os dois membros da rede de consórcio, que estão a estabelecer uma ligação.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < caminho de recurso do gateway do membro junção > - ConnectionName Aminhaligação - SharedKey "MySharedKeyAbc123"

**xPlat CLI: Estabelecer ligação**

Transfira o script da CLI do Azure e armazene-o localmente. A localização do script da CLI do Azure está especificada no parâmetro de implementação de modelo com o nome **par-GATEWAY-AZURE-CLI-SCRIPT**.

Execute o script com a entrada apropriada:

- **MyGatewayResourceId:** Caminho do recurso do Gateway. Este é o parâmetro de saída de implementação do modelo com o nome **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Caminho do recurso do gateway de junção do membro. Isto é fornecido pelo membro junção e é o parâmetro de implementação do modelo da sua implementação com o nome também **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName:** Um nome para identificar esta ligação de Gateway.
- **Chave partilhada:** O segredo previamente estabelecido entre os dois membros da rede de consórcio, que estão a estabelecer uma ligação.
- **Localização:** A região do Azure em que o seu recurso de gateway é implementado.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

A arquitetura será o seguinte após ter configurado com êxito a ligação entre **líder** e **membro** implementações.

![Arquitetura do líder e membro](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Atender a nova conta de membro

### <a name="generated-genesis-block"></a>Bloco de genesis gerado

Como o primeiro membro, a sua conta de Ethereum é atendida com um trilhão ether se a implementação gera o bloco de genesis (bloco de Genesis personalizado = não). Outros membros terão uma conta que não é atendida previamente e terá de aguardar a acumular Ether como começam a explorar blocos respetivos nós de extração. Para evitar que novos membros aguardar Ether, terá de atender explicitamente a contas de Ethereum junção membros.

Para fazer isso, os membros de associação tem de fornecer com a conta de Ethereum, que é apresentada no respetivo site de administração. Em seguida, pode utilizar o seu Web site de administração para transferir Ether da sua conta para a respetiva conta introduzindo a conta fornecida.

### <a name="custom-genesis-block"></a>Bloco de genesis personalizado

Se não for fornecido um bloco de genesis personalizado com uma conta de Ethereum especificada, pode usar MetaMask ou outra ferramenta para transferir ether do que especificado conta para a conta de Ethereum pré-geradas visível no Web site de administração. Para obter instruções sobre como utilizar MetaMask, consulte [criar conta de Ethereum](#create-ethereum-account).

Se um bloco de genesis personalizado é fornecido sem uma conta ou não tem acesso a todas as contas pré-alocada, terá de aguardar até que os nós de extração começam a explorar para gerar Ether na sua conta (moeda base). Quão rapidamente os fundos são gerados depende do nível de dificuldade que especificar no bloco personalizado genesis.

## <a name="create-ethereum-account"></a>Criar conta de Ethereum

Para criar uma conta adicional, pode usar uma variedade de soluções. Uma solução bastante é MetaMask, uma extensão do Chrome que fornece um cofre de identidade e ligação a uma rede Ethereum, pública, teste ou personalizado. MetaMask formulates uma transação para registar a conta na rede.

Esta transação, como nenhuma outra transação, passa a um de nós de transação e, eventualmente, ser minada num bloco, conforme ilustrado abaixo.

![Nó de transação](./media/ethereum-deployment/transaction-node.png)

Para instalar a extensão no Chrome, vá para personalizar e controlar o Google Chrome (botão de estouro) > mais ferramentas > extensões > obter mais extensões e procure MetaMask.

![Extensão de MetaMask](./media/ethereum-deployment/metamask-extension.png)

Uma vez instalado, abra MetaMask e crie um novo cofre. Por predefinição, o Cofre será ligado à rede de teste Morden. Altere esta opção para ligar à rede de consórcio privada implementada, especificamente para o Balanceador de carga à frente de nós de transação. A partir da saída do modelo, recuperar o expostos Ethereum RPC ponto final na porta 8545, com o nome como `ETHEREUM-RPC-ENDPOINT`e introduza-o no RPC personalizado, conforme mostrado abaixo.

![Definições de MetaMask](./media/ethereum-deployment/metamask-settings.png)

Ao criar o cofre, cria uma carteira, que contém uma conta. Para criar contas adicionais, selecione **contas de comutador** e, em seguida, o **+** botão.

![MetaMask criar conta](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Iniciar a alocação de Ether inicial

Na página de administrador, pode formular uma transação transferir Ether a conta pré-alocada para outra conta Ethereum. Esta transferência Ether é uma transação que é enviada para o nó de transação e é explorada num bloco, conforme ilustrado abaixo.

![Nó de transação](./media/ethereum-deployment/transaction-node-mined.png)

Através do ícone de área de transferência da carteira de MetaMask, copie o endereço da conta Ethereum ao qual pretende transferir ether e voltar para a página de administrador. Cole a conta copiada no campo de entrada para transferir 1000 ether da conta de Ethereum previamente alocada à sua conta recentemente criada. Clique em **submeter** e espere até que a transação a ser minado num bloco.

![Estado do nó](./media/ethereum-deployment/node-status2.png)

Assim que a transação é confirmada num bloco mined, o saldo da conta no MetaMask para a sua conta irá refletir a transferência de 1000 Ether.

![Transferência de MetaMask](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Transferência de Ether entre contas

Neste momento, está pronto para executar transações na sua rede de consórcio privada. A transação mais simples consiste em transferir Ether de uma conta para outra. Para formular tal uma transação, pode usar MetaMask mais uma vez, a transferência de dinheiro a primeira conta que utilizou acima para uma segunda conta.

Partir **carteira 1** no MetaMask, clique em enviar. Copiar o endereço do segundo carteira criado em **endereço de destinatário** entrada de campo e a quantidade de Ether para transferência no **quantidade** campo de entrada. Clique em **enviar** e aceite a transação.

![Transação de envio de MetaMask](./media/ethereum-deployment/metamask-send.png)

Mais uma vez, quando a transação é é explorada e consolidada para um bloco, os saldos de conta são refletidos em conformidade. Tenha em atenção que **carteira 1**do saldo é deduzido Ether mais de 15, uma vez que tivesse de pagar uma taxa de extração para processar a transação.

![Carteira 1](./media/ethereum-deployment/wallet1.png)

![Carteira 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Passos Seguintes

Agora está pronto para se concentrar na aplicação e desenvolvimento de contrato inteligentes em relação a sua rede de blockchain consortium privada.
