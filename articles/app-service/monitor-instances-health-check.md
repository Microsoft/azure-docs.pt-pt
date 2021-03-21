---
title: Monitorize a saúde das instâncias do Serviço de Aplicações
description: Saiba como monitorizar a saúde das instâncias do Serviço de Aplicações utilizando o Cheque de Saúde.
keywords: serviço de aplicativos azure, web app, verificação de saúde, tráfego de rotas, instâncias saudáveis, caminho, monitorização,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 0e08d016ab85587d451ad2a1e296e7f494ba283e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596030"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Monitorizar instâncias do Serviço de Aplicações usando verificação de saúde

![Falha no controlo de saúde][2]

Este artigo utiliza o health check no portal Azure para monitorizar as instâncias do Serviço de Aplicações. O exame de saúde aumenta a disponibilidade da sua aplicação removendo casos insalubres. O seu [plano de Serviço de Aplicações](./overview-hosting-plans.md) deve ser dimensionado para duas ou mais instâncias para utilizar o Health check. A via de verificação de saúde deve verificar os componentes críticos da sua aplicação. Por exemplo, se a sua aplicação depender de uma base de dados e de um sistema de mensagens, o ponto final do controlo de saúde deve ligar-se a esses componentes. Se a aplicação não conseguir ligar-se a um componente crítico, então o caminho deve devolver um código de resposta de 500 níveis para indicar que a aplicação não é saudável.

## <a name="what-app-service-does-with-health-checks"></a>O que o Serviço de Aplicações faz com verificações de saúde

- Quando for dado um caminho na sua aplicação, a Health verifique este caminho em todas as instâncias da sua aplicação de Serviço de Aplicações em intervalos de 1 minuto.
- Se uma instância não responder com um código de estado entre 200-299 (inclusive) após dois ou mais pedidos, ou não responder ao ping, o sistema determina que não é saudável e remove-o.
- Após a remoção, o exame de saúde continua a pingar o caso pouco saudável. Se continuar a responder sem sucesso, o Serviço de Aplicações reinicia o VM subjacente num esforço para devolver a ocorrência a um estado saudável.
- Se um caso não for saudável durante uma hora, será substituído por uma nova instância.
- Além disso, ao escalonar ou sair, o Serviço de Aplicações procura a via de verificação da Saúde para garantir que novas instâncias estão prontas.

> [!NOTE]
> O exame de saúde não segue 302 redirecionamentos. No máximo, um caso será substituído por hora, com um máximo de três instâncias por dia por Plano de Serviço de Aplicação.
>

## <a name="enable-health-check"></a>Ativar o Controlo de Saúde

![Navegação de verificação de saúde no Portal Azure][3]

- Para ativar a verificação de saúde, navegue pelo portal Azure e selecione a sua aplicação De Serviço de Aplicações.
- Em **Monitorização**, selecione **Health check**.
- **Selecione Ative** e forneça um caminho URL válido na sua aplicação, como `/health` ou `/api/health` .
- Clique em **Guardar**.

> [!CAUTION]
> Alterações na configuração da verificação de saúde reiniciam a sua aplicação. Para minimizar o impacto nas aplicações de produção, recomendamos [configurar slots](deploy-staging-slots.md) de encenação e trocar para a produção.
>

### <a name="configuration"></a>Configuração

Além de configurar as opções de verificação de Saúde, também pode configurar as [seguintes definições de aplicações:](configure-common.md)

| Nome de definição de aplicativo | Valores permitidos | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | O número máximo de falhas de ping. Por exemplo, quando programados para `2` , as suas instâncias serão removidas após `2` pings falhados. Além disso, quando está a aumentar ou sair, o Serviço de Aplicações procura a via de verificação da Saúde para garantir que novas instâncias estão prontas. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Para evitar casos de saúde esmagadora, não mais de metade dos casos serão excluídos. Por exemplo, se um Plano de Serviço de Aplicações for dimensionado para quatro instâncias e três não forem saudáveis, no máximo dois serão excluídos. As outras duas instâncias (uma saudável e outra pouco saudável) continuarão a receber pedidos. No pior dos cenários, em que todos os casos não são saudáveis, nenhum será excluído. Para anular este comportamento, defina a definição da aplicação para um valor entre `0` e `100` . Um valor mais elevado significa que mais casos insalubres serão removidos (o padrão é 50). |

#### <a name="authentication-and-security"></a>Autenticação e segurança

A verificação de saúde integra-se com as funcionalidades de autenticação e autorização do Serviço de Aplicações. Não são necessárias definições adicionais se estas funcionalidades de segurança estiverem ativadas. No entanto, se estiver a utilizar o seu próprio sistema de autenticação, a via de verificação da saúde deve permitir o acesso anónimo. Se o site estiver HTTP **S**-Apenas ativado, o pedido de verificação de saúde será enviado através de HTTP **S**.

As grandes equipas de desenvolvimento das empresas precisam frequentemente de aderir aos requisitos de segurança para as APIs expostas. Para garantir o ponto final do controlo de Saúde, deve primeiro utilizar funcionalidades como [restrições IP, certificados](app-service-ip-restrictions.md#set-an-ip-address-based-rule) [de cliente](app-service-ip-restrictions.md#set-an-ip-address-based-rule)ou uma Rede Virtual para restringir o acesso à aplicação. Pode garantir o ponto final do controlo de saúde exigindo os `User-Agent` jogos de pedido de entrada `ReadyForRequest/1.0` . O User-Agent não pode ser falsificado, uma vez que o pedido já seria garantido por funcionalidades de segurança anteriores.

## <a name="monitoring"></a>Monitorização

Depois de fornecer o caminho de verificação de saúde da sua aplicação, pode monitorizar a saúde do seu site utilizando o Azure Monitor. A partir da lâmina **de verificação de saúde** no Portal, clique nas **Métricas** na barra de ferramentas superior. Isto abrirá uma nova lâmina onde poderá ver o estado histórico de saúde do site e criar uma nova regra de alerta. Para obter mais informações sobre a monitorização dos seus sites, [consulte o guia no Azure Monitor](web-sites-monitor.md).

## <a name="limitations"></a>Limitações

A verificação de saúde não deve ser ativada nos sites de Funções Premium. Devido à rápida escala de Funções Premium, os pedidos de verificação de saúde podem causar flutuações desnecessárias no tráfego HTTP. As Funções Premium têm as suas próprias sondas de saúde internas que são usadas para informar decisões de escala.

## <a name="next-steps"></a>Passos seguintes
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações do motor de autoescala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações falhadas de escala/escala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
