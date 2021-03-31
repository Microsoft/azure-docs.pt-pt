---
title: Azure Lab Services vs. Azure DevTest Labs
description: Compare a Azure DevTest Labs e a Azure Lab Services.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: b1cd476faf6c457033ffeace03cd2e37b51e8578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480087"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Compare Azure DevTest Labs e Azure Lab Services
Existem dois serviços em Azure que lhe permitem configurar ambientes de laboratório na nuvem. 

- **Azure DevTest Labs** - Este serviço permite-lhe configurar rapidamente um ambiente para a sua equipa (por exemplo: ambiente de desenvolvimento ou ambiente de teste na nuvem). O proprietário do laboratório cria um laboratório, aprovisiona máquinas virtuais do Linux ou Windows, instala as ferramentas e o software necessário e disponibiliza-os aos utilizadores do laboratório. Os utilizadores de laboratório ligam-se a máquinas virtuais (VMs) em laboratório, e usam-nas para o seu trabalho diário, projetos de curto prazo. Quando os utilizadores começam a utilizar recursos no laboratório, o administrador do laboratório pode analisar os custos e utilização em múltiplos laboratórios e definir políticas abrangentes para otimizar os custos da equipa ou organização.
- **Azure Lab Services** - Este serviço permite criar tipos de laboratório geridos. Atualmente, os laboratórios de sala de aula são o único tipo de laboratório gerido que é apoiado pelos Serviços de Laboratório Azure. O próprio serviço trata de toda a gestão da infraestrutura para um tipo de laboratório gerido, desde girar VMs até lidar com erros, e escalar a infraestrutura. Depois de um administrador de TI criar uma conta de laboratório nos Serviços Azure Lab, um instrutor pode rapidamente configurar um laboratório para a sua aula, especificar o número e o tipo de VMs que são necessários para exercícios na classe, e adicionar utilizadores à classe. Uma vez que um utilizador se registe na classe, o utilizador pode aceder ao VM para fazer exercícios para a classe.  

## <a name="key-capabilities"></a>Principais capacidades

Estes serviços (Azure DevTest Labs e Azure Lab Services) suportam as seguintes capacidades/funcionalidades chave:

- **Configuração rápida e flexível de um laboratório**. Ao utilizar o Azure Lab Services, os proprietários do laboratório podem configurar rapidamente um laboratório para as suas necessidades. O serviço oferece a opção de cuidar de todos os trabalhos de infraestrutura Azure para tipos de laboratório geridos, ou para permitir aos proprietários de laboratório auto-gerir e personalizar infraestruturas na assinatura do proprietário do laboratório. O serviço fornece dimensionamento incorporado e resiliência da infraestrutura para laboratórios que o serviço gere automaticamente.
- **Experiência simplificada para utilizadores do laboratório**. Num laboratório gerido, como um laboratório de sala de aula, os utilizadores de laboratório podem registar-se num laboratório com um código de registo, e aceder ao laboratório a qualquer momento para usar os recursos do laboratório. Num laboratório criado no DevTest Labs, o proprietário do laboratório pode dar permissões para os utilizadores do laboratório criarem e acederem a máquinas virtuais, gerirem e reutilizarem discos de dados e configurarem segredos reutilizáveis.  
- **Análise e otimização de custos**. O proprietário do laboratório pode definir agendas de laboratório para encerrar e iniciar automaticamente as máquinas virtuais. O proprietário do laboratório pode definir uma agenda para especificar os blocos de tempo em que as máquinas virtuais do laboratório estão acessíveis para os utilizadores, definir políticas de utilização por utilizador ou laboratório para otimizar os custos e analisar as tendências de atividade e utilização num laboratório. Para tipos de laboratório geridos, como laboratórios de sala de aula, atualmente está disponível um subconjunto menor de opções de otimização de custos e análise.
- **Segurança incorporada**. O proprietário do laboratório pode configurar uma rede virtual privada e uma subrede para um laboratório e ativar um endereço IP público partilhado. Os utilizadores do laboratório podem aceder aos recursos de forma segura com a rede virtual configurada com o ExpressRoute ou rede de VPNs. (apenas disponível no DevTest Labs)
- **Integração com os fluxos de trabalho e ferramentas**. O Azure Lab Services permite-lhe integrar os laboratórios nos sistemas de gestão e site da organização. Pode aprovisionar automaticamente ambientes a partir das ferramentas de integração contínua/implementação contínua (CI/CD). (apenas disponível no DevTest Labs)

## <a name="scenarios"></a>Cenários

Eis alguns dos cenários que a Azure DevTest Labs e a Azure Lab Services apoiam:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurar um laboratório de computadores redimensionável na cloud para a sala de aula  

- Crie um laboratório de sala de aula gerido. Basta indicar ao serviço exatamente aquilo de que precisa e este irá criar e gerir a infraestrutura do laboratório automaticamente, para que se possa focar em dar a aula e não nos detalhes técnicos de um laboratório.
- Forneça aos estudantes um laboratório com máquinas virtuais configuradas com o que é necessário para uma aula. Dê a cada estudante um número limitado de horas para utilizar as VMs para trabalhos da aula.  
- Mova o laboratório de computadores físico da escola para a cloud. Dimensione automaticamente o número de VMs apenas para o limite máximo de custo e utilização definido no laboratório.
- Elimine o laboratório com um único clique quando terminar.

### <a name="use-devtest-labs-for-development-environments"></a>Utilizar o DevTest Labs para ambientes de desenvolvimento

O Azure DevTest Labs pode ser utilizado para implementar vários cenários-chave, mas apenas os cenários principais envolvem a utilização do DevTest Labs para alojar computadores de desenvolvimento para programadores. Neste cenário, o DevTest Labs fornece estas vantagens:

- Permita que os programadores aprovisionem rapidamente as computadores de desenvolvimento a pedido.
- Aprovisione ambientes do Windows e Linux com modelos e artefactos reutilizáveis.
- Os programadores podem personalizar facilmente os seus computadores de desenvolvimento quando for necessário.
- Os administradores podem controlar os custos ao garantir que os programadores não podem obter mais VMs do que as que necessitam para desenvolvimento e as VMs são encerradas quando não estão a ser utilizadas.

Para obter mais informações, veja [Utilizar o DevTest Labs para desenvolvimento](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Utilizar o DevTest Labs para ambientes de teste

Pode utilizar o Azure DevTest Labs para implementar vários cenários-chave, mas apenas os cenários principais envolvem a utilização do DevTest Labs para alojar computadores para técnicos de teste. Neste cenário, o DevTest Labs fornece estas vantagens:

- Os técnicos de teste podem testar a última versão da sua aplicação ao aprovisionar rapidamente ambientes do Windows e do Linux através de modelos e artefactos reutilizáveis.
- Os técnicos de teste podem dimensionar o teste de carga ao aprovisionar múltiplos agentes de teste.
- Os administradores podem controlar os custos ao garantir que os técnicos de teste não podem obter mais VMs do que as que necessitam para testes e as VMs são encerradas quando não estão a ser utilizadas.

Para obter mais informações, veja [Utilizar o DevTest Labs para testes](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Tipos de laboratórios
Você pode criar dois tipos de laboratórios: tipos de **laboratório geridos** com serviços de laboratório Azure e laboratórios com **serviços** de laboratório Azure. Se quiser inserir o que precisa num laboratório e deixar o serviço configurar e gerir a infraestrutura necessária para o laboratório, escolha entre um dos tipos de **laboratório geridos.** Atualmente, o **laboratório de sala de aula** é o único tipo de laboratório gerido que pode criar com os Serviços de Laboratório Azure. Se quiser gerir a sua própria infraestrutura, crie um laboratório utilizando **a Azure DevTest Labs**.

As seguintes secções fornecem mais detalhes sobre estes laboratórios. 

## <a name="managed-lab-types"></a>Tipos de laboratório geridos
O Azure Lab Services permite-lhe criar laboratórios cuja infraestrutura é gerida pelo Azure. Este artigo refere-se a eles como tipos de laboratório geridos. Os tipos de laboratório geridos oferecem diferentes tipos de laboratórios que se adequam à sua necessidade específica. Atualmente, apenas o tipo de laboratório gerido que é suportado é **o laboratório de sala de aula.** 

Os tipos de laboratório geridos permitem-lhe começar imediatamente, com a configuração mínima. O próprio serviço processa toda a gestão da infraestrutura do laboratório, desde gerar as VMs até processar os erros e dimensionar a infraestrutura. Para criar um laboratório gerido, como um laboratório de sala de aula, é preciso criar uma conta de laboratório para a sua organização primeiro. A conta de laboratório serve como a conta central em que são geridos todos os laboratórios na organização. 

Quando cria e utiliza recursos Azure nestes tipos de laboratório geridos, o serviço cria e gere recursos em subscrições internas da Microsoft. Não são criados na sua própria subscrição do Azure. O serviço mantém um registo da utilização destes recursos nas subscrições internas da Microsoft. A utilização é faturada na sua subscrição do Azure que contém a conta de laboratório.   

Aqui estão alguns dos casos de **utilização para tipos de laboratório geridos:** 

- Forneça aos estudantes um laboratório com máquinas virtuais configuradas com o que é necessário para uma aula. Dê a cada estudante um número limitado de horas para utilizar as VMs para trabalhos de casa ou projetos pessoais.
- Configure um conjunto de VMs de computação de alto desempenho para realizar investigações repletas de gráficos e computação. Execute as VMs necessárias e limpe as máquinas quando terminar. 
- Mova o laboratório de computadores físico da escola para a cloud. Dimensione automaticamente o número de VMs apenas para o limite máximo de custo e utilização definido no laboratório.  
- Aprovisione rapidamente um laboratório de máquinas virtuais para alojar um hackathon. Elimine o laboratório com um único clique quando terminar. 


## <a name="devtest-labs"></a>DevTest Labs
Poderá ter cenários em que pretende gerir toda a infraestrutura e configuração, na sua própria subscrição. Para o fazer, pode criar um laboratório com o Azure DevTest Labs no portal do Azure. Para estes laboratórios, não tem de criar uma conta de laboratório. Estes laboratórios não aparecem na conta de laboratório (que existe para os tipos de laboratório geridos).  

Aqui estão alguns dos casos de **utilização para a utilização de Laboratórios DevTest:** 

- Aprovisione rapidamente um laboratório de máquinas virtuais para alojar um hackathon ou uma sessão prática numa conferência. Elimine o laboratório com um único clique quando terminar. 
- Crie um conjunto de VMs configuradas com a sua aplicação e permita que a sua equipa utilize facilmente uma máquina virtual para deteção de erros.  
- Forneça aos programadores máquinas virtuais configuradas com todas as ferramentas necessárias. Agende o início e encerramento automático para minimizar os custos. 
- Crie repetidamente um laboratório de máquinas de teste como parte da sua implementação. Teste os bits mais recentes e limpe as máquinas de teste quando terminar. 
- Defina uma variedade de agentes de teste e máquinas virtuais configuradas de forma diferente para testar o desempenho e dimensionamento. 
- Ofereça sessões de formação para os seus clientes através de um laboratório configurado com a versão mais recente do seu produto. Dê a cada cliente um número limitado de horas para utilizar o laboratório. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Tipos de laboratório geridos vs. DevTest Labs
A seguinte tabela compara dois tipos de laboratórios suportados pelo Azure Lab Services: 

| Funcionalidades | Tipos de laboratório geridos | DevTest Labs |
| -------- | ----------------- | ---------- |
| Gestão da infraestrutura do Azure no laboratório. |  Gerido automaticamente pelo serviço | Gerido por conta própria  |
| Resiliência incorporada para problemas de infraestrutura | Processado automaticamente pelo serviço | Gerido por conta própria  |
| Gestão de subscrições | O serviço processa a alocação de recursos nas subscrições da Microsoft que suportam o serviço. O dimensionamento é processado automaticamente pelo serviço. | Pode gerir por conta própria na sua subscrição do Azure. Sem autoscalagem de assinaturas. |
| Implementação do Azure Resource Manager no laboratório | Não disponível | Disponível |

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos: 

- [Sobre os Laboratórios de Sala de Aula](../lab-services/classroom-labs-overview.md)
- [Sobre o DevTest Labs](devtest-lab-overview.md)
