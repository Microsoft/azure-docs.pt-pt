---
title: Problemas Azure Load Balancer problemas de saúde, frontend e disponibilidade de backend
description: Utilize as métricas disponíveis para diagnosticar o seu Equilibrador de Carga Padrão Azure degradado ou indisponível.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 6148cedbf004e3e63200ac50b91a40866c5b18db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719724"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>Problemas de saúde, problemas de saúde, frontend e disponibilidade de backend 

Este artigo é um guia para investigar questões que impactam a disponibilidade do seu frontend de carga IP e recursos de backend. 

## <a name="about-the-metrics-well-use"></a>Sobre as métricas que usaremos
As duas métricas a utilizar são *a disponibilidade de caminhos de dados* e o estado da sonda de *saúde* e é importante compreender o seu significado para obter insights corretos. 

## <a name="data-path-availability"></a>Disponibilidade do caminho dos dados
A métrica de disponibilidade de trajetória de dados é gerada por um ping TCP a cada 25 segundos em todas as portas frontend que têm regras DE NAT de equilíbrio de carga e entrada configuradas. Este ping TCP será então encaminhado para qualquer uma das instâncias de backend saudáveis (sondadas). Se o serviço receber uma resposta ao ping, é considerado um sucesso e a soma da métrica será iterada uma vez, se falhar não o fará. A contagem desta métrica é de 1/100 do total de pings TCP por período de amostra. Assim, queremos considerar a média, que mostrará a média de soma/contagem para o período de tempo. A métrica de disponibilidade de trajetória de dados agregada pela média dá-nos assim uma taxa de sucesso percentual para os pings TCP no seu frontend IP:porta para cada uma das suas regras de NAT de equilíbrio de carga e entrada.

## <a name="health-probe-status"></a>Estado da sonda de estado de funcionamento
A métrica do estado da sonda de saúde é gerada por um ping do protocolo definido na sonda de saúde. Este ping é enviado para cada instância na piscina de backend e na porta definida na sonda de saúde. Para as sondas HTTP e HTTPS, um ping bem sucedido requer uma resposta HTTP 200 OK, enquanto que com as sondas TCP qualquer resposta é considerada bem sucedida. Os sucessos ou falhas consecutivos de cada sonda determinam então se uma instância de backend é saudável e é capaz de receber tráfego para as regras de equilíbrio de carga a que o pool de backend é atribuído. Semelhante à disponibilidade de caminhos de dados usamos a agregação média, o que nos indica a média de pings bem sucedidos/totais durante o intervalo de amostragem. Este valor de estado da sonda de saúde indica a saúde de backend isoladamente do seu equilibrador de carga, sondando as suas instâncias de backend sem enviar tráfego através do frontend.

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnosticar equilibradores de carga degradados e indisponíveis
Como delineado no artigo de [saúde](load-balancer-standard-diagnostics.md#resource-health-status)de recursos , um balanceador de carga degradado é aquele que mostra entre 25% e 90% de disponibilidade de trajetória de dados, e um equilibrador de carga indisponível é aquele com menos de 25% de disponibilidade de trajetória de dados, durante um período de dois minutos. Estes mesmos passos podem ser dados para investigar a falha que você vê em qualquer estado de sonda de saúde ou alertas de disponibilidade de caminhos de dados que você configurado. Vamos explorar o caso em que verificamos a nossa saúde de recursos e descobrimos que o nosso balanceador de carga não está disponível com uma disponibilidade de caminho de dados de 0% - o nosso serviço está em baixo.

Primeiro, vamos para a visão detalhada das métricas da nossa lâmina de perspicácia do balanceador de carga. Pode fazê-lo através da lâmina do seu equilibrador de carga ou da ligação na sua mensagem de saúde de recursos.  Em seguida, navegamos para o separador de disponibilidade frontend e backend e revemos uma janela de 30 minutos do período de tempo em que ocorreu o estado degradado ou indisponível. Se virmos que a disponibilidade do nosso caminho de dados foi de 0%, sabemos que há um problema que impede o tráfego de todas as nossas regras de NA de equilíbrio de carga e entrada e podemos ver quanto tempo este impacto durou. 

O próximo lugar que precisamos procurar é a nossa métrica do estado da sonda de saúde para determinar se o nosso caminho de dados está indisponível é porque não temos casos saudáveis de backend para servir o tráfego. Se temos pelo menos uma instância de backend saudável para todas as nossas regras de equilíbrio de carga e entrada, sabemos que não é a nossa configuração que está a causar a indisponibilidade dos nossos caminhos de dados. Este cenário indica um problema na plataforma Azure, embora raro, não se preocupe se os encontrar como um alerta automatizado é enviado para a nossa equipa para resolver rapidamente todos os problemas da plataforma.

## <a name="diagnose-health-probe-failures"></a>Diagnosticar falhas na sonda de saúde
Vamos verificar o nosso estado da sonda de saúde e descobrir que todas as instâncias estão a mostrar-se pouco saudáveis. Esta descoberta explica porque é que o nosso caminho de dados não está disponível, uma vez que o tráfego não tem para onde ir. Devemos então analisar a seguinte lista de verificação para excluir erros de configuração comuns:
* Verifique a utilização do CPU para os seus recursos para verificar se as suas instâncias são de facto saudáveis
  * Pode verificar isto. 
* Se utilizar uma sonda HTTP ou HTTPS verifique se a aplicação é saudável e responsiva
  * Valide isto é funcional, acedendo diretamente às aplicações através do endereço IP privado ou endereço IP público de nível de instância associado à sua instância de backend
* Reveja os Grupos de Segurança da Rede aplicados aos nossos recursos de backend. Certifique-se de que não existem regras de uma prioridade maior do que o AllowAzureLoadBalancerInBound que bloqueie a sonda de saúde
  * Pode fazê-lo visitando a lâmina de rede dos seus VMs de backend ou conjuntos de balanças de máquinas virtuais
  * Se achar que este problema de NSG é o caso, mova a regra de Permitir existente ou crie uma nova regra de alta prioridade para permitir o tráfego AzureLoadBalancer
* Verifique o seu sossia. Certifique-se de que os seus VMs estão a ouvir na porta da sonda e reveja as suas regras de firewall de SISTEMA para garantir que não estão a bloquear o tráfego da sonda originário do endereço IP 168.63.129.16
  * Pode verificar as portas de audição executando netstat - a prompt de comando do Windows ou netstat -l num terminal Linux
* Não coloque uma firewall NVA VM no pool de backend do equilibrador de carga, use [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) para encaminhar o tráfego para rebasar instâncias através da firewall
* Certifique-se de que está a utilizar o protocolo certo, se utilizar HTTP para sondar uma porta de audição para uma aplicação não-HTTP, a sonda falhará

Se já passou por esta lista de verificação e ainda está a encontrar falhas nas sondas de saúde, pode haver problemas raros na plataforma com impacto no serviço de sonda para as suas instâncias. Neste caso, o Azure tem as suas costas e um alerta automatizado é enviado à nossa equipa para resolver rapidamente todos os problemas da plataforma.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a sonda de saúde Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [Saiba mais sobre as métricas do Balançador de Carga Azure](load-balancer-standard-diagnostics.md)


