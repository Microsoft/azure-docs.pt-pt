---
title: Azure Key Vault monitorização e alerta Microsoft Docs
description: Crie um painel de instrumentos para monitorizar a saúde do seu cofre e configurar alertas.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 9195bb59264731914740e1cca902707603e3502d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018125"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorização e alerta para Azure Key Vault

## <a name="overview"></a>Descrição geral

Uma vez que tenha começado a usar o cofre chave para armazenar os seus segredos de produção, é importante monitorizar a saúde do seu cofre chave para garantir que o seu serviço funciona como pretendido. À medida que começa a escalar o seu serviço, o número de pedidos enviados para o seu cofre-chave aumentará. Isto tem um potencial para aumentar a latência dos seus pedidos e, em casos extremos, fazer com que os seus pedidos sejam estrangulados, o que irá afetar o desempenho do seu serviço. Também precisa de ser alertado se o cofre da chave estiver a enviar um número incomum de códigos de erro, para que possa ser rapidamente notificado de qualquer política de acesso ou problemas de configuração de firewall. Este documento abordará os seguintes tópicos:

+ Métricas básicas do cofre de chaves para monitorizar
+ Como configurar métricas e criar um dashboard 
+ Como criar alertas em limiares especificados 

## <a name="basic-key-vault-metrics-to-monitor"></a>Métricas básicas do cofre de chaves para monitorizar

+ Disponibilidade de cofre  
+ Saturação de abóbada 
+ Latência da API de Serviço 
+ Acessos API de serviço total (filtro por tipo de atividade) 
+ Códigos de erro (Filtro por Código de Estado) 

**Disponibilidade do cofre** - Esta métrica deve estar sempre a 100% esta é uma métrica importante para monitorizar, uma vez que pode rapidamente mostrar-lhe se o seu cofre-chave sofreu uma paragem. 

**Saturação de abóbada** – O número de pedidos por segundo que um cofre-chave pode servir baseia-se no tipo de operação que está a ser realizada. Algumas operações de cofre têm um limiar de pedidos mais baixo por segundo. Esta métrica agrega o uso total do seu cofre chave em todos os tipos de operação para obter um valor percentual que indique o uso atual do cofre da chave. Para obter uma lista completa dos limites de serviço do cofre chave, consulte o seguinte documento. [Limites do Serviço Azure Key Vault](service-limits.md)

**API Latência de serviço** - Esta métrica mostra a latência média de uma chamada para o cofre chave. Embora o seu cofre-chave possa estar dentro dos limites de serviço, uma alta utilização do cofre chave pode introduzir latência que faz com que as aplicações a jusante falhem. 

**Total API Hits** - Esta métrica mostra todas as chamadas feitas para o seu cofre chave. Isto irá ajudá-lo a identificar quais aplicações estão a ligar para o seu cofre de chaves. 

**Códigos de erro** – Esta métrica irá mostrar-lhe se o cofre da chave está a sofrer uma quantidade invulgar de erros. Para obter uma lista completa de códigos de erro e orientação de resolução de problemas, consulte o seguinte documento. [Códigos de erro Azure Key Vault REST API](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Como configurar métricas e criar um dashboard

1. Inicie sessão no portal do Azure
2. Navegue até ao seu Cofre de Chaves
3. Selecione **métricas** em **monitorização** 

> [!div class="mx-imgBorder"]
> ![Screenshot que realça a opção Métricas na secção de Monitorização.](../media/alert-1.png)

4. Atualize o título da tabela para o que pretende ver no seu painel de instrumentos. 
5. Selecione o âmbito. Neste exemplo, selecionaremos um único cofre de chaves. 
6. Selecione a **disponibilidade geral de abóbada** métrica e agregação **Avg** 
7. Atualize o intervalo de tempo para as Últimas 24 Horas e atualize a granularidade temporal para 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a métrica de Availablility do Cofre Geral.](../media/alert-2.png)

8. Repita os passos acima para as métricas de Saturação de Abóbada e Serviço API. Selecione **Pin to Dashboard** para guardar as suas métricas num painel de instrumentos. 

> [!IMPORTANT]
> Selecione "Pin to Dashboard" e guarde todas as métricas que configurar. Se deixar a página e voltar sem poupar, perder-se-ão as alterações de configuração. 

9. Para monitorizar todos os tipos de operações no cofre da chave, utilize a Métrica total de **acessos de API de serviço** e selecione **Aplicar A Divisão por Tipo de Atividade**

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o botão De Divisão de Aplicação.](../media/alert-3.png)

10. Para monitorizar os códigos de erro no cofre da chave, utilize a Métrica **de Resultados da API de Serviço Total** e selecione Aplicar A Divisão por Tipo de **Atividade**

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a métrica de Resultados da API do Serviço Total selecionado.](../media/alert-4.png)

Agora vai ter um painel que se parece com isto. Pode clicar nos 3 pontos no topo direito de cada azulejo e pode reorganizar e redimensionar os azulejos conforme precisar. 

Assim que guardar e publicar o dashboard, criará um novo recurso na sua subscrição Azure. Poderá vê-lo a qualquer momento, procurando por "dashboard partilhado". 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o painel publicado.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Como configurar alertas no seu Cofre de Chaves 

Esta secção irá mostrar-lhe como configurar alertas no seu cofre chave para que possa alertar a sua equipa para agir imediatamente se o seu cofre-chave estiver num estado insalubre. Pode configurar alertas que enviem um e-mail, de preferência para uma equipa DL, despeça uma notificação da grelha de eventos ou ligue ou envie um número de telefone. Também pode escolher alertas estáticos com base num valor fixo, ou um alerta dinâmico que o alertará se uma métrica monitorizada exceder o limite médio do cofre de uma determinada altura dentro de um intervalo de tempo definido. 

> [!IMPORTANT]
> Por favor, note que pode levar até 10 minutos para os alertas recentemente configurados começarem a enviar notificações. 

### <a name="configure-an-action-group"></a>Configure um grupo de ação 

Um grupo de ação é uma lista configurável de notificações e propriedades.

1. Inicie sessão no portal do Azure
2. Pesquisa de **Alertas** na caixa de pesquisa
3. Selecione **Ações de Gestão**

> [!div class="mx-imgBorder"]
> ![Screenshot que realça o botão 'Gerir acções'.](../media/alert-6.png)

4. Selecione **+ Adicionar Grupo de Ação**

> [!div class="mx-imgBorder"]
> ![Screenshot que realça o botão + Add Action Group.](../media/alert-7.png)

5. Escolha o **Tipo de Ação** para o seu Grupo de Ação. Neste exemplo, criaremos um alerta de e-mail.

> [!div class="mx-imgBorder"]
> ![Screenshot que destaca os campos necessários para adicionar um grupo de ação.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o que é necessário para adicionar um e-mail ou alerta de mensagem SMS.](../media/alert-9.png)

6. Clique em **OK** na parte inferior da página. Criou com sucesso um grupo de ação. 

Agora que configuraste um grupo de ação, vamos configurar os limiares de alerta do cofre. 

### <a name="configure-alert-thresholds"></a>Limites de alerta de configuração 

1. Selecione o seu recurso de cofre chave no portal Azure e **selecione Alertas** em **Monitorização**

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a opção menu Alertas na secção De Monitorização.](../media/alert-10.png)

2. Selecione **nova regra de alerta**

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o botão + Regra de Alerta Novo.](../media/alert-11.png)

3. Selecione o âmbito da sua regra de alerta. Pode selecionar um único cofre ou vários. 

> [!IMPORTANT]
> Por favor, note que quando estiver a selecionar vários cofres para o âmbito dos seus alertas, todos os cofres selecionados devem estar na mesma região. Terá de configurar regras de alerta separadas para cofres em diferentes regiões. 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra como pode selecionar um cofre.](../media/alert-12.png)

4. Selecione as condições para os seus alertas. Pode escolher qualquer um dos seguintes sinais e definir a sua lógica de alerta. A equipa key Vault recomenda configurar os seguintes limiares de alerta. 

    + Disponibilidade do cofre de chaves cai abaixo de 100% (Limiar Estático)
    + A latência do cofre chave é superior a 500ms (Limiar Estático) 
    + A saturação geral do cofre é superior a 75% (Limiar Estático) 
    + A saturação geral do cofre excede a média (Limiar Dinâmico)
    + Códigos de erro totais superiores à média (Limiar Dinâmico) 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra onde seleciona condições para alertas.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exemplo 1: Configurar um limiar de alerta estático para a latência

Selecione **a latência geral da API** como o nome de sinal
> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o nome de sinal de latência da API de serviço geral.](../media/alert-14.png)

Consulte os seguintes parâmetros de configuração.

+ Definir o Limiar para **Estática** 
+ Coloque o operador em **maior do que**
+ Definir o Tipo de Agregação para **Média**
+ Definir o Valor limiar para **500**
+ Definir período de agregação para **5 minutos**
+ Definir a Frequência de Avaliação para **1 minuto**
+ Selecione **Feito**  

> [!div class="mx-imgBorder"]
> ![Screenshot que realça a lógica de alerta configurada.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exemplo 2: Configurar um limiar de alerta dinâmico para a saturação do cofre 

Quando utilizar um alerta dinâmico, poderá ver dados históricos do cofre-chave que selecionou. A área azul representa o uso médio do seu cofre chave. A área vermelha mostra picos que teriam desencadeado um alerta desde que outros critérios na configuração de alerta sejam cumpridos. Os pontos vermelhos mostram casos de violações em que os critérios para o alerta foram cumpridos durante a janela de tempo agregada. Pode alertar para o fogo depois de um certo número de violações dentro de um tempo definido. Se não pretender incluir dados passados, existe uma opção para excluir dados antigos abaixo em configurações avançadas. 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra um gráfico da saturação geral do cofre.](../media/alert-16.png)

Consulte os seguintes parâmetros de configuração.

+ Definir o Limiar para **Dinâmico** 
+ Coloque o operador em **maior do que**
+ Definir o Tipo de Agregação para **Média**
+ Definir a sensibilidade limiar para **médio**
+ Definir período de agregação para **5 minutos**
+ Definir a Frequência de Avaliação para **1 minuto**
+ **Opcional** Configurações avançadas de configuração 
+ Selecione **Feito**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-17.png)

5. Adicione o grupo de ação que configuraste

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra como adicionar um grupo de ação.](../media/alert-18.png)

6. Ativar o alerta e atribuir uma gravidade

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra onde ativar o alerta e atribuir uma gravidade.](../media/alert-19.png)

7. Criar o alerta 


## <a name="next-steps"></a>Passos seguintes

Parabéns, criou agora com sucesso um painel de monitorização e alertas configurados para o seu cofre chave! Depois de ter seguido todos os passos acima, deverá receber alertas de e-mail quando o cofre-chave satisfaça os critérios de alerta configurados. Apresentamos um exemplo abaixo. Utilize as ferramentas que criou neste artigo para monitorizar ativamente a saúde do seu cofre de chaves. 

### <a name="example-email-alert"></a>Exemplo alerta de e-mail 

> [!div class="mx-imgBorder"]
> ![Screenshot que destaca as informações necessárias para configurar um alerta de e-mail.](../media/alert-20.png)
