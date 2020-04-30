---
title: Monitorização e alerta do Cofre-Chave Azure Microsoft Docs
description: Crie um dashboard para monitorizar a saúde do seu cofre chave e configurar alertas.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726946"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorização e alerta para cofre de chaves Azure

## <a name="overview"></a>Descrição geral

Uma vez que tenha começado a usar o cofre chave para armazenar os seus segredos de produção, é importante monitorizar a saúde do seu cofre chave para garantir que o seu serviço funcione como pretendido. À medida que começar a escalar o seu serviço, o número de pedidos enviados para o seu cofre de chaves aumentará. Isto tem um potencial para aumentar a latência dos seus pedidos e, em casos extremos, fazer com que os seus pedidos sejam estrangulados, o que terá impacto no desempenho do seu serviço. Também precisa de ser alertado se o seu cofre está a enviar um número incomum de códigos de erro, para que possa ser rapidamente notificado de qualquer política de acesso ou problemas de configuração de firewall. Este documento abordará os seguintes tópicos:

+ Métricas básicas do cofre de chaves para monitorizar
+ Como configurar métricas e criar um dashboard 
+ Como criar alertas em limiares especificados 

## <a name="basic-key-vault-metrics-to-monitor"></a>Métricas básicas do cofre de chaves para monitorizar

+ Disponibilidade do Cofre  
+ Saturação do cofre 
+ Latência de Serviço API 
+ Total de acessos a API de serviço (filtro por tipo de atividade) 
+ Códigos de Erro (Filter by Status Code) 

**Disponibilidade do Cofre** - Esta métrica deve estar sempre a 100% esta é uma métrica importante para monitorizar, uma vez que pode mostrar-lhe rapidamente se o seu cofre principal sofreu uma paragem. 

**Saturação do cofre** – O número de pedidos por segundo que um cofre chave pode servir baseia-se no tipo de operação que está a ser executada. Algumas operações no cofre têm um limiar de pedidos por segundo mais baixo. Esta métrica agrega o uso total do seu cofre chave em todos os tipos de operação para obter um valor percentual que indica o seu uso atual do cofre chave. Para obter uma lista completa dos limites de serviço do cofre chave, consulte o seguinte documento. [Limites do Serviço Azure Key Vault](service-limits.md)

**Latência** de Serviço API - Esta métrica mostra a latência média de uma chamada para o cofre chave. Embora o seu cofre chave possa estar dentro dos limites de serviço, uma alta utilização do cofre chave pode introduzir latência que faz com que as aplicações a jusante falhem. 

**Total API Hits** - Esta métrica mostra todas as chamadas feitas para o seu cofre chave. Isto vai ajudá-lo a identificar quais as aplicações que estão chamando o seu cofre chave. 

**Códigos de Erro** – Esta métrica irá mostrar-lhe se o seu cofre chave está a sofrer uma quantidade invulgar de erros. Para obter uma lista completa de códigos de erro e orientação para resolução de problemas, consulte o seguinte documento. [Códigos de erro do cofre de chaves azure REST API](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Como configurar métricas e criar um dashboard

1. Inicie sessão no portal do Azure
2. Navegue para o seu cofre de chaves
3. Selecione **Métricas** sob **Monitorização** 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-1.png)

4. Atualize o título da tabela para o que pretende ver no seu painel de instrumentos. 
5. Selecione o âmbito. Neste exemplo, escolheremos um único cofre chave. 
6. Selecione a Disponibilidade e **Agregação** Geral Métrica **seletiva** 
7. Atualize o intervalo de tempo para as últimas 24 horas e atualize a granularidade do tempo para 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-2.png)

8. Repita os passos acima para as métricas de Saturação do Cofre e Serviço API. Selecione **Pin to Dashboard** para guardar as suas métricas num dashboard. 

> [!IMPORTANT]
> Selecione "Pin to Dashboard" e guarde todas as métricas que configurar. Se deixar a página e voltar a fazê-lo sem poupar, as alterações de configuração perder-se-ão. 

9. Para monitorizar todos os tipos de operações no cofre chave, utilize a Métrica total de api **hits do serviço** e selecione **Selecione Separação por Tipo** de Atividade

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-3.png)

10. Para monitorizar os códigos de erro no cofre da chave, utilize a Métrica **de Resultados da API do Serviço Total** e selecione A Divisão por Tipo de **Atividade**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-4.png)

Agora terá um painel que se parece com este. Pode clicar nos 3 pontos na parte superior direita de cada azulejo e pode reorganizar e redimensionar os azulejos conforme necessário. 

Assim que guardar e publicar o dashboard, criará um novo recurso na sua subscrição Azure. Poderá vê-lo a qualquer momento, procurando por "painel partilhado". 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Como configurar alertas no seu Cofre-Chave 

Esta secção irá mostrar-lhe como configurar alertas no seu cofre de chaves para que possa alertar a sua equipa para que tome medidas imediatamente se o seu cofre estiver em estado de saúde. Pode configurar alertas que enviem um e-mail, de preferência a uma equipa DL, disparar uma notificação da grelha de eventos, ou ligar ou enviar um número de telefone. Também pode escolher alertas estáticos com base num valor fixo, ou um alerta dinâmico que o alertará se uma métrica monitorizada exceder o limite médio do seu cofre chave um determinado número de vezes dentro de um intervalo de tempo definido. 

> [!IMPORTANT]
> Por favor, note que pode demorar até 10 minutos para alertas recentemente configurados para começar a enviar notificações. 

### <a name="configure-an-action-group"></a>Configure um grupo de ação 

Um grupo de ação é uma lista configurável de notificações e propriedades.

1. Inicie sessão no portal do Azure
2. Pesquisar **alertas** na caixa de pesquisa
3. Selecione **Ações de Gestão**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-6.png)

4. Selecione **+ Adicionar Grupo de Ação**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-7.png)

5. Escolha o **Tipo de Ação** para o seu Grupo de Ação. Neste exemplo, criaremos um alerta de e-mail.

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-9.png)

6. Clique em **OK** na parte inferior da página. Criou com sucesso um grupo de ação. 

Agora que configuraram um grupo de ação, vamos configurar os limiares de alerta do cofre chave. 

### <a name="configure-alert-thresholds"></a>Configurar limiares de alerta 

1. Selecione o seu recurso chave vault no portal Azure e selecione **Alertas** sob **Monitorização**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-10.png)

2. Selecione **nova regra de alerta**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-11.png)

3. Selecione o âmbito da sua regra de alerta. Pode selecionar um único cofre ou um múltiplo. 

> [!IMPORTANT]
> Por favor, note que quando estiver a selecionar vários cofres para o âmbito dos seus alertas, todos os cofres selecionados devem estar na mesma região. Terá de configurar regras de alerta separadas para cofres em diferentes regiões. 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-12.png)

4. Selecione as condições para os seus alertas. Pode escolher qualquer um dos seguintes sinais e definir a sua lógica para alertar. A equipa key vault recomenda configurar os seguintes limiares de alerta. 

    + Disponibilidade do cofre chave cai abaixo de 100% (Limiar Estático)
    + A latência do cofre chave é superior a 500ms (Limiar Estático) 
    + A saturação total do cofre é superior a 75% (Limiar Estático) 
    + Saturação total do cofre excede a média (Limiar Dinâmico)
    + Códigos de erro totais superiores à média (Limiar Dinâmico) 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exemplo 1: Configurar um limiar de alerta estático para a latência

Selecione **ALência Geral do Serviço API** como o nome do sinal
> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-14.png)

Por favor, consulte os seguintes parâmetros de configuração.

+ Definir o limiar para **estática** 
+ Definir o operador para **maior do que**
+ Definir o tipo de agregação para **a média**
+ Definir o valor limiar para **500**
+ Definir período de agregação para **5 minutos**
+ Definir a Frequência de Avaliação para **1 minuto**
+ Selecione **Feito**  

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exemplo 2: Configurar um limiar de alerta dinâmico para a saturação do cofre 

Quando utilizar um alerta dinâmico, poderá ver dados históricos do cofre chave que selecionou. A área azul representa o uso médio do seu cofre chave. A área vermelha mostra picos que teriam desencadeado um alerta desde que outros critérios na configuração de alerta sejam cumpridos. Os pontos vermelhos mostram casos de violações em que os critérios para o alerta foram cumpridos durante a janela de tempo agregada. Pode estabelecer um alerta para o fogo depois de um certo número de violações dentro de um determinado tempo. Se não quiser incluir dados passados, existe uma opção para excluir dados antigos abaixo em configurações avançadas. 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-16.png)

Por favor, consulte os seguintes parâmetros de configuração.

+ Definir o Limiar para **Dinâmico** 
+ Definir o operador para **maior do que**
+ Definir o tipo de agregação para **a média**
+ Definir a sensibilidade do limiar para **médio**
+ Definir período de agregação para **5 minutos**
+ Definir a Frequência de Avaliação para **1 minuto**
+ **Opcional** Configurar definições avançadas 
+ Selecione **Feito**

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-17.png)

5. Adicione o grupo de ação que configurado

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-18.png)

6. Ativar o alerta e atribuir uma gravidade

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-19.png)

7. Criar o alerta 


## <a name="next-steps"></a>Passos seguintes

Parabéns, criou com sucesso um painel de monitorização e alertas configurados para o seu cofre chave! Depois de ter seguido todos os passos acima, deve receber alertas de e-mail quando o seu cofre de chaves cumprir os critérios de alerta configurados. Apresentamos um exemplo abaixo. Utilize as ferramentas que criou neste artigo para monitorizar ativamente a saúde do seu cofre chave. 

### <a name="example-email-alert"></a>Alerta de e-mail de exemplo 

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure](../media/alert-20.png)
