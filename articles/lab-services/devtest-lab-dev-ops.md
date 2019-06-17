---
title: Integração do Azure DevTest Labs e DevOps | Documentos da Microsoft
description: Saiba como utilizar os laboratórios do Azure DevTest Labs dentro de uma integração contínua (CI) / pipelines de entrega contínua (CD) num ambiente empresarial.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078927"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integração de laboratórios DevTest do Azure e de DevOps do Azure
DevOps é uma metodologia de desenvolvimento de software que integra o desenvolvimento de software (desenvolvimento) com operações (Ops) para um sistema. Este sistema pode fornecer novos recursos, atualizações e correções em conformidade com os objetivos empresariais. Essa metodologia abrange tudo, desde a criação de novos recursos com base nos objetivos, padrões de utilização e os comentários dos clientes; Para corrigir, recuperar e o sistema de proteção, quando ocorrem problemas. Um componente facilmente identificado esta metodologia é a integração contínua (CI) / pipeline de entrega contínua (CD). Um pipeline CI/CD leva informações, código e recursos a partir de uma consolidação por uma série de passos que incluem a criação, teste e implementação, para produzir o sistema. Este artigo se concentra em diferentes formas de usar com eficiência laboratórios dentro de um pipeline num ambiente empresarial. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Vantagens de utilizar os laboratórios no fluxo de trabalho de DevOps 

### <a name="focused-access"></a>Acesso direcionado 
Usando um laboratório como um componente permite que um ecossistema específico associar um grupo limitado de pessoas. Normalmente, uma equipa ou grupo que está a funcionar numa área comum ou uma funcionalidade específica terá um laboratório atribuído.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicação de infraestrutura na cloud 
Um desenvolvedor pode configurar rapidamente a um ecossistema de desenvolvimento que inclui uma caixa de programação com o código-fonte e ferramentas. O desenvolvedor também pode criar um ambiente que é quase idêntico da configuração de produção. Ajuda com o desenvolvimento mais rápido ciclo interno. 

### <a name="pre-production"></a>Pré-produção 
Ter um laboratório no pipeline CI/CD torna mais fácil ter vários ambientes de pré-produção diferentes ou máquinas em execução ao mesmo tempo para Teste assíncrono. Suporte a diferentes infraestruturas como agentes de compilação podem ser implementadas e geridas num laboratório. 

## <a name="devops-with-devtest-labs"></a>DevOps com o DevTest Labs 

### <a name="development--operation"></a>Desenvolvimento / operação 
Um laboratório deve concentra-se numa equipa que está a funcionar numa área de funcionalidade. Esse foco comum permite a partilha de recursos de área específica, como ferramentas, scripts ou modelos do Resource Manager. Ele permite que as alterações mais rápidas, limitar os efeitos negativos a um grupo mais pequeno. Destes recursos partilhados permitem que o desenvolvedor criar VMs de desenvolvimento com todos os o código necessário, ferramentas e configuração. Eles podem ser criados a dinamicamente ou tem um sistema que cria imagens de base com as personalizações. Não só pode o desenvolvedor criar VMs, mas também podem criar ambientes de DevTest Labs com base nos modelos necessários para criar recursos do Azure adequados no laboratório. Quaisquer alterações ou trabalho destrutivo pode ser feito contra o ambiente de laboratório, sem afetar outras pessoas. Considere o cenário em que o produto é um sistema independente instalado no computador do cliente. Neste cenário, os laboratórios DevTest melhorou a criação da VM que inclui a instalação de software adicional através de artefactos e previamente criação das configurações do cliente para o teste de loop interno mais rápido do código. 
  
## <a name="cicd-pipeline"></a>Pipeline CI/CD 
O pipeline de CI/CD é um dos componentes mais importantes em DevOps que mover o código a partir de um pedido pull do desenvolvedor, ele se integra o código existente e implementa-o para o ecossistema de produção. Todos os recursos não precisam de estar dentro de um laboratório. Por exemplo, um anfitrião do Jenkins poderia ser configurar fora do laboratório como um recurso mais persistente. Aqui estão alguns exemplos específicos de integrar os laboratórios no pipeline. 

### <a name="build"></a>Compilação 
O pipeline de compilação está concentrado na criação de um pacote de componentes que será testado em conjunto para ser transferidas para o pipeline de lançamento. Laboratórios podem fazer parte do pipeline de compilação como a localização para os agentes de compilação e outros recursos de suporte. Ter a capacidade de criar dinamicamente a infra-estrutura permite maior controle. Com a capacidade de ter vários ambientes num laboratório, o cada compilação pode ser executada de forma assíncrona ao utilizar o ID de compilação como parte das informações do ambiente para identificar exclusivamente os recursos para a compilação específico.   

Para os agentes de compilação, a capacidade do laboratório para restringir o acesso aumenta a segurança e reduz a possibilidade de danos acidentais.  

### <a name="test"></a>Teste 
DevTest Labs permite que um pipeline de CI/CD automatizar a criação de recursos do Azure (VMs, ambientes) que pode ser utilizado para fins de teste manuais e automatizados. As VMs seriam criadas através de artefactos ou fórmulas que usam informações do processo de compilação para criar as diferentes configurações personalizadas necessárias para fins de teste.   

### <a name="release"></a>Libertar 
DevTest Labs é geralmente utilizado para verificação na secção de versão antes do código é implementado. Ele é semelhante ao teste na secção de compilação. Recursos de produção não devem ser implantados em DevTest Labs. 

### <a name="customization"></a>Personalização 
No Azure DevOps, existem tarefas existentes que permitem a manipulação de VMs e ambientes dentro do laboratórios específicos. Serviços de DevOps do Azure são uma forma de gerir o pipeline de CI/CD, pode integrar o laboratório de qualquer sistema que suporta a capacidade de chamar as APIs REST, executar scripts do PowerShell ou utilizar a CLI do Azure. 

Enquanto alguns gerentes de pipeline de CI/CD têm existente plugins de código-fonte aberto que pode gerir os recursos no Azure e o DevTest Labs. Terá de utilizar alguns scripts personalizados para atender às necessidades específicas do pipeline.  Com isso em mente, ao executar uma tarefa, um principal de serviço é utilizado com a função adequada para obter acesso ao laboratório. O principal de serviço tem, normalmente, o acesso de função de Contribuidor para o laboratório. Para obter mais informações, consulte [integrar o Azure DevTests Labs no seu pipeline de integração e entrega contínua do Azure DevOps](devtest-lab-integrate-ci-cd-vsts.md). 
 