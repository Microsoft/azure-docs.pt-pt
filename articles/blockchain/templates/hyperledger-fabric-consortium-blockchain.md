---
title: Implantar o modelo de solução do consórcio de malha de multilimiar no Azure
description: Como implantar e configurar o modelo de solução de rede do consórcio Fabric Consortium no Azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: be35cfa26204b36ad65da91252144b9167cb9e54
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325137"
---
# <a name="hyperledger-fabric-consortium-network"></a>Rede do consórcio de malha de multirazão

Você pode usar o modelo de solução do consórcio do Fabric do superrazãor para implantar e configurar uma rede do consórcio do Fabric do multirazão no Azure.

Depois de ler este artigo, irá:

- Obtenha conhecimento prático do blockchain, da malha do limiar e de arquiteturas de rede consórcio mais complicadas
- Saiba como implantar e configurar uma rede do consórcio de malha de multirazão de dentro do portal do Azure

## <a name="about-blockchain"></a>Sobre o blockchain

Se você for novo na Comunidade do blockchain, esse modelo de solução é uma ótima oportunidade para aprender sobre a tecnologia de maneira fácil e configurável no Azure. Blockchain é a tecnologia subjacente por trás de Bitcoin; no entanto, é muito mais do que apenas um habilitador para uma moeda virtual. É uma composição de banco de dados existente, sistema distribuído e tecnologias criptográficas que permitem a computação segura de várias partes com garantias em relação à imutabilidade, à capacidade de verificação, à auditoria e à resiliência a ataques. Protocolos diferentes empregam mecanismos diferentes para fornecer esses atributos. A [malha de hiperrazãor](https://github.com/hyperledger/fabric) é um desses protocolos.

## <a name="consortium-architecture-on-azure"></a>Arquitetura do consórcio no Azure

Para habilitar a malha de hiperrazãos no Azure, há dois tipos de implantação principais com suporte. Essas implantações foram projetadas para acomodar topologias diferentes, com base no destino desejado.

- **Máquina virtual única, servidor de desenvolvedor** -esse tipo de implantação é projetado como um ambiente de desenvolvimento usado para criar e testar soluções criadas na malha do hiperrazãor.
- **Várias máquinas virtuais, implantação em expansão** – esse tipo de implantação é projetado para ambientes que modelam um consórcio de diferentes participantes utilizando um ambiente compartilhado.

Em qualquer implantação, os blocos de construção que fazem o núcleo da malha de hiperrazãor são os mesmos.  As diferenças nas implantações são como esses componentes são escalados horizontalmente.

- **Nós de CA**: um nó que executa uma autoridade de certificação que é usada para gerar certificados que são usados para identidades na rede.
- **Nós do solicitante**: um nó que executa o serviço de comunicação implementando uma garantia de entrega, como a transmissão de ordem total ou transações atômicas.
- **Nós pares**: um nó que confirma as transações e mantém o estado e uma cópia do razão distribuído.
- **Nós CouchDB**: um nó que pode executar o serviço CouchDB que pode manter o banco de dados de estado e fornecer consultas avançadas de data chaincode, expandindo de chave/valor simples para o armazenamento de tipo JSON.

### <a name="single-virtual-machine-architecture"></a>Arquitetura de máquina virtual única

Como mencionado anteriormente, a arquitetura de máquina virtual única foi criada para que os desenvolvedores tenham um servidor com pouco espaço usado para desenvolver aplicativos. Todos os contêineres mostrados estão em execução em uma única máquina virtual. O serviço de pedidos está usando o [solo](https://github.com/hyperledger/fabric/tree/master/orderer) para esta configuração. Essa configuração *não* é um serviço de ordenação tolerante a falhas, mas foi projetada para ser leve para fins de desenvolvimento.

![Arquitetura de máquina virtual única](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Arquitetura de várias máquinas virtuais

A arquitetura de várias máquinas virtuais, expansão, é criada com alta disponibilidade e dimensionamento de cada componente no núcleo. Essa arquitetura é muito mais adequada para implantações de nível de produção.

![Arquitetura de várias máquinas virtuais](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Introdução

Para começar, você precisa de uma assinatura do Azure que possa dar suporte à implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/).

Quando você tiver uma assinatura, vá para a [portal do Azure](https://portal.azure.com). Selecione **criar um recurso > Blockchain > o objectlimiar Fabric Consortium**.

![Modelo do Marketplace de membro único da malha de Blockchain do](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Implementação

No modelo do **consórcio de malha de multirazãor** , selecione **criar**.

A implantação do modelo o orientará na configuração da rede do [hiperrazão 1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) de vários nós. O fluxo de implantação é dividido em quatro etapas: Noções básicas, configurações de rede do consórcio, configuração de malha e componentes opcionais.

### <a name="basics"></a>Noções básicas

Em **noções básicas**, especifique valores para parâmetros padrão para qualquer implantação. Como, assinatura, grupo de recursos e propriedades de máquina virtual básica.

![Noções básicas](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Prefixo de recurso** | Prefixo de nome para recursos provisionados como parte da implantação |6 caracteres ou menos |
**Nome de Utilizador** | O nome de usuário do administrador de cada uma das máquinas virtuais implantadas para este membro |1-64 caracteres |
**Tipo de autenticação** | O método para autenticar na máquina virtual |Senha ou chave pública SSH|
**Senha (tipo de autenticação = senha)** |A senha da conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve conter três dos seguintes tipos de caracteres: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial<br /><br />Embora todas as VMs tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento|12-72 caracteres|
**Chave SSH (tipo de autenticação = chave pública SSH)** |A chave de shell seguro usada para logon remoto ||
**Subscrição** |A assinatura na qual implantar ||
**Grupo de recursos** |O grupo de recursos no qual implantar a rede Consortium ||
**Localização** |A região do Azure na qual o primeiro membro será implantado ||

Selecione **OK**.

### <a name="consortium-network-settings"></a>Configurações de rede do consórcio

Em **configurações de rede**, especifique as entradas para criar ou ingressar em uma rede consórcio existente e definir as configurações da organização.

![Configurações de rede do consórcio](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Configuração da rede** |Você pode optar por criar uma nova rede ou ingressar em uma existente. Se você escolher *ingressar existente*, precisará fornecer valores adicionais. |Nova rede <br/> Ingressar existente |
**Senha da autoridade de certificação HLF** |Uma senha usada para os certificados gerados pelas autoridades de certificação que são criadas como parte da implantação. A senha deve conter três dos seguintes tipos de caracteres: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial.<br /><br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|1-25 caracteres |
**Configuração da organização** |Você pode personalizar o nome e o certificado da sua organização ou ter valores padrão a serem usados.|Predefinição <br/> Avançado |
**Configurações de rede VPN** | Provisionar um gateway de túnel VPN para acessar as VMs | Sim <br/> Não |

Selecione **OK**.

### <a name="fabric-specific-settings"></a>Configurações específicas de malha

Na **configuração do Fabric**, você configura o tamanho e o desempenho da rede e especifica as entradas para a disponibilidade da rede. Por exemplo, o orderr numérico e os nós de par, o mecanismo de persistência usado por cada nó e o tamanho da VM.

![Configurações de malha](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Tipo de escala** |O tipo de implantação de uma única máquina virtual com vários contêineres ou várias máquinas virtuais em um modelo de expansão.|VM única ou várias VMs |
**Tipo de disco da VM** |O tipo de armazenamento que faz o backup de cada um dos nós implantados. <br/> Para saber mais sobre os tipos de disco disponíveis, visite [selecionar um tipo de disco](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Implantação de várias VMS (configurações adicionais)

![Configurações de malha para várias implantações de VM](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nome do Parâmetro | Descrição | Valores permitidos |
|---|---|---|
**Número de nós do solicitante** |O número de nós que ordenam (organizam) as transações em um bloco. <br />Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) do hiperrazãor |1 a 4 |
**Tamanho da máquina virtual do nó do solicitante** |O tamanho da máquina virtual usada para os nós do solicitante na rede|BS padrão,<br />DS padrão,<br />FS padrão |
**Número de nós pares** | Nós que pertencem a membros do consórcio que executam transações e mantêm o estado e uma cópia do razão.<br />Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)do hiperrazãor.|1 a 4 |
**Persistência de estado do nó** |O mecanismo de persistência usado pelos nós pares. Você pode configurar esse mecanismo por nó par. Veja os detalhes abaixo para vários nós de mesmo nível.|CouchDB <br />LevelDB |
**Tamanho da máquina virtual do nó par** |O tamanho da máquina virtual usada para todos os nós na rede|BS padrão,<br />DS padrão,<br />FS padrão |

### <a name="multiple-peer-node-configuration"></a>Configuração de nó de vários pares

Este modelo permite que você escolha seu mecanismo de persistência por nó par. Por exemplo, se você tiver três nós pares, poderá usar CouchDB em um e LevelDB nos outros dois.

![Configuração de nó de vários pares](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Selecione **OK**.

### <a name="deploy"></a>Implementação

Em **Resumo**, examine as entradas especificadas e execute a validação de pré-implantação básica.

![Resumo](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Examine os termos legais e de privacidade e selecione **comprar** para implantar. Dependendo do número de VMs que estão sendo provisionadas, o tempo de implantação pode variar de alguns minutos a dezenas de minutos.

## <a name="next-steps"></a>Passos seguintes

Agora você está pronto para se concentrar no desenvolvimento de aplicativos e chaincode em relação à sua rede blockchain do Object-limiar Consortium.
