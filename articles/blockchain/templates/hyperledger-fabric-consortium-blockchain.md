---
title: Rede de consórcio Hyperledger recursos de infraestrutura no Azure
description: Modelo de solução para implementar e configurar uma rede de consórcio Hyperledger recursos de infraestrutura
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005809"
---
# <a name="hyperledger-fabric-consortium-network"></a>Rede de consórcio Hyperledger recursos de infraestrutura

Pode utilizar o modelo de solução de consórcio Hyperledger recursos de infraestrutura para implementar e configurar uma rede de consórcio Hyperledger recursos de infraestrutura no Azure.

Depois de ler este artigo, irá:

- Obter o conhecimento de trabalho de blockchain, Hyperledger recursos de infraestrutura e mais complicado consortium arquiteturas de rede
- Saiba como implementar e configurar uma rede de consórcio Hyperledger recursos de infraestrutura no portal do Azure

## <a name="about-blockchain"></a>Sobre blockchain

Se estiver familiarizado com a Comunidade de blockchain, este modelo de solução é uma ótima oportunidade para saber mais sobre a tecnologia de maneira fácil e configurável no Azure. Blockchain é a tecnologia subjacente Bitcoins; No entanto, é muito mais do que apenas um ativador para uma moeda virtual. É uma composição de base de dados existente, o sistema distribuído e tecnologias de criptografia, que permite que o cálculo de múltiplos intervenientes seguro com garantias para imutabilidade, capacidade de verificação, auditability e resiliência a ataques. Protocolos diferentes empregam mecanismos diferentes para fornecer esses atributos. [Recursos de infraestrutura Hyperledger](https://github.com/hyperledger/fabric) é um protocolo desse tipo.

## <a name="consortium-architecture-on-azure"></a>Arquitetura de Consortium no Azure

Para ativar Hyperledger recursos de infraestrutura no Azure, existem dois tipos de implementação principais que são suportados. Estas implementações são projetadas para acomodar topologias diferentes, com base no destino pretendido.

- **Máquina virtual individual, o servidor de desenvolvedor** -este tipo de implementação foi concebido como um ambiente de desenvolvimento usado para criar e testar soluções criadas com base nos recursos de infraestrutura Hyperledger.
- **Várias máquinas virtuais, aumentar horizontalmente a implementação** -este tipo de implementação é projetado para ambientes que modelam um consórcio de participantes diferentes tirar partido de um ambiente compartilhado.

Em qualquer implantação, os blocos de construção que estão a tornar os núcleos de recursos de infraestrutura Hyperledger são os mesmos.  As diferenças nas Implantações são como esses componentes são aumentados horizontalmente.

- **Nós de AC**: Um nó com a autoridade de certificação que é utilizado para gerar os certificados que são utilizados para as identidades na rede.
- **Nós orderer**: Um nó a executar o serviço de comunicação implementando uma garantia de entrega, como o total do pedido de difusão ou transações atômicas.
- **Configurar o peering entre nós**: Um nó em que consolida as transações e mantém o estado e uma cópia do livro razão distribuído.
- **Nós CouchDB**: Um nó que pode executar o serviço de CouchDB, que pode conter a base de dados de estado e fornecer avançadas de consulta de dados de chaincode, expansão de chave/valor simples para o armazenamento de tipo JSON.

### <a name="single-virtual-machine-architecture"></a>Arquitetura de máquina virtual única

Como mencionado anteriormente, a única virtual arquitetura do computador foi concebida para os desenvolvedores tenham um servidor de requisitos de espaço reduzido que é utilizado para desenvolver aplicativos. Todos os contentores mostrados em execução numa única máquina virtual. O serviço de ordenação está a utilizar [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) para esta configuração. Esta configuração é *não* uma ordenação do tolerância a falhas de falhas de serviço, mas foi concebido para ser leve para fins de desenvolvimento.

![Arquitetura de Máquina Virtual única](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Arquitetura de várias máquinas virtuais

A máquina virtual múltiplas, arquitetura de escalamento horizontal, é criada com elevada disponibilidade e dimensionamento de cada componente no núcleo. Esta arquitetura é muito mais adequada para implementações de nível de produção.

![Arquitetura de várias máquinas virtuais](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Introdução

Para começar, precisa de uma subscrição do Azure que suporta a implementação de várias máquinas virtuais e contas de armazenamento standard. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/).

Assim que tiver uma subscrição, vá para o [portal do Azure](https://portal.azure.com). Selecione **criar um recurso > Blockchain > Hyperledger Fabric Consortium**.

![Modelo do mercado de Blockchain de membro único do Hyperledger recursos de infraestrutura](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Implementação

Na **Hyperledger Fabric Consortium** modelo, selecione **criar**.

A implementação do modelo irá guiá-lo por meio de configuração do nó Multi [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) rede. O fluxo de implementação é dividido em quatro etapas: Noções básicas, as definições de rede de consórcio, configuração de recursos de infraestrutura e componentes opcionais.

### <a name="basics"></a>Noções básicas

Na **Noções básicas**, especifique os valores de parâmetros padrão para qualquer implementação. Como, por exemplo de subscrição, grupo de recursos e basic virtual machine propriedades.

![Noções básicas](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Prefixo de recursos** | Prefixo do nome para os recursos aprovisionados como parte da implementação |6 carateres ou menos |
**Nome de Utilizador** | O nome de utilizador do administrador para cada uma das máquinas virtuais implementadas para este membro |1 - 64 carateres |
**Tipo de autenticação** | O método para autenticar para a máquina virtual |Chave pública de palavra-passe ou SSH|
**Palavra-passe (tipo de autenticação = a palavra-passe)** |A palavra-passe da conta de administrador para cada uma das máquinas virtuais implementadas. A palavra-passe tem de conter três dos seguintes tipos de carateres: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial<br /><br />Embora todas as VMs tenham inicialmente a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento|12 - 72 carateres|
**Chave SSH (tipo de autenticação = a chave pública SSH)** |A chave secure shell utilizada para início de sessão remoto ||
**Subscrição** |A subscrição para a implementação ||
**Grupo de recursos** |O grupo de recursos ao qual pretende implementar a rede de consórcio ||
**Localização** |A região do Azure ao qual pretende implementar o primeiro membro no ||

Selecione **OK**.

### <a name="consortium-network-settings"></a>Definições de rede de consórcio

Na **as definições de rede**, especificar as entradas para criar ou associar um consórcio existente de rede e configurar as definições da organização.

![Definições de rede de consórcio](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Configuração da rede** |Pode optar por criar uma nova rede ou ingresse num existente. Se escolher *associar existente*, tem de fornecer valores adicionais. |Nova rede <br/> Associar existente |
**Palavra-passe de AC HLF** |Uma palavra-passe utilizada para os certificados gerados pelas autoridades de certificação que são criadas como parte da implementação. A palavra-passe tem de conter três dos seguintes tipos de carateres: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial.<br /><br />Embora todas as máquinas virtuais tenha, inicialmente, a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento.|1 - 25 caracteres |
**Configuração da organização** |Pode personalizar o nome da sua organização e do certificado ou têm valores padrão a ser utilizado.|Predefinição <br/> Avançado |
**Definições de rede VPN** | Aprovisionar um gateway de túnel VPN para acessar as VMs | Sim <br/> Não |

Selecione **OK**.

### <a name="fabric-specific-settings"></a>Definições específicas de recursos de infraestrutura

Na **configuração de recursos de infraestrutura**, configure o tamanho de rede e desempenho e especificar as entradas para a disponibilidade da rede. Número de como, por exemplo orderer e nós de ponto a ponto, o mecanismo de persistência usado por cada nó e o tamanho da VM.

![Definições de recursos de infraestrutura](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Tipo de dimensionamento** |O tipo de implementação de uma única máquina virtual com vários contentores ou várias máquinas virtuais num modelo de escalamento horizontal.|VM única ou várias VM |
**Tipo de disco da VM** |O tipo de armazenamento de segurança cada um de nós implementados. <br/> Para saber mais sobre os tipos de disco disponível, visite [selecionar um tipo de disco](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Implementação de várias VMS (configurações adicionais)

![Definições de recursos de infraestrutura para várias implementações de vm](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Número de nós de orderer** |O número de nós que solicite (organizar) transações num bloco. <br />Para obter detalhes adicionais sobre o serviço de ordenação, visite o Hyperledger [documentação](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 a 4 |
**Tamanho de máquina virtual do nó orderer** |O tamanho de máquina virtual utilizado para nós orderer na rede|Standard Bs,<br />Standard Ds,<br />Standard FS |
**Número de nós de ponto a ponto** | Nós que pertencem os membros de consórcio que executar transações e mantêm o estado e uma cópia do razão.<br />Para obter detalhes adicionais sobre o serviço de ordenação, visite o Hyperledger [documentação](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 a 4 |
**Persistência de estado do nó** |O mecanismo de persistência usado por nós ponto a ponto. Pode configurar esse mecanismo por nó de mesmo nível. Ver detalhes abaixo para vários nós de ponto a ponto.|CouchDB <br />LevelDB |
**Tamanho de máquina virtual do nó de mesmo nível** |O tamanho de máquina virtual utilizado para todos os nós na rede|Standard Bs,<br />Standard Ds,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Configuração do nó de mesmo nível vários

Este modelo permite-lhe escolher seu mecanismo de persistência por nó de mesmo nível. Por exemplo, se tiver três nós de ponto a ponto pode utilizar o CouchDB em um e LevelDB sobre os outros dois.

![Configuração do nó de mesmo nível vários](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Selecione **OK**.

### <a name="deploy"></a>Implementação

Na **resumo**, reveja as entradas especificadas e para executar a validação de pré-implementação básica.

![Resumo](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Reveja os termos legais e privacidade e selecione **Compra** para implementar. Dependendo do número de VMs a ser aprovisionado, o tempo de implantação pode variar de alguns minutos para dezenas de minutos.

## <a name="next-steps"></a>Passos Seguintes

Agora está pronto para se concentrar na aplicação e desenvolvimento de chaincode em relação a sua rede de blockchain Hyperledger consortium.
