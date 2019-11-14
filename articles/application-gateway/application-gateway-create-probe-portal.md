---
title: Criar uma investigação personalizada usando o portal
titleSuffix: Azure Application Gateway
description: Saiba como criar uma investigação personalizada para o gateway de aplicativo usando o portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074613"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o gateway de aplicativo usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma investigação de integridade personalizada a um gateway de aplicativo existente por meio do portal do Azure. Usando as investigações de integridade, Aplicativo Azure gateway monitora a integridade dos recursos no pool de back-end.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver um gateway de aplicativo, visite [criar um gateway de aplicativo](application-gateway-create-gateway-portal.md) para criar um gateway de aplicativo com o qual trabalhar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Criar investigação para SKU do gateway de aplicativo v2

As investigações são configuradas em um processo de duas etapas por meio do Portal. A primeira etapa é inserir os valores necessários para a configuração de investigação. Na segunda etapa, você testará a integridade do back-end usando essa configuração de teste e salvará a investigação. 

### <a name="createprobe"></a>Inserir Propriedades da investigação

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel portal do Azure favoritos, clique em todos os recursos. Clique no gateway de aplicativo na folha todos os recursos. Se a assinatura que você selecionou já tiver vários recursos, você poderá inserir partners.contoso.net no filtro por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **investigações de integridade** e, em seguida, selecione **Adicionar** para adicionar uma nova investigação de integridade.

   ![Adicionar nova investigação][4]

4. Na página **Adicionar investigação de integridade** , preencha as informações necessárias para a investigação e, quando concluir, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Esse valor é um nome amigável dado à investigação que é acessível no Portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo usado pela investigação de integridade. |
   |**Anfitrião**|, contoso.com|Esse valor é o nome do host virtual (diferente do nome de host da VM) em execução no servidor de aplicativos. A investigação é enviada para (protocolo)://(nome do host):(porta de httpsetting)/urlPath.  Isso é aplicável quando o multissite é configurado no gateway de aplicativo. Se o gateway de aplicativo estiver configurado para um único site, digite ' 127.0.0.1 '.|
   |**Escolha o nome do host nas configurações de HTTP de back-end**|Sim ou Não|Define o cabeçalho de *host* na investigação como o nome de host do recurso de back-end no pool de back-ends associado à configuração de http à qual essa investigação está associada. Especialmente necessário no caso de back-ends de vários locatários, como o serviço de aplicativo do Azure. [Saber mais](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com '/'. Para o caminho padrão de http:\//contoso.com apenas use '/' |
   |**Intervalo (segundos)**|30|Com que frequência a investigação é executada para verificar a integridade. Não é recomendável definir o menor que 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação aguarda antes de atingir o tempo limite. Se uma resposta válida não for recebida nesse período de tempo limite, a investigação será marcada como com falha. O intervalo de tempo limite precisa ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de integridade de back-end esteja disponível. Observe que o valor de tempo limite não deve ser maior que o valor de ' interval ' usado nesta configuração de investigação ou o valor de ' tempo limite de solicitação ' na configuração de HTTP que será associada a essa investigação.|
|**Limite não íntegro**|3|Número de tentativas de falha consecutivas a serem consideradas não íntegras. O limite pode ser definido como 1 ou mais.|
   |**Usar condições de correspondência de investigação**|Sim ou Não|Por padrão, uma resposta HTTP (S) com código de status entre 200 e 399 é considerada íntegra. Você pode alterar o intervalo aceitável de código de resposta de back-end ou corpo de resposta de back-end. [Saber mais](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Configurações de HTTP**|seleção da lista suspensa|A investigação será associada às configurações de HTTP selecionadas aqui e, portanto, monitorará a integridade desse pool de back-end que está associado à configuração de HTTP selecionada. Ele usará a mesma porta para a solicitação de investigação que a que está sendo usada na configuração de HTTP selecionada. Você só pode escolher essas configurações HTTP que não estão associadas a nenhuma outra investigação personalizada. <br>Observe que apenas essas configurações HTTP estão disponíveis para associação que têm o mesmo protocolo que o protocolo escolhido nesta configuração de investigação e têm o mesmo estado para a opção *escolher nome de host do back-end de configuração de http* .|
   
   > [!IMPORTANT]
   > A investigação monitorará a integridade do back-end somente quando ele estiver associado a uma ou mais configurações de HTTP. Ele monitorará os recursos de back-end desses pools de back-end que estão associados às configurações de HTTP às quais essa investigação está associada. A solicitação de investigação será enviada para a porta de:(http://(nome do host) de httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Testar a integridade do back-end com a investigação

Depois de inserir as propriedades de investigação, você pode testar a integridade dos recursos de back-end para verificar se a configuração de investigação está correta e se os recursos de back-end estão funcionando conforme o esperado.

1. Selecione **testar** e observe o resultado da investigação. O gateway de aplicativo testa a integridade de todos os recursos de back-end nos pools de back-end associados às configurações de HTTP usadas para essa investigação. 

   ![Testar integridade do back-end][5]

2. Se houver recursos de back-end não íntegros, verifique a coluna **detalhes** para entender o motivo do estado não íntegro do recurso. Se o recurso tiver sido marcado como não íntegro devido a uma configuração de investigação incorreta, selecione o link voltar **ao teste de investigação** e edite a configuração da investigação. Caso contrário, se o recurso tiver sido marcado como não íntegro devido a um problema com o back-end, resolva os problemas com o recurso de back-end e, em seguida, teste o back-end novamente selecionando **voltar ao** link de investigação e selecione **testar**.

   > [!NOTE]
   > Você pode optar por salvar a investigação mesmo com recursos de back-end não íntegros, mas isso não é recomendado. Isso ocorre porque o gateway de aplicativo remove esses recursos de back-end do pool de back-end que são determinados como não íntegros pela investigação. Caso não haja recursos íntegros em um pool de back-end, você não poderá acessar seu aplicativo e receberá um erro 502.

   ![Exibir resultado da investigação][6]

3. Selecione **Adicionar** para salvar a investigação. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Criar investigação para SKU do gateway de aplicativo v1

As investigações são configuradas em um processo de duas etapas por meio do Portal. A primeira etapa é criar a investigação. Na segunda etapa, você adiciona a investigação às configurações de http de back-end do gateway de aplicativo.

### <a name="createprobe"></a>Criar a investigação

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel portal do Azure favoritos, selecione **todos os recursos**. Selecione o gateway de aplicativo na página **todos os recursos** . Se a assinatura que você selecionou já tiver vários recursos, você poderá inserir partners.contoso.net no filtro por nome... para aceder facilmente ao gateway de aplicação.

3. Selecione **investigações** e, em seguida, selecione **Adicionar** para adicionar uma investigação.

   ![Adicionar folha de investigação com informações preenchidas][1]

4. Na folha **Adicionar investigação de integridade** , preencha as informações necessárias para a investigação e, quando concluir, selecione **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Esse valor é um nome amigável dado à investigação que é acessível no Portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo usado pela investigação de integridade. |
   |**Anfitrião**|, contoso.com|Esse valor é o nome do host virtual (diferente do nome de host da VM) em execução no servidor de aplicativos. A investigação é enviada para (protocolo)://(nome do host):(porta de httpsetting)/urlPath.  Isso é aplicável quando o multissite é configurado no gateway de aplicativo. Se o gateway de aplicativo estiver configurado para um único site, digite ' 127.0.0.1 '.|
   |**Escolha o nome do host nas configurações de HTTP de back-end**|Sim ou Não|Define o cabeçalho de *host* na investigação como o nome de host do recurso de back-end no pool de back-ends associado à configuração de http à qual essa investigação está associada. Especialmente necessário no caso de back-ends de vários locatários, como o serviço de aplicativo do Azure. [Saber mais](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com '/'. Para o caminho padrão de http:\//contoso.com apenas use '/' |
   |**Intervalo (segundos)**|30|Com que frequência a investigação é executada para verificar a integridade. Não é recomendável definir o menor que 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação aguarda antes de atingir o tempo limite. Se uma resposta válida não for recebida nesse período de tempo limite, a investigação será marcada como com falha. O intervalo de tempo limite precisa ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de integridade de back-end esteja disponível. Observe que o valor de tempo limite não deve ser maior que o valor de ' interval ' usado nesta configuração de investigação ou o valor de ' tempo limite de solicitação ' na configuração de HTTP que será associada a essa investigação.|
|**Limite não íntegro**|3|Número de tentativas de falha consecutivas a serem consideradas não íntegras. O limite pode ser definido como 1 ou mais.|
   |**Usar condições de correspondência de investigação**|Sim ou Não|Por padrão, uma resposta HTTP (S) com código de status entre 200 e 399 é considerada íntegra. Você pode alterar o intervalo aceitável de código de resposta de back-end ou corpo de resposta de back-end. [Saber mais](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para a porta de:(http://(nome do host) de httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Adicionar investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações de investigação são definidas nas configurações de http de back-end do gateway de aplicativo.

1. Clique em **configurações de http** no gateway de aplicativo para exibir a folha de configuração, clique nas configurações de http de back-end atuais listadas na janela.

   ![janela configurações de https][2]

2. Na página Configurações do **appgatewaybackendhttp** , marque a caixa de seleção **usar investigação personalizada** e escolha a investigação criada na seção [criar a investigação](#createprobe) no menu suspenso **investigação personalizada** .
   Ao concluir, clique em **salvar** e as configurações serão aplicadas.

## <a name="next-steps"></a>Passos seguintes

Exiba a integridade dos recursos de back-end conforme determinado pela investigação usando a [exibição integridade de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
