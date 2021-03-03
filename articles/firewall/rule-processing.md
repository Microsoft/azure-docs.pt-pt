---
title: Lógica de processamento de regras do Azure Firewall
description: O Azure Firewall tem regras NAT, regras de rede e regras de aplicações. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/01/2021
ms.author: victorh
ms.openlocfilehash: bbf838cfa2a6addc665df4b62e2322d056778b49
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741366"
---
# <a name="configure-azure-firewall-rules"></a>Configurar regras de Firewall Azure
Pode configurar regras DE NAT, regras de rede e aplicações no Azure Firewall. As recolhas de regras são processadas de acordo com o tipo de regra em ordem prioritária, números mais baixos para números mais altos de 100 a 65.000. Um nome de coleção de regras só pode ter letras, números, sublinhados, períodos ou hífens. Deve começar com uma letra ou número, e terminar com uma letra, número ou sublinhado. O comprimento máximo do nome é de 80 caracteres.

O melhor é inicialmente espaçar os números prioritários da sua coleção de regras em 100 incrementos (100, 200, 300, e assim por diante) para que tenha espaço para adicionar mais coleções de regras, se necessário.

> [!NOTE]
> Se permitir a filtragem baseada em ameaças baseadas em inteligência, essas regras são a maior prioridade e são sempre processadas primeiro. A filtragem da inteligência de ameaça pode negar o tráfego antes de serem processadas quaisquer regras configuradas. Para obter mais informações, consulte [a filtragem baseada em ameaças de Azure Firewall.](threat-intel.md)

## <a name="outbound-connectivity"></a>Conectividade de saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicações

Se configurar regras de rede e regras de aplicação, as regras de rede são aplicadas por ordem prioritária antes das regras de aplicação. As regras estão a acabar. Portanto, se uma correspondência for encontrada numa regra de rede, nenhuma outra regra é processada.  Se não houver correspondência de regras de rede, e se o protocolo for HTTP, HTTPS ou MSSQL, então o pacote é avaliado pelas regras de aplicação por ordem prioritária. Se ainda não for encontrado qualquer correspondência, o pacote é avaliado contra a recolha da [regra da infraestrutura.](infrastructure-fqdns.md) Se ainda não houver correspondência, então o pacote é negado por defeito.

#### <a name="network-rule-protocol"></a>Protocolo de regra de rede

As regras de rede podem ser configuradas para **TCP,** **UDP,** **ICMP** ou **qualquer** protocolo IP. Qualquer protocolo IP inclui todos os protocolos IP definidos no documento [de números atribuídos na Internet (IANA).](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) Se uma porta de destino estiver explicitamente configurada, a regra é traduzida para uma regra TCP+UDP.

Antes de 9 de novembro de 2020 **Qualquer um** significava **TCP**, **ou UDP**, ou **ICMP**. Então, você pode ter configurado uma regra antes dessa data com Protocolo = Qualquer, e portas de destino = '*'. Se não pretender permitir qualquer protocolo IP tal como definido atualmente, então modifique a regra para configurar explicitamente os protocolos que pretende (TCP, UDP ou ICMP).

## <a name="inbound-connectivity"></a>Conectividade de entrada

### <a name="nat-rules"></a>Regras NAT

A conectividade de entrada na Internet pode ser ativada configurando a tradução de endereços de rede de destino (DNAT) como descrito no [Tutorial: Filtrar o tráfego de entrada com DNAT Azure Firewall utilizando o portal Azure](tutorial-firewall-dnat.md). As regras DA são aplicadas na prioridade antes das regras da rede. Se for encontrada uma correspondência, é adicionada uma regra implícita de rede correspondente para permitir o tráfego traduzido. Por razões de segurança, a abordagem recomendada é adicionar uma fonte de internet específica para permitir o acesso do DNAT à rede e evitar a utilização de wildcards.

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

- Ação: Deny

|name  |Tipo de origem  |Origem  |Protocolo:Porta|FQDNs alvo|
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
- Ação: Deny

|name  |Protocolo  |Tipo de origem  |Origem  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Negar-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As ligações SSH são negadas porque uma recolha de regras de rede de maior prioridade bloqueia-a. O processamento de regras para neste momento.

## <a name="rule-changes"></a>Alterações de regras

Se alterar uma regra para negar o tráfego previamente permitido, quaisquer sessões relevantes existentes serão retiradas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).
