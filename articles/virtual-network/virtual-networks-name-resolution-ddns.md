---
title: Utilização de DNS dinâmicos para registar nomes de anfitriões em Azure [ Microsoft Docs
description: Aprenda a configurar DNS dinâmicos para registar nomes de anfitriões nos seus próprios servidores DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: 79efe3cef82a166ca6b56dea5cb07f15a5325083
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650318"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Utilizar o DNS dinâmico para registar nomes de anfitrião no seu próprio servidor DNS

[O Azure fornece resolução](virtual-networks-name-resolution-for-vms-and-role-instances.md) de nomes para máquinas virtuais (VM) e exemplos de papéis. Quando a sua resolução de nome supõe exceder as capacidades fornecidas pelo DNS padrão do Azure, pode fornecer os seus próprios servidores DNS. A utilização dos seus próprios servidores DNS dá-lhe a capacidade de adaptar a sua solução DNS de acordo com as suas necessidades específicas. Por exemplo, poderá ter de aceder aos recursos no local através do seu controlador de domínio Ative Directory.

Quando os seus servidores DNS personalizados forem hospedados como VMs Azure, pode reencaminhar consultas de nome de anfitrião para a mesma rede virtual para o Azure para resolver nomes de anfitriões. Se não pretender utilizar esta opção, pode registar os nomes de anfitriões vM no seu servidor DNS utilizando DNS dinâmicos (DDNS). O Azure não tem as credenciais para criar diretamente registos nos seus servidores DNS, pelo que são frequentemente necessários arranjos alternativos. Alguns cenários comuns, com alternativas seguem:

## <a name="windows-clients"></a>Clientes windows
Os clientes windows não unidos pelo domínio tentam atualizações DDNS não seguras quando iniciam, ou quando o seu endereço IP muda. O nome DNS é o nome de anfitrião mais o sufixo dNS primário. O Azure deixa o sufixo DNS primário em branco, mas pode definir o sufixo no VM, através da interface de [utilizador](https://technet.microsoft.com/library/cc794784.aspx) ou [powerShell](/powershell/module/dnsclient/set-dnsclient).

Os clientes windows unidos pelo domínio registam os seus endereços IP com o controlador de domínio utilizando DDNS seguros. O processo de união de domínio define o sufixo principal do DNS no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux
Os clientes linux geralmente não se registam com o servidor DNS no arranque, assumem que o servidor DHCP o faz. Os servidores DHCP da Azure não têm as credenciais para registar registos no seu servidor DNS. Pode utilizar uma ferramenta chamada `nsupdate` , que está incluída no pacote Bind, para enviar atualizações de DDNS. Como o protocolo DDNS é normalizado, pode utilizar `nsupdate` mesmo quando não estiver a utilizar o Bind no servidor DNS.

Pode utilizar os ganchos fornecidos pelo cliente DHCP para criar e manter a entrada do nome de anfitrião no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts em */etc/dhcp/dhclient-exit-hooks.d/*. Pode utilizar os ganchos para registar o novo endereço IP utilizando `nsupdate` . Por exemplo:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Também pode utilizar o `nsupdate` comando para executar atualizações DDNS seguras. Por exemplo, quando se está a utilizar um servidor Bind DNS, é gerado um par de chaves público-privado `http://linux.yyz.us/nsupdate/` (). O servidor DNS está configurado ( `http://linux.yyz.us/dns/ddns-server.html` ) com a parte pública da chave, para que possa verificar a assinatura no pedido. Para fornecer o par de chaves `nsupdate` para, use a `-k` opção, para que o pedido de atualização DDNS seja assinado.

Quando estiver a utilizar um servidor DNS do Windows, pode utilizar a autenticação Kerberos com o `-g` parâmetro dentro , mas não está `nsupdate` disponível na versão Windows de `nsupdate` . Para usar Kerberos, use `kinit` para carregar as credenciais. Por exemplo, pode carregar credenciais a partir de um [ficheiro keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), em seguida, `nsupdate -g` recolhe as credenciais, a partir da cache.

Se necessário, pode adicionar um sufixo de pesquisa DNS aos seus VMs. O sufixo DNS é especificado no ficheiro */etc/resolve.conf.* A maioria dos distros Linux gere automaticamente o conteúdo deste ficheiro, por isso normalmente não pode editá-lo. No entanto, pode anular o sufixo utilizando o comando do cliente `supersede` DHCP. Para sobrepor o sufixo, adicione a seguinte linha ao ficheiro */etc/dhcp/dhclient.conf:*

```
supersede domain-name <required-dns-suffix>;
```
