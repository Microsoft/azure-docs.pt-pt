---
title: Tutorial do Centro de Segurança do Azure - Responder a incidentes de segurança | Microsoft Docs
description: Tutorial do Centro de Segurança do Azure - Responder a incidentes de segurança
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: d726006d3ecce69f129b1576c7c6d12833582873
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081995"
---
# <a name="tutorial-respond-to-security-incidents"></a>Tutorial: Responder a incidentes de segurança
O Centro de Segurança analisa continuamente as suas cargas de trabalho da cloud híbrida com análises avançadas e informações de ameaças para o alertar de atividades maliciosas. Além disso, pode integrar alertas de outros produtos e serviços de segurança no Centro de Segurança e criar alertas personalizados com base nos seus próprios indicadores ou origens de informações. Assim que for gerado um alerta, é necessária uma ação rápida para analisar e remediar. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Triar alertas de segurança
> * Fazer investigações mais aprofundadas para determinar a origem e o âmbito dos incidentes de segurança
> * Procurar dados de segurança para ajudar à investigação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar as funcionalidades abrangidas neste tutorial, tem de estar no escalão de preço Standard do Centro de Segurança. Pode experimentar o Centro de segurança Standard sem encargos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

## <a name="triage-security-alerts"></a>Triar alertas de segurança
O Centro de Segurança proporciona uma vista unificada de todos os alertas de segurança. Os alertas de segurança são classificados de acordo com a gravidade e, se possível, sempre que houver alertas relacionados, os mesmos são combinados num incidente de segurança. Ao triar alertas e incidentes, deve:

- Ignorar alertas para os quais não sejam necessárias ações adicionais, como, por exemplo, se forem falsos positivos
- Agir para remedir os ataques conhecidos, como, por exemplo, bloquear o tráfego de rede de um endereço IP malicioso
- Determinar os alertas que exigem mais investigação


1. No menu principal do Centro de Segurança, em **DETEÇÃO**, selecione **Alertas de segurança**:

   ![Alertas de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. Na lista de alertas, clique num incidente de segurança, que é uma coleção de alertas, para saber mais sobre o mesmo. É aberto **Incidente de segurança detetado**.

   ![Incidente de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Neste ecrã, a descrição do incidente de segurança aparece na parte superior e a lista de alertas que fazem parte do mesmo. Clique no alerta que pretende investigar mais aprofundadamente para obter mais informações.

   ![Incidente de segurança](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   O tipo de alerta pode variar. Leia [Compreender os alertas de segurança no Centro de Segurança do Azure ](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) para obter mais detalhes sobre o tipo de alerta e possíveis passos de remediação. Para os alertas que podem ser dispensados em segurança, clique com o botão direito do rato nos mesmos e selecione a opção **Dispensar**:

   ![Alerta](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Se a origem e o âmbito da atividade maliciosa forem desconhecidos, avance para o próximo passo para investigar mais.

## <a name="investigate-an-alert-or-incident"></a>Investigar um alerta ou incidente
1. Na página **Alerta de segurança**, clique no botão **Iniciar investigação** (se já tiver começado, o nome muda para **Continuar investigação**).

   ![Investigação](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   O mapa de investigação é uma representação gráfica das entidades que estão ligadas a este incidente ou alerta de segurança. Ao clicar numa entidade no mapa, as informações sobre essa entidade mostrarão entidades novas e o mapa expande-se. As propriedades da entidade que está selecionada no mapa estão realçadas no painel do lado direito da página. As informações disponíveis em cada separador variam de acordo com a entidade selecionada. Durante o processo de investigação, reveja todas as informações relevantes para compreender melhor o movimento de um atacante.

2. Se precisar de mais provas ou se tiver de investigar adicionalmente entidades que tenham sido encontradas durante a investigação, avance para o passo seguinte.

## <a name="search-data-for-investigation"></a>Procurar dados para investigação

Pode utilizar as funcionalidades de pesquisa do Centro de Segurança para localizar mais provas de sistemas comprometidos e obter mais detalhes sobre as entidades que fazem parte da investigação.

Para fazer uma pesquisa, abra o dashboard do **Centro de Segurança**, clique em **Pesquisar** no painel de navegação do lado esquerdo, selecione a área de trabalho que contém as entidades que pretende pesquisar, escreva a consulta de pesquisa e clique no botão de pesquisa.

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com inícios rápidos e tutoriais posteriores, continue a executar o escalão Standard e mantenha o aprovisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Selecione a subscrição ou a política para a qual pretende voltar como Gratuita. **Política de segurança** abre-se.
3. Em **COMPONENTES DA POLÍTICA**, selecione **Escalão de preço**.
4. Selecione **Gratuito** para alterar a subscrição, do Escalão standard para o Escalão gratuito.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de segurança**.
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Em **Política de segurança – Recolha de Dados**, selecione **Desativar** em **Inclusão** para desativar o aprovisionamento automático.
4. Selecione **Guardar**.

>[!NOTE]
> Desativar o aprovisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure onde o agente tiver sido aprovisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a conhecer as funcionalidades do Centro de Segurança que devem ser utilizadas para responder a incidentes de segurança, tais como:

> [!div class="checklist"]
> * Incidentes de segurança que sejam uma combinação de alertas relacionados para um recurso
> * Mapa de investigação, que é uma representação gráfica das entidades que estão ligadas a um incidente ou alerta de segurança
> * Capacidades de pesquisa para localizar mais provas de sistemas comprometidos

Para saber mais sobre a funcionalidade de investigação do Centro de Segurança, veja:

> [!div class="nextstepaction"]
> [Investigar incidentes e alertas](security-center-investigation.md)
