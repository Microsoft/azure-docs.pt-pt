---
title: Entregar uma prova de conceito - Azure DevTest Labs / Microsoft Docs
description: Aprenda a fornecer uma prova de conceito para que o Azure DevTest Labs possa ser incorporado com sucesso num ambiente empresarial.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643292"
---
# <a name="deliver-a-proof-of-concept"></a>Entregar uma prova de conceito 

Um dos cenários-chave para o Azure DevTest Labs é permitir o desenvolvimento e teste de ambientes na nuvem. Os exemplos incluem:

* Criar desktops de desenvolvedorna nuvem.
* Configurar ambientes para testes.
* Permitindo o acesso a máquinas virtuais e outros recursos Azure.
* Criar uma área de caixa de areia para os desenvolvedores aprenderem e experimentarem.

A DevTest Labs também fornece os guarda-costas de política e controlos de custos para capacitar a empresa a fornecer "self-serve Azure" a desenvolvedores que aderem às políticas de segurança corporativa, regulação e conformidade. 

Cada empresa tem requisitos diferentes para como a Azure DevTest Labs pode ser incorporada com sucesso no seu ambiente. Este artigo descreve os passos mais comuns que as empresas precisam de completar para garantir uma prova de conceito bem sucedida. Uma prova de conceito é o primeiro passo para uma implementação bem sucedida de ponta a ponta. 

## <a name="getting-started"></a>Introdução 

Para começar a entregar uma prova de conceito. É importante passar algum tempo para aprender sobre o Azure e o DevTest Labs.  Aqui estão alguns recursos instantes: 

* [Compreender o portal Azure](https://azure.microsoft.com/features/azure-portal/)
* [Básicos de Laboratórios DevTest](devtest-lab-overview.md)
* [DevTest Labs apoiou cenários](devtest-lab-guidance-get-started.md)
* [Documentação empresarial da DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Introdução à rede Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Pré-requisitos 

Para completar com sucesso um piloto ou prova de conceito com a DevTest Labs, existem alguns pré-requisitos: 

* **Assinatura Azure**: As empresas têm frequentemente um [Acordo Empresarial](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) existente que permite o acesso ao Azure, e podem usar uma subscrição existente ou nova para devTest Labs. Alternativamente, as empresas podem usar uma [subscrição do Estúdio Visual](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante o piloto (aproveitando os créditos gratuitos do Azure). Se nenhuma dessas opções estiver disponível, uma empresa pode criar e utilizar uma [conta Azure gratuita.](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) Se houver um Acordo de Empresa em vigor, usar uma [subscrição Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) é uma ótima opção para ter acesso aos sistemas operativos do Windows 10/Windows 8.1 e tarifas descontadas para trabalhos de trabalho de desenvolvimento e teste. 
* Inquilino do **Diretório Ativo Azure**: Para permitir a gestão dos utilizadores (por exemplo, adicionar utilizadores ou adicionar proprietários de laboratórios), esses utilizadores devem fazer parte do inquilino do [Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) utilizado na subscrição azure para o piloto. Muitas vezes as empresas criarão [identidade híbrida](../active-directory/hybrid/whatis-hybrid-identity.md) para permitir que os utilizadores utilizem a sua identidade no local na nuvem, mas isso não é necessário para o piloto da DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Deteção do piloto 

É importante planear um piloto antes de começar a implementação. Saber antecipadamente que os recursos não ficarão por perto indefinidamente define as expectativas adequadas para os utilizadores do piloto. 

> [!IMPORTANT]
> Não podemos enfatizar a importância de espôr o piloto crispadamente e de criar expectativas à frente.

Responda a estas questões-chave antes de iniciar o piloto: 

* O que quer aprender, e como é o sucesso para o piloto? 
* Que cargas de trabalho ou cenários serão cobertos no piloto? É importante definir apenas um pequeno conjunto para garantir que o piloto pode ser examinado e concluído rapidamente. 
* Que recursos devem estar disponíveis no laboratório? Por exemplo: imagens personalizadas, imagens de mercado, políticas, topologia da rede. 
* Quem são os utilizadores e equipas que estarão envolvidos no piloto para verificar a experiência?  
* Qual é a duração do piloto? Escolha um prazo que alinhe bem o âmbito planeado, como duas semanas ou um mês. 
* Depois de o piloto estar completo, o que acontecerá com os recursos atribuídos que foram utilizados durante o piloto? Planeia descartar os recursos piloto? Pode pensar:
   
   "Se pudermos planear deitar fora as máquinas e laboratórios virtuais no final do piloto, então podemos configurar uma única subscrição para o piloto e fazer todo o nosso trabalho lá enquanto resolvemos as questões de lançamento em paralelo." 

Há uma tendência geral para tornar o piloto "perfeito" por isso representa de forma idêntica o que será o estado final após o serviço ser lançado na empresa. Esta é uma falsa suposição. Quanto mais perto de "perfeito", mais tem que completar *antes* de começar o piloto. O objetivo do piloto é tomar as decisões certas sobre a escala e a elaboração do serviço final. 

O foco do piloto deve ser escolher as cargas e dependências mínimas necessárias para responder à questão de saber se a Azure DevTest Labs é o serviço certo para a sua empresa. Recomendamos que escolha a carga de trabalho mais simples com menos dependências para ajudar a garantir um sucesso rápido e limpo. Se isso não for possível, escolha uma carga de trabalho mais representativa que exponha potenciais complexidades para que o sucesso na fase piloto possa ser replicado na fase de eliminação de escala. 

O exemplo que se segue demonstra uma prova de conceito bem orientada. 

## <a name="example-proof-of-concept-plan"></a>Exemplo: plano de prova de conceito 

Esta secção mostra uma amostra a utilizar para pesquisar uma prova de conceito do piloto para a DevTest Labs. 

> [!TIP]
> Para minimizar a possibilidade de configurar o seu projeto para falhas, recomendamos vivamente que não ignore o exemplo descrito nesta secção. 

### <a name="overview"></a>Descrição geral 

Estamos a planear desenvolver um novo ambiente em Azure baseado em DevTest Labs para os fornecedores usarem como um ambiente isolado da rede corporativa. Para determinar se a solução irá satisfazer os requisitos, desenvolveremos uma prova de conceito para validar a solução de ponta a ponta. Incluímos vários vendedores para experimentar e verificar a experiência. 

### <a name="outcomes"></a>Resultados 

Ao construir uma prova de conceito, focamo-nos primeiro nos resultados (o que estamos a tentar alcançar). No final da prova de conceito, esperamos: 

* Uma solução de ponta a ponta para os fornecedores utilizarem contas de hóspedes no Azure Ative Directory (Azure AD) para aceder a um ambiente isolado em Azure. O ambiente tem os recursos necessários para que sejam produtivos. 
* Quaisquer potenciais problemas de bloqueio que afetem a utilização e a adoção em escala mais ampla são enumerados e compreendidos.
* Os indivíduos envolvidos no desenvolvimento da prova de conceito têm uma boa compreensão de todo o código. Compreendem igualmente as garantias envolvidas e estão confiantes numa adoção mais ampla.

### <a name="open-questions-and-prerequisites"></a>Perguntas e pré-requisitos abertos 

* Temos uma subscrição criada que podemos usar para este projeto? 
* Temos um inquilino da AD Azure e um administrador global da Azure AD identificados que podem fornecer ajuda e orientação para questões relacionadas com a AD Azure? 
* Temos um lugar para colaborar para os indivíduos que trabalham no projeto? 

   * Código de origem e scripts (como Azure Repos) 
   * Documentos (como Microsoft Teams ou SharePoint)  
   * Conversas (como equipas da Microsoft) 
   * Artigos de trabalho (como placas azure) 
* Quais são os recursos necessários para os vendedores? Isto inclui aplicações disponíveis na rede, tanto localmente nas máquinas virtuais como em outros servidores necessários. 
* As máquinas virtuais serão unidas a um domínio em Azure? Em caso afirmativo, este será o Azure Ative Directory Domain Services (Azure AD DS) ou outra coisa qualquer? 
* Identificamos a equipa ou fornecedores que serão alvo da prova de conceito? Quem serão os clientes para o ambiente?
* Que região de Azure usaremos para a prova de conceito? 
* Temos uma lista de serviços que os fornecedores estão autorizados a usar através de DevTest Labs além de IaaS (VMs)? 
* Como planeamos treinar fornecedores/utilizadores na utilização do laboratório? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componentes da solução de prova de conceito 

Esperamos que a solução tenha os seguintes componentes: 

* Várias equipas de fornecedores vão usar um conjunto de laboratórios em Azure.
* Os laboratórios estão ligados a uma infraestrutura de rede que suporta os requisitos.
* Os vendedores têm acesso aos laboratórios via Azure AD e atribuições de papéis.
* Os fornecedores têm uma maneira de se conectarcom sucesso aos seus recursos. Especificamente, uma VPN site-to-site permite aceder diretamente a máquinas virtuais sem endereços IP públicos.
* Um conjunto de artefactos cobre o software necessário que os fornecedores precisam nas máquinas virtuais.

## <a name="additional-planning-and-design-decisions"></a>Decisões adicionais de planeamento e conceção 

Antes de lançar uma solução completa do DevTest Labs, tem de tomar algumas decisões importantes de planeamento e design. A experiência de trabalhar numa prova de conceito pode ajudá-lo a tomar estas decisões. A análise adicional inclui: 

* **Topologia de assinatura**: Os requisitos de nível empresarial para os recursos no Azure podem estender-se para além das quotas disponíveis dentro de [uma única subscrição](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Isto requer múltiplas subscrições azure e/ou pedidos de serviço para aumentar os limites iniciais de subscrição. É importante decidir antecipadamente como distribuir recursos através de subscrições. Um recurso valioso é o guia de decisão de [subscrição](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) porque é difícil mover recursos para outra subscrição mais tarde. Por exemplo, um laboratório não pode ser transferido para outra subscrição depois de criado.  
* **Topologia da rede**: A [infraestrutura](../app-service/networking-features.md) de rede padrão que a DevTest Labs cria automaticamente pode não ser suficiente para satisfazer os requisitos e constrangimentos para os utilizadores da empresa. É comum ver o [Azure ExpressRoute conectado redes virtuais,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para conectividade através de subscrições, e até [mesmo o encaminhamento forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) para garantir aconectividade no local apenas. A DevTest Labs permite que as redes virtuais existentes sejam ligadas ao laboratório para permitir o seu uso quando estiver a criar novas máquinas virtuais em laboratório. 
* **Acesso remoto de máquinas virtuais**: Existem muitas opções para aceder remotamente às máquinas virtuais localizadas nos Laboratórios DevTest. O mais fácil é usar iPs públicos ou iPs públicos partilhados. Estas são [as configurações disponíveis no laboratório.](devtest-lab-shared-ip.md) Se estas opções não forem suficientes, usar um gateway de acesso remoto também é uma opção. Esta opção é mostrada na arquitetura de referência empresarial [DevTest Labs](devtest-lab-reference-architecture.md) e descrita ainda mais na documentação remota de [gateway de desktop da DevTest Labs](configure-lab-remote-desktop-gateway.md). As empresas também podem usar o ExpressRoute ou uma VPN site-to-site para ligar os seus laboratórios à sua rede no local. Esta opção permite ligações diretas remotas ou SSH às máquinas virtuais com base no seu endereço IP privado sem exposição à internet. 
* **Permissões de manuseamento**: As duas permissões-chave geralmente utilizadas nos Laboratórios DevTest são [Proprietário e Utilizador](devtest-lab-add-devtest-user.md)de Laboratório . É importante decidir antes de lançar os Laboratórios DevTest em geral, quem será encarregado de cada nível de acesso no laboratório. Um modelo comum é o proprietário do orçamento (team lead, por exemplo) como o proprietário do laboratório e os membros da equipa como utilizadores de laboratório. Este modelo permite à pessoa (líder de equipa) responsável pelo orçamento ajustar as definições de política e manter a equipa dentro do orçamento.  

## <a name="completing-the-proof-of-concept"></a>Completar a prova de conceito 

Depois das aprendizagens esperadas terem sido cobertas, é hora de completar o piloto. Este é o momento de recolher feedback dos utilizadores, determinar se o piloto foi bem sucedido, e decidir se a organização vai avançar com uma escala de DevTest Labs na empresa. É também um ótimo momento para considerar automatizar a implantação de DevTest Labs e recursos associados para garantir a consistência em toda a escala. 

## <a name="next-steps"></a>Passos seguintes 

* [Documentação empresarial da DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Arquitetura de referência para uma empresa](devtest-lab-reference-architecture.md)
* [Dimensionando a sua implementação de DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orquestrar a implementação da Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
