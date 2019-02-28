---
title: Sobre o Azure Lab Services | Microsoft Docs
description: Saiba como o Lab Services pode tornar mais fácil criar, gerir e proteger laboratórios com máquinas virtuais que podem ser utilizadas pelos programadores, técnicos de teste, educadores, estudantes, entre outros.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 5c53c64c337cb2992b002ee02ceb9aab36e16de4
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958750"
---
# <a name="an-introduction-to-azure-lab-services"></a>Introdução ao Azure Lab Services
O Azure Lab Services permite-lhe configurar rapidamente um ambiente para a sua equipa (por exemplo: ambiente de desenvolvimento, ambiente de teste, ambiente de laboratório de sala de aula) na cloud. O proprietário do laboratório cria um laboratório, aprovisiona máquinas virtuais do Linux ou Windows, instala as ferramentas e o software necessário e disponibiliza-os aos utilizadores do laboratório. Os utilizadores do laboratório ligam-se a máquinas virtuais (VMs) no laboratório e utilizam-nas para o trabalho diário, projetos de curto prazo ou para fazer exercícios da sala de aula. Quando os utilizadores começam a utilizar recursos no laboratório, o administrador do laboratório pode analisar os custos e utilização em múltiplos laboratórios e definir políticas abrangentes para otimizar os custos da equipa ou organização.

> [!IMPORTANT]
> O **Azure DevTest Labs** está a ser expandido com novos tipos de laboratórios (Azure Lab Services)!
>  
> O Azure Lab Services permite-lhe criar tipos de laboratório gerido, como laboratórios de sala de aula. O próprio serviço processa todas as a gestão da infraestrutura para um tipo de laboratório gerido, de rotação a segurança de VMs para manipulação de erros e dimensionar a infraestrutura. Por enquanto, [DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) e [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) continuará a ser serviços separados, no Portal do Azure. 

## <a name="key-capabilities"></a>Principais capacidades

O Azure Lab Services suporta as seguintes funcionalidades-chave:

- **Configuração rápida e flexível de um laboratório**. Ao utilizar o Azure Lab Services, os proprietários do laboratório podem configurar rapidamente um laboratório para as suas necessidades. O serviço oferece a opção para cuidar de todo o trabalho de infraestrutura do Azure para tipos de laboratório geridos, ou para permitir aos proprietários de laboratório Self-gerir e personalizar a infraestrutura na subscrição do proprietário de laboratório. O serviço fornece dimensionamento incorporado e resiliência da infraestrutura para laboratórios que o serviço gere automaticamente.
- **Experiência simplificada para utilizadores do laboratório**. Num tipo de laboratório gerido, como um laboratório de sala de aula, os utilizadores de laboratório podem registar-se a um laboratório com um código de registo e aceder em qualquer altura o laboratório a utilização de recursos do laboratório. Num laboratório criado no DevTest Labs, o proprietário do laboratório pode dar permissões para os utilizadores do laboratório criarem e acederem a máquinas virtuais, gerirem e reutilizarem discos de dados e configurarem segredos reutilizáveis.  
- **Análise e otimização de custos**. O proprietário do laboratório pode definir agendas de laboratório para encerrar e iniciar automaticamente as máquinas virtuais. O proprietário do laboratório pode definir uma agenda para especificar os blocos de tempo em que as máquinas virtuais do laboratório estão acessíveis para os utilizadores, definir políticas de utilização por utilizador ou laboratório para otimizar os custos e analisar as tendências de atividade e utilização num laboratório. Para tipos de laboratório gerido, como laboratórios de sala de aula, atualmente um subconjunto mais pequeno de opções de Otimização e análise de custo estão disponíveis.
- **Segurança incorporada**. O proprietário do laboratório pode configurar uma rede virtual privada e uma subrede para um laboratório e ativar um endereço IP público partilhado. Os utilizadores do laboratório podem aceder aos recursos de forma segura com a rede virtual configurada com o ExpressRoute ou rede de VPNs. (apenas disponível no DevTest Labs)
- **Integração com os fluxos de trabalho e ferramentas**. O Azure Lab Services permite-lhe integrar os laboratórios nos sistemas de gestão e site da organização. Pode aprovisionar automaticamente ambientes a partir das ferramentas de integração contínua/implementação contínua (CI/CD). (apenas disponível no DevTest Labs)

> [!NOTE]
> Atualmente o Azure Lab Services suporta apenas as VM criadas a partir de imagens do Azure Marketplace. Se pretender utilizar imagens personalizadas ou criar outros recursos de PaaS num ambiente de laboratório, utilize o DevTest Labs. Para obter mais informações, consulte [Criar uma imagem personalizada no DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) e [Criar ambientes de laboratório com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Cenários

Eis alguns dos cenários que o Azure Lab Services suporta:

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
Pode criar dois tipos de laboratórios: **geridos tipos de laboratório** com o Azure Lab Services e **laboratórios** com o Azure Lab Services. Se quiser apenas de entrada que precisa num laboratório e permitir que o serviço, configurar e gerir a infraestrutura necessária para o laboratório, escolher um da **geridos tipos de laboratório**. Atualmente, **laboratório de sala de aula** é o tipo de laboratório gerido só pode criar com o Azure Lab Services. Se pretender gerir a sua própria infraestrutura, crie um laboratório usando **Azure DevTest Labs**.

As seguintes secções fornecem mais detalhes sobre estes laboratórios. 

## <a name="managed-lab-types"></a>Tipos de laboratório geridos
O Azure Lab Services permite-lhe criar laboratórios cuja infraestrutura é gerida pelo Azure. Este artigo se refere a elas como tipos de laboratório gerido. Gerido laboratório tipos oferta diferentes tipos de laboratórios que se adequam às suas necessidades específicas. Atualmente, está apenas geridas o tipo de laboratório, que é suportado **laboratório de sala de aula**. 

Tipos de laboratório geridos permitem-lhe começar imediatamente, com uma configuração mínima. O próprio serviço processa toda a gestão da infraestrutura do laboratório, desde gerar as VMs até processar os erros e dimensionar a infraestrutura. Para criar um tipo de laboratório gerido como um laboratório de sala de aula, terá de criar primeiro uma conta de laboratório para a sua organização. A conta de laboratório serve como a conta central em que são geridos todos os laboratórios na organização. 

Quando criar e utilizar recursos do Azure nesses tipos de laboratório gerido, o serviço cria e gere os recursos nas subscrições do Microsoft internos. Não são criados na sua própria subscrição do Azure. O serviço mantém um registo da utilização destes recursos nas subscrições internas da Microsoft. A utilização é faturada na sua subscrição do Azure que contém a conta de laboratório.   

Aqui estão alguns da **casos de utilização para tipos de laboratório gerido**: 

- Forneça aos estudantes um laboratório com máquinas virtuais configuradas com o que é necessário para uma aula. Dê a cada estudante um número limitado de horas para utilizar as VMs para trabalhos de casa ou projetos pessoais.
- Configure um conjunto de VMs de computação de alto desempenho para realizar investigações repletas de gráficos e computação. Execute as VMs necessárias e limpe as máquinas quando terminar. 
- Mova o laboratório de computadores físico da escola para a cloud. Dimensione automaticamente o número de VMs apenas para o limite máximo de custo e utilização definido no laboratório.  
- Aprovisione rapidamente um laboratório de máquinas virtuais para alojar um hackathon. Elimine o laboratório com um único clique quando terminar. 


## <a name="devtest-labs"></a>DevTest Labs
Poderá ter cenários em que pretende gerir toda a infraestrutura e configuração, na sua própria subscrição. Para o fazer, pode criar um laboratório com o Azure DevTest Labs no portal do Azure. Para estes laboratórios, não tem de criar uma conta de laboratório. Estes laboratórios não aparecem na conta de laboratório (o que existe para os tipos de laboratório geridos).  

Veja a seguir alguns **casos de utilização do DevTest Labs**: 

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
| Gestão da infraestrutura do Azure no laboratório. |  Gerido automaticamente pelo serviço | Gerido por conta própria  |
| Resiliência incorporada para problemas de infraestrutura | Processado automaticamente pelo serviço | Gerido por conta própria  |
| Gestão de subscrições | O serviço processa a alocação de recursos nas subscrições da Microsoft que suportam o serviço. O dimensionamento é processado automaticamente pelo serviço. | Pode gerir por conta própria na sua subscrição do Azure. Sem dimensionamento automático das subscrições. |
| Implementação do Azure Resource Manager no laboratório | Não disponível | Disponível |

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos: 

- [Sobre laboratórios de sala de aula ](./classroom-labs/classroom-labs-overview.md)
- [Sobre os laboratórios DevTest](devtest-lab-overview.md)
