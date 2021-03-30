---
title: Integração da Azure DevTest Labs e DevOps | Microsoft Docs
description: Aprenda a usar laboratórios da Azure DevTest Labs dentro de um pipeline de integração contínua (CI)/ entrega contínua (CD) em ambiente empresarial.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481073"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integração da Azure DevTest Labs e Azure DevOps
DevOps é uma metodologia de desenvolvimento de software que integra o desenvolvimento de software (Dev) com operações (Ops) para um sistema. Este sistema pode fornecer novas funcionalidades, atualizações e correções em alinhamento com os objetivos do negócio. Esta metodologia abrange tudo, desde a conceção de novas funcionalidades baseadas em objetivos, padrões de utilização e feedback do cliente; para corrigir, recuperar e endurecer o sistema quando ocorrem problemas. Um componente facilmente identificado desta metodologia é o gasoduto de integração contínua (CI)/ entrega contínua (CD). Um oleoduto CI/CD retira informações, códigos e recursos de um compromisso através de uma série de passos que incluem construção, teste e implantação, para produzir o sistema. Este artigo centra-se em diferentes formas de utilizar eficazmente laboratórios dentro de um oleoduto num ambiente empresarial. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Benefícios da utilização de laboratórios em fluxo de trabalho de DevOps 

### <a name="focused-access"></a>Acesso focado 
A utilização de um laboratório como componente permite que um ecossistema específico se associe a um grupo limitado de pessoas. Normalmente, uma equipa ou grupo que esteja a trabalhar numa área comum ou numa característica específica terá um laboratório atribuído a eles.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicação da infraestrutura na nuvem 
Um desenvolvedor pode rapidamente criar um ecossistema de desenvolvimento que inclui uma caixa dev com código fonte e ferramentas. O desenvolvedor também pode criar um ambiente quase idêntico à configuração de produção. Ajuda com o desenvolvimento mais rápido do loop interno. 

### <a name="pre-production"></a>Pré-produção 
Ter um laboratório no gasoduto CI/CD facilita a utilização de múltiplos ambientes ou máquinas de pré-produção diferentes ao mesmo tempo para testes assíncronos. Diferentes infraestruturas de apoio, tais como agentes de construção, podem ser implantadas e geridas dentro de um laboratório. 

## <a name="devops-with-devtest-labs"></a>DevOps com Laboratórios DevTest 

### <a name="development--operation"></a>Desenvolvimento / Operação 
Um laboratório deve estar focado numa equipa que está a trabalhar numa área de recurso. Este foco comum permite a partilha de recursos específicos da área, como ferramentas, scripts ou modelos de Gestor de Recursos. Permite alterações mais rápidas, limitando os efeitos negativos a um grupo mais pequeno. Estes recursos partilhados permitem ao desenvolvedor criar VMs de desenvolvimento com todo o código, ferramentas e configuração necessários. Podem ser criados dinamicamente ou ter um sistema que cria imagens base com as personalizações. Não só o desenvolvedor pode criar VMs, mas também pode criar ambientes DevTest Labs com base nos modelos necessários para criar os recursos Azure apropriados no laboratório. Quaisquer alterações ou trabalho destrutivo podem ser feitos contra o ambiente do laboratório sem afetar mais ninguém. Considere o cenário em que o produto é um sistema autónomo instalado na máquina do cliente. Neste cenário, a DevTest Labs melhorou a criação de VM que inclui a instalação de software adicional utilizando artefactos e configurações de clientes pré-construindo para testes de loop interno mais rápidos do código. 
  
## <a name="cicd-pipeline"></a>Pipeline de CI/CD 
O gasoduto CI/CD é um dos componentes críticos em DevOps que movem o código de um pedido de puxar do desenvolvedor, integra-o com o código existente, e o implanta no ecossistema de produção. Todos os recursos não precisam estar dentro de um laboratório. Por exemplo, um hospedeiro Jenkins poderia ser criado fora do laboratório como um recurso mais persistente. Aqui estão alguns exemplos específicos de integração de laboratórios no oleoduto. 

### <a name="build"></a>Compilar 
O gasoduto de construção está focado na criação de um pacote de componentes que serão testados em conjunto para serem entregues ao gasoduto de libertação. Os laboratórios podem fazer parte do oleoduto de construção como local para agentes de construção e outros recursos de apoio. Ter a capacidade de construir dinamicamente a infraestrutura permite um maior controlo. Com a capacidade de ter múltiplos ambientes em laboratório, cada construção pode ser executada assíncroticamente enquanto usa o ID de construção como parte da informação ambiental para identificar exclusivamente os recursos para a construção específica.   

Para os agentes de construção, a capacidade do laboratório de restringir o acesso aumenta a segurança e reduz a possibilidade de corrupçãos acidentais.  

### <a name="test"></a>Teste 
A DevTest Labs permite que um oleoduto CI/CD automatizar a criação de Recursos Azure (VMs, ambientes) que podem ser usados para testes automatizados e manuais. Os VMs seriam criados usando artefactos ou fórmulas que utilizassem informações do processo de construção para criar as diferentes configurações personalizadas necessárias para o teste.   

### <a name="release"></a>Libertar 
DevTest Labs é comumente usado para verificação na secção de libertação antes do código ser implementado. É semelhante a testes na secção Build. Os recursos de produção não devem ser implantados nos Laboratórios DevTest. 

### <a name="customization"></a>Personalização 
Em Azure DevOps, existem tarefas existentes que permitem a manipulação de VMs e ambientes dentro de laboratórios específicos. Enquanto os Serviços Azure DevOps são uma forma de gerir o pipeline CI/CD, pode integrar o laboratório em qualquer sistema que suporte a capacidade de chamar APIs de REST, executar scripts PowerShell ou usar O CLI do Azure. 

Embora alguns gestores de gasodutos CI/CD tenham plugins de código aberto existentes que podem gerir recursos dentro da Azure e da DevTest Labs. Poderá ser necessário utilizar alguns scripts personalizados para se adaptar às necessidades específicas do oleoduto.  Com isso em mente, ao executar uma tarefa, um diretor de serviço é usado com o papel apropriado para ter acesso ao laboratório. O diretor de serviço geralmente precisa do contributo de acesso ao laboratório. Para obter mais informações, consulte [Integrate Azure DevTests Labs no seu pipeline de integração e entrega contínua de Azure DevOps.](devtest-lab-integrate-ci-cd.md) 
 