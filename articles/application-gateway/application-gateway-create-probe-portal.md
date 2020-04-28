---
title: Criar uma sonda personalizada usando o portal
titleSuffix: Azure Application Gateway
description: Aprenda a criar uma sonda personalizada para Application Gateway usando o portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74074613"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Crie uma sonda personalizada para Application Gateway usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma sonda de saúde personalizada a uma porta de aplicação existente através do portal Azure. Utilizando as sondas de saúde, o Azure Application Gateway monitoriza a saúde dos recursos na piscina de fundo.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não tem um portal de aplicação, visite [Criar um Gateway de Aplicação](application-gateway-create-gateway-portal.md) para criar uma porta de entrada de aplicação para trabalhar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Criar sonda para Application Gateway v2 SKU

As sondas estão configuradas num processo em duas etapas através do portal. O primeiro passo é introduzir os valores necessários para a configuração da sonda. No segundo passo, testa a saúde do backend utilizando esta configuração da sonda e guarde a sonda. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Insira propriedades da sonda

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tem uma conta, pode [inscrever-se](https://azure.microsoft.com/free) para um julgamento gratuito de um mês.

2. No painel Favoritos do portal do Azure, clique em Todos os recursos. Clique na porta de entrada de aplicação na lâmina de todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir partners.contoso.net na caixa Filtrar por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **sondas de saúde** e, em seguida, selecione **Adicionar** para adicionar uma nova sonda de saúde.

   ![Adicione nova sonda][4]

4. Na página **adicionar sonda de saúde,** preencha as informações necessárias para a sonda e, quando estiver completa, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que é acessível no portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a sonda de saúde usa. |
   |**Anfitrião**|i.e contoso.com|Este valor é o nome do anfitrião virtual (diferente do nome de anfitrião VM) que está a funcionar no servidor de aplicações. A sonda é enviada para (protocolo)://(nome anfitrião):(porta de httpsetting)/urlPath.  Isto é aplicável quando vários sites estão configurados no Gateway da Aplicação. Se o Gateway de Aplicação estiver configurado para um único site, introduza '127.0.0.1'.|
   |**Escolha o nome do anfitrião das definições de HTTP de backend**|Sim ou Não|Define o cabeçalho do *hospedeiro* na sonda para o nome anfitrião do recurso traseiro na piscina traseira associada à Definição HTTP a que esta sonda está associada. Especialmente necessário em caso de apoios multi-inquilinos, como o serviço de aplicações Azure. [Mais informações](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ ou outro caminho|O resto da url completa para a sonda personalizada. Um caminho válido começa com '/'. Para o caminho padrão\/de http: /contoso.com basta usar '/' |
   |**Intervalo (segs)**|30|Quantas vezes a sonda é executada para verificar se há saúde. Não é aconselhável definir os inferiores a 30 segundos.|
   |**Timeout (secs)**|30|O tempo que a sonda espera antes de sair. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda está marcada como falhada. O intervalo de tempo tem de ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de saúde de backend esteja disponível. Note que o valor de tempo-out não deve ser superior ao valor 'Intervalo' utilizado nesta configuração da sonda ou ao valor "Tempo de Pedido" na definição HTTP que estará associado a esta sonda.|
|**Limiar com funcionamento incorreto**|3|Número de tentativas falhadas consecutivas para ser considerada insalubre. O limiar pode ser fixado para 1 ou mais.|
   |**Use condições de correspondência da sonda**|Sim ou Não|Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. Pode alterar o intervalo aceitável de código de resposta de backend ou corpo de resposta ao backend. [Mais informações](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Definições de HTTP**|seleção de dropdown|A sonda será associada com a definição (s) http selecionada aqui e, portanto, monitorizará a saúde dessa piscina de backend que está associada à definição http selecionada. Utilizará a mesma porta para o pedido de sonda que a utilizada na definição HTTP selecionada. Só é possível escolher as definições HTTP que não estão associadas a qualquer outra sonda personalizada. <br>Note que apenas as definições HTTP estão disponíveis para associação que têm o mesmo protocolo que o protocolo escolhido nesta configuração da sonda e têm o mesmo estado para o nome de *anfitrião Pick From Backend HTTP set switch.*|
   
   > [!IMPORTANT]
   > A sonda só monitorizará a saúde do backend quando estiver associada a uma ou mais definições http. Monitorizará os recursos de back-end das piscinas de back-end que estão associadas à definição(s) http a que esta sonda está associada. O pedido da sonda será enviado para http://(nome anfitrião):(porta de httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Teste de backend saúde com a sonda

Depois de entrar nas propriedades da sonda, pode testar a saúde dos recursos traseiros para verificar se a configuração da sonda está correta e que os recursos de back-end estão funcionando como esperado.

1. Selecione **Teste** e note o resultado da sonda. O gateway application testa a saúde de todos os recursos de backend nas piscinas de backend associadas à definição http(s) utilizada para esta sonda. 

   ![Teste de saúde de backend][5]

2. Se houver algum recurso de backend pouco saudável, então verifique a coluna **Details** para entender a razão para o estado pouco saudável do recurso. Se o recurso tiver sido marcado sem problemas devido a uma configuração de sonda incorreta, então selecione o **volte a sondar** e edite a configuração da sonda. Caso contrário, se o recurso tiver sido marcado insalubre devido a um problema com o backend, então resolva os problemas com o recurso de backend e, em seguida, teste o backend novamente selecionando o **link De volta para a sonda** e selecione **Test**.

   > [!NOTE]
   > Pode optar por salvar a sonda mesmo com recursos traseiros pouco saudáveis, mas não é recomendado. Isto porque o Gateway de Aplicação remove os recursos de backend da piscina de backend que são determinados como insalubres pela sonda. Caso não existam recursos saudáveis numa piscina de backend, não poderá aceder à sua aplicação e terá um erro de 502.

   ![Ver resultado da sonda][6]

3. Selecione **Adicionar** para salvar a sonda. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Criar sonda para Application Gateway v1 SKU

As sondas estão configuradas num processo em duas etapas através do portal. O primeiro passo é criar a sonda. No segundo passo, adicione a sonda às definições de http do gateway da aplicação.

### <a name="create-the-probe"></a><a name="createprobe"></a>Criar a sonda

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tem uma conta, pode [inscrever-se](https://azure.microsoft.com/free) para um julgamento gratuito de um mês.

2. No painel Favoritos do portal do Azure, selecione **Todos os recursos**. Selecione o gateway da aplicação na página **Todos os recursos.** Se a subscrição que selecionou já tiver vários recursos, pode introduzir partners.contoso.net na caixa Filtrar por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **Sondas** e, em seguida, **selecione Adicionar** para adicionar uma sonda.

   ![Adicione a lâmina da sonda com informações preenchidas][1]

4. Na lâmina **da sonda de saúde Adicionar,** preencha as informações necessárias para a sonda e, quando estiver completa, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que é acessível no portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a sonda de saúde usa. |
   |**Anfitrião**|i.e contoso.com|Este valor é o nome do anfitrião virtual (diferente do nome de anfitrião VM) que está a funcionar no servidor de aplicações. A sonda é enviada para (protocolo)://(nome anfitrião):(porta de httpsetting)/urlPath.  Isto é aplicável quando vários sites estão configurados no Gateway da Aplicação. Se o Gateway de Aplicação estiver configurado para um único site, introduza '127.0.0.1'.|
   |**Escolha o nome do anfitrião das definições de HTTP de backend**|Sim ou Não|Define o cabeçalho do *hospedeiro* na sonda para o nome anfitrião do recurso traseiro na piscina traseira associada à Definição HTTP a que esta sonda está associada. Especialmente necessário em caso de apoios multi-inquilinos, como o serviço de aplicações Azure. [Mais informações](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ ou outro caminho|O resto da url completa para a sonda personalizada. Um caminho válido começa com '/'. Para o caminho padrão\/de http: /contoso.com basta usar '/' |
   |**Intervalo (segs)**|30|Quantas vezes a sonda é executada para verificar se há saúde. Não é aconselhável definir os inferiores a 30 segundos.|
   |**Timeout (secs)**|30|O tempo que a sonda espera antes de sair. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda está marcada como falhada. O intervalo de tempo tem de ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de saúde de backend esteja disponível. Note que o valor de tempo-out não deve ser superior ao valor 'Intervalo' utilizado nesta configuração da sonda ou ao valor "Tempo de Pedido" na definição HTTP que estará associado a esta sonda.|
|**Limiar com funcionamento incorreto**|3|Número de tentativas falhadas consecutivas para ser considerada insalubre. O limiar pode ser fixado para 1 ou mais.|
   |**Use condições de correspondência da sonda**|Sim ou Não|Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. Pode alterar o intervalo aceitável de código de resposta de backend ou corpo de resposta ao backend. [Mais informações](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > O nome do anfitrião não é o mesmo que o nome do servidor. Este valor é o nome do anfitrião virtual que corre no servidor de aplicações. A sonda é enviada para http://(nome anfitrião):(port de httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Adicione sonda ao portal

Agora que a sonda foi criada, é hora de adicioná-la ao portal. As definições da sonda são definidas nas definições de http do gateway da aplicação.

1. Clique nas **definições de HTTP** no gateway da aplicação, para elevar a lâmina de configuração clique nas definições de http do backend atual listadas na janela.

   ![janela de configurações https][2]

2. Na página de definições da **appGatewayBackEndHttpSettings,** verifique a caixa de verificação **de sonda personalizada Use** e escolha a sonda criada na secção Criar a [sonda](#createprobe) na queda da **sonda Personalizada.**
   Quando estiver concluído, clique em **Guardar** e as definições são aplicadas.

## <a name="next-steps"></a>Passos seguintes

Veja a saúde dos recursos de backend como determinado pela sonda usando a visão de [saúde de backend](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
