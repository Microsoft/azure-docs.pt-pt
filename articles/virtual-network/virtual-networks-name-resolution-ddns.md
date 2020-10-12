---
title: Utilização de DNS dinâmicos para registar os hostnames no Azure Microsoft Docs
description: Saiba como configurar DNS dinâmicos para registar nomes de anfitriões nos seus próprios servidores DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: 9f5b535a341956e5675ba96ba9570bd3f2ff3443
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710954"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Utilizar o DNS dinâmico para registar nomes de anfitrião no seu próprio servidor DNS

[O Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuais (VM) e instâncias de função. Quando as necessidades de resolução do seu nome excederem as capacidades fornecidas pelo DNS padrão do Azure, pode fornecer os seus próprios servidores DNS. A utilização dos seus próprios servidores DNS dá-lhe a capacidade de adaptar a sua solução DNS de acordo com as suas necessidades específicas. Por exemplo, poderá ter de aceder aos recursos no local através do seu controlador de domínio Ative Directory.

Quando os seus servidores DNS personalizados são hospedados como VMs Azure, pode encaminhar consultas de nome de anfitrião para a mesma rede virtual para Azure para resolver os nomes de anfitriões. Se não pretender utilizar esta opção, pode registar os seus nomes de anfitriões VM no seu servidor DNS utilizando DNS dinâmicos (DDNS). O Azure não tem as credenciais para criar registos diretamente nos seus servidores DNS, pelo que são frequentemente necessários arranjos alternativos. Seguem-se alguns cenários comuns, com alternativas:

## <a name="windows-clients"></a>Clientes windows
Os clientes do Windows não acompanhados de domínio tentam atualizações DDNS não garantidas quando iniciam ou quando o seu endereço IP muda. O nome DNS é o nome de anfitrião mais o sufixo de DNS primário. O Azure deixa o sufixo de DNS primário em branco, mas pode definir o sufixo no VM, através da interface do [utilizador](https://technet.microsoft.com/library/cc794784.aspx) ou [do PowerShell](/powershell/module/dnsclient/set-dnsclient).

Os clientes windows ligados ao domínio registam os seus endereços IP com o controlador de domínio utilizando DDNS seguro. O processo de união de domínio define o sufixo de DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux
Os clientes Linux geralmente não se registam com o servidor DNS no arranque, eles assumem que o servidor DHCP o faz. Os servidores DHCP da Azure não têm as credenciais para registar registos no seu servidor DNS. Pode utilizar uma ferramenta chamada `nsupdate` , que está incluída no pacote Bind, para enviar atualizações do DDNS. Como o protocolo DDNS está normalizado, pode ser utilizado `nsupdate` mesmo quando não estiver a utilizar o Bind no servidor DNS.

Pode utilizar os ganchos fornecidos pelo cliente DHCP para criar e manter a entrada de nome de anfitrião no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts em */etc/dhcp/dhclient-exit-hooks.d/*. Pode utilizar os ganchos para registar o novo endereço IP utilizando `nsupdate` . Por exemplo:

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

Também pode utilizar o `nsupdate` comando para executar atualizações seguras do DDNS. Por exemplo, quando se utiliza um servidor Bind DNS, é gerado um par de chaves público-privado `http://linux.yyz.us/nsupdate/` (). O servidor DNS está configurado `http://linux.yyz.us/dns/ddns-server.html` com a parte pública da chave, para que possa verificar a assinatura no pedido. Para fornecer o par-chave para `nsupdate` , use a `-k` opção, para que o pedido de atualização DDNS seja assinado.

Quando estiver a utilizar um servidor DNS do Windows, pode utilizar a autenticação Kerberos com o `-g` parâmetro em , mas não está `nsupdate` disponível na versão Windows de `nsupdate` . Para usar Kerberos, use `kinit` para carregar as credenciais. Por exemplo, pode carregar credenciais a partir de um [ficheiro keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html), em `nsupdate -g` seguida, recolhe as credenciais, a partir da cache.

Se necessário, pode adicionar um sufixo de pesquisa de DNS aos seus VMs. O sufixo DNS é especificado no ficheiro */etc/resolve.conf.* A maioria dos distros Linux gerem automaticamente o conteúdo deste ficheiro, por isso normalmente não o podes editar. No entanto, pode anular o sufixo utilizando o comando do cliente `supersede` DHCP. Para sobrepor o sufixo, adicione a seguinte linha ao ficheiro */etc/dhcp/dhclient.conf:*

```
supersede domain-name <required-dns-suffix>;
```
