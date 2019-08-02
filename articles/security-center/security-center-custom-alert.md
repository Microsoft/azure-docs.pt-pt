---
title: Regras de Alerta Personalizadas no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a criar regras de alerta personalizadas no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 8845cb0a91edefa0350558f35488519ec37d064d
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663577"
---
# <a name="custom-alert-rules-in-azure-security-center-retired"></a>Regras de alerta personalizadas na central de segurança do Azure (desativada)
Este documento ajuda você a criar regras de alerta personalizadas (versão prévia) na central de segurança do Azure.

> [!NOTE]
> Os alertas personalizados foram retirados da central de segurança do Azure em 30 de junho de 2019. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Desativação de regras de alerta personalizadas na central de segurança do Azure

A experiência de alertas personalizados foi desativada em 30 de junho de 2019, devido à retirada da infraestrutura subjacente na qual ela se baseou. Todos os alertas personalizados definidos antes do retriement não estão tendo efeito e alertas de segurança com base nessas regras de alertas personalizados não estão sendo gerados. Você ainda pode exibir as consultas de suas regras de alerta personalizadas na central de segurança para recriá-las nas alternativas mencionadas abaixo:

Os usuários são aconselhados a:
- Habilitar o [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e usar sua funcionalidade de [análise](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) interna para recriar suas regras de alerta
- Recrie seus alertas com Azure Monitor alertas de log
                                     
Para manter seus alertas existentes e recriá-los no Azure Sentinel, [inicie o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Como primeira etapa, selecione o espaço de trabalho onde os alertas personalizados são armazenados e, em seguida, selecione o item de menu ' análise ' para configurar suas regras de alertas personalizados. Visite a [documentação](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) para obter informações adicionais.

> [!NOTE]
> Não há suporte para alertas personalizados usando consultas de instruções de [pesquisa](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) ou de [União](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) no Azure Sentinel. Edite esses alertas antes de executar a migração.

Para saber como recriar seus alertas usando Azure Monitor alertas de log, consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Para obter uma visão geral, consulte [alertas de log em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>O que são regras de alerta personalizadas no Centro de Segurança?

O Centro de Segurança tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) predefinidos, que são acionados quando ocorre uma ameaça ou atividade suspeita. Em alguns cenários, poderá querer criar um alerta personalizado para satisfazer necessidades específicas do seu ambiente.

As regras de alerta personalizadas no Centro de Segurança permitem-lhe definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente. Pode criar consultas e o resultado destas consultas pode ser utilizado como critérios para a regra personalizada e, depois deste critério ser correspondido, a regra é executada. Pode utilizar eventos de segurança de computadores, registos de soluções de segurança de um parceiro ou dados ingeridos com APIs para criar as suas consultas personalizadas.

> [!NOTE]
> Não há suporte para alertas personalizados no recurso de [investigação](security-center-investigation.md)da central de segurança.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Como criar uma regra de alerta personalizada no Centro de Segurança?

Abra o dashboard **Centro de Segurança** e siga estes passos para criar uma regra de alerta personalizada:

1.  No painel esquerdo, em **Deteção** clique em **Regras de alerta personalizadas (Pré-visualização)** .
2.  Na página **Centro de Segurança – Regras de alerta personalizadas (Pré-visualização)** , clique em **Nova regra de alerta personalizada**.

    ![Alerta personalizado](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  A página Criar regra de alerta personalizada é apresentada com as seguintes opções:

    ![Criar](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Escreva o nome desta regra personalizada no campo **Nome**.
5.  Escreva uma breve descrição que reflita o objetivo desta regra no campo **Descrição**.
6.  Selecione o nível de gravidade (Alta, Média, Baixa) de acordo com as suas necessidades no campo **Gravidade**.
7.  Selecione a subscrição na qual esta regra é aplicável no campo **Subscrição**.
8.  Selecione o espaço de trabalho que você deseja monitorar com essa regra no campo **espaço de trabalho** e, no campo consulta de **pesquisa** , a consulta que você deseja usar para obter os resultados.

    > [!NOTE]
    > Você precisa de permissão de gravação no espaço de trabalho que você selecionou para armazenar seu alerta personalizado.
    >
    >

    O resultado da consulta aciona o alerta. Tenha em atenção que quando escreve uma consulta válida, a marca de verificação verde é apresentada no canto direito deste campo:

    ![Consulta](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Selecione o intervalo de tempo em que a consulta acima irá ser executada no campo **Período**. Tenha em atenção que o resultado da pesquisa na parte inferior deste campo será alterado de acordo com o intervalo de tempo que selecionar.

    ![Período](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. No campo **Avaliação**, selecione a frequência com que esta regra deve ser avaliada e executada.
12. No campo **Número de resultados**, selecione o operador (maior que ou menor que).
13. No campo **Limiar**, escreva um número que será utilizado como referência para o operador que foi selecionado anteriormente.
14. Ative a opção **Suprimir Alertas** se quiser definir um tempo de espera antes de o Centro de Segurança enviar outro alerta para esta regra.
15. Clique em **OK** para concluir.

Depois de acabar de criar a nova regra de alerta, irá aparecer na lista de regras de alerta personalizadas. Quando as condições dessa regra estiverem preenchidas, será acionado um novo alerta e poderá vê-lo no dashboard **Alertas de Segurança**.

![Alerta](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Tenha em atenção que os parâmetros (consulta de pesquisa, limiar, etc.) que foram estabelecidos durante a criação da regra estão disponíveis no alerta para esta regra personalizada.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a criar uma regra de alerta personalizada no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
