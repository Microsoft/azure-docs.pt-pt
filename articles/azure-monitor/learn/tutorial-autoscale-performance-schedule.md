---
title: Dimensionamento automático dos recursos do Azure com base nos dados de desempenho ou numa agenda
description: Criar uma definição de dimensionamento automático para um plano do serviço de aplicações através de dados métricos e de uma agenda
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: 44fecf47ccd1ce07c7e51f7bcf51ef7823f2cf97
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088354"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Criar uma Definição de Dimensionamento Automático para recursos do Azure com base nos dados de desempenho ou numa agenda

As definições de dimensionamento automático permitem adicionar/remover instâncias de serviço com base nas condições predefinidas. Estas definições podem ser criadas através do portal. Este método proporciona uma interface de utilizador baseada no browser para criar e configurar uma definição de dimensionamento automático. 

Neste tutorial, irá: 
> [!div class="checklist"]
> * Criar uma Aplicação Web e um Plano do Serviço de Aplicações
> * Configurar regras de dimensionamento automático para aumentar e reduzir horizontalmente com base no número de pedidos recebidos por uma Aplicação Web
> * Acionar uma ação de aumento horizontal e ver o número de instâncias aumentar
> * Acionar uma ação de redução horizontal e ver o número de instâncias diminuir
> * Apague os seus recursos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Criar uma Aplicação Web e um Plano do Serviço de Aplicações
1. Clique na opção **Criar um recurso** no painel de navegação esquerdo.
2. Procure e selecione o item *Aplicação Web* e clique em **Criar**.
3. Selecione um nome de aplicação, como *MyTestScaleWebApp*. Criar um novo grupo de recursos * myResourceGroup' ou coloque-o no grupo de recursos à sua escolha.

Dentro de alguns minutos, os recursos deverão ser aprovisionados. Utilize a Aplicação Web e o Plano do Serviço de Aplicações correspondente no resto deste tutorial.

   ![Criar um novo serviço de aplicações no portal](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navegar para as definições de Dimensionamento Automático
1. No painel de navegação esquerdo, selecione a opção **Monitorizar**. Depois de a página ser carregada, selecione o separador **Dimensionamento Automático**.
2. É apresentada uma lista dos recursos na sua subscrição que suportam o dimensionamento automático. Identifique o Plano do Serviço de Aplicações criado anteriormente no tutorial e clique no mesmo.

    ![Navegar para as definições de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Na definição de dimensionamento automático, clique no botão **Ativar Dimensionamento Automático**.

Os passos seguintes ajudam-no a preencher o ecrã de dimensionamento automático para ter o aspeto da imagem que se segue:

   ![Guardar a definição de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Configurar o perfil predefinido
1. Indique um **Nome** para a definição de dimensionamento automático.
2. No perfil predefinido, certifique-se de que a opção **Modo de dimensionamento** está definida como "Dimensionar para uma contagem de instâncias específica".
3. Defina a contagem de instâncias como **1**. Esta definição garante que, quando não estiver ativo ou em vigor nenhum outro perfil, o perfil predefinido devolve a contagem de instâncias como 1.

   ![Navegar para as definições de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Criar perfil de periodicidade

1. Clique na ligação **Adicionar uma condição de dimensionamento** no perfil predefinido.

2. Edite o **Nome** deste perfil para "Perfil de segunda a sexta".

3. Certifique-se de que a opção **Modo de dimensionamento** está definida como "Dimensionamento com base na métrica".

4. Para **Limites de instância**, defina **Mínimo** como "1", **Máximo** como "2" e **Predefinição** como "1". Esta definição assegura que este perfil não dimensiona automaticamente o plano do serviço para ter menos de uma instância ou mais de duas. Se o perfil não tiver dados suficientes para tomar uma decisão, utiliza o número predefinido de instâncias (neste caso, 1).

5. Para **Agendar**, selecione "Repetir dias específicos".

6. Defina o perfil para repetir de segunda a sexta, das 09:00 PST às 18:00 PST. Esta definição assegura que este perfil só está ativo e é aplicável das 09: 00 às 18:00, de segunda a sexta. Durante as restantes horas, o perfil "Predefinido" é o perfil utilizado pela definição de dimensionamento automático.

## <a name="create-a-scale-out-rule"></a>Criar uma regra de aumento horizontal

1. No "Perfil de segunda a sexta".

2. Clique na ligação **Adicionar uma regra**.

3. Defina **Origem da métrica** como "outros recursos". Defina **Tipo de recurso** como "Serviços de Aplicações" e **Recurso** como a Aplicação Web criada anteriormente neste tutorial.

4. Defina **Agregação de tempo** como "Total", **Nome da métrica** como "Pedidos" e **Estatística de intervalo de agregação** como "Soma".

5. Defina **Operador** como "Maior que", **Limiar** como "10" e **Duração** como "5 minutos".

6. Selecione **Operação** como "Aumentar contagem em", **Contagem de instâncias** como "1" e **Repouso** como "5 minutos".

7. Clique no botão **Adicionar**.

Esta regra assegura que, se a Aplicação Web receber mais de dez pedidos em cinco minutos ou menos, é adiciona mais uma instância ao Plano do Serviço de Aplicações, para gerir a carga.

   ![Criar uma regra de aumento horizontal](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Criar uma regra de redução horizontal
Recomendamos que tenha sempre uma regra de redução horizontal a acompanhar uma regra de aumento horizontal. Ter ambas assegura que os recursos não são sobreaprovisionados. O sobreaprovisionamento significa que tem mais instâncias em execução do que as necessárias para processar a carga atual. 

1. No "Perfil de segunda a sexta".

2. Clique na ligação **Adicionar uma regra**.

3. Defina **Origem da métrica** como "outros recursos". Defina **Tipo de recurso** como "Serviços de Aplicações" e **Recurso** como a Aplicação Web criada anteriormente neste tutorial.

4. Defina **Agregação de tempo** como "Total", **Nome da métrica** como "Pedidos" e **Estatística de intervalo de agregação** como "Média".

5. Defina **Operador** como "Menor que", **Limiar** como "5" e **Duração** como "5 minutos".

6. Selecione **Operação** como "Diminuir contagem em", **Contagem de instâncias** como "1" e **Repouso** como "5 minutos".

7. Clique no botão **Adicionar**.

    ![Criar uma regra de redução horizontal](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Guardar** a definição de dimensionamento automático.

    ![Guardar a definição de dimensionamento automático](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Acionar uma ação de aumento horizontal
Para acionar a condição de aumento horizontal na definição de dimensionamento automático que acabou de criar, a Aplicação Web tem de receber mais de dez pedidos em menos de cinco minutos.

1. Abra uma janela do browser e navegue para a Aplicação Web que criou anteriormente neste tutorial. Pode encontrar o URL para a Aplicação Web no Portal do Azure ao navegar para o recurso da Aplicação Web e ao clicar no botão **Procurar** no separador "Descrição Geral".

2. Numa sucessão rápida, recarregue a página mais de 10 vezes.

3. No painel de navegação esquerdo, selecione a opção **Monitorizar**. Depois de a página ser carregada, selecione o separador **Dimensionamento Automático**.

4. Na lista, selecione o Plano do Serviço de Aplicações utilizado neste tutorial.

5. Na definição de dimensionamento automático, clique no separador **Histórico de execuções**.

6. Verá um gráfico que reflete a contagem de instâncias do Plano do Serviço de Aplicações ao longo do tempo.

7. Dentro de alguns minutos, a contagem de instâncias deve aumentar de 1 para 2.

8. No gráfico, pode ver as entradas do registo de atividade para cada ação de dimensionamento executada por esta definição de dimensionamento automático.

## <a name="trigger-scale-in-action"></a>Acionar uma ação de redução horizontal
A condição de redução horizontal na definição de dimensionamento automático é acionada se existirem menos de cinco pedidos para a Aplicação Web durante um período de dez minutos. 

1. Certifique-se de que não estão a ser enviados pedidos para a Aplicação Web.

2. Carregar o Portal do Azure.

3. No painel de navegação esquerdo, selecione a opção **Monitorizar**. Depois de a página ser carregada, selecione o separador **Dimensionamento Automático**.

4. Na lista, selecione o Plano do Serviço de Aplicações utilizado neste tutorial.

5. Na definição de dimensionamento automático, clique no separador **Histórico de execuções**.

6. Verá um gráfico que reflete a contagem de instâncias do Plano do Serviço de Aplicações ao longo do tempo.

7. Dentro de alguns minutos, a contagem de instâncias deve diminuir de 2 para 1. O processo demora pelo menos cem minutos.  

8. No gráfico, pode ver o conjunto correspondente de entradas do registo de atividade para cada ação de dimensionamento executada por esta definição de dimensionamento automático.

    ![Ver ações de redução horizontal](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Limpar recursos

1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione a Aplicação Web criada neste tutorial.

2. Na página de recursos, clique em **Eliminar**, confirme a eliminação ao escrever **sim** na caixa de texto e, em seguida, clique em **Eliminar**.

3. Em seguida, selecione o recurso do Plano do Serviço de Aplicações e clique em **Eliminar**.

4. Confirme a eliminação ao escrever **sim** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:  
> [!div class="checklist"]
> * Criou uma Aplicação Web e um Plano do Serviço de Aplicações
> * Configurou regras de dimensionamento automático para aumentar e reduzir horizontalmente com base no número de pedidos recebidos pela Aplicação Web
> * Acionou uma ação de aumento horizontal e viu o número de instâncias aumentar
> * Acionou uma ação de redução horizontal e viu o número de instâncias diminuir
> * Limpou os recursos


Para saber mais sobre definições de dimensionamento automático, avance para a [descrição geral de dimensionamento automático](../../azure-monitor/platform/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Arquivamento dos dados de monitorização](tutorial-archive-data.md)

