---
title: Entregar uma prova de conceito - Azure DevTest Labs | Microsoft Docs
description: Aprenda a entregar uma prova de conceito para que a Azure DevTest Labs possa ser incorporada com sucesso num ambiente empresarial.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288029"
---
# <a name="deliver-a-proof-of-concept"></a>Entregar uma prova de conceito 

Um dos cenários-chave para a Azure DevTest Labs é permitir o desenvolvimento e testar ambientes na nuvem. Os exemplos incluem:

* Criar desktops de desenvolvedores na nuvem.
* Configurar ambientes para testes.
* Permitindo o acesso a máquinas virtuais e outros recursos Azure.
* Criação de uma área de caixa de areia para os desenvolvedores aprenderem e experimentarem.

A DevTest Labs também fornece os guarda-costas de políticas e controlos de custos para capacitar a empresa a fornecer "self-serve Azure" a desenvolvedores que aderem às políticas de segurança corporativa, regulamentar e de conformidade. 

Todas as empresas têm diferentes requisitos para como a Azure DevTest Labs pode ser incorporada com sucesso no seu ambiente. Este artigo descreve os passos mais comuns que as empresas precisam de completar para garantir uma prova de conceito bem sucedida. Uma prova de conceito é o primeiro passo para uma implantação bem sucedida de ponta a ponta. 

## <a name="getting-started"></a>Introdução 

Para começar a entregar uma prova de conceito. É importante passar algum tempo para aprender sobre a Azure e a DevTest Labs.  Aqui estão alguns recursos ini para começar: 

* [Compreender o portal Azure](https://azure.microsoft.com/features/azure-portal/)
* [Fundamentos da DevTest Labs](devtest-lab-overview.md)
* [DevTest Labs apoiou cenários](devtest-lab-guidance-get-started.md)
* [Documentação da empresa DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Introdução à rede Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Pré-requisitos 

Para completar com sucesso um piloto ou prova de conceito com a DevTest Labs, existem alguns pré-requisitos: 

* **Subscrição Azure**: As empresas têm frequentemente um [Acordo Empresarial](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) existente que permite o acesso ao Azure, e podem usar uma subscrição existente ou nova para a DevTest Labs. Em alternativa, as empresas podem utilizar uma [subscrição do Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante o piloto (aproveitando os créditos gratuitos do Azure). Se nenhuma dessas opções estiver disponível, uma empresa pode criar e utilizar uma [conta Azure gratuita.](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) Se houver um Acordo Empresarial em vigor, a utilização de uma [subscrição Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) é uma ótima opção para ter acesso aos sistemas operativos de clientes windows 10/Windows 8.1 e tarifas com desconto para o desenvolvimento e teste de cargas de trabalho. 
* **Inquilino do Azure Ative Directory**: Para permitir a gestão de utilizadores (por exemplo, adicionar utilizadores ou adicionar proprietários de laboratório), esses utilizadores devem fazer parte do inquilino do [Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) utilizado na assinatura Azure para o piloto. Muitas vezes, as empresas vão criar [identidade híbrida](../active-directory/hybrid/whatis-hybrid-identity.md) para permitir que os utilizadores utilizem a sua identidade no local na nuvem, mas isso não é necessário para o piloto da DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Scoping do piloto 

É importante planear um piloto antes de começar a implementação. Sabendo antecipadamente que os recursos não ficarão por aqui indefinidamente, estabelece expectativas apropriadas para os utilizadores do piloto. 

> [!IMPORTANT]
> Não podemos enfatizar o suficiente a importância de digitalizar crisply o piloto e definir expectativas à frente.

Responda a estas perguntas-chave antes de iniciar o piloto: 

* O que quer aprender, e como é o sucesso para o piloto? 
* Que cargas de trabalho ou cenários serão cobertos pelo piloto? É importante definir apenas um pequeno conjunto para garantir que o piloto pode ser examinado e concluído rapidamente. 
* Que recursos devem estar disponíveis no laboratório? Por exemplo: imagens personalizadas, imagens de marketplace, políticas, topologia de rede. 
* Quem são os utilizadores e equipas que estarão envolvidos no piloto para verificar a experiência?  
* Qual é a duração do piloto? Escolha um prazo que se alinhe bem ao âmbito planeado, como duas semanas ou um mês. 
* Depois de o piloto estar completo, o que acontecerá com os recursos atribuídos que foram utilizados durante o piloto? Planeia descartar os recursos piloto? Pode pensar:
   
   "Se pudermos planear deitar fora as máquinas virtuais e os laboratórios no final do piloto, então podemos configurar uma única subscrição para o piloto e fazer todo o nosso trabalho lá enquanto resolvemos as questões de lançamento da escala em paralelo." 

Há uma tendência geral para tornar o piloto "perfeito" por isso representa de forma idêntica o que será o estado final depois de o serviço ser lançado na empresa. Isto é uma falsa suposição. Quanto mais perto de "perfeito", mais temos de completar *antes* de começar o piloto. O objetivo do piloto é tomar as decisões certas sobre o escalonamento e a elaboração do serviço final. 

O foco do piloto deve ser escolher as cargas e dependências mínimas necessárias para responder à questão de saber se a Azure DevTest Labs é o serviço certo para a sua empresa. Recomendamos que escolha a carga de trabalho mais simples com menos dependências para ajudar a garantir um sucesso rápido e limpo. Se isso não for possível, escolha uma carga de trabalho mais representativa que exponha potenciais complexidades para que o sucesso na fase piloto possa ser replicado na fase de escala. 

O exemplo a seguir demonstra uma prova de conceito bem traçada. 

## <a name="example-proof-of-concept-plan"></a>Exemplo: plano de prova de conceito 

Esta secção mostra uma amostra para usar para digitalizar uma prova de conceito do piloto para a DevTest Labs. 

> [!TIP]
> Para minimizar a possibilidade de configurar o seu projeto para falhas, recomendamos vivamente que não ignore o exemplo descrito nesta secção. 

### <a name="overview"></a>Descrição Geral 

Estamos a planear desenvolver um novo ambiente em Azure baseado na DevTest Labs para os fornecedores usarem como um ambiente isolado da rede corporativa. Para determinar se a solução vai satisfazer os requisitos, desenvolveremos uma prova de conceito para validar a solução de ponta a ponta. Incluímos vários fornecedores para experimentar e verificar a experiência. 

### <a name="outcomes"></a>Resultados 

Ao construir uma prova de conceito, focamo-nos primeiro nos resultados (o que estamos a tentar alcançar). No final da prova de conceito, esperamos: 

* Uma solução de trabalho de ponta a ponta para os fornecedores utilizarem contas de hóspedes no Azure Ative Directory (Azure AD) para aceder a um ambiente isolado em Azure. O ambiente tem os recursos necessários para que sejam produtivos. 
* Quaisquer potenciais problemas de bloqueio que afetem o uso e a adoção de escala mais ampla são enumerados e compreendidos.
* Os indivíduos envolvidos no desenvolvimento da prova de conceito têm uma boa compreensão de todos os códigos. Compreendem igualmente as garantias envolvidas e estão confiantes numa adoção mais ampla.

### <a name="open-questions-and-prerequisites"></a>Perguntas abertas e pré-requisitos 

* Temos uma subscrição criada que podemos usar para este projeto? 
* Temos um inquilino da AD Azure e um administrador global da AD Azure identificado que pode fornecer ajuda e orientação para questões relacionadas com a AD do Azure? 
* Temos um lugar para colaborar para os indivíduos que trabalham no projeto? 

   * Código fonte e scripts (como Azure Repos) 
   * Documentos (como Microsoft Teams ou SharePoint)  
   * Conversas (como As Equipas da Microsoft) 
   * Artigos de trabalho (como Azure Boards) 
* Quais são os recursos necessários para os fornecedores? Isto inclui aplicações disponíveis na rede, tanto localmente nas máquinas virtuais como em outros servidores necessários. 
* As máquinas virtuais serão unidas a um domínio em Azure? Em caso afirmativo, serão os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS) ou outra coisa? 
* Identificámos a equipa ou os fornecedores que serão alvo da prova de conceito? Quem serão os clientes do ambiente?
* Que região de Azure usaremos para a prova de conceito? 
* Temos uma lista de serviços que os fornecedores podem utilizar através da DevTest Labs para além de IaaS (VMs)? 
* Como planeamos treinar fornecedores/utilizadores na utilização do laboratório? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componentes da solução de prova de conceito 

Esperamos que a solução tenha os seguintes componentes: 

* Várias equipas de fornecedores usarão um conjunto de laboratórios em Azure.
* Os laboratórios estão ligados a uma infraestrutura de rede que suporta os requisitos.
* Os vendedores têm acesso aos laboratórios através da Azure AD e atribuições de funções.
* Os fornecedores têm uma maneira de se conectarem com sucesso aos seus recursos. Especificamente, uma VPN site-to-site permite aceder diretamente a máquinas virtuais sem endereços IP públicos.
* Um conjunto de artefactos cobre o software necessário que os fornecedores precisam nas máquinas virtuais.

## <a name="additional-planning-and-design-decisions"></a>Decisões adicionais de planeamento e design 

Antes de lançar uma solução completa da DevTest Labs, tem de tomar decisões importantes de planeamento e design. A experiência de trabalhar numa prova de conceito pode ajudá-lo a tomar estas decisões. Outra consideração inclui: 

* **Topologia de subscrição**: Os requisitos de nível empresarial para os recursos em Azure podem estender-se para além das [quotas disponíveis numa única subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md) Isto requer várias subscrições do Azure e/ou pedidos de serviço para aumentar os limites iniciais de subscrição. É importante decidir adiantadamente como distribuir recursos por subscrições. Um recurso valioso é o guia de decisão de [subscrição](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) porque é difícil mover recursos para outra subscrição mais tarde. Por exemplo, um laboratório não pode ser transferido para outra subscrição depois de ser criado.  
* **Topologia da rede**: A [infraestrutura de rede padrão](../app-service/networking-features.md) que a DevTest Labs cria automaticamente pode não ser suficiente para satisfazer os requisitos e constrangimentos para os utilizadores da empresa. É comum ver [redes virtuais conectadas a Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/), [hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para conectividade entre subscrições, e até mesmo [o encaminhamento forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) para garantir apenas conectividade no local. A DevTest Labs permite que as redes virtuais existentes sejam ligadas ao laboratório para permitir a sua utilização quando se está a criar novas máquinas virtuais em laboratório. 
* **Acesso remoto a máquinas virtuais**: Existem muitas opções para aceder remotamente às máquinas virtuais localizadas em DevTest Labs. O mais fácil é usar iPs públicos ou iPs públicos partilhados. Estas são [as configurações disponíveis no laboratório.](devtest-lab-shared-ip.md) Se estas opções não forem suficientes, usar um gateway de acesso remoto também é uma opção. Esta opção é mostrada na [arquitetura de referência empresarial da DevTest Labs](devtest-lab-reference-architecture.md) e descrita mais adiante na [documentação de gateway de desktop remota da DevTest Labs](configure-lab-remote-desktop-gateway.md). As empresas também podem usar o ExpressRoute ou uma VPN local para ligar os seus laboratórios à sua rede no local. Esta opção permite ligações diretas de desktop remoto ou SSH às máquinas virtuais com base no seu endereço IP privado sem exposição à internet. 
* **Permissões de manuseamento**: As duas permissões-chave geralmente utilizadas nos Laboratórios DevTest são [Proprietário e Utilizador de Laboratório.](devtest-lab-add-devtest-user.md) É importante decidir antes de lançar de laboratórios de devTest em geral quem será confiado a cada nível de acesso no laboratório. Um modelo comum é o proprietário do orçamento (líder da equipa, por exemplo) como o proprietário do laboratório e os membros da equipa como utilizadores de laboratório. Este modelo permite que a pessoa (team lead) responsável pelo orçamento ajuste as definições de política e mantenha a equipa dentro do orçamento.  

## <a name="completing-the-proof-of-concept"></a>Completar a prova de conceito 

Depois das aprendizagens esperadas terem sido cobertas, é hora de completar o piloto. Este é o momento de recolher feedback dos utilizadores, determinar se o piloto foi bem sucedido, e decidir se a organização vai avançar com um lançamento em escala da DevTest Labs na empresa. É também um ótimo momento para considerar automatizar a implementação de DevTest Labs e recursos associados para garantir consistência em toda a escala. 

## <a name="next-steps"></a>Passos seguintes 

* [Documentação da empresa DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Arquitetura de referência para uma empresa](devtest-lab-reference-architecture.md)
* [Aumentar a sua implantação de DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
