---
title: Visão geral da política da firewall de aplicação web azure (WAF)
description: Este artigo é uma visão geral das políticas globais, por site e por-URI da Web Application Firewall (WAF).
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: d90c887c2e93b44a8dd13dab0b4f03665ea335c3
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960961"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Visão geral da política da firewall de aplicação web azure (WAF)

As políticas de firewall de aplicação web contêm todas as definições e configurações waf. Isto inclui exclusões, regras personalizadas, regras geridas, e assim por diante. Estas políticas estão então associadas a um gateway de aplicação (global), a um ouvinte (por site) ou a uma regra baseada no caminho (per-URI) para que eles entrem em vigor.

> [!NOTE]
> As políticas azure Web Application Firewall (WAF) por site e por URI estão em Pré-visualização pública.
> 
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Não há limite para o número de políticas que se pode criar. Quando se cria uma política, deve estar associada a uma porta de entrada de aplicação para produzir efeitos. Pode ser associado a qualquer combinação de gateways de aplicação, ouvintes e regras baseadas em caminhos.

## <a name="global-waf-policy"></a>Política global de WAF

Quando associa uma política waf globalmente, todos os sites por trás do seu Application Gateway WAF estão protegidos com as mesmas regras geridas, regras personalizadas, exclusões e quaisquer outras configurações configuradas.

Se quiser que uma única política se aplique a todos os sites, pode associar a política ao gateway da aplicação. Para obter mais informações, consulte [Create Web Application Firewall policies for Application Gateway](create-waf-policy-ag.md) para criar e aplicar uma política WAF usando o portal Azure. 

## <a name="per-site-waf-policy"></a>Política de WAF por site

Com as políticas de WAF por site, pode proteger vários sites com diferentes necessidades de segurança por trás de uma única WAF utilizando políticas por site. Por exemplo, se houver cinco sites por trás do seu WAF, pode ter cinco políticas waf separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas, conjuntos de regras geridos e todas as outras definições de WAF para cada site.

Digamos que a sua porta de entrada de candidatura tem uma política global aplicada a ela. Depois aplica-se uma política diferente a um ouvinte na porta de entrada de aplicações. A política do ouvinte entra agora em vigor apenas para aquele ouvinte. A política global do portal de aplicação ainda se aplica a todos os outros ouvintes e regras baseadas em caminhos que não têm uma política específica atribuída a eles.

## <a name="per-uri-policy"></a>Política Per-URI

Para uma personalização ainda maior até ao nível URI, pode associar uma política de WAF a uma regra baseada em caminhos. Se houver certas páginas dentro de um único site que requerem políticas diferentes, pode fazer alterações na política waf que apenas afetam um determinado URI. Isto pode aplicar-se a uma página de pagamento ou início de sessão, ou a quaisquer outras URIs que necessitem de uma política waf ainda mais específica do que os outros sites por trás do seu WAF.

Tal como acontece com as políticas de WAF por site, políticas mais específicas sobrepõe-se às menos específicas. Isto significa que uma política por URI num mapa de caminhos de URL sobrepõe-se a qualquer política de WAF por site ou global acima dele.

## <a name="example"></a>Exemplo

Digamos que tem três sites: contoso.com, fabrikam.com e adatum.com todos atrás da mesma porta de aplicação. Você quer uma WAF aplicada aos três sites, mas você precisa de segurança adicional com adatum.com porque é onde os clientes visitam, navegam e compram produtos.

Pode aplicar uma política global ao WAF, com algumas definições básicas, exclusões ou regras personalizadas, se necessário para impedir que alguns falsos positivos bloqueiem o tráfego. Neste caso, não há necessidade de ter regras globais de injeção SQL em execução porque fabrikam.com e contoso.com são páginas estáticas sem backend SQL. Para que possa desativar essas regras na política global.

Esta política global é adequada para contoso.com e fabrikam.com, mas é preciso ter mais cuidado com adatum.com onde se trata de um sinal de informação e de pagamentos. Pode aplicar uma política por site ao ouvinte adato e deixar as regras SQL em funcionamento. Também assuma que há um cookie bloqueando algum tráfego, para que você possa criar uma exclusão para que esse cookie pare o falso positivo. 

O adatum.com/payments URI é onde tens de ter cuidado. Por isso, aplique outra política sobre esse URI e deixe todas as regras ativadas, e também remova todas as exclusões.

Neste exemplo, tem uma política global que se aplica a dois sites. Você tem uma política por site que se aplica a um site, e depois uma política por URI que se aplica a uma regra específica baseada em caminhos. Consulte (insira o link aqui quando existe) como criar políticas por site e por URI para o PowerShell correspondente para este exemplo.

## <a name="existing-waf-configurations"></a>Configurações WAF existentes

Todas as novas definições waf da Firewall de aplicação web (regras personalizadas, configurações de conjuntos de regras geridas, exclusões, e assim por diante.) existem numa política WAF. Se tiver um WAF existente, estas definições podem ainda existir na sua configuração WAF. Para obter mais informações sobre a mudança para a nova política da WAF, consulte (link: Migrar WAF Config para uma Política WAF). 


## <a name="next-steps"></a>Passos seguintes

Crie políticas por site e por URI utilizando o Azure PowerShell.
