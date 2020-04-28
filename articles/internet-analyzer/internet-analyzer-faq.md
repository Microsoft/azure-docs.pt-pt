---
title: Análise de Internet FAQ [ Microsoft Docs
description: As FAQ para O Analisador de Internet Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74184260"
---
# <a name="azure-internet-analyzer-faq-preview"></a>FaQ do Analisador de Internet Azure (Pré-visualização)

Este é o FAQ para O Analisador de Internet Azure, se tiver perguntas adicionais, vá ao fórum de [feedback](https://aka.ms/internetAnalyzerFeedbackForum) e publique a sua pergunta. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrado de forma rápida e fácil.

## <a name="how-do-i-participate-in-the-preview"></a>Como posso participar na pré-estreia?

A pré-visualização está disponível para selecionar clientes. Se estiver interessado em aderir à pré-visualização, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com).
2. Navegue para a página **de Assinaturas.**
3. Clique na subscrição do Azure com a qual planeia utilizar o Internet Analyzer.
4. Vá às definições dos **fornecedores** de Recursos para a subscrição.
5. Procure **microsoft.Network** e clique no botão **Registar** (ou **voltar a registar)**.
![pedido de acesso](./media/ia-faq/request-preview-access.png)

6. [Solicite a aprovação](https://aka.ms/internetAnalyzerContact) fornecendo-nos o seu endereço de e-mail e o ID de subscrição azure que foi usado para fazer o pedido de acesso.
7. Uma vez aprovado o seu pedido, receberá uma confirmação por e-mail e poderá criar/atualizar/modificar os recursos do Analisador de Internet a partir da recente subscrição do Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Preciso incorporar o cliente para fazer um teste?

Sim, o cliente do Analisador de Internet deve ser instalado na sua aplicação para recolher métricas específicas aos seus utilizadores. [Aprenda a instalar o cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Sou cobrado por participar na pré-estreia?
Não, o Azure Internet Analyzer é gratuito para usar na pré-visualização. Não existe um acordo de nível de serviço durante a pré-visualização.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Que cenários é projetado para abordar o Internet Analyzer?

O Internet Analyzer foi concebido para lhe fornecer insights de desempenho em rede com base na população do utilizador. Para ajudar a tomar as melhores decisões de desempenho para os seus utilizadores, o Internet Analyzer compara o desempenho de dois pontos finais da Internet utilizando a sua população de utilizadores distinta. Enquanto o Analisador de Internet pode responder a uma infinidade de perguntas, algumas das mais comuns são:

* Qual é o impacto do desempenho da migração para a nuvem? 
    * *Teste sugerido: Personalizado (a sua atual infraestrutura no local) vs. Azure (qualquer ponto final pré-reconfigurado)*
* Qual é o valor de colocar os meus dados no limite vs. num centro de dados? 
    *  *Teste sugerido: Azure vs. Azure Front Door, Azure vs. Azure CDN da Microsoft*
* Qual é o benefício de desempenho da Porta da Frente Azure?
    *  *Teste sugerido: Personalizado/ Azure/ CDN vs. Porta da Frente Azure*
* Qual é o benefício de desempenho do Azure CDN da Microsoft? 
    *  *Teste sugerido: Custom/ Azure/ AFD vs. Azure CDN da Microsoft*
* Como é que o Azure CDN da Microsoft se acumula? 
    *  *Teste sugerido: Personalizado (outro ponto final da CDN) vs. Azure CDN da Microsoft*
* Qual é a melhor nuvem para a sua população de utilizadores finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço na nuvem) vs. Azure (qualquer ponto final pré-reconfigurado)*

## <a name="which-tests-can-i-run-in-preview"></a>Que testes posso fazer na pré-estreia?

Cada teste que cria no Analisador de Internet é composto por dois pontos finais — Endpoint A e Endpoint B. Qualquer uma das seguintes combinações pode ser executada como teste:  
* Dois pontos finais reconfigurados,
* Um costume e um ponto final pré-reconfigurado, ou
* Dois [pontos finais personalizados](internet-analyzer-custom-endpoint.md) (um ponto final personalizado deve residir em Azure).

Os seguintes pontos finais pré-reconfigurados estão disponíveis durante a pré-visualização:
* **Regiões de Azure**
    * Sul do Brasil
    * Índia Central
    * E.U.A. Central
    * Ásia Leste
    * E.U.A. Leste
    * Oeste do Japão
    * Europa do Norte
    * África do Sul Norte
    * Ásia Sudeste
    * Emirados Unidos norte
    * Oeste do Reino Unido  
    * Europa ocidental
    * E.U.A. Oeste
    * E.U.A.Oeste 2
* **Múltiplas combinações da região de Azure**
    * Leste dos EUA, Brasil Sul
    * Leste dos EUA, Ásia Oriental
    * Europa Ocidental, Brasil Sul
    * Europa Ocidental, Sudeste Asiático
    * Europa Ocidental, Emirados Unidos
    * Oeste dos EUA, Leste dos EUA
    * Oeste dos EUA, Europa Ocidental
    * Oeste dos EUA, Emirados Unidos
    * Europa Ocidental, Emirados Unidos norte, Sudeste Asiático
    * Oeste dos EUA, Europa Ocidental, Ásia Oriental
    * Oeste DOS EUA, Norte da Europa, Sudeste Asiático, Emirados Unidos norte, África do Sul Norte 
* **Porta frontal Azure + Azure** - implantada em qualquer combinação única ou múltipla da região de Azure listada acima
* **Azure + Azure CDN da Microsoft** - implantado em qualquer combinação única da região azure listada acima
* **Azure + Azure Traffic Manager** - implantado em qualquer combinação de várias regiões de Azure listadas acima

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Como é que o Internet Analyzer é diferente dos outros serviços de monitorização fornecidos pelo Azure?

O Internet Analyzer ajuda-o a compreender o desempenho dos seus utilizadores finais e ajuda a tomar decisões para melhorar o seu desempenho. Enquanto outras ferramentas de monitorização do Azure fornecem informações sobre os seus serviços Azure, o Internet Analyzer foca-se em medir o desempenho da Internet de ponta a ponta para os seus utilizadores.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Como é que os dados de medição são tratados pelo Analisador de Internet?

O Azure tem processos de [segurança fortes e cumpre um vasto leque de normas de conformidade.](https://azure.microsoft.com/support/trust-center/) Só você e a sua equipa designada têm acesso aos seus dados. Os colaboradores da Microsoft só podem ter acesso restrito a ele em circunstâncias limitadas específicas com o seu conhecimento. Está encriptado em trânsito e em repouso.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a nossa visão geral do Analisador de [Internet.](internet-analyzer-overview.md)
