---
title: Crie uma sonda personalizada usando o portal
titleSuffix: Azure Application Gateway
description: Saiba como criar uma sonda personalizada para o Application Gateway utilizando o portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95975960"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Crie uma sonda personalizada para o Application Gateway utilizando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma sonda de saúde personalizada a uma porta de entrada de aplicação existente através do portal Azure. Utilizando as sondas de saúde, o Azure Application Gateway monitoriza a saúde dos recursos na piscina traseira.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não tiver um gateway de aplicações, visite [Criar um Gateway de Aplicação](./quick-create-portal.md) para criar uma porta de aplicação para trabalhar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Criar sonda para Application Gateway v2 SKU

As sondas são configuradas num processo de duas etapas através do portal. O primeiro passo é introduzir os valores necessários para a configuração da sonda. No segundo passo, testa a saúde do backend utilizando esta configuração da sonda e salva a sonda. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Insira propriedades da sonda

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tem uma conta, pode inscrever-se para um [julgamento gratuito de um mês.](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, clique em Todos os recursos. Clique no gateway de aplicação na lâmina de todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir partners.contoso.net na caixa Filtrar por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **sondas de saúde** e, em seguida, selecione **Adicionar** para adicionar uma nova sonda de saúde.

   ![Adicionar nova sonda][4]

4. Na página da **sonda De saúde** Adicionar, preencha as informações necessárias para a sonda e, quando estiver concluída, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que é acessível no portal.|
   |**Protocolo**|HTTP OU HTTPS | O protocolo que a sonda usa. |
   |**Anfitrião**|i.e contoso.com|Este valor é o nome do anfitrião virtual (diferente do nome do anfitrião VM) que está a ser gerido no servidor de aplicações. A sonda é enviada para \<protocol\> \<host name\> :\<port\>/\<urlPath\>|
   |**Escolha o nome do anfitrião a partir das definições HTTP backend**|Sim ou Não|Define o cabeçalho do *anfitrião* na sonda para o nome do anfitrião a partir das definições HTTP às quais esta sonda está associada. Especialmente necessário no caso de apoios multi-inquilinos, como o serviço de aplicações Azure. [Saiba mais](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Escolha a porta a partir das definições HTTP de backend**| Sim ou Não|Define a *porta* da sonda de saúde para a porta a partir das definições HTTP às quais esta sonda está associada. Se escolher não, pode entrar numa porta de destino personalizada para usar |
   |**Porta**| 1-65535 | Porta personalizada para ser usada para as sondas de saúde | 
   |**Caminho**|/ ou qualquer caminho válido|O restante da url completa para a sonda personalizada. Um caminho válido começa com '/'. Para o caminho padrão de http: \/ /contoso.com basta usar '/' |
   |**Intervalo (segs)**|30|Quantas vezes a sonda é executada para verificar se há saúde. Não é aconselhável definir o inferior a 30 segundos.|
   |**Intervalo (segs)**|30|A quantidade de tempo que a sonda espera antes do tempo de 20 minutos. Se não for recebida uma resposta válida dentro deste período de tempo, a sonda será marcada como falhada. O intervalo de tempo tem de ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de saúde de backend está disponível. Note que o valor de tempo de desatado não deve ser mais do que o valor 'Intervalo' utilizado nesta definição de sonda ou o valor 'Tempo de pedido' na definição HTTP que estará associado a esta sonda.|
   |**Limiar com funcionamento incorreto**|3|Número de tentativas falhadas consecutivas para ser considerado insalubre. O limiar pode ser definido para 1 ou mais.|
   |**Utilize condições de correspondência da sonda**|Sim ou Não|Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. Pode alterar o intervalo aceitável de código de resposta de backend ou do corpo de resposta de backend. [Saiba mais](./application-gateway-probe-overview.md#probe-matching)|
   |**Definições de HTTP**|seleção de dropdown|A sonda ficará associada com a definição(s) HTTP selecionada aqui e, portanto, monitorizará a saúde desse pool de backend que está associado à definição http selecionada. Utilizará a mesma porta para o pedido de sonda que o que está a ser utilizado na definição HTTP selecionada. Só pode escolher as definições HTTP que não estão associadas a qualquer outra sonda personalizada. <br>Note que apenas as definições HTTP(s) estão disponíveis para associação que têm o mesmo protocolo que o protocolo escolhido nesta configuração de sonda e têm o mesmo estado para o interruptor *de definição de Nome de Anfitrião Pick From Backend HTTP.*|
   
   > [!IMPORTANT]
   > A sonda monitorizará a saúde do backend apenas quando estiver associada a uma ou mais definições HTTP. Monitorizará os recursos de back-end das piscinas de back-end que estão associadas à definição(s) HTTP(s) à qual esta sonda está associada. O pedido de sonda será enviado como \<protocol\> \<hostName\> : . \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Teste de saúde com a sonda

Depois de introduzir as propriedades da sonda, pode testar a saúde dos recursos back-end para verificar se a configuração da sonda está correta e que os recursos de back-end estão funcionando como esperado.

1. Selecione **Teste** e note o resultado da sonda. O gateway de aplicação testa a saúde de todos os recursos de backend nas piscinas de backend associadas à(s) Definição HTTP(s) utilizada para esta sonda. 

   ![Saúde de backend de teste][5]

2. Se houver algum recurso de backend pouco saudável, verifique a coluna **Details** para entender a razão do estado pouco saudável do recurso. Se o recurso tiver sido marcado como insalubre devido a uma configuração incorreta da sonda, então selecione o **Go back para sondar** link e edite a configuração da sonda. Caso contrário, se o recurso tiver sido marcado como insalubre devido a um problema com o backend, então resolva os problemas com o recurso backend e, em seguida, teste novamente o backend selecionando o **Go de volta para a** ligação sonda e selecione **Test**.

   > [!NOTE]
   > Pode optar por salvar a sonda mesmo com recursos de backend pouco saudáveis, mas não é recomendado. Isto porque o Gateway de Aplicação não irá encaminhar pedidos para os servidores backend do pool backend que são determinados como insalubres pela sonda. Caso não existam recursos saudáveis num pool de backend, não poderá aceder à sua aplicação e terá um erro HTTP 502.

   ![Ver resultado da sonda][6]

3. **Selecione Adicionar** para salvar a sonda. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Criar sonda para Application Gateway v1 SKU

As sondas são configuradas num processo de duas etapas através do portal. O primeiro passo é criar a sonda. No segundo passo, adicione a sonda às definições http backend do gateway de aplicações.

### <a name="create-the-probe"></a><a name="createprobe"></a>Criar a sonda

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tem uma conta, pode inscrever-se para um [julgamento gratuito de um mês.](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, selecione **Todos os recursos**. Selecione o gateway de aplicação na página **Todos os recursos.** Se a subscrição que selecionou já tiver vários recursos, pode introduzir partners.contoso.net na caixa Filtrar por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **Sondas** e, em seguida, **selecione Adicionar** para adicionar uma sonda.

   ![Adicione a lâmina da sonda com informações preenchidas][1]

4. Na lâmina da **sonda de saúde Add,** preencha as informações necessárias para a sonda e, quando estiver concluída, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que é acessível no portal.|
   |**Protocolo**|HTTP OU HTTPS | O protocolo que a sonda usa. |
   |**Anfitrião**|i.e contoso.com|Este valor é o nome do anfitrião virtual (diferente do nome do anfitrião VM) que está a ser gerido no servidor de aplicações. A sonda é enviada para (protocolo)://(nome de anfitrião):(port from httpsetting)/urlPath.  Isto é aplicável quando vários sites são configurados no Gateway de aplicação. Se o Gateway de Aplicação estiver configurado para um único local, insira '127.0.0.1'.|
   |**Escolha o nome do anfitrião a partir das definições HTTP backend**|Sim ou Não|Define o cabeçalho do *anfitrião* na sonda para o nome de anfitrião do recurso back-end na piscina de back-end associada à definição HTTP à qual esta sonda está associada. Especialmente necessário no caso de apoios multi-inquilinos, como o serviço de aplicações Azure. [Saiba mais](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Caminho**|/ ou qualquer caminho válido|O restante da url completa para a sonda personalizada. Um caminho válido começa com '/'. Para o caminho padrão de http: \/ /contoso.com basta usar '/' |
   |**Intervalo (segs)**|30|Quantas vezes a sonda é executada para verificar se há saúde. Não é aconselhável definir o inferior a 30 segundos.|
   |**Intervalo (segs)**|30|A quantidade de tempo que a sonda espera antes do tempo de 20 minutos. Se não for recebida uma resposta válida dentro deste período de tempo, a sonda será marcada como falhada. O intervalo de tempo tem de ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de saúde de backend está disponível. Note que o valor de tempo de desatado não deve ser mais do que o valor 'Intervalo' utilizado nesta definição de sonda ou o valor 'Tempo de pedido' na definição HTTP que estará associado a esta sonda.|
   |**Limiar com funcionamento incorreto**|3|Número de tentativas falhadas consecutivas para ser considerado insalubre. O limiar pode ser definido para 1 ou mais.|
   |**Utilize condições de correspondência da sonda**|Sim ou Não|Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. Pode alterar o intervalo aceitável de código de resposta de backend ou do corpo de resposta de backend. [Saiba mais](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > O nome do anfitrião não é o mesmo que o nome do servidor. Este valor é o nome do anfitrião virtual em execução no servidor de aplicações. A sonda é enviada para \<protocol\> \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Adicione sonda ao portal

Agora que a sonda foi criada, é hora de adicioná-la ao portal. As definições da sonda são definidas nas definições de backend http do gateway de aplicações.

1. Clique nas **definições HTTP** no gateway de aplicações, para levantar a lâmina de configuração clique nas definições de backend de corrente listadas na janela.

   ![janela de definições https][2]

2. Na página de definições de **configurações do AppGatewayBackEndHttpSettings,** verifique a caixa de verificação **da sonda personalizada Use** e escolha a sonda criada na secção ['Criar'](#createprobe) na secção **de sonda personalizada.**
   Quando estiver concluído, clique em **Guardar** e as definições são aplicadas.

## <a name="next-steps"></a>Passos seguintes

Veja a saúde dos recursos de backend como determinado pela sonda utilizando a [visão de saúde backend](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
