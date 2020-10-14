---
title: Visão geral da política do Azure Web Application Firewall (WAF)
description: Este artigo é uma visão geral das políticas globais, por site e per-URI da Web Application Firewall (WAF).
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 10/14/2020
ms.author: victorh
ms.openlocfilehash: 789a60c80d14301c107824d178a3566d358d287f
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056400"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Visão geral da política do Azure Web Application Firewall (WAF)

As políticas de firewall de aplicação web contêm todas as definições e configurações da WAF. Isto inclui exclusões, regras personalizadas, regras geridas, e assim por diante. Estas políticas estão então associadas a uma porta de aplicação (global), a um ouvinte (por local) ou a uma regra baseada em caminhos (per-URI) para que eles entrem em vigor.

> [!NOTE]
> As políticas Azure Web Application Firewall (WAF) per-URI estão em Visualização Pública.
> 
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Não há limite para o número de políticas que se pode criar. Quando se cria uma política, deve ser associada a uma porta de aplicação para fazer efeito. Pode ser associado a qualquer combinação de gateways de aplicações, ouvintes e regras baseadas em caminhos.

## <a name="global-waf-policy"></a>Política global da WAF

Quando associa uma política WAF globalmente, todos os sites por trás da sua Aplicação Gateway WAF estão protegidos com as mesmas regras geridas, regras personalizadas, exclusões e quaisquer outras configurações configuradas.

Se quiser que uma única política se aplique a todos os sites, pode associar a política à porta de aplicação. Para obter mais informações, consulte [as políticas de Criar firewall de aplicação web para](create-waf-policy-ag.md) o Gateway de aplicação para criar e aplicar uma política WAF utilizando o portal Azure. 

## <a name="per-site-waf-policy"></a>Política de WAF por local

Com as políticas WAF por site, pode proteger vários sites com diferentes necessidades de segurança por trás de uma única WAF ao utilizar as políticas por site. Por exemplo, se existirem cinco sites por trás da WAF, poderá ter cinco políticas WAF separadas (uma para cada serviço de escuta) para personalizar as exclusões, as regras personalizadas, os conjuntos de regras geridos e todas as outras definições de WAF para cada site.

Digamos que o gateway de aplicação tem uma política global aplicada. Em seguida, aplica uma política diferente a um serviço de escuta no gateway de aplicação. A política do serviço de escuta é agora aplicada apenas a esse serviço de escuta. A política global do gateway de aplicação ainda se aplica a todos os outros serviços de escuta e às regras baseadas em caminhos que não têm nenhuma política específica atribuída.

## <a name="per-uri-policy"></a>Política per-URI

Para uma personalização ainda maior até ao nível URI, pode associar uma política de WAF a uma regra baseada em caminhos. Se houver certas páginas dentro de um único site que exijam políticas diferentes, pode fazer alterações na política da WAF que apenas afetam um dado URI. Isto pode aplicar-se a uma página de pagamento ou de entrada, ou a quaisquer outros URIs que necessitem de uma política waf ainda mais específica do que os outros sites por trás da sua WAF.

Tal como acontece com as políticas de WAF por site, as políticas mais específicas sobrepõem-se às menos específicas. Isto significa que uma política per-URI num mapa de caminhos DE URL substitui qualquer política de WAF por local ou global acima dela.

## <a name="example"></a>Exemplo

Digamos que tem três sites: contoso.com, fabrikam.com e adatum.com todos atrás do mesmo portal de aplicações. Você quer um WAF aplicado nos três sites, mas você precisa de segurança adicional com adatum.com porque é onde os clientes visitam, navegam e compram produtos.

Pode aplicar uma política global à WAF, com algumas definições básicas, exclusões ou regras personalizadas, se necessário, para impedir que alguns falsos positivos bloqueiem o tráfego. Neste caso, não há necessidade de ter regras globais de injeção de SQL em funcionamento porque fabrikam.com e contoso.com são páginas estáticas sem backend SQL. Para que possa desativar essas regras na política global.

Esta política global é adequada para contoso.com e fabrikam.com, mas é preciso ter mais cuidado com adatum.com onde o sinal de informação e pagamentos são tratados. Pode aplicar uma política por local ao ouvinte do adatum e deixar as regras SQL em funcionamento. Também assuma que há um cookie bloqueando algum tráfego, para que você possa criar uma exclusão para que o cookie pare o falso positivo. 

O adatum.com/payments URI é onde tens de ter cuidado. Por isso, aplique outra política sobre esse URI e deixe todas as regras ativadas, e também remova todas as exclusões.

Neste exemplo, tem uma política global que se aplica a dois sites. Você tem uma política por site que se aplica a um site, e depois uma política per-URI que se aplica a uma regra específica baseada em caminhos. Consulte (inserir link aqui quando existir) como criar por site e políticas por URI para o exemplo do PowerShell correspondente.

## <a name="existing-waf-configurations"></a>Configurações WAF existentes

Todas as definições waf da Web Application Firewall (regras personalizadas, configurações de definição de regras geridas, exclusões e assim por diante.) existem numa política da WAF. Se tiver um WAF existente, estas definições ainda podem existir na sua configuração WAF. Para obter mais informações sobre a mudança para a nova política da WAF, [Migrar WAF Config para uma Política da WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Passos seguintes

- [Crie políticas por local e por URI utilizando a Azure PowerShell](per-site-policies.md).
