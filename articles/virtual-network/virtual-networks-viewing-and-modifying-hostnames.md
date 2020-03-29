---
title: Visualização e modificação dos nomes de anfitriões [ Microsoft Docs
description: Como visualizar e alterar nomes de anfitriões para máquinas virtuais Azure, funções web e trabalhadores para resolução de nomes
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058665"
---
# <a name="viewing-and-modifying-hostnames"></a>Visualização e modificação dos nomes de anfitriões
Para permitir que as suas instâncias de função sejam referenciadas pelo nome do anfitrião, deve definir o valor para o nome do anfitrião no ficheiro de configuração do serviço para cada função. Faça isso adicionando o nome de anfitrião desejado ao atributo **vmName** do elemento **Role.** O valor do atributo **vmName** é usado como base para o nome anfitrião de cada instância de função. Por exemplo, se o **vmName** for *webrole* e houver três instâncias desse papel, os nomes de anfitriões dos casos serão *webrole0*, *webrole1*, e *webrole2*. Não é necessário especificar um nome de anfitrião para máquinas virtuais no ficheiro de configuração, porque o nome de anfitrião de uma máquina virtual é povoado com base no nome da máquina virtual. Para obter mais informações sobre a configuração de um serviço Microsoft Azure, consulte [o Azure Service Configuration Schema (.cscfg File)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visualização de nomes de anfitriões
Pode ver os nomes de anfitriões de máquinas virtuais e exemplos de papéis num serviço na nuvem utilizando qualquer uma das ferramentas abaixo.

### <a name="service-configuration-file"></a>Ficheiro de configuração do serviço
Pode descarregar o ficheiro de configuração do serviço para um serviço implantado a partir da lâmina **Configure** do serviço no portal Azure. Em seguida, pode procurar o atributo **vmName** para o elemento **nome do papel** para ver o nome do anfitrião. Tenha em mente que este nome de anfitrião é usado como base para o nome anfitrião de cada instância de função. Por exemplo, se o **vmName** for *webrole* e houver três instâncias desse papel, os nomes de anfitriões dos casos serão *webrole0*, *webrole1*, e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto
Depois de ativar as ligações Remote Desktop (Windows), Windows PowerShell (Windows) ou SSH (Linux e Windows) às suas máquinas virtuais ou instâncias de papéis, pode visualizar o nome do anfitrião a partir de uma ligação de ambiente de trabalho remoto ativo de várias formas:

* Digite o nome de anfitrião no comando ou no terminal SSH.
* Digite ipconfig /tudo no pedido de comando (apenas windows).
* Ver o nome do computador nas definições do sistema (apenas no Windows).

### <a name="azure-service-management-rest-api"></a>API REST De Gestão de Serviços Azure
A partir de um cliente REST, siga estas instruções:

1. Certifique-se de que tem um certificado de cliente para se ligar ao portal Azure. Para obter um certificado de cliente, siga os passos apresentados em [Como: Transferir e Importar Publicar Definições e Informações de Subscrição](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Detete uma entrada de cabeçalho chamada x-ms-versão com um valor de 2013-11-01.
3. Envie um pedido no seguinte\/formato: https: /management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Procure o elemento **HostName** para cada elemento **RoleInstance.**

> [!WARNING]
> Também pode visualizar o sufixo de domínio interno para o seu serviço de nuvem a partir da resposta de chamada REST, verificando o elemento **InternalDnsSufix,** ou executando ipconfig /tudo a partir de um pedido de comando numa sessão de ambiente de trabalho remoto (Windows), ou executando gato /etc/resolve.conf de um terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificação de um nome de anfitrião
Pode modificar o nome do anfitrião para qualquer máquina virtual ou instância de função, carregando um ficheiro de configuração de serviço modificado, ou renomeando o computador a partir de uma sessão de Ambiente de Trabalho Remoto.

## <a name="next-steps"></a>Passos seguintes
[Resolução de Nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Configuração de serviço Azure Schema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede virtual Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar as definições de DNS utilizando ficheiros de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

