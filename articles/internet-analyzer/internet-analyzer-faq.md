---
title: Análise de Internet FAQ / Microsoft Docs
description: O FAQ para Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74184260"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer FAQ (Pré-visualização)

Esta é a FAQ para Azure Internet Analyzer, se tiver perguntas adicionais, vá ao [fórum de feedback](https://aka.ms/internetAnalyzerFeedbackForum) e publique a sua pergunta. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrada de forma rápida e fácil.

## <a name="how-do-i-participate-in-the-preview"></a>Como participo na pré-estreia?

A pré-visualização está disponível para selecionar clientes. Se estiver interessado em aderir à pré-visualização, por favor, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com).
2. Navegue para a página **de Subscrições.**
3. Clique na subscrição Azure com a que planeia utilizar o Internet Analyzer.
4. Aceda às **definições dos fornecedores de recursos** para a subscrição.
5. Procure no **Microsoft.Network** e clique no botão **Registar** (ou **re-registar)**.
![pedido de acesso](./media/ia-faq/request-preview-access.png)

6. [Solicite a aprovação](https://aka.ms/internetAnalyzerContact) fornecendo-nos o seu endereço de e-mail e o ID de subscrição Azure que foi usado para fazer o pedido de acesso.
7. Uma vez aprovado o seu pedido, receberá uma confirmação de e-mail e poderá criar/atualizar/modificar os recursos do Analisador de Internet a partir da subscrição Azure recentemente permitida.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Preciso incorporar o cliente para fazer um teste?

Sim, o cliente Analisador de Internet deve ser instalado na sua aplicação para recolher métricas específicas dos seus utilizadores. [Saiba como instalar o cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Sou cobrado por participar na pré-estreia?
Não, o Azure Internet Analyzer é gratuito para usar na pré-visualização. Não existe nenhum acordo de nível de serviço durante a pré-visualização.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Que cenários é que o Internet Analyzer é projetado para abordar?

O Internet Analyzer foi concebido para lhe dar informações de desempenho em rede com base na população do utilizador. Para ajudar a tomar as melhores decisões de desempenho para os seus utilizadores, o Internet Analyzer compara o desempenho de dois pontos finais da Internet usando a sua população de utilizadores distinta. Enquanto o Analisador de Internet pode responder a uma infinidade de perguntas, algumas das mais comuns são:

* Qual é o impacto de desempenho da migração para a nuvem? 
    * *Teste sugerido: Custom (a sua infraestrutura atual no local) vs. Azure (qualquer ponto final pré-configurado)*
* Qual é o valor de colocar os meus dados no limite vs. num centro de dados? 
    *  *Teste sugerido: Azure vs. Azure Front Door, Azure vs. Azure CDN da Microsoft*
* Qual é o benefício de desempenho de Azure Front Door?
    *  *Teste sugerido: Custom/ Azure/ CDN vs. Azure Front Door*
* Qual é o benefício de desempenho da Azure CDN da Microsoft? 
    *  *Teste sugerido: Custom/ Azure/ AFD vs. Azure CDN da Microsoft*
* Como é que o Azure CDN da Microsoft se empilha? 
    *  *Teste sugerido: Personalizado (outro ponto final CDN) vs. Azure CDN da Microsoft*
* Qual é a melhor nuvem para a sua população de utilizadores finais em cada região? 
    *  *Teste sugerido: Personalizado (outro serviço em nuvem) vs. Azure (qualquer ponto final pré-configurado)*

## <a name="which-tests-can-i-run-in-preview"></a>Que testes posso fazer na pré-estreia?

Cada teste que cria no Internet Analyzer é composto por dois pontos finais — Endpoint A e Endpoint B. Qualquer uma das seguintes combinações pode ser executada como testes:  
* Dois pontos finais pré-configurados,
* Um costume e um ponto final pré-configurado, ou
* Dois [pontos finais personalizados](internet-analyzer-custom-endpoint.md) (um ponto final personalizado deve residir em Azure).

Os seguintes pontos finais pré-configurados estão disponíveis durante a pré-visualização:
* **Regiões do Azure**
    * Sul do Brasil
    * Índia Central
    * E.U.A. Central
    * Ásia Leste
    * E.U.A. Leste
    * Oeste do Japão
    * Europa do Norte
    * África do Sul Norte
    * Sudeste Asiático
    * Uae Norte
    * Oeste do Reino Unido  
    * Europa Ocidental
    * E.U.A. Oeste
    * E.U.A. Oeste 2
* **Múltiplas combinações da região de Azure**
    * East US, Brasil Sul
    * Leste dos EUA, Ásia Oriental
    * West Europe
    * Europa Ocidental, Sudeste Asiático
    * West Europe, UAE North
    * Eua Ocidentais, Leste dos EUA
    * Eua Ocidentais, Europa Ocidental
    * West US, UAE North
    * Europa Ocidental, Emirados Árabes Unidos Norte, Sudeste Asiático
    * Eua Ocidentais, Europa Ocidental, Ásia Oriental
    * Eua Ocidentais, Norte da Europa, Sudeste Asiático, Emirados Árabes Unidos Norte, África do Sul Norte 
* **Azure + Azure Front Door** - implantado em qualquer combinação única ou múltipla da região de Azure listada acima
* **Azure + Azure CDN da Microsoft** - implantado em qualquer combinação da região de Azure listada acima
* **Azure + Azure Traffic Manager** - implantado em qualquer combinação múltipla da região de Azure listada acima

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Como é que o Analisador de Internet é diferente dos outros serviços de monitorização prestados pela Azure?

O Internet Analyzer ajuda-o a compreender o desempenho dos seus utilizadores finais e ajuda a tomar decisões para melhorar o seu desempenho. Enquanto outras ferramentas de monitorização do Azure fornecem informações sobre os seus serviços Azure, o Internet Analyzer foca-se em medir o desempenho da Internet de ponta a ponta para os seus utilizadores.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Como é tratados os dados de medição pelo Analisador de Internet?

O Azure tem [processos de segurança fortes e cumpre uma ampla gama de normas de conformidade.](https://azure.microsoft.com/support/trust-center/) Só você e a sua equipa designada têm acesso aos seus dados. O pessoal da Microsoft só pode ter acesso restrito a ele em circunstâncias limitadas específicas com o seu conhecimento. Está encriptado em trânsito e em repouso.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a nossa [Visão Geral do Analisador de Internet.](internet-analyzer-overview.md)
