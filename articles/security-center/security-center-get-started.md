---
title: Início Rápido do Centro de Segurança do Azure - Carregar uma subscrição do Azure para o Centro de Segurança Standard | Microsoft Docs
description: Este início rápido mostra como fazer a atualização para o escalão de preço Standard do Centro de Segurança para obter segurança adicional.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 37f85afbdd55d3f14638f0833f69bb1992770449
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086076"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Início rápido: Carregar sua subscrição do Azure para o Centro de segurança Standard
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças entre cargas de trabalho na cloud híbrida. Enquanto o escalão Gratuito oferece segurança limitada apenas para os recursos do Azure, o escalão Standard expande estas capacidades para o local e para outras clouds. O Centro de Segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças com análise e inteligência, e a responder rapidamente quando sob ataque. Pode experimentar o Centro de segurança Standard sem encargos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Neste artigo, vai fazer a atualização para o escalão Standard para obter segurança adicional e instalar o Microsoft Monitoring Agent nas suas máquinas virtuais para monitorizar ameaças e vulnerabilidades de segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para atualizar uma subscrição para o escalão Standard, tem de lhe ser atribuída a função Proprietário da Subscrição, Contribuidor da Subscrição ou Administrador de Segurança.

## <a name="enable-your-azure-subscription"></a>Ativar a subscrição do Azure

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

   ![Descrição geral do Centro de Segurança][2]

**Centro de Segurança – Descrição Geral** oferece uma vista unificada da postura de segurança das suas cargas de trabalho da cloud híbrida, permitindo-lhe detetar e avaliar a segurança das cargas de trabalho e identificar e mitigar o risco. O Centro de Segurança ativa automaticamente qualquer uma das suas subscrições do Azure que não foram anteriormente carregadas por si ou por outro utilizador da subscrição para o escalão Gratuito.

Pode ver e filtrar a lista de subscrições, clicando no item de menu **Subscrições**. O Centro de Segurança irá agora começar a avaliar a segurança destas subscrições para identificar vulnerabilidades de segurança. Para personalizar os tipos de avaliações, pode modificar a política de segurança. As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares.

Poucos minutos depois de iniciar o Centro de Segurança pela primeira vez, poderá ver:

- **Recomendações** de formas de melhorar a segurança das suas subscrições do Azure. Clique no mosaico **Recomendações** para iniciar uma lista prioritária.
- Um inventário de recursos de **Computação e aplicações**, **Rede**, **Segurança de dados** e **Identidade e acesso**, que estão agora a ser avaliados pelo Centro de Segurança, juntamente com a postura de segurança de cada um deles.

Para tirar total partido do Centro de Segurança, tem de executar os passos abaixo para fazer a atualização para o escalão Standard e instalar o Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para o escalão Standard
Para efeitos dos inícios rápidos e tutoriais do Centro de Segurança, tem de atualizar para o escalão Standard. Existe uma avaliação gratuita do Centro de segurança Standard. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

1. No menu principal do Centro de Segurança, selecione **Introdução**.
 
   ![Introdução][4]

2. Em **Atualização**, o Centro de Segurança apresenta uma lista de subscrições e áreas de trabalho elegíveis para inclusão. 
   - Pode clicar em **Aplicar a sua versão de avaliação** para ver uma lista de todas as subscrições e áreas de trabalho com o respetivo estado de elegibilidade de avaliação.
   -    Pode atualizar as subscrições e áreas de trabalho que não são elegíveis para avaliação.
   -    Pode selecionar as áreas de trabalho e subscrições elegíveis para iniciar a avaliação.
3. Clique em **Iniciar avaliação** para iniciar a avaliação nas subscrições selecionadas.


  ![Alertas de segurança][9]

## <a name="automate-data-collection"></a>Automatizar a recolha de dados
O Centro de Segurança recolhe dados de VMs do Azure e de computadores que não pertencem ao Azure para monitorizar ameaças e vulnerabilidades de segurança. Os dados são recolhidos com o Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise. Por predefinição, o Centro de Segurança irá criar uma nova área de trabalho para si.

Quando o aprovisionamento automático está ativado, o Centro de Segurança instala o Microsoft Monitoring Agent em todas as VMs do Azure suportadas e nas VMs novas que forem criadas. O aprovisionamento automático é vivamente recomendado.

Para ativar o aprovisionamento automático do Microsoft Monitoring Agent:

1. No menu principal do Centro de Segurança, selecione **Política de Segurança**.
2. Na linha da subscrição, selecione **Editar definições >**.
3. No separador **Recolha de Dados**, defina **Aprovisionamento automático** para **Ativar**.
4. Selecione **Guardar**.
****
  ![Ativar o aprovisionamento automático][6]

Com estas novas informações sobre as VMs do Azure, o Centro de Segurança pode fornecer Recomendações adicionais relacionadas com o estado de atualização do sistema de estado, configurações de segurança do SO, proteção de ponto final, bem como gerar Alertas de segurança adicionais.

  ![Recomendações][8]

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com inícios rápidos e tutoriais posteriores, continue a executar o escalão Standard e mantenha o aprovisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Na linha da subscrição para a qual quer voltar como gratuita, selecione **Editar definições >**.
3. Selecione **Escalão de preço** e **Gratuito** para alterar a subscrição do escalão Standard para o escalão Gratuito.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de segurança**.
2. Na linha da subscrição para a qual quer desativar o aprovisionamento automático, selecione **Editar definições >**.
3. No separador **Recolha de Dados**, defina **Aprovisionamento automático** para **Desativar**.
4. Selecione **Guardar**.

>[!NOTE]
> Desativar o aprovisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure onde o agente tiver sido aprovisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, fez a atualização para o escalão Standard e aprovisionou o Microsoft Monitoring Agent para gestão de segurança unificada e proteção contra ameaças entre as cargas de trabalho da cloud híbrida. Para obter mais informações sobre como utilizar o Centro de Segurança, avance para o início rápido para inclusão de computadores Windows que estão no local e noutras clouds.

> [!div class="nextstepaction"]
> [Quickstart: Integrar computadores do Windows ao centro de segurança do Azure](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
