---
title: Integração de Azure DevTest Labs e DevOps Microsoft Docs
description: Aprenda a utilizar laboratórios de Azure DevTest Labs dentro de um pipelines de integração contínua (CI)/ entrega contínua (CD) em ambiente empresarial.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67078927"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integração de Azure DevTest Labs e Azure DevOps
DevOps é uma metodologia de desenvolvimento de software que integra o desenvolvimento de software (Dev) com operações (Ops) para um sistema. Este sistema pode fornecer novas funcionalidades, atualizações e correções em alinhamento com objetivos de negócio. Esta metodologia abrange tudo, desde a conceção de novas funcionalidades com base em objetivos, padrões de utilização e feedback do cliente; para fixar, recuperar e endurecer o sistema quando ocorrerem problemas. Um componente facilmente identificado desta metodologia é o gasoduto de integração contínua (CI)/ entrega contínua (CD). Um oleoduto CI/CD retira informações, códigos e recursos de um compromisso através de uma série de passos que incluem a construção, teste e implantação, para produzir o sistema. Este artigo centra-se em diferentes formas de usar eficazmente laboratórios dentro de um oleoduto em um ambiente empresarial. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Benefícios da utilização de laboratórios em Fluxo de Trabalho de DevOps 

### <a name="focused-access"></a>Acesso focado 
Usar um laboratório como componente permite que um ecossistema específico se associe a um grupo limitado de pessoas. Normalmente, uma equipa ou grupo que esteja a trabalhar numa área comum ou uma característica específica terá um laboratório atribuído a eles.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicação de infraestrutura na nuvem 
Um desenvolvedor pode rapidamente configurar um ecossistema de desenvolvimento que inclui uma caixa de v com código fonte e ferramentas. O desenvolvedor também pode criar um ambiente quase idêntico à configuração de produção. Ajuda com o desenvolvimento mais rápido do loop interno. 

### <a name="pre-production"></a>Pré-produção 
Ter um laboratório no oleoduto CI/CD facilita a dispor de múltiplos ambientes ou máquinas de pré-produção diferentes ao mesmo tempo para testes assíncronos. Diferentes infraestruturas de apoio, como agentes de construção, podem ser implantadas e geridas dentro de um laboratório. 

## <a name="devops-with-devtest-labs"></a>DevOps com DevTest Labs 

### <a name="development--operation"></a>Desenvolvimento / Operação 
Um laboratório deve estar focado numa equipa que está a trabalhar numa área de recurso. Este foco comum permite a partilha de recursos específicos da área, como ferramentas, scripts ou modelos de Gestor de Recursos. Permite mudanças mais rápidas, limitando os efeitos negativos a um grupo menor. Estes recursos partilhados permitem ao desenvolvedor criar VMs de desenvolvimento com todo o código, ferramentas e configuração necessários. Podem ser criados de forma dinâmica ou ter um sistema que cria imagens base com as personalizações. Não só o desenvolvedor pode criar VMs, mas também criar ambientes DevTest Labs com base nos modelos necessários para criar os recursos Azure apropriados no laboratório. Quaisquer alterações ou trabalhos destrutivos podem ser feitos contra o ambiente do laboratório sem afetar mais ninguém. Considere o cenário em que o produto é um sistema autónomo instalado na máquina do cliente. Neste cenário, a DevTest Labs melhorou a criação de VM que inclui a instalação de software adicional utilizando artefactos e configurações de clientes pré-construção para testes mais rápidos em loop interno do código. 
  
## <a name="cicd-pipeline"></a>Gasoduto CI/CD 
O oleoduto CI/CD é um dos componentes críticos em DevOps que movem o código a partir de um pedido de puxão do desenvolvedor, integra-o com o código existente, e implanta-o para o ecossistema de produção. Todos os recursos não precisam de estar dentro de um laboratório. Por exemplo, um hospedeiro Jenkins poderia ser criado fora do laboratório como um recurso mais persistente. Aqui estão alguns exemplos específicos de integração de laboratórios no oleoduto. 

### <a name="build"></a>Compilação 
O gasoduto de construção está focado na criação de um pacote de componentes que serão testados em conjunto para serem entregues ao gasoduto de libertação. Os laboratórios podem fazer parte do oleoduto de construção como o local para os agentes de construção e outros recursos de apoio. Ter a capacidade de construir dinamicamente a infraestrutura permite um maior controlo. Com a capacidade de ter múltiplos ambientes num laboratório, cada construção pode ser executada assíncronamente enquanto utiliza o ID de construção como parte da informação ambiental para identificar os recursos exclusivamente para a construção específica.   

Para os agentes de construção, a capacidade do laboratório de restringir o acesso aumenta a segurança e reduz a possibilidade de corrupção acidental.  

### <a name="test"></a>Teste 
A DevTest Labs permite que um oleoduto CI/CD automatizar a criação de Recursos Azure (VMs, ambientes) que podem ser utilizados para testes automatizados e manuais. Os VMs seriam criados usando artefactos ou fórmulas que usam informações do processo de construção para criar as diferentes configurações personalizadas necessárias para o teste.   

### <a name="release"></a>Libertar 
DevTest Labs é comumente usado para verificação na secção de libertação antes de o código ser implementado. É semelhante aos testes na secção Build. Os recursos de produção não devem ser implantados nos Laboratórios DevTest. 

### <a name="customization"></a>Personalização 
Em Azure DevOps, existem tarefas existentes que permitem a manipulação de VMs e ambientes em laboratórios específicos. Enquanto os Serviços Azure DevOps são uma forma de gerir o pipeline CI/CD, pode integrar o laboratório em qualquer sistema que suporte a capacidade de chamar APIs REST, executar scripts PowerShell ou usar o Azure CLI. 

Enquanto alguns gestores de gasodutos CI/CD têm plugins de código aberto existentes que podem gerir recursos dentro do Azure e do DevTest Labs. Pode ser necessário utilizar alguns scripts personalizados para se adaptar às necessidades específicas do oleoduto.  Com isso em mente, ao executar uma tarefa, um diretor de serviço é usado com o papel adequado para ter acesso ao laboratório. O diretor de serviço geralmente precisa do papel de contribuinte para o laboratório. Para mais informações, consulte [A Integrate Azure DevTests Labs no seu Pipeline de integração e entrega contínua Azure DevOps.](devtest-lab-integrate-ci-cd-vsts.md) 
 