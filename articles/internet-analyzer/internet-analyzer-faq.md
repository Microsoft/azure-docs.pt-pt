---
title: Perguntas frequentes sobre o Internet Analyzer | Microsoft Docs
description: As perguntas frequentes sobre o Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 61cb6003549530f381b9cbbed74c1cb62c91431c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512902"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Perguntas frequentes do Azure Internet Analyzer (versão prévia)

Estas são as perguntas frequentes para o Azure Internet Analyzer – se você tiver outras perguntas, vá para o [Fórum de comentários](https://aka.ms/internetAnalyzerFeedbackForum) e poste sua pergunta. Quando uma pergunta é frequente, a adicionamos a este artigo para que ela possa ser encontrada de forma rápida e fácil.

## <a name="how-do-i-participate-in-the-preview"></a>Como posso participar na pré-visualização?

A pré-visualização está disponível para determinados clientes. Se você estiver interessado em ingressar na versão prévia, faça o seguinte:

1. Iniciar sessão no [portal do Azure](https://ms.portal.azure.com).
2. Navegue até a página **assinaturas** .
3. Clique na assinatura do Azure com a qual você planeja usar o analisador da Internet.
4. Vá para as configurações de **provedores de recursos** para a assinatura.
5. Procure **Microsoft. Network** e clique no botão **registrar** (ou **registrar novamente**).
](./media/ia-faq/request-preview-access.png) ![solicitação de acesso

6. [Solicite a aprovação](https://aka.ms/internetAnalyzerContact) fornecendo seu endereço de email e a ID de assinatura do Azure que foi usada para fazer a solicitação de acesso.
7. Depois que sua solicitação tiver sido aprovada, você receberá uma confirmação por email e poderá criar/atualizar/modificar os recursos do Internet Analyzer da assinatura do Azure recém permitida.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>É necessário inserir o cliente para executar um teste?

Sim, o cliente do Internet Analyzer deve ser instalado em seu aplicativo para coletar métricas específicas para seus usuários. [Saiba como instalar o cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>A minha participação na pré-visualização é faturada?
Não, o Azure Internet Analyzer é gratuito para uso na versão prévia. Não há nenhum contrato de nível de serviço durante a versão prévia.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Quais cenários o Internet Analyzer foi projetado para abordar?

O Internet Analyzer foi projetado para fornecer informações de desempenho de rede com base em sua população de usuário. Para ajudar a tomar as melhores decisões de desempenho para seus usuários, o Internet Analyzer compara o desempenho de dois pontos de extremidade de Internet usando sua população de usuário distinta. Embora o Internet Analyzer possa responder a uma infinidade de perguntas, algumas das mais comuns são:

* Qual é o impacto no desempenho da migração para a nuvem? 
    * *Teste sugerido: personalizado (sua infraestrutura local atual) versus Azure (qualquer ponto de extremidade pré-configurado)*
* Qual é o valor de colocar meus dados na borda versus em um data center? 
    *  *Teste sugerido: Azure vs. Azure front door, Azure vs. CDN do Azure da Microsoft*
* Qual é o benefício de desempenho da porta frontal do Azure?
    *  *Teste sugerido: Custom/Azure/CDN versus Azure front door*
* Qual é o benefício de desempenho da CDN do Azure da Microsoft? 
    *  *Teste sugerido: personalizado/Azure/AFD versus CDN do Azure da Microsoft*
* Como a CDN do Azure da Microsoft se empilha? 
    *  *Teste sugerido: personalizado (outro ponto de extremidade da CDN) versus CDN do Azure da Microsoft*
* Qual é a melhor nuvem para sua população de usuário final em cada região? 
    *  *Teste sugerido: personalizado (outro serviço de nuvem) versus Azure (qualquer ponto de extremidade pré-configurado)*

## <a name="which-tests-can-i-run-in-preview"></a>Quais testes posso executar na visualização?

Cada teste que você cria no Internet Analyzer é composto por dois pontos de extremidade, o ponto final e o ponto de extremidade B. Qualquer uma das seguintes combinações pode ser executada como testes:  
* Dois pontos de extremidade pré-configurados,
* Um ponto de extremidade personalizado e um preconfigurado, ou
* Dois [pontos de extremidades personalizados](internet-analyzer-custom-endpoint.md) (um ponto de extremidade personalizado deve residir no Azure).

Os seguintes pontos de extremidade pré-configurados estão disponíveis durante a visualização:
* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * EUA Central
    * Ásia Oriental
    * EUA Leste
    * Oeste do Japão
    * Europa do Norte
    * Norte da África do Sul
    * Sudeste Asiático
    * Norte dos E.A.U.
    * Reino Unido Oeste  
    * Europa Ocidental
    * Oeste dos E.U.A.
    * EUA Oeste 2
* **Várias combinações de regiões do Azure**
    * Leste dos EUA, sul do Brasil
    * Leste dos EUA, Ásia Oriental
    * Europa Ocidental, sul do Brasil
    * Europa Ocidental, Sudeste Asiático
    * Europa Ocidental, Norte dos EAU
    * Oeste dos EUA, leste dos EUA
    * Oeste dos EUA, Europa Ocidental
    * Oeste dos EUA, Norte dos EAU
    * Europa Ocidental, Norte dos EAU, Sudeste Asiático
    * Oeste dos EUA, Europa Ocidental, Ásia Oriental
    * Oeste dos EUA, Europa Setentrional, Sudeste Asiático, Norte dos EAU, norte da África do Sul 
* **Azure + Azure front door** -implantado em qualquer uma ou várias combinações de regiões do Azure listadas acima
* **Azure + CDN do Azure da Microsoft** -implantado em qualquer combinação de região única do Azure listada acima
* **Azure + Azure Traffic Manager** -implantado em qualquer combinação de várias regiões do Azure listada acima

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Como o Internet Analyzer é diferente dos outros serviços de monitoramento fornecidos pelo Azure?

O Internet Analyzer ajuda você a entender o desempenho de seus usuários finais e ajuda a tomar decisões para melhorar seu desempenho. Enquanto outras ferramentas de monitoramento do Azure fornecem informações sobre seus serviços do Azure, o Internet Analyzer se concentra na medição do desempenho de ponta a ponta da Internet para seus usuários.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, Confira nossa [visão geral do Internet Analyzer](internet-analyzer-overview.md).
