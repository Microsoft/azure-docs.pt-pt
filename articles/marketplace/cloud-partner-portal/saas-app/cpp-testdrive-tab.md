---
title: SaaS application oferta teste de unidade de configuração do Azure | Documentos da Microsoft
description: Configure a versão de teste para a oferta de aplicação SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: e97e79ef3077431d8fc627cd634a67b0a01b6f8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993100"
---
# <a name="saas-application-test-drive-tab"></a>Guia de Test-Drive do aplicativo de SaaS

Utilize o separador de Test-Drive para fornecer uma experiência de avaliação para os seus clientes.

## <a name="test-drive-benefits"></a>Benefícios da unidade de teste

A criação de uma experiência de avaliação para os seus clientes é uma prática recomendada, certifique-se de que podem comprar com confiança. As opções de avaliação disponíveis, Test-Drive é leva a mais uma forma eficaz de geração de alta qualidade e leva de conversão maior deles.

Versão de teste fornece aos clientes com uma avaliação autodidata prático dos principais recursos e benefícios, demonstrados num cenário de implementação em tempo real de seu produto.

## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um cliente potencial pesquisa e descobre a sua aplicação no Marketplace. O cliente inicia sessão e se concorda com os termos de utilização. Neste momento, o cliente recebe o seu ambiente previamente configurada para experimentar para um número fixo de horas, enquanto recebe uma oportunidade potencial altamente qualificada para dar seguimento com. Para obter mais informações, consulte [o que é o Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="publishing-steps"></a>Passos de publicação

Os passos de publicação principais para adicionar um teste de unidade são:

1. Definir o seu cenário de teste de unidade
2. Criar e/ou modificar o modelo do Resource Manager
3. Criar o manual passo a passo do teste de unidade
4. Voltar a publicar a oferta

## <a name="setting-up-a-test-drive"></a>Configuração de uma versão de teste

Existem quatro tipos diferentes de versões de teste disponíveis, cada um com base no tipo de produto e marketplace que estiver num cenário.

|  **Tipo**          |  **Descrição**  |  **Instruções de configuração**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Uma versão de teste de Gestor de recursos do Azure é um modelo de implementação que contém todos os recursos do Azure que compõem uma solução que está a ser criada pelo editor. Produtos de acordo com esse tipo de versão de teste são aqueles que utilizam apenas os recursos do Azure.               |       [Versão de Teste do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Alojado             |       Uma versão de teste alojado retira a complexidade da configuração pelo alojamento da Microsoft e manter o serviço que executa o utilizador de teste de unidade de aprovisionamento e desaprovisionamento.             |         [Versão de Teste Alojada](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Aplicação Lógica              |       Uma unidade de teste da aplicação lógica é um modelo de implementação que se destina a abranger todas as arquiteturas de solução complexas. Todas as aplicações do Dynamics ou produtos personalizados, devem utilizar este tipo de versão de teste.            |      [Unidade de teste da aplicação lógica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Uma unidade de teste do Power BI consiste num link incorporado a um dashboard personalizados internos. Qualquer produto que pretende demonstrar que uma interactive visual do Power BI deve utilizar este tipo de versão de teste. É tudo o que precisa para carregar o URL de BI de energia incorporados.          |        [Versão de teste de BI de energia](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>Versão de teste do Power BI

Utilize os seguintes passos para configurar uma versão de teste.

1. Na nova oferta, selecione **Test-Drive**.
2. Na versão de teste, selecione **Sim**.

   ![Ativar unidade de teste](./media/saas-enable-test-drive.png)

   Quando ativa a uma versão de teste, verá os detalhes e a configuração técnica forms, que são mostrados na captura de ecrã seguinte.

   ![Formulário de configuração de unidade de teste](./media/saas-test-drive-yes.png)

3. Sob **detalhes**, fornecer informações para os seguintes campos:
  
   - Descrição – descrevem sua versão de teste e o que os utilizadores podem fazer com ele. Pode utilizar tags de HTML básicos para formatar a descrição.
   - Manual do usuário – carregar um documento do Manual do usuário que seus clientes podem utilizar quando o que ele estão com a versão de teste. Este manual tem de ser um ficheiro pdf.
   - Teste de unidade demonstração vídeo (opcional) – pode fornecer um vídeo (YouTube ou Vimeo) para os seus clientes ver a antes da versão de teste. Forneça um URL para o vídeo.

4. Sob **técnico configuração**, fornecer informações para os seguintes campos:

   - Tipo de teste de unidade – selecione **Power BI** na lista pendente.
   - Ligar ao Dashboard do Power BI – forneça uma ligação para o dashboard.

5. Quando concluir a configuração a versão de teste, selecione **guardar**.


## <a name="next-steps"></a>Passos Seguintes

[Separador Detalhes da Loja](./cpp-storefront-tab.md)