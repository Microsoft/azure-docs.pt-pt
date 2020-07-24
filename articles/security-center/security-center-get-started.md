---
title: Upgrade para nível padrão - Azure Security Center
description: Este quickstart mostra-lhe como fazer upgrade para o nível de preços padrão do Security Center para segurança adicional.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: f16df87ca72b66003d8870dcd9d778616ea840d4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038550"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Início Rápido: Carregar uma subscrição do Azure para o Centro de Segurança Standard
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças entre cargas de trabalho na cloud híbrida. Enquanto o nível Free oferece uma segurança limitada apenas para os seus recursos Azure, o nível padrão alarga essas capacidades a instalações e outras nuvens. O Centro de Segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças com análise e inteligência, e a responder rapidamente quando sob ataque. Pode tentar o Security Center Standard sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Neste artigo, você atualiza para o nível padrão para uma segurança adicional e instala o agente Log Analytics nas suas máquinas virtuais para monitorizar vulnerabilidades e ameaças de segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para atualizar uma subscrição do nível padrão, deve ser-lhe atribuída a função de Proprietário de Assinatura, Colaborador de Assinatura ou Administrador de Segurança.

## <a name="enable-your-azure-subscription"></a>Ativar a subscrição do Azure

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure,** selecione **Security Center**. **Centro de Segurança - A visão geral** abre.

   ![Descrição geral do Centro de Segurança][2]

**Centro de Segurança – Descrição Geral** oferece uma vista unificada da postura de segurança das suas cargas de trabalho da cloud híbrida, permitindo-lhe detetar e avaliar a segurança das cargas de trabalho e identificar e mitigar o risco. O Centro de Segurança ativa automaticamente qualquer uma das suas subscrições do Azure que não foram anteriormente carregadas por si ou por outro utilizador da subscrição para o escalão Gratuito.

Pode ver e filtrar a lista de subscrições, clicando no item de menu **Subscrições**. O Centro de Segurança irá agora começar a avaliar a segurança destas subscrições para identificar vulnerabilidades de segurança. Para personalizar os tipos de avaliações, pode modificar a política de segurança. As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares.

Poucos minutos depois de iniciar o Centro de Segurança pela primeira vez, poderá ver:

- **Recomendações** de formas de melhorar a segurança das suas subscrições do Azure. Clique no mosaico **Recomendações** para iniciar uma lista prioritária.
- Um inventário de recursos de **Computação e aplicações**, **Rede**, **Segurança de dados** e **Identidade e acesso**, que estão agora a ser avaliados pelo Centro de Segurança, juntamente com a postura de segurança de cada um deles.

Para tirar o máximo partido do Security Center, é necessário completar os passos abaixo para atualizar para o nível padrão e instalar o agente Log Analytics.

## <a name="upgrade-to-the-standard-pricing-tier"></a>Upgrade para o nível de preços padrão
Para efeitos do início rápido e tutoriais do Security Center, tem de fazer upgrade para o nível padrão. Há um julgamento gratuito do Security Center Standard. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

1. No menu principal do Centro de Segurança, selecione **Introdução**.
 
   ![Introdução][4]

2. Em **Atualização**, o Centro de Segurança apresenta uma lista de subscrições e áreas de trabalho elegíveis para inclusão. 
   - Pode clicar em **Aplicar a sua versão de avaliação** para ver uma lista de todas as subscrições e áreas de trabalho com o respetivo estado de elegibilidade de avaliação.
   -    Pode atualizar as subscrições e áreas de trabalho que não são elegíveis para avaliação.
   -    Pode selecionar as áreas de trabalho e subscrições elegíveis para iniciar a avaliação.
3. Clique em **Iniciar avaliação** para iniciar a avaliação nas subscrições selecionadas.


  ![Alertas de segurança][9]

## <a name="automate-data-collection"></a>Automatizar a recolha de dados
O Centro de Segurança recolhe dados de VMs do Azure e de computadores que não pertencem ao Azure para monitorizar ameaças e vulnerabilidades de segurança. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. Por predefinição, o Centro de Segurança irá criar uma nova área de trabalho para si.

Quando o provisionamento automático está ativado, o Security Center instala o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. O aprovisionamento automático é vivamente recomendado.

Para permitir o provisionamento automático do agente Log Analytics:

1. No menu principal do Centro de Segurança, selecione **definições de preços &**.
2. Na linha da subscrição, clique na subscrição na qual pretende alterar as definições.
3. No separador **Recolha de Dados**, defina **Aprovisionamento automático** para **Ativar**.
4. Selecione **Guardar**.
---
  ![Ativar o aprovisionamento automático][6]

Com estas novas informações sobre as VMs do Azure, o Centro de Segurança pode fornecer Recomendações adicionais relacionadas com o estado de atualização do sistema de estado, configurações de segurança do SO, proteção de ponto final, bem como gerar Alertas de segurança adicionais.

  ![Recomendações][8]

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com os quickstarts e tutoriais subsequentes, continue a executar o nível padrão e mantenha o provisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Volte ao menu principal do Centro de Segurança e **selecione definições de preços &**.
2. Clique na subscrição que pretende alterar para o nível livre.
3. Selecione **o nível de preços** e selecione **Free** para alterar a subscrição do nível padrão para o nível Livre.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Volte ao menu principal do Centro de Segurança e **selecione definições de preços &**.
2. Limpe a subscrição em que pretende desativar o provisionamento automático.
3. No separador **Recolha de Dados**, defina **Aprovisionamento automático** para **Desativar**.
4. Selecione **Guardar**.

>[!NOTE]
> A desativação do fornecimento automático não remove o agente Log Analytics dos VMs Azure onde o agente foi provisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos seguintes
Neste quickstart, atualizou-se para o nível padrão e forneiu o agente Log Analytics para uma gestão unificada de segurança e proteção contra ameaças através das suas cargas de trabalho em nuvem híbrida. Para obter mais informações sobre como utilizar o Centro de Segurança, avance para o início rápido para inclusão de computadores Windows que estão no local e noutras clouds.

> [!div class="nextstepaction"]
> [Início Rápido: Incluir computadores Windows no Centro de Segurança do Azure](quick-onboard-windows-computer.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
