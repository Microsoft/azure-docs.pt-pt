---
title: Lógica de processamento de regras do Azure Firewall
description: O Azure Firewall tem regras nat, regras de rede e regras de aplicações. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264781"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall
Pode configurar regras nat, regras de rede e aplicações em Azure Firewall. As regras são processadas de acordo com o tipo de regra. 

> [!NOTE]
> Se permitir a filtragem baseada em ameaças, essas regras são a maior prioridade e são sempre processadas primeiro. A filtragem da inteligência de ameaça pode negar o tráfego antes de quaisquer regras configuradas serem processadas. Para mais informações, consulte a [filtragem baseada em informações baseadas em ameaças de Firewall.](threat-intel.md)

## <a name="outbound"></a>Saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e aplicações

Se configurar as regras de rede e as regras de aplicação, as regras de rede são aplicadas por ordem prioritária antes das regras de aplicação. As regras estão a acabar. Portanto, se um fósforo for encontrado numa regra de rede, nenhuma outra regra é processada.  Se não houver uma correspondência de regras de rede, e se o protocolo for HTTP, HTTPS ou MSSQL, então o pacote é avaliado pelas regras de aplicação em ordem prioritária. Se ainda não for encontrado um fósforo, o pacote é avaliado contra a recolha das regras de [infraestrutura.](infrastructure-fqdns.md) Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="inbound"></a>Entrada

### <a name="nat-rules"></a>Regras na NAT

A conectividade de acesso à Internet pode ser ativada através da configuração da Tradução de Endereços da Rede de Destino (DNAT), conforme descrito no [Tutorial: Filtrar o tráfego de entrada com o DNAT da Firewall Azure utilizando o portal Azure](tutorial-firewall-dnat.md). As regras do NAT são aplicadas priorariamente antes das regras da rede. Se for encontrada uma correspondência, é adicionada uma regra de rede correspondente implícita para permitir a adição do tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido.

As regras de aplicação não são aplicadas para ligações de entrada. Por isso, se pretender filtrar o tráfego HTTP/S de entrada, deve utilizar firewall de aplicação web (WAF). Para mais informações, consulte o que é o Firewall de [aplicação web do Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemplos

Os exemplos seguintes mostram os resultados de algumas destas combinações de regras.

### <a name="example-1"></a>Exemplo 1

A ligação ao google.com é permitida devido a uma regra de rede correspondente.

**Regra da rede**

- Ação: Permitir


|nome  |Protocol  |Tipo de fonte  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir web     |TCP|Endereço IP|*|Endereço IP|*|80,443

**Regra de candidatura**

- Ação: Negar

|nome  |Tipo de fonte  |Origem  |Protocolo:Porto|FQDNs alvo|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Endereço IP|*|http:80,https:443|google.com

**Resultado**

A ligação à google.com é permitida porque o pacote corresponde à regra da rede *de internet de permitir.* O processamento de regras para neste momento.

### <a name="example-2"></a>Exemplo 2

O tráfego ssh é negado porque uma prioridade mais alta A recolha de regras da rede *Deny* bloqueia-o.

**Coleção de regras de rede 1**

- Nome: Recolha de autorizações
- Prioridade: 200
- Ação: Permitir

|nome  |Protocol  |Tipo de fonte  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Permitir-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Coleção de regras de rede 2**

- Nome: Deny-collection
- Prioridade: 100
- Ação: Negar

|nome  |Protocol  |Tipo de fonte  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As ligações SSH são negadas porque uma maior prioridade de recolha de regras de rede bloqueia-a. O processamento de regras para neste momento.

## <a name="rule-changes"></a>Alterações de regras

Se alterar uma regra para negar o tráfego anteriormente permitido, quaisquer sessões relevantes são retiradas.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).