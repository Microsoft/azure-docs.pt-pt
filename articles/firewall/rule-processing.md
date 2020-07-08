---
title: Lógica de processamento de regras do Azure Firewall
description: O Azure Firewall tem regras NAT, regras de rede e regras de aplicações. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81113441"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall
Pode configurar regras DE NAT, regras de rede e aplicações no Azure Firewall. As recolhas de regras são processadas de acordo com o tipo de regra em ordem prioritária, números mais baixos para números mais altos de 100 a 65.000. Um nome de coleção de regras só pode ter letras, números, sublinhados, períodos ou hífens. Deve começar com uma letra ou número, e terminar com uma letra, número ou sublinhado. O comprimento máximo do nome é de 80 caracteres.

O melhor é inicialmente espaçar os números prioritários da sua coleção de regras em 100 incrementos (100, 200, 300, e assim por diante) para que tenha espaço para adicionar mais coleções de regras, se necessário.

> [!NOTE]
> Se permitir a filtragem baseada em ameaças baseadas em inteligência, essas regras são a maior prioridade e são sempre processadas primeiro. A filtragem da inteligência de ameaça pode negar o tráfego antes de serem processadas quaisquer regras configuradas. Para obter mais informações, consulte [a filtragem baseada em ameaças de Azure Firewall.](threat-intel.md)

## <a name="outbound-connectivity"></a>Conectividade de saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicações

Se configurar regras de rede e regras de aplicação, as regras de rede são aplicadas por ordem prioritária antes das regras de aplicação. As regras estão a acabar. Portanto, se uma correspondência for encontrada numa regra de rede, nenhuma outra regra é processada.  Se não houver correspondência de regras de rede, e se o protocolo for HTTP, HTTPS ou MSSQL, então o pacote é avaliado pelas regras de aplicação por ordem prioritária. Se ainda não for encontrado qualquer correspondência, o pacote é avaliado contra a recolha da [regra da infraestrutura.](infrastructure-fqdns.md) Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="inbound-connectivity"></a>Conectividade de entrada

### <a name="nat-rules"></a>Regras NAT

A conectividade de entrada na Internet pode ser ativada configurando a tradução de endereços de rede de destino (DNAT) como descrito no [Tutorial: Filtrar o tráfego de entrada com DNAT Azure Firewall utilizando o portal Azure](tutorial-firewall-dnat.md). As regras DA são aplicadas na prioridade antes das regras da rede. Se for encontrada uma correspondência, é adicionada uma regra implícita de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido.

As regras de inscrição não são aplicadas para ligações de entrada. Por isso, se pretender filtrar o tráfego HTTP/S de entrada, deve utilizar a Firewall de Aplicação Web (WAF). Para mais informações, consulte o que é o Firewall de [Aplicação Web Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram os resultados de algumas destas combinações de regras.

### <a name="example-1"></a>Exemplo 1

A ligação à google.com é permitida devido a uma regra de rede correspondente.

**Regra da rede**

- Ação: Permitir


|name  |Protocolo  |Tipo de origem  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir web     |TCP|Endereço IP|*|Endereço IP|*|80,443

**Regra de candidatura**

- Ação: Negar

|name  |Tipo de origem  |Origem  |Protocolo:Porto|FQDNs alvo|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Endereço IP|*|http:80,https:443|google.com

**Resultado**

A ligação a google.com é permitida porque o pacote corresponde à regra da rede *Allow-web.* O processamento de regras para neste momento.

### <a name="example-2"></a>Exemplo 2

O tráfego SSH é negado porque uma prioridade maior A recolha de regras de rede *Deny* bloqueia-o.

**Recolha de regras de rede 1**

- Nome: Allow-collection
- Prioridade: 200
- Ação: Permitir

|name  |Protocolo  |Tipo de origem  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Recolha de regras de rede 2**

- Nome: Coleção Deny
- Prioridade: 100
- Ação: Negar

|name  |Protocolo  |Tipo de origem  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Negar-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As ligações SSH são negadas porque uma recolha de regras de rede de maior prioridade bloqueia-a. O processamento de regras para neste momento.

## <a name="rule-changes"></a>Alterações de regras

Se alterar uma regra para negar o tráfego previamente permitido, quaisquer sessões relevantes existentes serão retiradas.

## <a name="next-steps"></a>Próximos passos

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).