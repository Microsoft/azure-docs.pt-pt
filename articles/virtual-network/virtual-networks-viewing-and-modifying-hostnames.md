---
title: Ver e modificar os nomes de anfitrião | Documentos da Microsoft
description: Como ver e alterar os nomes de anfitrião para máquinas virtuais do Azure, da web e funções de trabalho para resolução de nomes
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3fdb0f566789382a1606b19e4fac179f9ecf40cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887464"
---
# <a name="viewing-and-modifying-hostnames"></a>Ver e modificar os nomes de anfitrião
Para permitir que as instâncias da função a ser referenciado por nome de anfitrião, tem de definir o valor para o nome de anfitrião no ficheiro de configuração do serviço para cada função. Fazer isso adicionando o nome de anfitrião pretendido para o **vmName** atributo da **função** elemento. O valor do **vmName** atributo é utilizado como base para o nome de anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e há três instâncias de função, os nomes de anfitrião das instâncias será *webrole0*, *webrole1*, e *webrole2*. Não é necessário especificar um nome de anfitrião para máquinas virtuais no ficheiro de configuração, porque o nome de anfitrião para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço do Microsoft Azure, consulte [esquema de configuração de serviço do Azure (. cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Ver os nomes de anfitrião
Pode ver os nomes de anfitrião de máquinas virtuais e instâncias de função num serviço cloud, utilizando qualquer uma das ferramentas abaixo.

### <a name="service-configuration-file"></a>Ficheiro de configuração de serviço
Pode transferir o ficheiro de configuração de serviço para um serviço implementado a partir da **configurar** painel do serviço no portal do Azure. Em seguida, pode procurar o **vmName** atributo para o **nome da função** elemento para ver o nome de anfitrião. Tenha em atenção que este nome de anfitrião é utilizado como base para o nome de anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e há três instâncias de função, os nomes de anfitrião das instâncias será *webrole0*, *webrole1*, e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto
Depois de ativar a área de trabalho remota (Windows), comunicação remota do Windows PowerShell (Windows) ou ligações SSH (Linux e Windows) para suas máquinas virtuais ou instâncias de função, pode ver o nome de anfitrião a partir de uma ligação de ambiente de trabalho remoto Active Directory de várias formas:

* Escreva o nome de anfitrião na linha a linha de comandos ou SSH terminal.
* Digitar ipconfig/todos ao comando (apenas Windows) de linha de comandos.
* Ver o nome de computador nas definições do sistema (apenas Windows).

### <a name="azure-service-management-rest-api"></a>REST API de gestão de serviço do Azure
De um cliente REST, siga estas instruções:

1. Certifique-se de que tem um certificado de cliente para ligar ao portal do Azure. Para obter um certificado de cliente, siga os passos apresentados no [como: Transferir e importar publicar as informações de subscrição e definições](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Defina uma entrada de cabeçalho com o nome x-ms-version com um valor de 2013-11-01.
3. Enviar um pedido no seguinte formato: https:\//management.core.windows.net/\<id da subscrição\>/serviços/hostedservices/\<nome do serviço\>? detalhes incorporar = true
4. Procure o **HostName** elemento para cada **RoleInstance** elemento.

> [!WARNING]
> Também pode ver o sufixo de domínio interno do serviço em nuvem da resposta de chamada REST, verificando a **InternalDnsSuffix** elemento, ou ao executar o ipconfig/tudo a partir de um prompt de comando numa sessão de área de trabalho remota (Windows) ou por executar o gato /etc/resolv.conf um SSH terminal (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificar um nome de anfitrião
Pode modificar o nome de anfitrião para qualquer máquina virtual ou instância de função ao carregar um ficheiro de configuração do serviço modificado ou ao mudar o nome do computador a partir de uma sessão de ambiente de trabalho remoto.

## <a name="next-steps"></a>Passos Seguintes
[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração de serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede Virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Especifique as definições de DNS usando arquivos de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

