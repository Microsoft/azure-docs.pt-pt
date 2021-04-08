---
title: Definições de DNS de política de Azure Firewall
description: Pode configurar as políticas do Azure Firewall com configurações de servidor DNS e procuração de DNS.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633653"
---
# <a name="azure-firewall-policy-dns-settings"></a>Definições de DNS de política de Azure Firewall

Pode configurar um servidor DNS personalizado e ativar o proxy DNS para as políticas do Azure Firewall. Pode configurar estas definições quando implementar a firewall ou mais tarde a partir da página de **definições DE DNS.**

## <a name="dns-servers"></a>Servidores DNS

Um servidor DNS mantém e resolve nomes de domínio para endereços IP. Por padrão, o Azure Firewall utiliza o Azure DNS para resolução de nomes. A definição **do servidor DNS** permite configurar os seus próprios servidores DNS para resolução de nomes Azure Firewall. Pode configurar um único ou vários servidores.

### <a name="configure-custom-dns-servers"></a>Configurar servidores DNS personalizados

1. Selecione a sua política de firewall.
2. Em **Definições**, selecione **Definições DNS**.
3. Nos **servidores DNS,** pode escrever ou adicionar servidores DNS existentes que tenham sido previamente especificados na sua Rede Virtual.
4. Selecione **Guardar**.
5. A firewall agora direciona o tráfego DNS para o(s) servidor(s) especificado para resolução de nomes.

## <a name="dns-proxy"></a>Proxy DNS

Pode configurar a Azure Firewall para agir como um representante do DNS. Um proxy DNS atua como um intermediário para pedidos dns de máquinas virtuais de clientes para um servidor DNS. Se configurar um servidor DNS personalizado, deve ativar o proxy DNS para evitar a incompatibilidade de resolução de DNS e ativar a filtragem FQDN nas regras de rede.

Se não ativar o proxy DNS, os pedidos de DNS do cliente podem viajar para um servidor DNS num momento diferente ou devolver uma resposta diferente em comparação com a da firewall. O proxy DONS coloca o Azure Firewall no caminho dos pedidos do cliente para evitar inconsistências.

A configuração do DNS Proxy requer três passos:

1. Ativar o proxy DNS nas definições de DNS da Azure Firewall.
2. Configurar opcionalmente o seu servidor DNS personalizado ou utilizar o padrão fornecido.
3. Por fim, tem de configurar o endereço IP privado do Azure Firewall como um endereço DNS personalizado nas definições do servidor DNS da sua rede virtual. Isto garante que o tráfego DNS é direcionado para a Firewall Azure.

### <a name="configure-dns-proxy"></a>Configure o proxy DNS

Para configurar o proxy DNS, tem de configurar a definição de servidores DNS de rede virtual para utilizar o endereço IP privado de firewall. Em seguida, ative as **definições DNS** políticas de DNS da política do DNS do DNS do DNS do DNS .

#### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS de rede virtual

1. Selecione a rede virtual onde o tráfego DNS será encaminhado através do Azure Firewall.
2. Em **Definições**, selecione **servidores DNS**.
3. Selecione **Custom** em **servidores DNS**.
4. Insira o endereço IP privado da firewall.
5. Selecione **Guardar**.

#### <a name="enable-dns-proxy"></a>Ativar o proxy DNS

1. Selecione a sua política Azure Firewall.
2. Em **Definições**, selecione **as definições de DNS**.
3. Por predefinição, **o DNS Proxy** está desativado. Quando ativado, a firewall ouve na porta 53 e encaminha os pedidos dns para os servidores DNS configurados.
4. Reveja a configuração dos **servidores DNS** para se certificar de que as definições são adequadas para o seu ambiente.
5. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Filtragem FQDN nas regras da rede](fqdn-filtering-network-rules.md)