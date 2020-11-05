---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como escalar o seu recurso Web App, Cloud Service, Virtual Machine ou Virtual Machine Scale definido em Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: d37a33ea575bbb8481d7d50dad8eab0f9ce0899d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361207"
---
# <a name="get-started-with-autoscale-in-azure"></a>Começa com a Autoscale em Azure
Este artigo descreve como configurar as suas definições de Autoscale para o seu recurso no portal Microsoft Azure.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md)

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descubra as definições de Autoscale na sua subscrição

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Pode descobrir todos os recursos para os quais a Autoscale é aplicável no Azure Monitor. Utilize os seguintes passos para uma passagem passo a passo:

1. Abra o [portal Azure.][1]
1. Clique no ícone Azure Monitor no painel esquerdo.
  ![Monitor aberto do Azure][2]
1. Clique **em Autoescala** para ver todos os recursos para os quais a Autoscale é aplicável, juntamente com o seu estado atual de Autoscale.
  ![Descubra autoescala no Monitor Azure][3]

Pode utilizar o painel de filtros na parte superior para analisar a lista para selecionar recursos num grupo de recursos específico, tipos de recursos específicos ou num recurso específico.

Para cada recurso, encontrará a contagem de instâncias atual e o estado de Autoscale. O estado de autoescala pode ser:

- **Não configurado** : Ainda não ativou a Autoscale para este recurso.
- **Ativado** : Ativou a Autoscale para este recurso.
- **Desativado** : Desativou a Autoescalada para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Crie a sua primeira definição de Autoscale

Vamos agora passar por uma simples passagem passo a passo para criar a sua primeira definição de Autoscale.

1. Abra a lâmina **de escala automática** no Azure Monitor e selecione um recurso que pretende escalar. (Os seguintes passos utilizam um plano de Serviço de Aplicações associado a uma aplicação web. Pode [criar a sua primeira aplicação web ASP.NET em Azure em 5 minutos.][4]
1. Note que a contagem de instâncias atuais é 1. Clique **em Ativar a escala automática**.
  ![Definição de escala para nova aplicação web][5]
1. Forneça um nome para a definição de escala e, em seguida, clique em **Adicionar uma regra**. Note as opções de regra de escala que se abrem como um painel de contexto no lado direito. Por predefinição, isto define a opção de escalar a contagem de exemplos em 1 se a percentagem de CPU do recurso exceder 70%. Deixe-o nos seus valores predefinidos e clique em **Adicionar**.
  ![Criar definição de escala para uma aplicação web][6]
1. Agora criaste a tua regra de primeira escala. Note que o UX recomenda as melhores práticas e afirma que "Recomenda-se ter pelo menos uma escala em regra." Para tal:

    a. Clique em **Adicionar uma regra**.

    b. Definir **o operador** para menos **de**.

    c. Fixar **limiar** para **20**.

    d. Definir **operação** para **Diminuir a contagem por**.

   Deve agora ter uma definição de escala que escama/escalas com base na utilização do CPU.
   ![Escala baseada no CPU][8]
1. Clique em **Guardar**.

Parabéns! Criou agora com sucesso a sua primeira definição de escala para autoescalar a sua aplicação web com base na utilização do CPU.

> [!NOTE]
> Os mesmos passos são aplicáveis para começar com um conjunto de escala de máquina virtual ou função de serviço de nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Escala com base num horário
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para dias específicos da semana.

1. Clique **Em Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição predefinida.
1. **Selecione Repita os dias específicos** para o horário.
1. Selecione os dias e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base no horário][9]
### <a name="scale-differently-on-specific-dates"></a>Escala de forma diferente em datas específicas
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para datas específicas.

1. Clique **Em Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição predefinida.
1. **Selecione Especificar as datas de início/fim** para o horário.
1. Selecione as datas de início/fim e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base em datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Veja o histórico de escala do seu recurso
Sempre que o seu recurso é dimensionado para cima ou para baixo, um evento é registado no registo de atividade. Pode ver o histórico de escala do seu recurso nas últimas 24 horas, mudando para o separador **história do Run.**

![Histórico de execuções][11]

Se pretender ver o histórico de escala completa (até 90 dias), **selecione Clique aqui para ver mais detalhes.** O registo de atividades abre, com autoescala pré-selecionada para o seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Ver a definição de escala do seu recurso
A Autoscale é um recurso Azure Resource Manager. Pode ver a definição de escala no JSON mudando para o separador **JSON.**

![Definição de escala][12]

Pode escoar alterações no JSON diretamente, se necessário. Estas alterações serão refletidas depois de as salvares.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desative a autoescalação e dimensione manualmente as suas instâncias
Pode haver alturas em que pretende desativar a definição da escala atual e escalar manualmente o seu recurso.

Clique no botão **de desativação automática** na parte superior.
![Desativar a autoescala][13]

> [!NOTE]
> Esta opção desativa a sua configuração. No entanto, pode voltar a fazê-lo depois de voltar a ativar a Autoscale.

Pode agora definir o número de casos que pretende escalar manualmente.

![Definir escala manual][14]

Pode sempre voltar à Autoescala clicando em **Ativar a autoescala** e, em seguida, **guardar**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Encaminhar o tráfego para instâncias saudáveis (Serviço de Aplicações)

Quando você é dimensionado para várias instâncias, o App Service pode realizar verificações de saúde nas suas instâncias para encaminhar o tráfego apenas para as instâncias saudáveis. Para tal, abra o Portal ao seu Serviço de Aplicações e, em seguida, selecione **Verificação de Saúde** em **Monitorização**. **Selecione Ative** e forneça um caminho URL válido na sua aplicação, como `/health` ou `/api/health` . Clique em **Guardar**.

Para ativar a funcionalidade com os modelos ARM, desacorda a `healthcheckpath` propriedade do recurso para o caminho de `Microsoft.Web/sites` verificação de saúde no seu site, por exemplo: `"/api/health/"` . Para desativar a função, volte a colocar a propriedade na cadeia `""` vazia.

### <a name="health-check-path"></a>Caminho de verificação de saúde

O caminho deve responder dentro de um minuto com um código de estado entre 200 e 299 (inclusive). Se o caminho não responder dentro de um minuto, ou devolver um código de estado fora do alcance, então a instância é considerada "insalubre". O Serviço de Aplicações não segue 302 redirecionamentos na via de verificação de saúde. O Health Check integra-se com as funcionalidades de autenticação e autorização do Serviço de Aplicações, o sistema chegará ao ponto final mesmo que estas funcionalidades de secuidade estejam ativadas. Se estiver a utilizar o seu próprio sistema de autenticação, o caminho de verificação de saúde deve permitir o acesso anónimo. Se o site tiver HTTP **S** -Apenas ativado, o pedido de verificação de saúde será enviado através de HTTP **S**.

A via de verificação de saúde deve verificar os componentes críticos da sua aplicação. Por exemplo, se a sua aplicação depender de uma base de dados e de um sistema de mensagens, o ponto final do controlo de saúde deve ligar-se a esses componentes. Se a aplicação não conseguir ligar-se a um componente crítico, então o caminho deve devolver um código de resposta de 500 níveis para indicar que a aplicação não é saudável.

#### <a name="security"></a>Segurança 

As equipas de desenvolvimento das grandes empresas precisam frequentemente de aderir aos requisitos de segurança para as suas APIs expostas. Para garantir o ponto final do healthcheck, deve primeiro utilizar funcionalidades como [restrições IP,](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules) [certificados](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)de cliente ou uma Rede Virtual para restringir o acesso à aplicação. Pode assegurar o próprio ponto final do healthcheck, exigindo que `User-Agent` o pedido de entrada corresponda `ReadyForRequest/1.0` . O User-Agent não pode ser falsificado, uma vez que o pedido já estava assegurado pelas funcionalidades de segurança anteriores.

### <a name="behavior"></a>Comportamento

Quando o caminho de verificação de saúde é fornecido, o Serviço de Aplicações irá fazer o caminho em todas as instâncias. Se um código de resposta bem sucedido não for recebido após 5 pings, este caso é considerado "insalubre". Casos pouco saudáveis serão excluídos da rotação do balançador de carga. Pode configurar o número necessário de pings falhados com a definição da `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` aplicação. Esta definição de aplicação pode ser definida para qualquer inteiro entre 2 e 10. Por exemplo, se isto for definido `2` para , as suas instâncias serão removidas do equilibrador de carga após dois pings falhados. Além disso, quando estiver a escalonar ou sair, o Serviço de Aplicações irá verificar o caminho da verificação de saúde para garantir que as novas instâncias estão prontas para pedidos antes de serem adicionadas ao equilibrador de carga.

Os casos saudáveis restantes podem experimentar um aumento da carga. Para evitar sobrecarregar as restantes instâncias, não mais de metade dos seus casos serão excluídos. Por exemplo, se um Plano de Serviço de Aplicações for dimensionado para 4 instâncias e 3 não saudáveis, no máximo 2 serão excluídos da rotação do loadbalancer. As outras 2 instâncias (1 saudável e 1 insalubre) continuarão a receber pedidos. No pior dos cenários, em que todos os casos não são saudáveis, nenhum será excluído. Se quiser anular este comportamento, pode definir a definição da `WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` aplicação para um valor entre `0` e `100` . Defini-lo para um valor mais elevado significa que mais casos insalubres serão removidos (o valor predefinido é de 50).

Se um caso não for saudável durante uma hora, será substituído por uma nova instância. No máximo, um caso será substituído por hora, com um máximo de três instâncias por dia por Plano de Serviço de Aplicação.

### <a name="monitoring"></a>Monitorização

Depois de fornecer o caminho de verificação de saúde da sua aplicação, pode monitorizar a saúde do seu site usando o Azure Monitor. A partir da lâmina **de verificação de saúde** no Portal, clique nas **Métricas** na barra de ferramentas superior. Isto abrirá uma nova lâmina onde poderá ver o estado histórico de saúde do site e criar uma nova regra de alerta. Para obter mais informações sobre a monitorização dos seus sites, [consulte o guia no Azure Monitor](../../app-service/web-sites-monitor.md).

## <a name="moving-autoscale-to-a-different-region"></a>Mover autoescala para uma região diferente
Esta secção descreve como mover a autoescala do Azure para outra região sob a mesma Subscrição e Grupo de Recursos. Pode utilizar a API REST para mover configurações de autoescala.
### <a name="prerequisite"></a>Pré-requisito
1. Certifique-se de que a subscrição e o Grupo de Recursos estão disponíveis e os detalhes nas regiões de origem e destino são idênticos.
1. Certifique-se de que a autoestama Azure está disponível na [região de Azure para onde pretende mover-se](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Mover
Utilize [a API REST](https://docs.microsoft.com/rest/api/monitor/autoscalesettings/createorupdate) para criar uma definição de autoescala no novo ambiente. A definição de autoescala criada na região de destino será uma cópia da definição de autoescala na região de origem.

[As definições de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) que foram criadas em associação com a definição de autoescala na região de origem não podem ser movidas. Terá de recriar definições de diagnóstico na região de destino, após a criação de definições de auto-venda. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Saiba mais sobre a movimentação de recursos em todas as regiões de Azure
Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte a [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)

## <a name="next-steps"></a>Próximos passos
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações do motor de autoescala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações falhadas de escala/escala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
