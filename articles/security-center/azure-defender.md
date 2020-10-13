---
title: Visão geral do Azure Defender e dos planos disponíveis
description: Conheça os planos, proteções e alertas do Azure Defender. Em seguida, ative o Azure Defender nas suas subscrições para uma segurança avançada.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bfff96666981a522cd6d91828604696a12ecad56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576864"
---
# <a name="introduction-to-azure-defender"></a>Introdução ao Azure Defender

As características do Azure Security Center cobrem os dois grandes pilares da segurança na nuvem:

- **Gestão da postura de segurança na nuvem (CSPM)** - O Centro de Segurança está disponível **gratuitamente** para todos os utilizadores do Azure. A experiência gratuita inclui funcionalidades de CSPM, tais como pontuação segura, deteção de configurações de segurança nas suas máquinas Azure, inventário de ativos e muito mais. Use estas funcionalidades de CSPM para fortalecer a sua postura híbrida em nuvem e acompanhar o cumprimento das políticas incorporadas.

- **Cloud workload protection (CWP)** - A plataforma integrada de proteção da carga de trabalho em nuvem (CWPP), **Azure Defender,** traz uma proteção avançada, inteligente, dos seus recursos e cargas híbridas e de carga de trabalho azures e híbridos. O Azure Defender traz uma gama de funcionalidades de segurança adicionais, conforme descrito nesta página. Além das políticas incorporadas, quando tiver ativado qualquer plano Azure Defender, pode adicionar políticas e iniciativas personalizadas. Pode adicionar normas regulamentares - como nist e Azure CIS - bem como o Azure Security Benchmark para uma visão verdadeiramente personalizada da sua conformidade.

O painel Azure Defender no Security Center proporciona visibilidade e controlo das funcionalidades CWP para o seu ambiente:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Um exemplo do painel do Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Que tipos de recursos o Azure Defender pode proteger?

O Azure Defender fornece alertas de segurança e proteção avançada de ameaças para máquinas virtuais, bases de dados SQL, contentores, aplicações web, sua rede, e muito mais.

Quando ativa o Azure Defender a partir da área de Preços e Definições do Azure Security Center, os **seguintes planos** do Defender estão todos ativados simultaneamente e fornecem defesas abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender para Serviço de Aplicações](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para IoT](defender-for-iot-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registos de contentor](defender-for-container-registries-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)

Cada um destes planos é explicado separadamente na documentação do Centro de Segurança.


## <a name="hybrid-cloud-protection"></a>Proteção de nuvem híbrida

Além de defender o seu ambiente Azure, pode adicionar as capacidades do Azure Defender ao seu ambiente de nuvem híbrida:

- Proteja os seus servidores não-Azure
- Proteja as suas máquinas virtuais noutras nuvens (como AWS e GCP)
- Proteja os seus dispositivos IoT

Você receberá inteligência de ameaça personalizada e alertas prioritários de acordo com o seu ambiente específico para que possa se concentrar no que mais importa.

Para alargar a proteção a máquinas virtuais e bases de dados SQL que se encontram noutras nuvens ou no local, inserte [o Arco Azure](https://azure.microsoft.com/services/azure-arc/) e ativa o Azure Defender. O Azure Arc para servidores é um serviço gratuito, mas os serviços que são utilizados em servidores ativados arc, por exemplo, Azure Defender, serão cobrados de acordo com os preços desse serviço. Saiba mais em [Adicionar máquinas não-Azure com Arco Azure](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> O conector nativo da AWS lida de forma transparente com a implantação do Arco Azure para si. Saiba mais em [Ligar as suas contas AWS ao Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-alerts"></a>Alertas do Azure Defender 

Quando o Azure Defender deteta uma ameaça em qualquer área do seu ambiente, gera um alerta. Estes alertas descrevem detalhes dos recursos afetados, sugeriram medidas de reparação e, em alguns casos, uma opção para desencadear uma aplicação lógica em resposta.

Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança integrado, pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).

> [!NOTE]
> Alertas de diferentes fontes podem levar diferentes quantidades de tempo a aparecer. Por exemplo, alertas que requerem a análise do tráfego da rede podem demorar mais tempo a aparecer do que alertas relacionados com processos suspeitos em funcionamento em máquinas virtuais.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender avançada capacidade de proteção

O Azure Defender utiliza análises avançadas para recomendações personalizadas relacionadas com os seus recursos. 

As proteções incluem a segurança das portas de gestão dos seus VMs com acesso just-in-time e controlos de aplicações adaptativos para criar listas de permitir o que as aplicações devem e não devem ser executadas nas suas máquinas. 

Utilize os azulejos de proteção avançados no painel Azure Defender para monitorizar e configurar cada uma destas proteções. 

## <a name="vulnerability-assessment-and-management"></a>Avaliação e gestão de vulnerabilidades

O Azure Defender inclui a verificação de vulnerabilidades para as suas máquinas virtuais e registos de contentores sem custos adicionais. Os scanners são alimentados pela Qualys mas você não precisa de uma licença Qualys ou mesmo uma conta Qualys - tudo é tratado perfeitamente dentro do Centro de Segurança. 

Reveja as conclusões destes scanners de vulnerabilidade e responda a todos eles de dentro do Centro de Segurança. Isto aproxima o Centro de Segurança de ser o único painel de vidro para todos os seus esforços de segurança na nuvem.

Saiba mais nas seguintes páginas:

- [Solução integrada de avaliação de vulnerabilidade do Security Center para máquinas virtuais Azure](deploy-vulnerability-assessment-vm.md)
- [Identificar vulnerabilidades em imagens nos registos de contentores do Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os benefícios do Azure Defender. 

> [!div class="nextstepaction"]
> [Ativar o Azure Defender](security-center-pricing.md)